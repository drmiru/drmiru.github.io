---
id: 3492
title: Creating clustered storage spaces a lot faster
date: 2014-11-04T21:22:36+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=3492
permalink: /creating-clustered-storage-spaces-a-lot-faster/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Cloud and Datacenter Management
  - Powershell
  - Storage
  - Storage Spaces
tags:
  - Storage Spaces
---
The process for creating a new clustered space includes a fair amount of single steps to be processed in the right order, no matter if you use the Server Manager GUI or PowerShell.

  * creating the virtual disk
  * partitioning and formatting the virtual disk
  * adding the virtual disk to the cluster
  * converting it into CSVFS

The Powershell cmdlet **New-Volume** has been introduced with Server 2012 R2. It significantly simplifies the process of creating a new clustered space by combining the steps above into a single cmdlet. The following example shows how easy it is using this cmdlet.

<pre>#Creating new clustered space
New-Volume -StoragePoolFriendlyName Pool1 -FriendlyName vdisk1 -PhysicalDiskRedundancy 3 -FileSystem CSVFS_NTFS  -NumberOfColumns 4 -ResiliencySettingName Mirror -Size 1TB</pre>

The next examples creates a new tiered, clustered space

<pre>#Creating new clustered space
$ssd_tier = Get-StoragePool -FriendlyName Pool1 | get-StorageTier | where {$_.Mediatype -eq 'SSD'}
$hdd_tier = Get-StoragePool -FriendlyName Pool1 | get-StorageTier | where {$_.Mediatype -eq 'HDD'}
New-Volume -StoragePoolFriendlyName Pool1 -FriendlyName vdisk1_tiered -PhysicalDiskRedundancy 3 -FileSystem CSVFS_NTFS  -NumberOfColumns 4 -ResiliencySettingName Mirror -StorageTiers $ssd_tier, $hdd_tier -StorageTierSizes 200GB,1TB</pre>