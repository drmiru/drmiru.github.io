---
id: 365
title: How to change the pool master in a XenServer farm
date: 2010-12-13T21:50:20+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=365
permalink: /how-to-change-the-pool-master-in-a-xenserver-farm/
aktt_notify_twitter:
  - 'no'
categories:
  
  - XenServer
---
First you have to disable HA on the pool

**#xe pool-ha-disable**

List the hosts in the pool

**#xe host-list**

Promote the new slave

**\# xe pool-designate-new-master host-uuid=<type the uuid of the new master>**

> If the pool master is not available the switch has to be enforced to succeed.

 **#xe pool-emergency-transition-to-master**

re-establish connectio to the slave hosts

 **#xe pool-recover-slave**

You&#8217;re done!