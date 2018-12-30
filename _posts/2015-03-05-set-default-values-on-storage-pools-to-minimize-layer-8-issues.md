---
id: 3806
title: Set default values on Storage Pools to minimize layer 8 issues
date: 2015-03-05T23:03:49+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=3806
permalink: /set-default-values-on-storage-pools-to-minimize-layer-8-issues/
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
Storage Spaces allows you to define default values on a Storage Pool, so every new created virtual disk (space) is using those values, even if the administrator does not provide them or doesn&#8217;t know how to provide them. A common issue arises when fabric administrators are using one of the GUI options to create spaces, either the Server Manager or SCVMM. Why?

Server Manager GUI does not provide advanced options while creating a virtual disk such as:

  * write back cache
  * interleave
  * physical disks to use
  * rebuild options
  * missing disks response
  * column size

SCVMM in vCurrent does not support one of the option above, nor the option to create tiered spaces.

So how could we ensure that new spaces are created with the correct options (which may vary according to your specific environment of course)?

The answer is (of course you know it already :-)&#8230;Powershell

The following CMDLETs let you define the default options:

  * Set-StoragePool
  * Set-ResiliencySetting

&nbsp;

### Example

We want to ensure that each newly created virtual disk has the following options set:

  * Resiliency Setting:  DUAL MIRROR
  * Writeback Cache Auto Size: 1GB
  * Number of Columns: 4
  * Interleave: 64KB
  * Enclosureaware: TRUE
  * ProvisioningType: FIXED
  * RepairPolicy: PARALLEL
  * Retire missing PhysicalDisks: ALWAYS

And here are the commands to achieve this:

<pre class="crayon-selected">#Set the default Pool Options

Get-StoragePool "My Pool1" | Set-StoragePool -RetireMissingPhysicalDisks Always -RepairPolicy Parallel
Get-StoragePool "My Pool1" | Set-StoragePool -ResiliencySettingNameDefault Mirror -ProvisioningTypeDefault Fixed -EnclosureAwareDefault $true -AutoWriteCacheSize $true</pre>

<pre>#Set the default Options for MIRROR Resiliency

Get-StoragePool "My Pool1" | Set-ResiliencySetting -Name Mirror -NumberOfDataCopiesDefault 2 -NumberOfColumnsDefault 4 -InterleaveDefault 64KB</pre>

With these two simple commands we can ensure that new spaces follow our policy regardless of how they have been created. However, the limitation of SCVMM not being capable of creating tiered spaces remains, at least in the current version. The settings also help when using the New-Volume CMDLET to &#8220;<a href="http://www.miru.ch/creating-clustered-storage-spaces-a-lot-faster/" target="_blank">Create clustered Storage Spaces a lot faster</a>&#8221;

If you want to know more about Storage Spaces in general I&#8217;d recommend to read through the following blogs and documentations:

**Storage Spaces survival Guide**

<a href="http://blogs.technet.com/b/josebda/archive/2014/10/22/storage-spaces-survival-guide-links-to-presentations-articles-blogs-tools.aspx" target="_blank">http://blogs.technet.com/b/josebda/archive/2014/10/22/storage-spaces-survival-guide-links-to-presentations-articles-blogs-tools.aspx</a>

**Monitor Storage Tiers Performance**

<a href="https://technet.microsoft.com/library/c8c07015-3e0e-48be-b4c4-e58dabf39553.aspx#BKMK_HOW_OptimizeOnDemand" target="_blank">https://technet.microsoft.com/library/c8c07015-3e0e-48be-b4c4-e58dabf39553.aspx#BKMK_HOW_OptimizeOnDemand</a>

&nbsp;

**The matter of Column Sizes with Storage Spaces**

<a href="http://www.miru.ch/why-column-size-does-matter-with-storage-spaces/" target="_blank">http://www.miru.ch/why-column-size-does-matter-with-storage-spaces/</a>

&nbsp;

**Get a Storage Spaces Health and Statistics Overview via Powershell**

<a href="http://www.miru.ch/get-a-brief-storage-spaces-status-overview-via-powershell/" target="_blank">http://www.miru.ch/get-a-brief-storage-spaces-status-overview-via-powershell/</a>

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;