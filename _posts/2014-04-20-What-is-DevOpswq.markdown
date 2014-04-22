---
layout: post
title:  "What is DevOps."
date:   2014-04-22 14:00:00
categories: DevOps
---
I'm not an expert in DevOps myself. This blog is merely a digest note from my reading and very little experience. I'm intending to update it when I gain more experience or from your help. 

# What is DevOps

DevOps stands for Development and Operations. First of all, it's an "another" software development methodology. Of course, as other siblings, this methodology is created to solve some problem in software world. 

![Wiki](http://upload.wikimedia.org/wikipedia/commons/b/b5/Devops.svg http://en.wikipedia.org/wiki/DevOps)

## The problem

Simply speaking, development guys and operations guys are enemy of each other. Development means to change and evolve. While operations need stability. Some statistic numbers: 80% downtime is due to changes. 47% of total time dedicated to deployment.

But of course we need development. The problem is the misunderstanding between Dev and Ops, that we call **The wall of Confusion**. To add fuel into the fire, many global companies have Dev team in Vietnam and Ops team in Europe. Geometry distance will enlarge the wall. 

The worst day is **release/deployment** day, when changes are pushed from Dev team to Ops team. The most typical problem is the differences in environment between Dev and Ops. The Ops team has to hack the product, modify all configuration files. And when they give up, changes will be rolled back in Ops, bugs are reported to Devs, and Dev guys will say f\*\*k  

## The solution

DevOps focuses on communication. Cooperation between Dev and Ops must start before and continues long after release/deployment. 

We should also considering the voices again DevOps. I read [an article](http://jeffknupp.com/blog/2014/04/15/how-devops-is-killing-the-developer/) of Jeff Knupp, and I think his point of view is interesting (not that I agree). He complained force a developer to handle tasks of QA, sysadmin, analyst. That's the birth of "full-stack" developer, and some traditional dev who only love programming will feel annoyed (not in my case definitely). 

Not so convinced?

## Which companies used DevOps

All of software companies can use it, of course. 
But the one who need it the most is the companies with very frequent releases. For example, Flickr with 10 deployments per day. Facebook with 1 deployment each day. Amazon with a deployment per 11 seconds.   

## Ok I'll use DevOps, but how to use?

As mentioned above, DevOps is a methodology. That means you need tools, training, changes in approach, changes in culture, etc. to adopt it. The following is just some tips based on my experience to help. 

* **Chef**: with chef, you write the code to configure the infrastructure (infrastructure as a code). The good point is you don't have to worry about the different in env between Dev and Ops. You can check this [slide](http://www.slideshare.net/JulianDunn/chef-introduction-princeton-meetup) for more info.

* **Docker**: provide a unique sandbox environment for apps. It's quite popular now, and is a competitor of Chef.

* **The culture**: train dev with QA, sysadmin roles. Train sysadmin how to program. Build a team with both of them.  

* **Deploying**: deploy frequently. A deploy with smaller code changes is better than an upgrade with hundred files changes. 

This [slides](http://www.slideshare.net/OCTOTechnology/introduction-to-devops-28779951) provides plenty good examples. 

Now it's your turn to help. Please sharing your own experience using DevOps. 
