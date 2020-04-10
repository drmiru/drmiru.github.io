---
id: 970
title: How to use a dedicated interface / VLAN for Hyper-V replica traffic
date: 2013-03-19T02:53:14+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=970
permalink: /how-to-use-a-dedicated-interface-vlan-for-hyper-v-replica-traffic/
aktt_notify_twitter:
  - 'yes'
s2mail:
  - 'yes'
aktt_tweeted:
  - "1"
categories:
  - Hyper-V
  - Microsoft
  - Virtualization
tags:
  - Hyper-V
  - Replica
---
&nbsp;

After two months of blogging-abstinence (mainly caused by finishing my MCSE private cloud certification) I&#8217;m starting with a series of blog posts about experiences I made within Hyper-V projects. First of all I must admit that I&#8217;ve been a big VMware enthusiast for the past years. (always fighting in a good mood with [Aidan Finn](http://www.aidanfinn.com/), AKA Mr. Hyper-V <span style="font-family: Wingdings;">🙂</span>) …But since I&#8217;ve seen the potential of Server 2012 together with the System Center 2012 product family I had to look closer than I did on Hyper-V 2008 R2. Currently I&#8217;m involved in projects where using Hyper-V as an alternative to VMware or replacing VMware is the use case, including migration and transition path of course.

In this post I&#8217;m covering **Hyper-V replica** and its replication traffic. If you want to know what Hyper-V replica does and how it works I recommend to read [Aidan&#8217;s post](http://www.aidanfinn.com/?p=12147).

**Scenario:**

  * Hyper-V Cluster with replica broker role enabled, replicates a VM named VM1 to a Hyper-V standalone host in a DR site
  * You want the replication traffic to use the Live-Migration Network in the main site.

![](http://www.miru.ch/wp-content/uploads/2013/03/032213_0615_Howtouseade1.jpg) 

&nbsp;

Some short facts before we go deeper.

  * Hyper-V Replica uses http/https protocol to transfer block changes occurring within virtual machine disk files.
  * By default it uses Kerberos mutual authentication between a pair of Hyper-V hosts (meaning Replica-Source and Replica-Destination host)

Now if you look at the two facts above you&#8217;ll notice that it might not be too easy to separate the replica traffic if using Kerberos authentication. How come? Kerberos requires proper DNS and Active Directory configuration to work. Enabling a VM for replication involves the definition of the target host by name / fqdn. Usually your target host name will respond with the IP address bound to the management interface / vSwitch / virtual network adapter, wherever you defined the primary IP. Replication traffic is sent via the management interface from the source host to the management interface of the destination host. What we want is to use the live migration VLAN for replication traffic in this example. While this may sound weird when thinking about WAN connections, it may be more interesting when replication lots of virtual machines within the same data center to a recovery or repro-environment. You really not want to have your management VLAN filled with replication traffic.

<span style="color: #5b9bd5; font-size: 12pt;"><strong><span style="font-family: Wingdings;">&#8211;> </span>To tweak the replication traffic occurring on a different VLAN, you have to use certificate based mutual authentication, where you can &#8220;fake&#8221; the name resolution between source and destination replica hosts.<br /> </strong></span>

Refer to these blog post on how to create and use certificates for Hyper-V replica (also covering replica broker scenario for clusters)

<http://blogs.technet.com/b/virtualization/archive/2012/03/13/hyper-v-replica-certificate-requirements.aspx>

<http://blogs.technet.com/b/virtualization/archive/2012/07/02/requesting-certificates-for-hyper-v-replica-from-cas.aspx>

&nbsp;

**How to proceed….  
** 

**This is a short workflow checklist for what you have to do**

  * Edit the hosts file to add records for the destination host (either the replica broker network name or the standalone Hyper-V host)  
    The host resolution should point to an IP on the Live Migration or Replication Network on the destination host / replica broker
  * If either source or destination or both are cluster nodes, add the replica broker role first (choose the hostname which will be resolved to the replication VLAN)
  * Create Certificates for source and destination replica server (if using clusters be sure to add the network name of the replica broker resource as a subject alternative name. You can also use a wildcard certificate)
  * Import the certificates and if required private root CA certificates on each hyper-V host (local machine cert store)
  * <div>
      If using either private, self-signed or certificates, add the following registry value to all Hyper-V hosts<br /> HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Virtualization\Replication<br /> DisableCertRevocationCheck = 1 (REG_DWORD)
    </div>

  * <div>
      Enable the destination host replica settings to accept replications from the replica broker&#8211;> Now you should be able to enable a VM for replication while having the replication traffic handled by the LM interfaces. I&#8217;ve tested this configuration with physical separation as well as with a converged fabric configuration.</p>
    </div>

&nbsp;

Other helpful resources:

<http://blogs.technet.com/b/virtualization/archive/2012/03/27/why-is-the-quot-hyper-v-replica-broker-quot-required.aspx>

<http://blogs.technet.com/b/virtualization/archive/2012/04/09/configure-your-replica-server-to-receive-replication-traffic-from-specific-primary-server-s.aspx>

<http://blogs.technet.com/b/virtualization/archive/2012/06/15/interpreting-replication-health-part-1.aspx>

<http://blogs.technet.com/b/virtualization/archive/2012/06/21/interpreting-replication-health-part-2.aspx>