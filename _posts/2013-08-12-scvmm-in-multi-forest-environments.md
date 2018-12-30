---
id: 1279
title: SCVMM in multi-forest environments
date: 2013-08-12T23:32:22+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1279
permalink: /scvmm-in-multi-forest-environments/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
bfa_virtual_template:
  - hierarchy
categories:
  - Hyper-V
  - Kerberos
  - Microsoft
  - Security
  - System Center
  - Virtualization
tags:
  - Cross-Forest
  - Kerberos
  - SCVMM
---
In this article I&#8217;m going to highlight some of the most common multi-forest scenarios you may have to deal with operating a System Center Virtual Machine Manager. The official Technet documentation claims that multi-forests scenarios are supported using Kerberos. Unfortunately I did not find an official deep dive on that yet.

<span style="font-size: 14pt;"><strong>Managing Hyper-V Hosts and Clusters in multiple AD Forests<br /> </strong></span>

![](http://www.miru.ch/wp-content/uploads/2013/08/081213_2304_SCVMMinmult1.png)

This works pretty straight forward as long as you configure a host management &#8220;Run As account&#8221; for each forest. There is no need to trust the foreign forest (from a SCVMM management perspective).
  
Why? Because SCVMM uses CredSSP and provides &#8220;fresh credentials&#8221; for each task submitted to the remote host.

**_Update:_** _(special thanks to [Thomas Maurer](http://www.thomasmaurer.ch) for this feedback)_

The following limitations apply when adding untrusted hosts:

  * Updating VMM agent on host fails. Host must be removed and re-added to VMM
  * Creating a host cluster or adding a cluster node fails
  * Cluster validation fails
  * A converged network setup with management interface being part of the logical switch fails (WMI error)
  * VMM console does not display host or cluster properties when run from a remote client due to certificate errors on WSMAN level

&nbsp;

<span style="font-size: 14pt;"><strong>Accessing SCVMM via console from a different AD Forest<br /> </strong></span>

![](http://www.miru.ch/wp-content/uploads/2013/08/081213_2304_SCVMMinmult2.png)**
  
** 

This is a tricky one because a lot scenarios are as follows:

  * SCVMM is located in a so called &#8220;resource forest&#8221;
  * Admin / User Account is located in a user or so called &#8220;account forest&#8221;.
  * The resource forest has an outgoing one-way trust to one or multiple account forests

If a user from the account forest tries to access SCVMM in the resource forest, the SQL server instance running the VMM database tries to verify the AD user object from the account forest.

&#8211;> <span style="color: #2f5496;"><strong>Thx to <a href="http://www.server-talk.eu">Michel Luescher</a> from Microsoft Switzerland for the hint!<br /> </strong></span>

VMM Console shows the following error after entering the user credentials from the account forest:

![](http://www.miru.ch/wp-content/uploads/2013/08/081213_2304_SCVMMinmult3.png)

To verify the root cause execute the following SQL query against your VMM Database using the SQL instance service account:

<span style="font-family: Consolas; font-size: 9pt;"><span style="color: blue;">select</span><br /> <span style="color: fuchsia;">suser_sid<span style="color: blue;"><br /> <span style="color: gray;">(<span style="color: red;">&#8216;AccountForest\user&#8217;<span style="color: gray;">)<br /> </span></span></span></span></span></span>

If the query returns &#8220;NULL&#8221;, your SQL Server or the underlying service account is not able to resolve the user&#8217;s SID from the account forest.

**To make this scenario working properly, you basically have two possibilities**.

  1. Enable a two-way trust between the account and the resource forest
  2. Run the VMM SQL server instance under a service account from the account forest. Additionally you can enable selective forest authentication to leverage security level.

Both solution are not very sexy from a security point of view. However I&#8217;d prefer solution 2) as it has less impact overall while assuming you have a single account forest. Otherwise you have to enable outgoing trust relationships from all required account forests to the VMM resource forest.

Ok, so …. but..

What if…

…both solutions above are not accepted for whatever reasons.

Well you can still create user accounts in the resource forest as an exception. The main pitfall here is that VMM users can&#8217;t manage their account respectively their passwords in the resource forest. You could implement a custom solution or the IISPWD website enabling users to change their resource account password. However, there is no notification mechanism in the VMM management console telling the users that their password is going to expire.

Other resources:

<http://social.technet.microsoft.com/wiki/contents/articles/884.scvmm-implementation-in-cross-domains-topology.aspx>

<http://technet.microsoft.com/en-us/library/bb740760.aspx>