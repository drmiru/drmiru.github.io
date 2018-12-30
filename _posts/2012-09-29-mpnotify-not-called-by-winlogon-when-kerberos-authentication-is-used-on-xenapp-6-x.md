---
id: 859
title: MPNotify not called by Winlogon when Kerberos authentication is used on XenApp 6.x
date: 2012-09-29T14:44:47+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=859
permalink: /mpnotify-not-called-by-winlogon-when-kerberos-authentication-is-used-on-xenapp-6-x/
aktt_notify_twitter:
  - 'no'
categories:
  - Appsense
  - Citrix
  - Virtualization
tags:
  - Appsense
  - Kerberos
  - MPNotify
  - Winlogon
  - XenApp
---
As I first noticed this behavior in March 2012 I didn&#8217;t expect to have a support case with Citrix, Microsoft and Appsense for about 6 months duration. The behavior affects services and applications implemented as network credential providers on a XenApp host. Usually they get launched by MPNotify which is spawned by Winlogon upon a correct and regognized logon method. But MPNotify was never kicked off when using Kerberos or Kerberos Pass-through logon method on XenApp servers. So in my case this affected the Appsense Environment Manager on the XenApp hosts, because the agent runs under LOCALSYSTEM and uses a credential provider for impersonation. So long story in short words.. Appsense EM was off the game when using Kerberos logon method on XA session hosts.

Citrix has now published a hotfix for XenApp where MPNotify gets spawned correctly on Kerberos logons.

Hotfix for XenApp 6.0 HRP1
  
<a href="http://support.citrix.com/article/CTX134317" target="_blank">http://support.citrix.com/article/CTX134317</a>

Hotfix for XenApp 6.5 HRP1
  
The HF for XA6.5 HRP01 has not been published yet. You might call Citrix Support to get the private or wait a few weeks till it has been officially released cauz I&#8217;m not allowed to share the private at this time.

! For Appsense EM you need the most recent version **8.3.232.0** which also includes a fix required for the issue mentioned above.

Happy Kerberos-ing &#8230; 🙂

&nbsp;