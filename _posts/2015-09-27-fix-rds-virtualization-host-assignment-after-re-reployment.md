---
id: 4277
title: Fix RDS Virtualization Host assignment after re-reployment
date: 2015-09-27T21:59:10+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4277
permalink: /fix-rds-virtualization-host-assignment-after-re-reployment/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Cloud and Datacenter Management
  - Desktop Virtualization
  - Hyper-V
  - Microsoft
  - RDS
  
tags:
  - Hyper-V
  - Powershell
  - RDS
  - SMA
---
[<img class="alignleft size-medium wp-image-4281" src="../images/2015/09/RDSArchitectureW2K12-300x170.jpg" alt="RDSArchitectureW2K12" width="300" height="170" srcset="../images/2015/09/RDSArchitectureW2K12-300x170.jpg 300w, ../images/2015/09/RDSArchitectureW2K12.jpg 797w" sizes="(max-width: 300px) 100vw, 300px" />](../images/2015/09/RDSArchitectureW2K12.jpg)Microsoft based VDI deployments require to install a specific Windows Feature called &#8220;RDS-Virtualization&#8221; onto all Hyper-V hosts being part of a RDS VDI Deployment.

After adding a Hyper-V host to an existing RDS VDI deployment, the host gets registered with a unique ID and it&#8217;s hostname within the broker database. The corresponding tables are rds.Server and rds.Rdvh.  Another important piece is the VmHostAgent service on the Hyper-V host, configured with certain parameters corresponding the deployment like the broker machines, VDI Desktop collections. The responsible key is:

HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Services\VmHostAgent

If you have to re-deploy / stage such a Hyper-V host, the RDS VDI deployment does not know anything about the host anymore, as the host does not have the required assignment within its VmHostAgent configuration. Of course you could start to tweak the registry of the host, restarting several services to fix this issue. I&#8217;ve created a SMA runbook which does the work in a, let&#8217;s say, more &#8220;proper&#8221; way. It can be used to just add a fresh Hyper-V host to an existing deployment or to &#8220;re-add&#8221; a re-staged host. If the parameter -RemoveExistingHost is specified It removes the existing host from the deployment using the RDS PowerShell CMDLETs. If the removal fails, the host is cleared from the database using a native TSQL statement. Afterwards, the host is re-added to the deployment using the native RDS CMDLETs again.

You can find the source code on <a href="https://gallery.technet.microsoft.com/scriptcenter/SMA-Runbook-to-fix-RDS-53c61973" target="_blank">TechNet</a>

Hope this helps,

Feel free to comment if you have found a better way to fix this issue.

&nbsp;

&nbsp;

&nbsp;

&nbsp;