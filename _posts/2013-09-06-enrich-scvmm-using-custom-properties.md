---
id: 1269
title: Enrich SCVMM using Custom Properties
date: 2013-09-06T21:05:55+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1269
permalink: /enrich-scvmm-using-custom-properties/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
bfa_virtual_template:
  - hierarchy
categories:
  - Microsoft
  - Powershell
  - System Center
  - Virtualization
tags:
  - Powershell
  - SCVMM
---
SCVMM does provide ten default  custom properties (CustomProperty1-10). Like the custom properties of an AD object, objects in VMM can be enriched with information by adding data to those properties. Now there&#8217;s even more. You can define additional custom properties at will.  So thinking about possible use cases I came along the following&#8230;

Wouldn&#8217;t it be nice if..

  * VMM would show information about VM network adapter configuration like IP Address, Subnetmask, VLAN, etc?

You can actually do that using the custom properties on the various objects.

First we have to create new custom property objects and assign them to the object type &#8220;Virtual Machine&#8221; within SCVMM. This can be done using the GUI Console as well, but we&#8217;ll use good old Powershell 🙂

<pre>New-SCCustomProperty -Name "IPv4 Address" -Description "" -AddMember @("VM")
New-SCCustomProperty -Name "IPv4 Mask" -Description "" -AddMember @("VM")
New-SCCustomProperty -Name "VLAN" -Description "" -AddMember @("VM")</pre>

Next, we will add the new properties to our VM view

&nbsp;

&nbsp;

So how the hell we get the information pumped into VMM now? Well there are of course several ways we can use to get IP information from our guests. Again, R2 will add more power here as we have a connection to IPAM from SCVMM manage IP info centrally. For now I&#8217;ve decided to get the info via CIM. Therefore I created a Powershell script which does the following:

  * -Get all Hyper-V Hosts in SCVMM (there are filter capabilities using script parameter)
  * -Get Virtual Machine Class and associated network adapter object information via CIM
  * -Read IPv4 Address and Netmask, get the VLAN ID from the VM network adapter
  * -Pump the information into the apropriate custom properties of the VM objects within VMM
  * -I just wrote it in a quick and dirty way, just to show how you could do stuff like this and how you can leverage the information content of VMM using custom properties.

&nbsp;

The result can then look like this:

&nbsp;

You can get a copy of the script <a href="http://1drv.ms/1DNvz7t" target="_blank"><strong>here</strong></a>

Some other things..

  * Whatever you like to put into custom properties, it has to be a [STRING]
  * The script has issues with multihomed VMs
  * You&#8217;ll use the script at your own risk

&nbsp;

&nbsp;