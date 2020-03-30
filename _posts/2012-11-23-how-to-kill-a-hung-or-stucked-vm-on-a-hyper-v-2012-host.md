---
id: 952
title: How to kill a hung or stucked VM on a Hyper-V 2012 host.
date: 2012-11-23T23:18:48+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=952
permalink: /how-to-kill-a-hung-or-stucked-vm-on-a-hyper-v-2012-host/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
bfa_virtual_template:
  - hierarchy
categories:
  - Hyper-V
  - Microsoft
  - Powershell
  - Virtualization
tags:
  - Hyper-V
  - Powershell
---
Even this happens very rarely on a Hyper-V host 😉 ,  it might still be useful under some circumstances to kill a hung or stucked VM. Most of the times the hung situation is caused by a guest shutdown request which does not get completed successfully. The GUI doesn&#8217;t provide you a solution here (neither SCVMM, Hyper-V Manager or Failover Cluster Manager). Good old Powershell will solve it for you.

Here are the steps in detail:

**First we want to get the GUID of our affected VM**

<pre>$vmname = "CMIRUW702"
$id = (get-vm | ? {$_.name -eq $vmname} | select id).id.guid</pre>

**Now as we have to GUID we search for the VM worker process holding the virtual machine resources**

<pre>$vm_pid = (Get-WmiObject Win32_Process | ? {$_.Name -match 'vmwp' -and $_.CommandLine -match $id}).ProcessId</pre>

**And finally kill the VM worker process**

<pre>stop-process $vm_pid -Force</pre>

&nbsp;

**Here again all together as a function:**

<pre>function KillVM{  
param($vmname)
  $id = (get-vm | ? {$_.name -eq "$vmname"} | select id).id.guid
  If ($id) {write-host "VM GUID found: $id"}
  Else {write-warning "VM or GUID not found for VM: $vmname"; break}
  $vm_pid = (Get-WmiObject Win32_Process | ? {$_.Name -match 'vmwp' -and $_.CommandLine -match $id}).ProcessId
  If ($vm_pid) {write-host "Found VM worker process id: $vm_pid"}
  Else {write-warning "No VM worker process found for VM: $vmname"}
  Write-host "Killing VM worker process of VM: $vmname"
  stop-process $vm_pid -Force
}</pre>

Have fun!