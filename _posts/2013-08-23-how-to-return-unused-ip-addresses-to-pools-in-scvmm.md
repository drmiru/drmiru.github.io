---
id: 1301
title: How to return unused IP Addresses to Pools in SCVMM
date: 2013-08-23T09:47:34+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1301
permalink: /how-to-return-unused-ip-addresses-to-pools-in-scvmm/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
full_width_post:
  - default
post_description_display:
  - show
categories:
  - Powershell
  - System Center
  - Virtualization
tags:
  - Powershell
  - SCVMM
---
It&#8217;s common to create and dispose / delete lots of virtual machine in an automated private cloud. SCVMM comes with it&#8217;s own implementation of a tiny DHCP-Server like service. The feature allows the assignment of IP addresses from a pool to deployed virtual machines. SCVMM tracks the assignments by adding them as &#8220;AllocatedIPAddress&#8221; object types. SCVMM R2 together with Server 2012 R2 will allow an integrated IP address management using IPAM extensions for SCVMM. For now we have to manage it ourselves.

The following script searches for assigned IP addresses where the virtual network adapter the address has been assigned to does not exist any more. Each orphaned IP is then returned back to it&#8217;s Pool.

<pre>#Importing SCVMM Module
Import-Module VirtualMachineManager

$assignedIPs = Get-SCIpaddress
Foreach ($ip in $assignedIPs)
{
    $assignedNIC = Get-SCVirtualNetworkAdapter -ID $ip.AssignedToId -ErrorAction SilentlyContinue
    If (!$assignedNIC)
    {
        "$($ip.Name) returned IP to Pool $($ip.AllocatingAddressPool)"
        $ip | Revoke-SCIPAddress -ReturnToPool $true
    }
}</pre>

&nbsp;

If you like to run the script based on a schedule why not use a Powershell Scheduled Job?

<pre>$dailyTrigger = New-JobTrigger -Daily -At "5:00 AM"
Register-ScheduledJob -Name ReturnSCIPaddresses -ScriptBlock {D:\Scripts\ReturnUnusedPoolIPAddresses.ps1} -Trigger $dailyTrigger}</pre>

&nbsp;

&nbsp;