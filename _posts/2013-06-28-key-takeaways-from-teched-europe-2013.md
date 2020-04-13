---
id: 1191
title: Key takeaways from TechEd Europe 2013
date: 2013-06-28T13:34:26+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1191
permalink: /key-takeaways-from-teched-europe-2013/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Hyper-V
  - Microsoft
  - System Center
  
tags:
  - System Center 2012 R2
  - TechEd
  - Windows Server 2012 R2
---
When I first thought about joining TechEd Europe this year, I did not had a lot of expectations regarding new products and features showing up. Fortunately I&#8217;ve been corrected and completely flushed with amazing, incredible new stuff coming up with R2 versions of Server 2012 and System Center 2012.

For the first time we&#8217;ve seen Microsoft aligning the core platform with the whole System Center stack. In just a 6 month timeframe since releasing Server 2012 RTM, they made the R2 preview version public, together with Windows 8.1. It&#8217;s hard to pick up the most interesting and blasting content and features, because there was so much I had to look at. In this post I&#8217;m not going to copy the session schedule buzzwords, but trying to summarize the things which impressed me the most, and why I think Microsoft has found a unique way to prove that they have a complete and fully scalable cloud stack.

**Storage / SMB  
** This is IMHO THE key turning factor. Microsoft is challenging storage vendors and providing enterprise scale storage functionalities running on commodity disks (JBODs).  
Server 2012 R2 will be a big game changer to the storage world. Talking to the vendors showed me, that they are at least in some way afraid and unsure about the impact, the new storage capabilities will have and how customers will take advantage of these features.  
Storage Spaces have been introduced with Server 2012, where is has been already a huge improvement for creating scalable storage building blocks using JBODs without the need of expensive enterprise class SAN storage. Now in 2012 R2, the features have been extended even more with automatic Storage Tearing, Write-Back Cache, three-way mirroring, dual-parity, just to name some of the enhancements.  
Deduplication of online files is a great feature leveraging the existing dedup service in Server 2012, because you can now dedup eg. VDI storage easily while the VHDX files are being accessed and in use. Again, this is something you normally get for a fortune from traditional storage vendors.

SMB 3.02 has as well a lot of new capabilities, making it IMHO the storage protocol for the future. For those who still think SMB is just a protocol to access office files and movies, you might have to change their minds quickly. SMB with its capabilities can deliver data transfer rates far beyond current fibre channel speeds. Demos showed us that when using RDAM NICs, the bottleneck is mostly the memory. Yes, SMB 3 hits the edge of what DDR3 can actually deliver. Continuous available file shares now support auto-rebalancing. The load balancing mechanism now support &#8220;per share&#8221; balancing, which was previously &#8220;per node&#8221;. The awareness of CSV ownership adds a significantly performance gain.

The Demos we&#8217;ve seen where just mind-blowing. We&#8217;ve seen for example a SQL simulated 8k workload running with 1.6 million IOPS on a 3-shelf JBOD environment with SSD disks and storage spaces on it. The load has been generated within a VM running on Hyper-V 2012 R2. The storage was exposed using a Scaleout File Cluster, connected with 2 redundant Infiniband NICs using RDMA. #enoughsaid

**Networking  
** IPAM Integration with SCVMM, a multi-tenant inbox edge-gateway leveraging the network isolation features in SCVMM / Hyper-V. The gateway enable direct routing and NAT for isolated CA spaces.  
Cisco Nexus 1000V can be fully integrated in the stack. The new version of NetMon, which is names MA &#8220;Message Analyzer&#8221; leverages the new capabilities of the Gen2 extensible switch in Hyper-V. It enables remote debugging of VM traffic, even in isolated environments. CA / PA space can now be &#8220;pinged&#8221; without having IP-based access to check connectivity.

**Hyper-V  
** Sharing VHDX files enables seamless guest clustering without the need to expose iSCSI or FC LUNs to guest cluster nodes. This dramatically enhances automation and flexibility for guest level clustering.  
Online VHDX resizing has been requested by a lot of us, it&#8217;s there now.  
Generation 2 VM Hardware (eg. Synthetic NIC with PXE capabilities, SCSI based boot disk).  
Hyper-V Replica support tertiary replication to an additional site / host or cluster, while bringing more flexibility into play. Replica intervals can now be selected from 30 seconds, to 5 minutes or 15 minutes. Hyper-V Recovery Manager lives on Azure as a SAAS app and enabled orchestration of Hyper-V replica disaster recovery.  
Live Migration now supports cross-subnets, Compression (up to 3:1) and is using SMB / RDMA and multi channeling, which significantly improves performance. This again is amazing what you can do now in combination with RDMA (SMB Direct).

Live cloning / export of running VMs

VM Console access now uses RDP but does not require TCP/IP connection because it&#8217;s using the VM bus. For all who said, remote console is a pain, here&#8217;s your solution.

**SCVMM  
** SCVMM gets more robust and feature rich in R2. Microsoft continues to deliver features they are using on Azure to customers. They then can use them on premise to build scalable, automated private or hybrid clouds. You can now deploy not only bare metal Hyper-V Hosts / Clusters, but also Scale Out File Servers from within VMM. With a few clicks you create a SOFS and add it to your cloud infrastructure. Given by the fact VMM comes with a new Storage Management API, this enables End to End deployment scenarios for storage, compute and networking building blocks out from a single console.

**Windows Azure Pack**  
If you know, how the Azure self-service portal looks like, that&#8217;s exactly what you get with Windows Azure Pack. While the self-service portal is just the front end, a REST API and a Powershell based automation framework enables customers and service providers to highly automate and orchestrate their cloud platforms.

**Automation  
** As a big Powershell fanboy from the beginning on (Monad 1.0), I&#8217;m really happy with the version 4. The success story continues. A lot of new CMDLETS, syntax simplifications and of course the &#8220;Desired State Configuration&#8221;. DST is an open standard protocol based management layer, implemented in Powershell. Its purpose is to define target configuration states for servers, with the least possible lines of code. Now a lot of people asked how Microsoft is going to position this new feature in conjunction with SCCM. It^s not a replacement of course, it&#8217;s more like a add-on, to leverage automation of deployment, configuration and consistency (compliance) of mid and large environments where you have a lot of server workloads to provision and to maintain.

&nbsp;

&nbsp;

&nbsp;