---
id: 531
title: How to search for a VM by MAC address on XenServer and vSphere ESX via Powershell
date: 2011-05-26T20:50:30+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=531
permalink: /how-to-search-for-a-vm-by-mac-address-on-xenserver-and-vsphere-esx/
aktt_notify_twitter:
  - 'no'
categories:
  - Powershell
  
  - XenServer
tags:
  - MAC Address find VM Powershell
---
If you have hundreds or thousands of VMs you could run into a problem if  someone wants to now the correspondig VM to the MAC address XYZ. Because the network administrator isn&#8217;t a big help finding virtual adapters on his physical network infrastructure, the hypervisor is the choice to search for the apropriate virtual NIC. If you have a plenty of VMs you would probably going crazy clicking through each VM&#8217;s NIC properties.

I&#8217;ve created two functions which return the VM name having the virtual NIC you are searching for by providing the searched MAC as an argument. One is for XENServer the other for vSphere ESX. Of course I assume you have the SDK / PowerCLI installed and the SnapIns loaded before executing the functions.

## XenServer

Usage:  get\_xen\_vm\_by\_mac &#8220;5a:4e:ca:50:fe:e5&#8221;

<pre>Function get_xen_vm_by_mac([STRING]$MACfilter)
{
  If (!$MACFilter)
  {
     write-warning "Wrong Number of Arguments"
     write-host "you must supply the MAC address as an argument if calling this function"
     break
  }

  $vif = get-xenserver:VIF | ? { $_.MAC -match $MACFilter}
  If ($vif) {$uuid = $vif.uuid}
  If ($uuid) {$vif_vm = Get-XenServer:VIF.VM -VIF $uuid}
  If ($vif_vm)
  {
    'Xen VM:'+$vif_vm.name_label + ' has MAC ' + $MACfilter
  }
  Else
  {
    "no Xen VM found which owns the MAC $MACFilter"
  }
}</pre>

## vSphere ESX

Usage:  find\_vm\_by_mac &#8220;00:50:56:92:fe:e5&#8221;

<pre>Function find_vm_by_mac([STRING]$MACfilter)
{
 $vms = Get-VM
 Foreach ($object in $vms)
 {
   $nic = $object.networkadapters | ? {$_.MacAddress -match $MACfilter}
   If ($nic)
   {
     $object.Name+','+$nic.Name+','+$nic.NetWorkName+','+$nic.MacAddress    
     $icount += 1    
   }    
 }
 If ($icount -lt 1)
 {
   "No VM with corresponding MAC: $MACfilter has been found."
 }
}</pre>

So what? What is that for?

Well let&#8217;s take the case you have an IP conflict and you have no idea which virtual host is already configured with this IP address. You would then ping the IP, check your ARP table and execute one of the functions above and your done.