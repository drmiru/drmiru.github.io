---
id: 4804
title: Cluster Aware Update for Nano Server
date: 2016-12-17T23:15:07+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4804
permalink: /cluster-aware-update-for-nano-server/
bfa_virtual_template:
  - hierarchy
categories:
  - Cloud and Datacenter Management
  - Failover Cluster
  - Nano Server
  - Powershell
  - Storage Spaces
tags:
  - CAU
  - Failover Cluster
  - Nano Server
---
### CAU and Nano, huh?

The past months I&#8217;ve been working more and more on the new deployment type of Windows Server 2016, the Nano Server. Nano Server is a great variant to deploy Windows Server for fabric roles like Hyper-V, Storage Spaces Direct or container hosts. When working with clusters, you might be familiar with CAU &#8220;[Cluster aware Update](https://technet.microsoft.com/en-us/library/hh831694(v=ws.11).aspx)&#8220;, a tool delivered as a part of Failover Clustering Management Tools. It&#8217;s primary intent is to orchestrate updating a Windows Failover Cluster using Windows Update Client / WSUS, with no downtime. While this works great for Desktop and Core installations, it doesn&#8217;t for Nano Server. Reason for this. the required WMI classes are just not present in Nano Server.

### Nano Server Basics

For more information on Nano Server in general, you might consider to read some of my recent posts:

<blockquote data-secret="0VyKTXzhkK" class="wp-embedded-content">
  <p>
    <a href="http://www.miru.ch/deploy-packages-and-windows-updates-to-nano-servers/">Deploy Packages and Windows Updates to Nano Servers</a>
  </p>
</blockquote>



<blockquote data-secret="o6HNC232o0" class="wp-embedded-content">
  <p>
    <a href="http://www.miru.ch/sneak-peek-on-nano-server-2016-tp5/">Sneak Peek on Nano Server 2016 TP5</a>
  </p>
</blockquote>



### **So?**

So back to CAU (Cluster aware Update). I really needed this functionality in Nano Server as well, as I deploy S2D (Storage Spaced Direct) clusters primarily on Nano Server for various reasons. Therefore I&#8217;ve created a PowerShell script which basically imitates the CAU tool, without the need for the legacy WMI classes. The script uses CIM and Failover Cluster CMDLETs.

Currently the script has the following capabilities:

  * Per node pre- and post-script
  * Global pre- and post-script
  * Check all cluster nodes up
  * Check Clustered Storage Subsystem Health (if present) before processing (includes pools and vdisks)
  * Gracefully suspending each node
  * Check if node is up (ClusSvc paused) after reboot
  * Check Clustered Storage Subsystem Health before pausing next node (wait for rebuild, includes pools and vdisks)
  * Logging to C:\Windows\Logs\ClusterUpdate.log (in [CMTrace](http://blog.tyang.org/2012/04/17/sccm-2012-log-parser-cmtrace-exe/) format)

[<img class="alignleft size-medium wp-image-4811" src="http://www.miru.ch/wp-content/uploads/2016/12/CAUNanoCMTrace-300x262.png" alt="caunanocmtrace" width="300" height="262" srcset="http://www.miru.ch/wp-content/uploads/2016/12/CAUNanoCMTrace-300x262.png 300w, http://www.miru.ch/wp-content/uploads/2016/12/CAUNanoCMTrace-768x670.png 768w, http://www.miru.ch/wp-content/uploads/2016/12/CAUNanoCMTrace.png 860w" sizes="(max-width: 300px) 100vw, 300px" />](http://www.miru.ch/wp-content/uploads/2016/12/CAUNanoCMTrace.png)

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

The script is available on [my GitHub repo here](https://github.com/drmiru/Powershell/tree/master/Cluster). Feel free to test and report any bugs.

DISCLAIMER
  
Although, the script has been tested heavily, you are using it at your very own risk. I don’t take any responsibility for messed up Clusters.

&nbsp;