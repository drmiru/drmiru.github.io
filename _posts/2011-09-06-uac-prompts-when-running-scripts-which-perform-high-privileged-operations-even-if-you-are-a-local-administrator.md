---
id: 647
title: UAC prompts when running scripts which perform high privileged operations, even if you are a local administrator
date: 2011-09-06T21:31:08+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=647
permalink: /uac-prompts-when-running-scripts-which-perform-high-privileged-operations-even-if-you-are-a-local-administrator/
aktt_notify_twitter:
  - 'no'
categories:
  - Appsense
  - User Virtualization
tags:
  - Appsense EM UAC Script
---
Imagine this:

You have a 2008 R2 RDS host or a Win7 client machine and a logon script which want&#8217;s to copy some files, (templates) in my case, to C:\ProgramData, a UAC protected folder. UAC will prompt you (if UAC is enabled of course) even if you are member of the local administrators group. Well, there are solutions, but assigning Domain Admin permissions the executive account is not acceptable IMHO. I found some badly documented hacks arount the AppCompat Tool. The easiest way to get rid of this is to set the &#8220;**_\_COMPAT\_LAYER**&#8221; environment variable. (yes these are TWO underscore characters.

So just set the value of this varianle to set &#8220;**RunAsInvoker&#8221;** before you start something that tries to execute a UAC protected executable or tries to write to a UAC protected folder.

Example:

<pre>set __COMPAT_LAYER=RunAsInvoker
robocopy <a href="file://\\%logonserver%\netlogon\templates">\\%logonserver%\netlogon\templates</a> %ProgramData%\CompanyTemplates /S /E</pre>

This works also for Appsense EM actions, where you specify the executing user (which has to be a local admin of course or having apropriate access rights)