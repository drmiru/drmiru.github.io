---
id: 412
title: How to reset a password on a windows box
date: 2011-03-01T21:25:24+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=412
permalink: /how-to-reset-a-password-on-a-windows-box/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Security
tags:
  - reset password administrator
---
<span style="color: #ff0000;"><strong>Update!: Regardless of the fact that the method described below still works for Windows 2012 incl. Domain Controllers, it is not a supported way and you use it at your own risk.</strong></span>

There are several ways to reset a local admin password. The one I&#8217;m posting here is one of the simplest one, and YES!, it works for a domain admin password too! So I think you&#8217;ll gonna think about your physical server storage and access security again after you&#8217;ve read this post.

So how that works?

  * Use a windows bootable installation media (physical or ISO)
  * Choose Language
  * Choose &#8220;Repair&#8221; option
  * Choose instance of windows to repair
  * Select &#8220;command prompt&#8221; as recovery option
  * Change to %systemdrive%\Windows\System32
  * Rename utilman.exe to utilman.exe.orig
  * copy cmd.exe to utilman.exe
  * Reboot from local disk
  * At the logon prompt Press &#8220;Windows-Key &#8211; U&#8221; (this opens a command prompt
  * net user administrator MyNewDamnPassword
  * Logon with your new password
  * Delete utilman.exe and rename utilman.exe.orig back to utilman.exe

Surprised? Shocked? Don&#8217;t mind about tricks like this one, but think about your servers physical placement and who has access to them!
  
(and of course in a virtual world you might think about who can mount a ISO to VM and boot it  from that)