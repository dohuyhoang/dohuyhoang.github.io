---
layout: post
title:  "Introduction to OpenCloud"
date:   2014-04-22 16:00:00
categories: OpenCloud Cloud OpenStack CloudStack IaaS
---

The **Cloud** is one of the most popular term on Internet nowadays. Cloud computing is widely adopted by all scaled enterprises. By wikipedia definition, cloud computing is computing that involves a large number of computers connected through a communication network such as the Internet, similar to utility computing. In science, cloud computing is a synonym for distributed computing over a network, and means the ability to run a program or application on many connected computers at the same time.  

Infrastructure as a Service (IaaS) is one of the three fundamental services of cloud computing. It aims for providing a hardware infrastructure, including the virtual servers, network connections, IP address, storage hardwares, etc. or a pool of resources. This resource pool can be scale easily later. 

One well-known example of IaaS provider is Amazon Web Service (AWS). They provide users scalable clusters with unlimited VMs. Users don't care about networking or hardwares below. Users can add more storage space, CPU, RAM for their VMs anytime. 

![CloudStack](/assets/media/CloudStack.gif 'Stack of three fundamental services')

![CloudStackDetail](/assets/media/CloudStackDetail.png 'Detail of three services')

AWS is considered public cloud. Amazon controls the hardware, and all users share that resource pool. All users' data is stored on Amazon's hardware property. That create the fear of vendor lock-in - a day when the IaaS refuse to serve and claim all data on their servers. That gives birth for the **Private Cloud** trend - the cloud that runs on enterprise's own hardware. On the market, there are plenty of Open Source and commercial product that help building private cloud:

* OpenStack
* CloudStack
* Eucalyptus

They are all open source. Among them, OpenStack is having the momentum. They have largest community, as well as supported by many companies: HP, IBM, Rackspace, NASA, Mirantis, etc.    



<http://en.wikipedia.org/wiki/Cloud-computing>


