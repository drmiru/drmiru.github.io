---
id: 86
title: 'What&#8217;s new in VMware vSphere 4 @ Magirus &#8211; Day 1'
date: 2009-07-02T16:11:44+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=86
permalink: /whats-new-in-vmware-vsphere-4-magirus-day-1/
categories:
  
tags:
  - VMware
  - vSphere
---
<span style="text-decoration: underline;"><strong>What&#8217;s new in VMware vSphere 4 @ Magirus &#8211; Day 1 </strong></span>

Not motivated to list all new features, there are many sites including VMware one,  which list them completeley. Just wanted to highlight featurea and facts that I&#8217;ll keep in mind.

  * &#8211; Service Console (linux kernel 2.6) and VM Kernel are now 64Bit native and SC is encapsuled within a dedicated VMDK, so SC is now a VM in fact (what a surprise :-). Requires a local VMFS partition to host the SC!!!
  * &#8211; Native SATA Support (cool for Labs)
  * &#8211; Asymmetric LUN Access (LUA)
  * &#8211; Paravirtualized SCSI Adapter for VMs
  * &#8211; vCenter linked Mode (manage more than one vCenter within the same GUI (each VC keeps it&#8217;s own database, some information synced via ADAM between VC Instances)  
    &#8211;> only supported if all VCs are within the same forest/domain or whithin a two-way trust environment
  * &#8211; Update Manager supports now Patch staging (pre download patches to Hosts, remmediate later). Unfortunately there is no multi-repository support, still only one Update Manager and patch repository per vCenter. Local Repositories can be managed within vCenter Client.
  * &#8211; Orchestrator (devel, automation, orchestration) is installed by default with vCenter. There is no much information about that. 🙁
  * &#8211; Upgrade VMs. Upgrade VMware Tools first before vHW! Upgrade vHW only if all of the nodes within the ESX cluster are on 4.0!
  * &#8211; Direct upgrade of ESX Hosts is performed using Update Manager, but only works if existing ESX installation has a local VMFS (even if it&#8217;s possible to select a shared VMFS to place the Service Console vmdk, this is only a theoretic alternative I think.)
  * &#8211; Performance Graph enhancements (looks similar to legacy vmktools), very nice
  * &#8211; Perfmon Integration in Windows guests. Counters available for virtual CPU etc.
  * &#8211; Alarms / Events. A bunch of new events and triggers to monitor the infrastructure (cpu, network, storage, hosts, vms, clusters, etc.)
  * &#8211; vApp : import or create your own vApp. (container with multiple VMs exportable as OVF / OFA with enhanced properties)
  * &#8211; vNetwork Distributed Switch Architecture  
    -> enables support for Cisco NEXUS 1000 virtual switch  
    -> distributed virtual switches are configured on vCenter level, connected hosts get configured with hidden vSwitches and Uplinks to manage the connectivity to the physical environment. Base for VMsafeAPI and vShield zones

enough for now&#8230; let&#8217;s go to the lab&#8230;