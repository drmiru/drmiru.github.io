---
id: 764
title: Powershell script to fix XenDesktop 5.x DDC / vSphere VM assignment
date: 2012-05-20T21:20:44+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=764
permalink: /powershell-script-to-fix-xendesktop-5-x-ddc-vsphere-vm-assignment/
aktt_notify_twitter:
  - 'no'
categories:
  - Citrix
  - Desktop Virtualization
  - Powershell
  - Virtualization
  - XenDesktop
tags:
  - Powershell
  - powerstate unknown
  - vSphere
  - XenDesktop
---
As the issue described in the previous <a href="http://www.miru.ch/2012/05/powershell-script-to-fix-xendesktop-4-0-ddc-vsphere-vm-assignment/" target="_blank">post</a> might also affect XenDesktop 5 deployments with vSphere I decided to create a script for XD 5.x too and post it to the community.

In XD 5.x one of the most recurring issues together with vSphere vCenter is that broker machines appear with a power state &#8220;unknown&#8221; in Desktop Studio. Fixing this requires some powershell commands.

The following script will do the work for you. It contains an optional switch  &#8220;-AutoRestart&#8221; which will automatically restart the CitrixBrokerService on All DDC of the XD site, to make the changes more or less immediately active. Depending on your deployment size it make take some time, until the machine&#8217;s power state will be shown accordingly.

&nbsp;

Here it is&#8230; and here for <a href="http://www.miru.ch/wp-content/uploads/2012/05/FixXD5VMAssignment.ps1_.txt" target="_blank">download</a>

Any comments are welcome. Tweet me @DRMIRU

<pre>&lt;#
 .SYNOPSIS
 Fixes XenDesktop 5.x machine assignment between XD database and vsphere vCenter
 (usually this fixes BrokerMachines where the powerstate is marked as "unknown" because
 assignment between the DDC broker machine and the VM on vCenter inventory has been lost)
 .DESCRIPTION
 ################################################################################################
 # ScriptName:    FixXD5VMAssignment.ps1
 # Author:        Michael Rüefli (www.miru.ch) / @drmiru
 # Date:         20.05.2012
 # Version:         1.0
 #
 # History:
 #                20.05.2012 / V1.0 / Initial version
 ################################################################################################

 .PARAMETER AutoRestart
 This switch forces the restart of the broker service on any controller within the XD site
 Note that PSRemoting has to be enabled on all site DDCs
 .EXAMPLE
 FixXD5VMAssignment.ps1 -AutoRestart
 .NOTES
 Prerequisites:
 - XenDesktop 5 Management CMDLETS
 - vSphere PowerCLI 4.x or higher
 ################################################################################################
#&gt;
param([switch] $AutoRestart)

## General Parameter - to EDIT ###############
$vchost = "svcenter.domain.com" #the vCenter Server FQDN
$vcuser = "vcUser" #The vCenter user account
$vcpwd = 'thePassword' #Fill in your vCenter user pw
##############################################

trap { # this installs an general simple error handler
 Write-Host "Error in Script:" $_.Exception.GetType().FullName + $_.Exception.Message
 continue; # exiting a trap with "continue" says to continue on the next line of the script
}

Write-Host "-------------------------------------------" -ForegroundColor green
Write-Host "FIX DDC / vCenter Hosting assignment Script" -ForegroundColor green
Write-Host "-------------------------------------------" -ForegroundColor green
$ErrorActionPreference:Continue

#Add XD 5 SnapIns
$ctxsnapin = Get-PSSnapin Citrix.Common.Commands -ErrorAction SilentlyContinue
If (!$ctxsnapin)
{
 Write-Host "Adding Citrix XD PoSh SnapIn..."
 Add-PSSnapin Citrix*
}

#Add VMware SnapIn
$vspheresnapin = Get-PSSnapin VMware.VimAutomation.Core -ErrorAction SilentlyContinue
If (!$vspheresnapin)
{
 Write-Host "Adding vSphere PoSh SnapIn..."
 Add-PSSnapin VMware.VimAutomation.Core
}

#Connecting to vCenter
$vcconnection = Connect-VIServer -Server $vchost -User $vcuser -password $vcpwd
If (!$vcconnection)
{
 Write-Warning "Error while connecting to vCenter. Check hostname , username and password in script"
}

#Collect VMs
Write-Host "Collecting VMs...please wait..."
$DDCVms = Get-BrokerMachine | ?  {$_.Powerstate -eq 'unknown'}

Write-Host "Processing with VMs..."
Foreach ($DDCobj in $DDCVms)
{
 #Get the VM UUID on vCenter
 $MachineNameShort = ($DDCobj.MachineName).split('\')[1]
 $BrokerMachineName = $DDCobj.MachineName
 $VCVMuuid = (Get-VM  $MachineNameShort | %{(Get-View $_.Id).config.uuid})
 $DDCVMid = $DDCobj.HostedMachineId

 #Fixing the Brokermachine ID on DDC database
 If ($DDCVMid -ne $VCVMuuid)
 {
 Write-Host "Fixing HostedMachineID for Machine: $BrokerMachineName"
 Set-BrokerMachine -MachineName $BrokerMachineName -HostedMachineId "$VCVMuuid"
 }
}

#Restart Broker Service on all controllers (requires PSRemoting enabled on all DDCs)
If ($AutoRestart)
{
 Write-Host "AutoRestart Switch has been set"
 Write-Host "Enumerating DDCs"
 $BrokerControllers = Get-BrokerController | select DNSName
 Foreach ($objbroker in $BrokerControllers)
 {
 $BrokerFQDN = $objbroker.DNSName
 Write-Host "Restarting Broker Service on Controller: $BrokerFQDN"
 Enter-PSSession -ComputerName $BrokerFQDN
 Stop-Service "CitrixBrokerService"
 Start-Service "CitrixBrokerService"
 Exit-PSSession
 }
}</pre>

&nbsp;