---
id: 315
title: 'PubForum 2010 Berlin &#8211; 3rd conference day'
date: 2010-10-10T11:44:58+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=315
permalink: /pubforum-2010-berlin-3rd-conference-day/
aktt_notify_twitter:
  - 'no'
categories:
  - Virtualization
tags:
  - Authentication Debugging Streaming PubForum
---
**Session #1 Authentication on Citrix level by Lalit Kaushal**

  * Basic about Kerberos authentication process on XenApp and XenDesktop and the involved components
  
    KDC, TGS, TGT
  * Passthrough Session (passing the user credential from one session to another)
  * Passthrough Authentication (SSONSVR or Kerberos Ticket
  * Smart Card Authentication
  
    &#8211; PNAgent always asks for PIN, some CSP like ActiveIdentiy provide PIN Caching
  
    &#8211; CDM driver module includes smart card redirection which all runs in usermode
  
    &#8211; scardhook.dll captures SC auth., forwards it to CtxSmartCardSvc, then to VC user moder API (PICA)
  
    &#8211; 4 new services on behavior of Smart Card on XenApp 6 / XenDesktop

Because we have several issues in our company regarding Smart Card Authentication to XenDesktop / XenApp I take a note of the Kerberos-combination. This could also be solution to simplify the Authentication process in our XE environment. (Logon locally wih SC, then go further via Keberos on WI to the VDA.

**Session #2 Troubleshooting / Debugging Overview by Lalit Kaushal**

Utilities to use: (tracing / dumping)
  
&#8211; CDFControl and <a href="http://support.citrix.com/article/CTX122741" target="_blank">CDFAnalyzer</a>
  
&#8211; SystemDump
  
&#8211; UserDump
  
&#8211; DumpCheck
  
&#8211; ProcessMonitor
  
&#8211; ProcessExplorer
  
&#8211; ProcDump (debug CPU spikes)
  
&#8211; Wireshark / Fiddler
  
&#8211; WinDbg
  
!analyze -v
  
!mv m <module name>
  
!analyze -m -hang
  
!locks
  
&#8211;> read bottom up

BTG (Brief troubleshooting guide)
  
<http://support.citrix.com/article/CTX106727>

great overview session about debugging techniques. Thanks Lalit!

**Session #3 Streaming / Application Delivery by Jesus Gonzales** Some improvements to App Streaming 5.2 / 6

  * Isolation of Windows Services (RadeHlprService.exe controls the creation / deletion start and stop of isolated services (6.x)
  * Sandbox Reuse &#8211;> Profile (bubble) sharing of different application started (Since 5.2)
  * No more CAB file in Version 6.0. It&#8217;s now a folder structure (This doesn&#8217;t look like a magic change, but there are some plans that citrix wants to mount a remote disk instead of streaming files down)

So last session for this forum, just trying to catch the plane now and looking forward to Pubforum in Dublin 2011.
  
Great Forum!! Thanx everyone!