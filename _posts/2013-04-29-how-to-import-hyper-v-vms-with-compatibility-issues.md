---
id: 1066
title: How to import Hyper-V VMs with compatibility issues
date: 2013-04-29T21:15:30+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1066
permalink: /how-to-import-hyper-v-vms-with-compatibility-issues/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Hyper-V
  - Microsoft
  - Powershell
  - Virtualization
tags:
  - Hyper-V
---
I recently came along some issues while trying to import previously exported or copied virtual machines for a customer. The recent admin did not document what he tried to do so far, but several of the virtual machines where in a likely &#8220;inconsistent&#8221; state. Importing those VMs using the Hyper-V Manager GUI or Powershell CMDLETS failed with different errors, but all pointed to the same root causes.

  * VM has been exported from a recent hardware with CPUs of different Generation
  * VM has been copied manually while being in a saved state
  * VM has been copied manually, while mixing up config files and snapshots from different VMs (don&#8217;t ask me how this can happen, apparently with enough beer before going to work??)

The following steps show how to get rid of most compatibility issues.

&nbsp;

**1. Create a compatibility report**

<pre>$report = compare-VM -Path 'C:\ClusterStorage\Volume1\CMIRUW702\VirtualMachines\CF91993E-B76B-4D0D-ADFD-4D4DEDACE5A4.XML'</pre>

**2. Show all incompatibilities**

<pre>$report | ft -auto</pre>

**3. Show first incompatibility**

<pre>$report.Incompatibilities[0].Source</pre>

You should get an output showing you the root cause for incompatibilities

_VMName Name SnapshotType CreationTime ParentSnapshotName_  
_&#8212;&#8212; &#8212;- &#8212;&#8212;&#8212;&#8212; &#8212;&#8212;&#8212;&#8212; &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;_  
_CMIRUW702 DEMO Clean Planned 24.11.2012 01:13:51_

**4. Fix the incompatibility (in this case, remove the snapshot)**

<pre>$report.Incompatibilities[0].Source | Remove-VMSavedState</pre>

**5. Import the VM using the modified report data**

<pre>import-vm -CompatibilityReport $report</pre>

&nbsp;

Being aware that there are also error situations which can&#8217;t be fixed the steps above should help to save time and nervs.