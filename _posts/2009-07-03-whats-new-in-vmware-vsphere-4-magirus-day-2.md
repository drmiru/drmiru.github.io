---
id: 114
title: 'What&#8217;s new in VMware vSphere 4 @ Magirus &#8211; Day 2'
date: 2009-07-03T14:01:21+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=114
permalink: /whats-new-in-vmware-vsphere-4-magirus-day-2/
categories:
  - Virtualization
tags:
  - VMware
  - vSphere
---
**<span style="text-decoration: underline;">What&#8217;s new in VMware vSphere 4 @ Magirus &#8211; Day 2</span>**

Again I&#8217;m outlining my personal impressions of this update course&#8217;s 2nd day.

  * &#8211; Networking (continued)
  
    &#8211;> PWLAN support (enables private vlans, promiscuous / isolated / community) to separate VMs lying on the same vlan. In fact a vlan can be split up again into different PWLANs.
  * &#8211; Nondisruptive Migration to Distributed Virtual Switches
  
    &#8211;> Create new distributed switch, define uplinks, add first adapter to uplink, define port groups, remap VMs to new Port Groups (wizard driven), add second adapters to uplinks
  * &#8211; Storage Management
  
    &#8211;> Pluggable Storage Architecture allows 3rd Party Modules for failover / multipathing
  
    &#8211;> iSCSI enhancements (bidirectional CHAP, reduced CPU overhead for swiSCSI, per target CHAP, swiSCSI no longer required Service Console
  
    &#8211;> HBA Round Robin Multipathing is no more just experimental 🙂
  
    &#8211;> Enhanced storage reporting, monitoring and alerting / triggering
  
    &#8211;> Thin provisioning now within GUI (thin provisioning does not support fault tolerance!)
  
    &#8211;> VMFS  dynamic grow (if storage supports that)
  
    &#8211;> Storage VMOTION supports change of provisioning mode when changing the Datastore (thick <> thin).  SVMOTION now in GUI together with VMOTION (Migrate). SVMOTION works now without using snapshots. (creates a destination disks, copies content ->change block tracking ->,  fast suspend/resume to switch to destination disk. SVMOTION works now with independent disks too (at least in the lab I saw it working)
  
    &#8211;> Replica Management (Resignaturing) is now Datastore based (admin can choose to use the existing label for mirror LUNs)
  * HA
  
    &#8211;> Host Monitoring can be disabled (useful for maintenance on hosts or network infrastructure to prevent disrupts or side effects)
  
    &#8211;> Specific Failoverhost can be configured
  
    &#8211;> Percentage or Number value can be assigned to failover policy
  
    &#8211;> VM Monitoring (checks vmware tools heartbeat). If a VM stucks in bluescreen, it gets restarted if configured so.
  * Fault Tolerance
  
    &#8211;> adds a secondary VM on another node (copies via vLockstep the whole state to the secondary VM)
  
    &#8211;> a lot of requirements and limitations make this technology not useful for a lot of use cases (1vCPU, no snaps, no paravirtualized guests, etc, etc.)
  
    &#8211;> requires dedicated vmknics on vmkernel PG)
  
    &#8211;> requires SSL verification enabled on vCenter! This means every host incl. vCenter must have a valid and trusted cert installed!!

Saw today that RVTOOLS (<http://www.robware.net/>).  RVTools is a small .NET 2.0 application which uses the VI SDK to display information about your virtual machines. Very useful.

A must is the following website: <http://vsphere-land.com/>

cheers

drmiru