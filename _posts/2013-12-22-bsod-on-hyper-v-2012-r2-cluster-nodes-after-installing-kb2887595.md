---
id: 2032
title: BSOD on Hyper-V 2012 R2 Cluster Nodes after installing KB2887595
date: 2013-12-22T12:09:10+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=2032
permalink: /bsod-on-hyper-v-2012-r2-cluster-nodes-after-installing-kb2887595/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Hyper-V
  - Virtualization
tags:
  - BSOD
  - Hyper-V
  - NetLBFO
  - VMQ
---
After recently patching all 2012 R2 Hyper-V Cluster Nodes in our own datacenter, I noticed randomly BSODs. After a short investigation I was able to narrow it down a little bit. As soon VMM started to rebalance the VMs, one after each node went into BSOD. It seemed that the first live migration task succeeded, while subsequent tasks failed. As soon the second LM process started, the target host died. So WTF&#8230; was going on here.

Analyzing the memory dump, I could see **Mslbfoprovider.sys** was mostly causing the BSOD. Crawling through the KB articles of all patches CAU has deployed to the nodes, I found that **KB2887595** replaced **Mslbfoprovider.sys** which is the core driver for NIC load balancing and failover teaming (NETLBFO).

Digging a bit deeper into the dumpy analysis I could see that the last function called was related to VMQ.  
STACK_TEXT:  
fffff800\`4876a8c8 fffff800\`46dc97e9 : 00000000\`0000000a 00000000\`00000028 00000000\`00000002 00000000\`00000000 : nt!KeBugCheckEx  
fffff800\`4876a8d0 fffff800\`46dc803a : 00000000\`00000000 00000000\`00000003 fffff800\`00691000 fffff800\`4876aa10 : nt!KiBugCheckDispatch+0x69  
fffff800\`4876aa10 fffff800\`0213572f : fffff800\`02134dfe 00000000\`00000000 00000000\`00000000 fffff800\`4876ab58 : nt!KiPageFault+0x23a  
<span style="color: #ff0000;">fffff800`4876aba8 fffff800`02134dfe : 00000000`00000000 00000000`00000000 fffff800`4876ab58 00000000`00000000 : MsLbfoProvider!vmqcGetFirstMappedMNic+0xf</span>  
fffff800\`4876abb0 fffff800\`0069c44b : 00000000\`00000000 00000000\`00000000 00000000\`00000000 ffffe000\`0ce416c0 : MsLbfoProvider!LbfoReceiveNetBufferListsComplete+0x92  
fffff800\`4876ac00 fffff800\`0068d09d : 00000000\`00000002 00000000\`00000000 ffffe000\`79a8c910 00000000\`00000000 : NDIS!ndisInvokeNextReceiveCompleteHandler+0xf35b  
fffff800\`4876ac90 fffff800\`0068cf42 : ffffe000\`101521a0 00000000\`00000002 ffffe000\`79a8c910 ffffe000\`0cccfc10 : NDIS!ndisReturnNetBufferListsInternal+0x12d  
fffff800\`4876acf0 fffff800\`01a54071 : 00000000\`00000000 00000000\`00000000 ffffe000\`0d8051c0 00000000\`00000000 : NDIS!NdisReturnNetBufferLists+0x72  
fffff800\`4876ad50 fffff800\`01a539db : ffffe000\`0ccd2000 ffffe000\`0f710001 ffffe000\`79a8c910 00000000\`00000001 : vmswitch!VmsPtNicPvtPacketRouted+0x1b1  
fffff800\`4876ade0 fffff800\`01a536ae : 00000003\`00000002 00000000\`00000000 ffffe000\`79a8c910 fffff800\`4876ae00 : vmswitch!VmsExtIoPacketRouted+0x28b  
fffff800\`4876ae90 fffff800\`006911b5 : fffff800\`4876af49 00000000\`00000000 00000000\`00000000 fffff800\`01a53a15 : vmswitch!VmsExtPtSendNetBufferListsComplete+0x9e  
fffff800\`4876aee0 fffff800\`0069078b : ffffe000\`0f7f11a0 ffffe000\`79a8c910 fffff800\`00000005 00000000\`00000000 : NDIS!ndisMSendCompleteNetBufferListsInternal+0x135  
fffff800\`4876afb0 fffff800\`01a53a2d : ffffe000\`0f7f11a0 ffffe000\`79a8c910 ffffe000\`0f76a000 fffff800\`01a53580 : NDIS!NdisMSendNetBufferListsComplete+0x2bb  
fffff800\`4876b120 fffff800\`01a535d2 : 00000000\`00000200 00000000\`00000000 fffff800\`4876b178 00000000\`00000000 : vmswitch!VmsExtIoPacketRouted+0x2dd  
fffff800\`4876b1d0 fffff800\`0069c4c6 : ffffe000\`0f7f11a0 00000000\`00000000 fffff800\`4876b1a8 00000000\`00000000 : vmswitch!VmsExtMpReturnNetBufferLists+0x42

I you&#8217;re unfamilar with VMQ I&#8217;d highly recommend the following post series.

<a href="http://blogs.technet.com/b/networking/archive/2013/09/10/vmq-deep-dive-1-of-3.aspx" target="_blank">http://blogs.technet.com/b/networking/archive/2013/09/10/vmq-deep-dive-1-of-3.aspx</a>

<a href="http://blogs.technet.com/b/networking/archive/2013/09/10/vmq-deep-dive-2-of-3.aspx" target="_blank">http://blogs.technet.com/b/networking/archive/2013/09/10/vmq-deep-dive-2-of-3.aspx</a>

<a href="http://blogs.technet.com/b/networking/archive/2013/09/10/vmq-deep-dive-3-of-3.aspx" target="_blank">http://blogs.technet.com/b/networking/archive/2013/09/10/vmq-deep-dive-3-of-3.aspx</a>

&nbsp;

### Debugging continues&#8230;.

However, I decided to uninstall the update KB2887595 as my first trial and error step. The issue was then gone. I checked the firmware and the drivers to ensure that my hosts are on the most recent level available from Intel, they where, so no option to update anything here.

After that I picked a second node suffering the BSOD behavior and just disabled VMQ by the following command.

<pre>Get-NetAdapter | ? {$_.InterfaceDescription -match  '82599'} | Set-NetAdapterVmq -Enabled $false</pre>

The filter I used is related to the physical NICs which we use in our hosts (Intel 82599 dual-port 10Gbps). After disabling VMQ, I stressed the live migration by migrating a bunch of VMs to the target host where I just disabled VMQ.

Everything was fine and no more BSOD.

So using Powershell I disabled and re-enabled VMQ on all the remaining nodes where even KB2887595 still was installed.

<pre>Invoke-Command node1,node2,node3,node4,node5 -Scriptblock {Get-NetAdapter | ? {$_.InterfaceDescription -match  '82599'} | Set-NetAdapterVmq -Enabled $false}</pre>

<pre>Invoke-Command node1,node2,node3,node4,node5 -Scriptblock {Get-NetAdapter | ? {$_.InterfaceDescription -match  '82599'} | Set-NetAdapterVmq -Enabled $true}</pre>

The I verified VMQ was enabled again.

<pre>Invoke-Command node1,node2,node3,node4,node5 -Scriptblock {Get-NetAdapter | ? {$_.InterfaceDescription -match  '82599'} | Get-NetAdapterVmq}</pre>

After this procedure I stressed the whole cluster again by live migrating VMs around like a fool. No more issues, no more BSODs.

So everything fine now? Apparently yes, beside the fact that it leaves me in a &#8220;head scratching&#8221; state without having a clue what happened to VMQ by installing KB2887595. The issue may or may not be related to the NIC types and drivers I use. I&#8217;ll continue to observe this at my customers and update this post as soon I have more information about the root cause.

Once again, before you deploy patches to your productive environment, test..test..test&#8230; and test.. 🙂

<span style="color: #ff0000;">Update: 22.12.2013 : 04:07 pm</span>

<span style="color: #ff0000;">Unfortunately the issue came back after a few live migrations. BTW, I use a converged logical switch setup. So next try was to disable VMQ optimization on the Live Migration Native Port Profile, but even after remediating the logical switch, the issue was still there. So for the moment it leaves me with either disabling VMQ on the physical NICs or uninstall the rollup patch. I&#8217;ll keep this post up to date as soon I have new findings.</span>