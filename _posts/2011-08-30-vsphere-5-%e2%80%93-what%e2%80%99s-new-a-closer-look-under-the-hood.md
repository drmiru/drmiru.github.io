---
id: 624
title: vSphere 5 – what’s new , a closer look under the hood
date: 2011-08-30T09:52:11+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/2011/08/624/
permalink: '/vsphere-5-%e2%80%93-what%e2%80%99s-new-a-closer-look-under-the-hood/'
aktt_notify_twitter:
  - 'no'
categories:
  - Virtualization
  - VMware
tags:
  - vSphere 5 ESXi
---
Attending the vSphere 5 upgrade class gave me a pretty good overview of vSphere 5&#8217;s new features and enhanced functionalities. VMware has again made a step forward to provide a – please forgive me – &#8220;Cloud proven&#8221; virtualization solution.

And that is the content:

  * [ESXi in a nutshell](#_ESXi_in_a)
  * [vCenter 5 in a nutshell](#_vCenter_5_in)
  * [Virtual machines (V8)](#_Virtual_machines_(V8))
  * [Networking (Distributed vSwitches)](#_Networking_(Distributed_vSwitches))
  * [Storage](#_Storage)
  * [Scalability / High Availability](#_Scalability_/_High)
  * [Alternative deployment methods](#_Scalability_/_High)
  * [vSphere PowerCLI 5](#_vSphere_PowerCLI_5)
  * [vCenter Server appliance](#_vCenter_Server_Appliance)

&nbsp;

# ESXi in a nutshell

  * ESX classic has gone, what means, only ESXi is available, so you&#8217;d better get familiar with your vSphere PowerCLI and vSphere CLI know how, if you&#8217;ve not already done so
  * Installation creates 4GB scratch partition
  * 2098 MB Memory required
  * Scripted installation still possible, some kickstart commands depreciated
  * Upgrade via Update Manager requires 350MB /boot partition!!! (so in most cases you won&#8217;t be able to upgrade a ESX 4.x, because nobody created a 350MB boot partition <span style="font-family: Wingdings;">J</span>). IMHO It&#8217;s although better to create a clean and fresh installation.
  * Image builder (PowerCLI) allows creation of custom images containing 3<sup>rd</sup> party agents and additional drivers and auto deployment images
  * DCUI (direct console user interface) provides now the identical commands as the remote CLI via the built in busybox toolset. So local console debugging becomes more easier again

# vCenter 5 in a nutshell

  * Requires x64 OS (2003 R2 / 2008 R2), min. 4GB Memory
  * vCenter also available as a Linux based appliance
  * No longer requires dbo rights for database upgrades
  * Now uses 64-bit DSN
  * No Converter on install media (will VMware deliver a version 5 enterprise converter soon ?)
  * Data migration tool helps to transfer vCenter settings to a new box
  * Update Manager supports now simultaneous host patching and ESX to ESXi migrations (if you&#8217;d really want to do that…)
  * Update Manager does not support guest OS patching any more
  * vSphere Web Client is not really a full replacement for the vSphere classic client, but although a cool alternative for basic operational tasks as it supports also Firefox and Linux based browsers. The web client has an SDK for customizations. Currently it does not support connecting directly to an ESXi, only via vCenter web service.
  * vCenter Solutions Manager (Currently, there are no integrations, but the basic idea behind is to provide a API framework for 3<sup>rd</sup> party vendors to integrate with vCenter, eg. Netapp SnapManager, veeam backup/replication). Also the VMware own existing solutions like Chargeback, Capacity IQ, etc. should be integrated there in future versions.

# Virtual machines (V8)

  * 32vCpus per VM
  * 1TB RAM per VM
  * USB 3.0 devices
  * Smart Card readers (can now be used to authenticate against virtual machines using vSphere client)
  * E1000e network adapter
  * VMDirectPath I/O version 2
  * UI for multicore vCPUs (define what VMs see (physical / virtual cores)
  * Snapshot consolidation (commit / cleanup snapshot chains where snapshot descriptor file and effective delta files are out of sync). This solves orphaned deltas, where all changes still go to the delta, but no snapshot can be found.

# Networking (Distributed vSwitches)

  * NetFlow delivery of physical/virtualized network traffic to to NetFlow collector
  * Port mirroring on distributed virtual switches (dramatically enhances troubleshooting of network traffic between virtual machines) Who did not wait for this feature to come? Unfortunately it&#8217;s only available to Enterprise Plus license because it relies on the distributed vSwitch architecture
  * <div>
      Network I/O control with predefined / user defined network resource pools
    </div>
    
      * VMware Fault Tolerance traffic
      * iSCSI traffic
      * Management traffic
      * NFS traffic
      * Virtual machine traffic
      * Vmotion traffic
      * vSphere replication traffic
  * Enhanced ESXi stateless firewall with granular access control. Custom services can be added via xml file (eg. Veeam backup or other services)

# Storage

  * <div>
      VMFS version 5
    </div>
    
      * Guid Partition Table (GPT) replaces master boot record to support partitions greater that 2TB
      * Max datastore size increased to 64TB
      * Max RDM size increased to 64TB
      * Max file size on a datastore remains on 2 TB
      * Newly created VMFS 5 datastores have a fixed 1MB block size
      * File system subblock size has decreased from 64K to 8K
  * Storage VMotion supports migration of VMs having snapshots or using linked clones
  * <div>
      Storage VMotion has a new mirroring architecture. If a VM is moved to another data store, VM monitor loads a I/O mirroring driver which enables constant writes to both sides based on a block level bitmap (source and destination). Here is how that works exactly:
    </div>
    
      * The VM directory is copied from the source to the destination datastore
      * The mirror driver creates a single pass and copies the virtual disk files from the source to the destination.
      * During the copy process the mirror driver tracks all changes on block level bitmap to recognize changes in blocks. All changes are written simultaneously to both, the source and the destination disk files. And here&#8217;s one part of the magic, because this procedure eliminates the iterative process of vSphere 4 where VMs with high IOPS characteristics where hard to storage VMotion.
      * An additional virtual machine is started by the VMMonitor in background using the copied files on the destination datastore
      * After the sinlgepass copy process has finished, Storage VMotion transfers the control to the machine on the destination datastore. (you can follow this other part of the magic using esxtop utility)
      * The virtual machine directory and the virtual machine&#8217;s disk files are deleted from the source datastore
    
    This enhanced the reliability of storage VMotion required for Storage DRS.</li> 
    
      * Storage DRS
      * SSD cache (auto detection of local or shared SSD disk) -> faster storage VMotion or VM swapfile location
      * <a name="OLE_LINK3"></a>vSphere 5 has now a software FCoE adapter (required partial FCoE offload capabilities on the physical NIC)
      * VAAI enhancements (NAS support, Monitoring of thin provisioned storage of thin provisioned storage arrays)
      * VSA (virtual storage appliance)
  
        The VSA is a virtual appliance running on an ESXi host. It provides HA/DRS / VMotion functionalities between ESXi hosts with local storage. Multiple VSA instances on multiple ESX hosts can be combined to create a VSA cluster (max 3 nodes). VSA cluster provides datastore replication to provide automatic failover from HW/SW failures. It&#8217;s kind of distributed- or grid storage architecture if you like. The drawbacks are the relatively high price and the limitations of supported server types (currently some DELL / HP)
      * Profile-driven storage (enables tags on datastores to create storage categories for virtual machine placement. Virtual machines and datastores can then be mapped to a profile which simplifies the placement of new virtual machines by a simple categorization. ESX admin can see if a machine is compliant regarding it&#8217;s placement on the actual storage category)</ul> 
    
    # Scalability / High Availability
    
    ### VMotion
    
      * Multi-NIC support of up to four 10Gbps NICs or up to sixteen 1Gbps NICS
      * Support for &#8220;higher&#8221; latency links of up to 10ms
      * Improved error reporting
      * Reduced application overhead (not sure if this is really a benefit)
    
    ### HA
    
      * Datastore Heartbeats ensures HA reliability if management network has an outage (requires at least two shared VMFS datastores within the cluster accessible by all cluster nodes) NFS is also supported as heartbeat datastore. HA is not available any more if the cluster does not have at least two heartbeat datastores!
      * <div>
          Master / Slave concept
        </div>
        
          * Master agent ensures protection of protected VMs and maintains current state of protected VMs and slave agents
          * Slave agent monitors own protected VMs and monitors the health state of the master agent. If master fails all slaves participate in a new master election
      * Management network partition support
      * DNS dependency has been removed
    
    ### Resource Pools
    
      * <div>
          Resource pools are now maintained in vCenter rather than on the ESX hosts. vCenter has complete control over resource pool regardless of where the pool is created
        </div>
        
        &nbsp;</li> </ul> 
        
        ### Fault Tolerance
        
          * Not much <span style="font-family: Wingdings;">J</span>
          * Support for Westmere-ex, Sandy Bridge and AMD bulldozer processors
          * Broader guest OS support
          * <div>
              Still, SMP is not supported
            </div>
            
            &nbsp;</li> </ul> 
            
            ### EVC
            
              * Support for future processor generations
              * Ability to change EVC mode for disconnected hosts
              * EVC validation check on reboot (helps in case somebody changed BIOS settings)
            
            &nbsp;
            
            # Alternative deployment methods
            
              * **Auto deployment** (provisioning of stateless ESXi hosts)
  
                This technology uses a combination of DHCP/TFTP and PXE services to deliver stateless ESX hosts. The ESX host loads the hypervisor, a host profile and optional an answer file containing additional settings into the memory. The host runs it&#8217;s state and configuration entirely in the memory, deployments to disks are not supported. Auto deployment server can be installed separately on a server but communicates with the vCenter server. If vCenter becomes available, host deployment continues to work, but if the deployment server or one of it&#8217;s dependencies like DHCP or TFTP are unavailable hosts are completely down once they are restarted. Installing patches via Update Manager is limited to those which don&#8217;t require a reboot of course. To implement additional software packages or patches which require a reboot, the ESXi deployment image has to be updated using Image Builder tool.
            
            # vSphere PowerCLI 5
            
            The new version of this powerful scripting, automation and development framework has new cmdlets and bug fixes which I personally waited for a long time. See [**here**](http://www.vmware.com/support/developer/PowerCLI/changelog.html) for release notes. Image Builder and Auto Deployment both completely rely on PowerCLI cmdlets.
            
            # vCenter Server Appliance
            
              * vCenter server is now available as a SLES based virtual appliance.
              * Supports either the built in IBM DB2 or remote Oracle or DB2 as database instances. The built in database should only be used for small environments up to 5 ESXi hosts and 50 VMs, says VMware.
              * Installed and ready to use within a few minutes
              * Provides same API capabilities and functionalities to vSphere Client, vSphere Web Client and vSphere PowerCLI as the Windows based product.
              * <div>
                  Current Limitations:
                </div>
                
                <ul style="margin-left: 72pt;">
                  <li>
                    No support for vCenter Heartbeat
                  </li>
                  <li>
                    No support for MSSQL databases
                  </li>
                  <li>
                    No Support for vCenter Linked Mode
                  </li>
                  <li>
                    No IPv6 Support
                  </li>
                </ul>
            
            Some personal thoughts…
  
            The appliance requires an external Oracle or DB2 database for mid or large environments. So the license savings for a Windows Server and SQL License could be lost if you have to pay for an Oracle database instance. As the vCenter availability will become more and more important it&#8217;s not too bad IMHO to have a preconfigured Linux appliance, because you probably won&#8217;t have Windows admins &#8220;playing around&#8221; with the heart of your vSphere infrastructure. Although I&#8217;m pretty sure that VMware will continue to push this variant and perhaps we&#8217;ll see the Windows base vCenter disappearing within the next few version…