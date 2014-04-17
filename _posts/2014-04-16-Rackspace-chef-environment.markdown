---
layout: post
title:  "My Rackspace chef environment"
date:   2014-04-15 13:52:47
categories: OpenStack Chef Rackspace 
---

This is a note of how I change Rackspace Private Cloud (RPC) chef's environment before bootstrap own nodes.

{% highlight json %}
{
   "name": "MDI-private-cloud",
   "description": "Mobile Device - Rackspace Private Cloud v4.2.2",
   "cookbook_versions": {
   },
   "json_class": "Chef::Environment",
   "chef_type": "environment",
   "default_attributes": {
   },
   "override_attributes": {
	 "custom_template_banner": "Mobile Devices Ingenierie Cloud",
	 "developer_mode": false,
	 "enable_testing_repos": false,
         "nova": {
            "network": {
                "provider": "neutron"
            }
          },
         "neutron": {
            "ovs": {
	    	"external_bridge": "",
                "provider_networks": [
	              {
	                 "label": "ph-eth0",
	                 "bridge": "br-eth0",
	                 "vlans": "1:1"
	               }
	         ],
	         "network_type": "gre"
	     }
	  },
	  "mysql": {
	     "allow_remote_root": true,
	     "root_network_acl": "%"
	  },
	  "osops_networks": {
	     "nova": "192.168.1.0/24",
	     "public": "192.168.1.0/24",
	     "management": "192.168.1.0/24"
	  }
   }
}
{% endhighlight %}
