---
id: 1057
title: Automating online Extension of a CSV using Powershell
date: 2013-04-12T11:55:13+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1057
permalink: /automating-online-extension-of-a-csv-using-powershell/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
bfa_virtual_template:
  - hierarchy
categories:
  - Failover Cluster
  - Microsoft
  - Powershell
tags:
  - Failover Cluster
  - Powershell
---
In this article I&#8217;d like to give an example on how to automate an online extension of a Cluster Shared Volume on a remote Server (Cluster Node).

Before you&#8217;re going to kill your Failover Cluster, please consider that the following snippets and commands assume that&#8230;. <span style="font-size: 12pt;"><br /> </span>

  * You know, what you&#8217;re doing
  * You hopefully named your Cluster Shared Volumes identically to the logical volume names
  * You increased already the physical disk size of your CSV
  * You installed the Powershell Failover Cluster Management Tools (<span style="font-family: Consolas;">Add-WindowsFeature RSAT-Clustering-Powershell</span>)
  * You enabled and configured WinRM on all of the Cluster Nodes

**First we have to get the current owner of the CSV**

    $volume_name = "CSV1"
    $cluster_name = "hvcluster2"
    $csv_owner = (Get-ClusterSharedVolume -Cluster $cluster_name | ? {$_.Name -eq "CSV1"}).OwnerNode.name 

&nbsp;

**Now let&#8217;s get the Volume ID from Diskpart Output  
** 

    $volume = Invoke-Command -ComputerName $csv_owner -ScriptBlock {"list volume" | diskpart} | Select-String $volume_name | Select-String -Pattern '[0-9]{1,3}'
    $volume_id = $matches[0] 

**Here comes the tricky part. As there is currently no CMDLET to extend a volume (if there is any please ping me) I had to use old fashion DISKPART command.  
With the Volume ID we got from the previous command, we&#8217;re going to invoke the remote DISKPART command for volume extension**

<pre class="crayon-selected">Invoke-Command -ComputerName $csv_owner -ScriptBlock {"RESCAN","SELECT VOLUME $($args[0])","EXTEND"| diskpart} -Args $volume_id</pre>

There are of course also other ways and simplifications to the code above, but to keep it readable I did not combine all steps. It would be really nice if we had a Powershell CMDLET for Logical Volume Extension to get rid of old fashioned DISKPART. Other DISKPART commands can be easily replaced with CMDLETs. Have a look for a some examples within the [post of Thomas Maurer](http://www.thomasmaurer.ch/2012/04/replace-diskpart-with-windows-powershell-basic-storage-cmdlets/).