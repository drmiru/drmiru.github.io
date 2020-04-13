---
id: 753
title: Powershell script to fix XenDesktop 4.0 DDC / vSphere VM assignment
date: 2012-05-20T11:56:00+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=753
permalink: /powershell-script-to-fix-xendesktop-4-0-ddc-vsphere-vm-assignment/
aktt_notify_twitter:
  - 'no'
categories:
  - Citrix
  - Powershell
  - Virtualization
  - VMware
  - XenDesktop
tags:
  - assignment
  - DDC
  - vSphere
  - XenDesktop
---
Due to an administrative action, restore or rebuild of the vCenter database,  assignments between the hypervisor connection from DDC&#8217;s pool management service and the vSphere vCenter might be lost. The XenDesktop PoolManagementService manages the assignment by adding a special annotation attribute to each VM which has been added to a pool. The attribute name is CTXGuestOSId where the value represents the SID of the VM&#8217;s AD computer account.

The scripts checks if the Attribute has been set on vSphere side and if so, if it matches the current SID of the computer account. After completion it restarts the PoolManagementService. The following prerequisites are given:

  * Script has to be run on a XenDesktop 4 DDC machine
  * XenDesktop SDK Powershell SnapIn has to be installed
  * vSphere PowerCLI 4.0 or later has to be installed

And here it is&#8230; <a href="../images/2012/05/FixDDCHypAssignments.ps1_.txt" target="_blank">download</a>

<pre>#####################################################################
# Scriptname:      FixDDCVCHosting.ps1
# Description:     Script to fix DDC / vCenter Pool assignments
# Author:          Michael Rueefli, INSERTO AG
# Version:         1.0
# Requirements:    VMWare PowerCLI Snap-in, XenDesktop 4 SDK Snap-in
# History:                                          
#####################################################################

## General Parameter - to EDIT ###############
$vchost = "vchost.domain.com" #the vCenter Server FQDN
$vcuser = "vcuser" #The vCenter user account
$vcpwd = '' #Fill in your vCenter user pw if you like
$vmfilter = 'WinXPVM0' #Include Filter for VM names
$clusterfilter = "VDI-Cluster1" #Include filter for ESX Cluster Name
##############################################

trap { # this installs an general simple error handler
 Write-Host "Error in Script:" $_.Exception.GetType().FullName + $_.Exception.Message
 continue; # exiting a trap with "continue" says to continue on the next line of the script
}

Write-Host "-------------------------------------------" -ForegroundColor green
Write-Host "FIX DDC / vCenter Hosting assignment Script" -ForegroundColor green
Write-Host "-------------------------------------------" -ForegroundColor green
$ErrorActionPreference:Continue

#Add XDS 4 SnapIn
Add-PSSnapin XDCommands

#Add VMware SnapIn
Add-PSSnapin VMware*

#Connecting to vCenter
Connect-VIServer -Server $vchost -User $vcuser -password $vcpwd 

#Collect VMs
Write-Host "Collecting VMs...please wait..."
$VCVms = Get-Cluster | ? {$_.Name -match "$clusterfilter"} | get-vm | ? {$_.Name -match $vmfilter}
$DDCVms = Get-XdVirtualDesktop

Write-Host "Processing with VMs..."
Foreach ($vcobj in $VCVms)
{
 $guestosid  = ($vcobj | Get-Annotation -CustomAttribute CTXGuestOsId).Value
 If (!$guestosid)
 {
 $vcobjname = $vcobj.Name
 $DDCvmobj = $DDCVms | ? {$_.Name -match "$vcobjname"}
 $DDCvmobjName = $DDCvmobj.Name
 $DDCvmobjSID = (new-object system.security.principal.NtAccount($DDCvmobjName)).translate([system.security.principal.securityidentifier])

 Write-Host "Fixing CTXGuestOsID for VM: $vcobjname"
 If ($DDCvmobjSID)
 {
 Write-Host "Setting CTXGuestOSId to: $DDCvmobjSID for machine: $vcobjname"
 $vcobj | Set-Annotation -CustomAttribute CTXGuestOsId -value $DDCvmobjSID.Value
 }
 Else
 {
 write-warning "Could not resolve machine SID for: $DDCvmobjName"
 }
 }
 Elseif ($guestosid)
 {
 $DDCvmobjSID = (new-object system.security.principal.NtAccount($DDCvmobjName)).translate([system.security.principal.securityidentifier])
 If ($DDCvmobjSID -ne $guestosid)
 {

 Write-Host "Fixing CTXGuestOSId to: $DDCvmobjSID for machine: $vcobjname"
 $vcobj | Set-Annotation -CustomAttribute CTXGuestOsId -value $DDCvmobjSID.Value
 }
 }
}

Get-Service ctxpoolmgr -ea Continue | Stop-Service
Get-Service ctxpoolmgr -ea Continue | Start-Service</pre>