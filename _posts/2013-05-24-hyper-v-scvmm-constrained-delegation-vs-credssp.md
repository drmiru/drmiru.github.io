---
id: 1086
title: 'Hyper-V &#038; SCVMM &#8211; Constrained Delegation VS. CredSSP'
date: 2013-05-24T17:02:36+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1086
permalink: /hyper-v-scvmm-constrained-delegation-vs-credssp/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Hyper-V
  - Powershell
  - System Center
  
tags:
  - CredSSP
  - double-hop
  - Kerberos
  - Powershell
  - SCVMM
---
A lot of customers I&#8217;m currently engaged in Hyper-V Projects have started their Hyper-V 2012 deployments without SCVMM. As they are trying to integrated it actually one of the most popular questions arising is:

What about SCVMM and Constrained Kerberos Delegation?

To explain the situation let&#8217;s first have a look at the following <a href="http://www.aidanfinn.com/?p=13711" target="_blank"><strong>post</strong></a> from Aidan Finn which provides great information about the delegation problem and how to solve it.

SCVMM does not require to configure Constrained Kerberos Delegation at all, because it always uses &#8220;fresh credentials&#8221; passed via <a href="http://en.wikipedia.org/wiki/Security_Support_Provider_Interface" target="_blank">CredSSP</a> when issuing commands and tasks. Therefore it uses pre-configured RunAs accounts or explicit credentials, where the second option is not really recommended for various reasons. This is a big enhancement especially for larger environments where you&#8217;d have to configure tons of delegations without SCVMM.

**Powershell double-hop scenarios  
** 

As mentioned above SCVMM solves the issue for you as it always issues commands remotely via WinRM on the target Hyper-V host using [CredSSP](http://en.wikipedia.org/wiki/Security_Support_Provider_Interface).

But you might run into another issue while working with Server Manager 2012 or System Center Orchestrator, because Powershell does not allow double-hop scenarios using Kerberos delegation, but CredSSP.

Powershell Remoting supporting [CredSSP](http://en.wikipedia.org/wiki/Security_Support_Provider_Interface) was introduced with Version 2.0. [CredSSP](http://en.wikipedia.org/wiki/Security_Support_Provider_Interface) Support for WSMAN is disabled by default.

See <a href="http://msdn.microsoft.com/en-us/library/windows/desktop/ee309365(v=vs.85).aspx" target="_blank"><strong>here</strong></a> on how to enable it via CMDLETs or Group Policy.