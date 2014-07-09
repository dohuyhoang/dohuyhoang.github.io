---
layout: post
title:  "Configure OpenStack network on OVH"
date:   2014-07-07 16:00:00
categories: OpenStack OVH neutron
---

Configure OpenStack network on OVH servers indeed a toughest mission that I've ever done. OVH has MAC-IP check on gateway, public IPs which has different CIDR from the host machine, public IPs may be fragmented. I managed to solve (partial) problem by various tricks.


## Constraints of OVH networks

* The OVH router only access defined MAC address, packets with strange MAC (same IP source) will be rejected. It can be tested by ping the gateway:

```bash
sudo nping --icmp --icmp-type 8 --source-mac fa:16:3e:e6:78:01 176.31.105.254
sudo nping --icmp --icmp-type 8 176.31.105.254
```
* RIPE IPs with different CIDR is still managed by the same router, by mapping MAC address. So VMs with RIPE IP must config gateway = gateway of host. These are the RIPE IP block that I use:

```
=================================
IP              MAC
46.105.252.216  02:00:00:c5:7a:13
46.105.252.217  02:00:00:51:23:e0
46.105.252.218  02:00:00:ee:60:a6
46.105.252.219  02:00:00:85:83:df
==================================
```

* OpenStack uses NAT at gateway virtual router, so in this case, we need to change the MAC address of gateway virtual router interface.

* OpenStack use network namespace to separate networks. So to debug/configure OpenStack network, must run this commands with the namespace. For example, run ```ipconfig``` command, must use this:

```bash
sudo ip netns
sudo ip netns exec qrouter-d1ceaf19-4cf0-4334-9f96-7601c70cbf4a ifconfig
```

instead this:

```bash
sudo ifconfig
```

* OVH ARP control: all traffic between local nodes must go through gateway (no two local nodes talking directly to each other):

```bash
sudo arp -a
vss-gw-6k.fr.eu (176.31.105.254) at 00:07:b4:00:00:02 [ether] on eth0
controller (176.31.105.64) at 00:07:b4:00:00:02 [ether] on eth0

```

## Overview 

This is the output of my network config:


```
                             |    br-ex: 176.31.255.64    |
                             |                            | 
                OVH          |                    46.105.252.216               192.168.100.1
Internet-----|gateway|-------||eth0|------------------|qg||                         |qr|--(Localnet)
                                                        |                            |
          176.31.255.255     176.31.255.64              |_____| neutron router |_____| 192.168.100/24                                                                       |      
                                                                   iptables NAT                                                                              46.105.252.217 <--> 192.168.100.40
                                                        46.105.252.218 <--> 192.168.100.41
                                                        46.105.252.219 <--> 192.168.100.41
                                                        46.105.252.216 <--- 192.168.100.0/24 
<-----------OVH network-----------------> <-----------------OpenStack network----------------------->

```

* ```qg```, ```qr```: two OpenStack virtual interfaces of neutron router.
* ```br-ex```: bridge interfaces ```eth0``` and ```qg```
* ```neutron router```: use an iptables NAT to translate an floating IP to an OpenStack local network.
* VM without floating IP: general NAT: ```192.168.100.0/24 --> 46.105.252.216``` (router interface)
* VM with floating IP:    two way NAT: ```192.168.100.40  <--> 46.105.252.217```



## Prepare host networking

Run this script to bridge OVH network to OpenStack network

```bash
#! /bin/bash
echo "Begin network configuration"
sudo ifconfig br-ex hw ether MAC-OF-eth0
sudo ifconfig eth0 promisc up
sudo ovs-vsctl add-port br-ex eth0

sudo route add default gw 176.31.105.254 br-ex
sudo ifconfig br-ex 176.31.105.64
sudo ifconfig eth0 0.0.0.0

sudo service neutron-plugin-openvswitch-agent restart
sudo service neutron-l3-agent restart
sudo service neutron-dhcp-agent restart
sudo service neutron-metadata-agent restart
```

## Create external network

Full [guide](http://docs.openstack.org/icehouse/install-guide/install/apt-debian/content/neutron-initial-networks.html)
External network will stand between VMs internal network and the outside network (Internet). It must have:

* CIDR: the same with RIPE IP block (46.105.252.216 - 46.105.252.219)
* Gateway: the gateway IP of host

```bash
neutron net-create ext-net --shared --router:external=True
```

```bash
neutron subnet-create ext-net --name ext-subnet \
  --allocation-pool start=46.105.252.216,end=46.105.252.219\
    --disable-dhcp --gateway 176.31.105.254 46.105.252.216/24
```

## Create tenant networks

Create a demo ```192.168.100.1``` network, can use CLI or dashboard:

* Use Dashboard: ```Project->Network->Networks->Create Network```
* Console:

```bash
neutron net-create demo-net
neutron subnet-create demo-net --name demo-subnet 192.168.100.0/24
```


## Create an internal router 

* Dashboard: ```Project->Network->Routers->Create Router```
* Console:

```bash
neutron router-create demo-router
```


## Connect external network to router

Set gateway for router is ext-net, then connect router to demo-net:

* Dashboard: ```Project->Network->Routers```
* Console:

```bash
neutron router-interface-add demo-router demo-subnet
neutron router-gateway-set demo-router ext-net
```

## OVH MAC address solution

### Check OpenStack NAT table

```bash
sudo ip netns exec qrouter-d1ceaf19-4cf0-4334-9f96-7601c70cbf4a iptables -t nat -S
```

### Set router gateway = default gateway of host ```176.31.105.254```. Add this route to Network node

```bash
sudo ip netns
sudo ip netns exec qrouter-d1ceaf19-4cf0-4334-9f96-7601c70cbf4a route add 176.31.105.254/32 dev qg-1cc33b0c-7d
sudo ip netns exec qrouter-d1ceaf19-4cf0-4334-9f96-7601c70cbf4a route add default gw 176.31.105.254 qg-1cc33b0c-7d
```

### Change MAC address for router gateway port, use the MAC address of the first IP in RIPE block (46.105.252.216)

```bash
sudo ip netns exec qrouter-d1ceaf19-4cf0-4334-9f96-7601c70cbf4a ifconfig qg-1cc33b0c-7d hw ether 02:00:00:c5:7a:13
```

### Add MAC addresses of RIPE ip block to router gateway

OVH map each IP in RIPE with a MAC address. We must add all of these MAC addresses to OVH router.

For example, if we have that RIPE:

```
==================================================================================
IP              MAC                     Purpose
46.105.252.216  02:00:00:c5:7a:13       Router IP, do not change
46.105.252.217  02:00:00:51:23:e0       Floating IP, can assign to VM
46.105.252.218  02:00:00:ee:60:a6       Floating IP, can assign to VM
46.105.252.219  02:00:00:85:83:df       Floating IP, can assign to VM
===================================================================================
```

We already add the first MAC address to router gateway. Then now add the second, third, fourth MAC to gateway interface:

```bash
sudo ip netns exec qrouter-f918cbb7-dc0c-4713-a6f5-3c66b46e12cf ip link add link qg-0103d6fa-31 address 02:00:00:51:23:e0 eth0.1 type macvlan
sudo ip netns exec qrouter-f918cbb7-dc0c-4713-a6f5-3c66b46e12cf ip link add link qg-0103d6fa-31 address 02:00:00:ee:60:a6 eth0.2 type macvlan
sudo ip netns exec qrouter-f918cbb7-dc0c-4713-a6f5-3c66b46e12cf ip link add link qg-0103d6fa-31 address 02:00:00:85:83:df eth0.3 type macvlan
sudo ip netns exec qrouter-f918cbb7-dc0c-4713-a6f5-3c66b46e12cf ip link set up eth0.1
sudo ip netns exec qrouter-f918cbb7-dc0c-4713-a6f5-3c66b46e12cf ip link set up eth0.2
sudo ip netns exec qrouter-f918cbb7-dc0c-4713-a6f5-3c66b46e12cf ip link set up eth0.3
```

## Modify the security group 
**On dash board**: ``Project -> Compute -> Access & Security -> Securities Group -> Manage Rules``: add ICMP, TCP port 22 rules.

**Or on console**:

```bash
neutron security-group-rule-create --protocol icmp \
  --direction ingress --remote-ip-prefix 0.0.0.0/0 default

  neutron security-group-rule-create --protocol tcp \
    --port-range-min 22 --port-range-max 22 \
      --direction ingress --remote-ip-prefix 0.0.0.0/0 default
```

## Conclusion 

At this point, the VMs with floating IP assigned can be connected from a remote host. However, this solution doesn't tackle fragmented public IP block yet. As I remember correctly, someone has shared his solution to deal with multiple floating IP blocks by direct injecting to neutron floating ip database. I'll come up after testing that approach.





