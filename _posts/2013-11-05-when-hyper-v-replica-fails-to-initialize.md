---
id: 1839
title: When Hyper-V Replica fails to initialize
date: 2013-11-05T23:42:26+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1839
permalink: /when-hyper-v-replica-fails-to-initialize/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Hyper-V
  - Kerberos
  - Virtualization
tags:
  - Hyper-V
  - Kerberos
  - Replica
---
Enabling Hyper-V Replica is usually a no-brainer. But what if it fails to establish a replication? Weird error messages and corresponding weird event log entries don&#8217;t help to debug most of times. In this blog post I&#8217;m going to go through the basics of Hyper-V Replica and the most common configuration issues which can prevent a successful replication initialization.

If you are absolutely unfamiliar with Hyper-V Replica, I recommend to carefully read the following blog posts:

<http://blogs.technet.com/b/yungchou/archive/2013/01/10/hyper-v-replica-explained.aspx>

<http://www.microsoft.com/en-us/download/details.aspx?id=29016>

&nbsp;

## Hyper-V Replica Basics in a nutshell

  * Replication is enabled on a per-VM base, but communication takes place on the management interface between the source and the destination Hyper-V hosts
  * Replication can either take place between standalone hosts, a standalone host and a cluster or between two clusters
  * If clusters are involved, Hyper-V Replica broker role has to be configured on the Hyper-V Cluster. The connection between source and destination then involves the broker role name and IP address. Refer to this blog post on how to configure the Hyper-V Replica Broker cluster role
  * Server 2012 R2 even enables multi-hop replications eg. (Site A -> Site B -> Site C)
  * If Kerberos is used as the authentication method, transfer is not encrypted and uses a HTTP connection (Default TCP 80) from source to destination host
  * If Kerberos is used, the hosts have to be joined to the same domain or must be within a trust relationship with forest-wide authentication
  * If certificates are used for authentication, transfer is encrypted and signed and uses a HTTPS connection (Default TCP 443)
  * If certificates are used, the replication can be directed via other interfaces than the management interface. See my [previous post here](http://www.miru.ch/how-to-use-a-dedicated-interface-vlan-for-hyper-v-replica-traffic/)

## Common Errors

&nbsp;

**Error Code <span style="color: red;">0x00002F78</span>
  
** 

![](data:image/png;base64,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)

**Possible root causes:
  
** 

  * Firewall on either source or destination hosts don&#8217;t allow HTTP resp. HTTPS connections
  * A proxy has been specified on system level using netsh winhttp and destination host address is not in the exclusion list

&nbsp;

**Error Code <span style="color: red;">0x00002EFE</span>
  
** 

![](http://www.miru.ch/wp-content/uploads/2013/11/110513_2142_WhenHyperVR1.png)

**Possible root causes:
  
** 

  * Kerberos authentication fails, check DNS resolution and SPNs
  * If source and destination host are not in the same domain, Kerberos ticket size can be an issue. Refer to my [previous post](http://www.miru.ch/how-the-kerberos-token-size-can-affect-winrm-and-other-kerberos-based-services/) for more information
  * Authenticated Users or Users group does not have the permission to access the destination computer via network, check user rights assignments
  * Incoming replications are not enabled on the destination host / Replica Broker

&nbsp;

**Error Code <span style="color: red;">0x80004004<br /> </span>**

![](http://www.miru.ch/wp-content/uploads/2013/11/110513_2142_WhenHyperVR2.png)**
  
** 

&nbsp;

**Possible root causes:
  
** 

  * A previous replication object already exists on the destination host. This mostly occurs if a previous relationship has not been cleaned up. Ensure that the destination VM does not exists and the corresponding files have been deleted from the Replica folder on the destination host
  * <div>
      Incoming replications are not enabled on the destination host / Replica Broker
    </div>
    
    &nbsp;</li> </ul> 
    
    &nbsp;
    
    **Error Code <span style="color: red;">0x80070005</span>
  
** 
    
    ![](http://www.miru.ch/wp-content/uploads/2013/11/110513_2142_WhenHyperVR3.png)
    
    **Possible root causes:
  
** 
    
      * SMB share is used on destination host is used as the target directory but Kerberos constrained delegation is not properly configured. Refer to [this blog post](http://blogs.technet.com/b/virtualization/archive/2013/06/14/using-smb-shares-with-hyper-v-replica.aspx) on how it works and how to configure it correctly
    
    &nbsp;
    
    I hope I could help with some hints to the most common errors you can run into when enabling Hyper-V Replica. However, if you find other error numbers or weird behaviors, let me know and write me an email to:
  
    drmiru(at)Hotmail.com. I&#8217;ll then update this post.