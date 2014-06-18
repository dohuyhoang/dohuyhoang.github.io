---
layout: post
title:  "Which OpenCloud I choose? OpenStack, CloudStack or VMware"
date:   2014-04-15 15:52:47
categories: OpenCloud OpenStack CloudStack VMware
---

In recent years, we can observer a rapid advance of cloud IaaS technology. In the past, only VMware made its name and widely used in DC management. Then the rise of AWS make a huge impact on the industry. To fight again the Godzilla EC2, and with the needs of private cloud, latecomers enterprises and communities join force to create different OpenSource IaaS platform:

* Eucalyptus: a second broadest community and an official partner with AWS.
* CloudStack: a most completed product, used to be an closed software belong to Citrix. They decide to contribute source code to Apache to join the race. Small but well-tested. 
* OpenNebula: with smallest community. 
* OpenStack: with broadest community, back by giants, and illimited pocket of money. 

All of them joined a fierce stack war. I can't say which of them will win, but we will focus on the leading group: CloudStack - a number one in term of industry deploying, OpenStack - a number one in term of community and supports, and VMware - an ancient giant. 

# OpenCloud vs VMware

The most traditional technology for DC virtualization is VMware vCenter. Why I called it *traditional*. Because this tool has been developed for a long time, and was adopted by many legacy systems. However, we hardly called it Cloud technology. I have a honor to consult Mr. Thierry Carrez - OpenStack release manager. He gave me an answer that I found the most correct:

*Traditional workloads run on servers that are like pets. They are
unique, you care for them, heal them when they are hurt, have HA in
place to make sure they survive etc.*

*Cloud servers are like cattle. They are generic, are set up
automatically using automation, and when they exhibit signs of failure
you shoot them.*

* *If your goal is to virtualize traditional workloads, you may find VMWare
offering more attractive, with nice HA / live migration features to
ensure the survival of your hand-crafted servers.*
* *If your goal is more to heavily use automation, develops style deployments
and Amazon AWS like services, then OpenStack is the way to go.*

[Pet and cattle analogy][petcattle]


# Why not CloudStack?
CloudStack is the top competitor of OpenStack in OpenCloud market now.

CloudStack is obvious much product oriented. I think **OpenStack and CloudStack** is similar to **Windows vs Linux kernel**. 

* Although CloudStack is open-source, but recently report, Citrix contributes more than 50% to the project. 
* While OpenStack has Rackspace, IBM, HP, Redhat, Mirantis are top contributors, and dozen of companies contributes tiny parts. In addition, each platinum contributer has their own product-tested version based on core OpenStack.  

# OpenStack
The concept is similar to Linux kernel and Linux distro: OpenStack is a framework (Linux kernel). Some of the companies in that ecosystem package the solution so that it's ready to use (Linux distro):

* **Rackspace Private Cloud (Rackspace)**
* Linux distributions like Red Hat or Ubuntu (Canonical) have offerings OpenStack package.
* ENovance (France/Canada) 
* Cloudscaling (US) 
* Mirantis (US/Russia)
* IBM (commercial version)

# Rackspace Private Cloud
Rackspace Private Cloud is an OpenStack configuration that has been developed, tested, and packaged by Rackspace OpenStack specialists. Offering that is easy to install, with a configuration that comes from our expertise in developing OpenStack clouds for our customers.
#### Strength
* Easy to install.
* Well documented.
* One deployment design (designed HA, networking, etc).
* Based on latest release version of OpenStack (Havana)
* Using Chef cookbook.
* Central forum for discussion. Fast (free) support. 
* Rackspace is the company contributed the most for OpenStack recently.

#### Weakness
* Not flexible (must follow Rackspace deployment design).
* Use latest OpenStack release version, not development version.
* Lack of OpenStack new features (incubating projects). Not sure can we add them manually. 
* Not sure OpenStack third parties extension can work.


[petcattle]: http://www.slideshare.net/randybias/pets-vs-cattle-the-elastic-cloud-story


