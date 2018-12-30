---
id: 387
title: Exchange 2010 SP1 Setup fails while checking prerequisites for CAS Role
date: 2011-01-14T22:43:36+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=387
permalink: /exchange-2010-sp1-setup-fails-while-checking-prerequisites-for-cas-role/
aktt_notify_twitter:
  - 'no'
categories:
  - Virtualization
---
Trying to install Exchange 2010 SP1 on 2008 R2 was not that easy as I thought. Everything but the CAS role could be installed.
  
After installing latest windows patches setup asked always for two additional hotfixes while checking prerequisites for CAS role. Google and MS KB showed up weird results..

But to keep it short.. here&#8217;s the solution.

Windows Server 2008 R2 needs the following hotfixes : 979744, 983440, 979099 and 982867. Some of them are currently limited to Windows 7.  But as Windows 7 and Server 2008 R2 share a lot of code so they do for hotfixes. Just Download them, install them, reboot your server and your done!

More info here&#8230;

<http://technet.microsoft.com/en-us/library/bb691354.aspx>