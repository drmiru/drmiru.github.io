---
id: 43
title: How to run an ESX 3.x Host inside VMware Workstation 6.5
date: 2009-04-20T18:04:19+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=43
permalink: /how-to-run-an-esx-3x-host-inside-vmware-workstation-65/
categories:
  
tags:
  - ESX
  - VMware
---
This post describes how to configure a VMware Workstation 6.5 VM to run an ESX 3.x Hypervisor inside it.  
Follow the steps below&#8230;

&#8211; Create a new Linux VM  
&#8211; Add 768 MB RAM or more  
&#8211; Add 1-2 CPUs  
&#8211; Add a local SCSI disk with at least 4GB

Edit the .vmx file and add the following lines:

\### ESX on Workstation Add on  
ethernet0.virtualDev = &#8220;e1000&#8221;  
ethernet0.connectionType = &#8220;bridged&#8221;  
ethernet0.adressType = &#8220;generated&#8221;  
monitor\_control.restrict\_backdoor = TRUE  
monitor_control.vt32 = TRUE  
monitor.virtual_exec = hardware  
\### End ESX on Workstation Add on

&#8211; save the .VMX File and start the VM  
&#8211; boot from ISO file or physical CDROM to install your copy of ESX 3.x

You&#8217;re done!