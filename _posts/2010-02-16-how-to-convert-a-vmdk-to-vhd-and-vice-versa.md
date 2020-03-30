---
id: 199
title: How to convert a vmdk to vhd and vice versa?
date: 2010-02-16T15:42:24+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=199
permalink: /how-to-convert-a-vmdk-to-vhd-and-vice-versa/
categories:
  - Virtualization
---
Starwind Software offers a free tool to convert virtual disk files from vmdk to vhd format and vice versa. This is especially useful if using Provisioning Server and there is the need to update PVS Agent or Hypervisor Tools inside the guest (such as vmware tools). Just convert to vmdk, boot and update your stuff, then convert back to vhd&#8230; done!

No (time stealing) reverse imaging any more.  
Find the tool here&#8230;

<a href="http://www.starwindsoftware.com/download/1/StarWindConverter.exe" target="_blank">http://www.starwindsoftware.com/download/1/StarWindConverter.exe</a>

There is also a good post covering this.  
<a href="http://jariangibson.com/2009/12/09/upgrading-provisioning-services-vdisks-target-device-software-virtual-machine-tools-and-network-drivers/" target="_blank">http://jariangibson.com/2009/12/09/upgrading-provisioning-services-vdisks-target-device-software-virtual-machine-tools-and-network-drivers/</a>