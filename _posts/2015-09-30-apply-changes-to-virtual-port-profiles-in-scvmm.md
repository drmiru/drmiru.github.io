---
id: 4290
title: Apply changes to virtual Port Profiles in SCVMM
date: 2015-09-30T07:18:53+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4290
permalink: /apply-changes-to-virtual-port-profiles-in-scvmm/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Cloud and Datacenter Management
  - Hyper-V
  - Microsoft
  - Powershell
  - System Center
  
tags:
  - Powershell
  - SCVMM
  - System Center 2012 R2
---
If you use Logical Network / Logical Switch capabilities in SCVMM, and you hopefully do so ;-), then you might want to change virtual port profile / uplink port profile settings from time to time. Changes to the following properties can be made on the fly:

  * Security Settings
  * Bandwidth Settings (QoS)
  * Offload Settings (except SRIOV, which can&#8217;t be changed to an already deployed Logical Switch)
  * Uplink Port Profile load balancing / teaming mode

A common misunderstanding is that VMM will propagate the changes automatically to all vNICs. When you change parameters of one ore more of the settings above, those changes will not be propagated to the VM vNICs automatically. In the mean time, the vNIC or Logical Switch compliance state changes to &#8220;not compliant&#8221;.

How to propagate changes to all related objects?

For virtual Network Adapters

<pre class="crayon-selected">Get-SCVirtualMachine | Get-SCVirtualNetworkAdapter | where-object {$_.VirtualNetworkAdapterComplianceStatus -ne "Compliant"} | Repair-SCVirtualNetworkAdapter -RunAsynchronously</pre>

For host Logical Switch instances

<pre> Get-SCVMhost | Get-SCVirtualNetwork  | where-object {$_.LogicalSwitchComplianceStatus -ne 'Compliant'} | Repair-SCVirtualNetwork -RunAsynchronously</pre>

Of course it might require a bit of research first, how and why the compliance state is not fulfilled, before firing of the commands above. Leaving out the repair-* CMDLETs prints out a list of the objects not in a compliant state and as well the reason for not being so.

Hope this helps to save some time.