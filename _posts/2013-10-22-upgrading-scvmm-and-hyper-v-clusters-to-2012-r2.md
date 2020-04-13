---
id: 1699
title: Upgrading SCVMM and Hyper-V Clusters to 2012 R2
date: 2013-10-22T18:13:06+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1699
permalink: /upgrading-scvmm-and-hyper-v-clusters-to-2012-r2/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Failover Cluster
  - Hyper-V
  - Microsoft
  - System Center
  
tags:
  - Hyper-V
  - Server 2012 R2
  - System Center 2012 R2
---
In this post I wanted to give a short wrap up about my experiences upgrading existing Hyper-V 2012 Clusters and VMM 2012 SP1 to the new R2 version. I decided to leave out all print screens to keep the post short and useful as a guidline.

### General considerations

However you manage your current environment, be sure to have at least one management PC or jump server with either Windows 8.1 or Server 2012 R2 ready before you start. If Windows 8.1, then install the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=39296" target="_blank">RSAT Tools for Windows 8.1</a>

&nbsp;

### SCVMM

This should be your first piece to upgrade as it&#8217;s the heart of your private cloud. VMM 2012 SP1  can&#8217;t properly manage Hyper-V 2012 R2 hosts and clusters. Basic features work, but as as said, not all.

<span style="text-decoration: underline;"><strong>Procedure</strong></span>

  1. Uninstall existing ADK
  2. Download and install ADK 8.1 from http://www.microsoft.com/en-us/download/details.aspx?id=39982
  3. Uninstall VMM Rollup Packs
  4. Uninstall VMM Service and Console (as there is no inplace upgrade support)
  5. Choose to retain the VMM database
  6. Install VMM R2
  7. Update the VMM Agents on all Hyper-V nodes, Library- and Infrastructure servers (PXE,WSUS, etc)
  8. Remove SCOM Connection if present
  9. Re-Install SCOM Mgmt Console with R2 Version
 10. Reconfigure SCOM Connection

### Hyper-V Nodes / Clusters

This is  a bit more tricky than the management part above. You can&#8217;t mix 2012 and 2012 R2 nodes in the same cluster.

This is my favorite method:

  1. Evict half of the nodes from the Hyper-V cluster (I hope you sized your environment correctly to support all workloads on half of the physical resources. Otherwise talk to the service owners, which service you can safely take down during the upgrade phase 🙂
  2. Rebuild the evicted nodes with R2 (if you have already Baremetal Deployment with VMM in place this saves you a lot of time of course)
  3. Configure Logical Switches and Networking as you did on the 2012 nodes
  4. Create a new cluster within the same host group
  5. Migrate Live Migrate VMs to the new cluster
  6. Remove the old cluster / nodes and rebuild them with R2
  7. Join the remaing node to the new cluster
  8. Redistribute your workloads

###  Virtual Machines and Services

  * Check and verify VM and Service Templates, some of mine where invalid after the upgrade (had to reattach the VHD files from the library server)
  * There is currently no way to migrate Gen1 VM to Gen2 version. If you create new Gen2 VMs and attach the existing virtual disks the VM won&#8217;t boot (inaccessivle boot device as it expects a IDE controller attached disk). I wouln&#8217;t go that far to say there is no way to fix that, but out of the box you cannot migrate a Gen1 VM to Gen2.
  * Gen2 VMs support the following operating systems: 
      * Windows Server 2012
      * Windows Server 2012 R2 Preview
      * 64-bit versions of Windows 8
      * 64-bit versions of Windows 8.1 Preview

###  Additional Notes

As this is a quick post It might be subject to change, as I&#8217;ll do more migrations in the future. Watch for updates.