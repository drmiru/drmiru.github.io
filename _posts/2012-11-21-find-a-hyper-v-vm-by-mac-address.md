---
id: 941
title: Find a Hyper-V VM by MAC Address
date: 2012-11-21T23:19:23+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=941
permalink: /find-a-hyper-v-vm-by-mac-address/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Hyper-V
  - Microsoft
  - Powershell
  
tags:
  - Hyper-V
  - MAC Address
  - Powershell
---
This simple but useful approach became very popular if I watch my blog hit stats. So here&#8217;s the final part for Hyper-V.

How to identify a virtual machine if you just know it&#8217;s MAC Address?

For SCVMM 2012 SP1

<pre>Get-SCVirtualNetworkAdapter -All | ? MACAddress -eq '00:50:56:AF:00:6D' | Select Name,MACAddress,VirtualNetWork</pre>

For Hyper-V Manager

<pre>Get-VM | Get-VMNetworkAdapter | ? MacAddress -eq '00155D640F05' | select VMName,MacAddress,IPAdd resses</pre>

&nbsp;

Just to refer my recent posts&#8230;

<a title="Powershell one-liner to find XenServer guest by MAC address" href="http://www.miru.ch/2012/07/powershell-one-liner-to-find-xenserver-guest-by-mac-address/" rel="bookmark">Powershell one-liner to find XenServer guest by MAC address</a>

<a title="Powershell one-liner to find vSphere guest by MAC address" href="http://www.miru.ch/2012/04/powershell-one-liner-to-find-vsphere-guest-by-mac-address/" rel="bookmark">Powershell one-liner to find vSphere guest by MAC address</a>

&nbsp;

&nbsp;

&nbsp;