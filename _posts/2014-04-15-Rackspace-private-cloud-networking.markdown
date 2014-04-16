---
layout: post
title:  "Introduce Rackspace Private Cloud Networking"
date:   2014-04-15 13:52:47
categories: OpenStack Rackspace OpenCloud Networking
---
# Rackspace Private Cloud networking

## OpenStack networking concepts
In OpenStack neutron, there are two types of networks:

* **Provider network**: simply speaking, it is a physical network inside (or between) your DCs. This network is shared between tenants. There are two way of config this network:
	* Flat (untagged)
	* VLAN (tagged)

* **Tenant network**: or virtual network inside connect each tenant. Of course, it is not shared between 2 tenant. However, two tenant network may connect to each other using a Neutron virtual router + L3 (Layer 3) agent. This type of network may be configed using: 
	* vlan (tagged)
	* gre (unique id)

## Network design 

### Flat network 

Most basic design. Physical network resides in the same broadcast domain.

Check out the [Rackspace knowledge center][RPC_knowledge] for more information

[RPC_knowledge]: http://www.rackspace.com/knowledge_center/article/about-rackspace-private-cloud
