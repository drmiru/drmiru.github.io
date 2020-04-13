---
id: 1115
title: UE-V Logoff Helper Tool
date: 2013-06-10T19:23:24+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1115
permalink: /ue-v-logoff-helper-tool/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Microsoft
  - User Virtualization
  
tags:
  - Logoff
  - UE-V
  - UEVLogoffHelper
---
In one of my [recent posts](http://www.miru.ch/2013/04/why-ue-v-is-not-ready-yet-for-volatile-vdi-envionments/) I covered an issue with Microsoft UE-V regarding open applications at logoff. To keep it short here, application settings of opened applications are not synced back to the central location store when logging off. This behaviour is by design in the current UE-V release and might change in a future version.

Because this hurts non-persistent VDI desktop users as well as such jumping around different physical endpoints, I tried to find a way getting rid of this missing functionality of UE-V Agent. The first tries where pretty success less, until I found a way using the Windows API function WinAPI.SetProcessShutdownParameters to increase the exit notification priority of a windows process. My goal was to have my own little helper tool to be notified first, when Windows sends the WndEndProc message to all form based applications. Usually form based applications close their main form, when they receive the notification. My thought was that I can then stop the logoff process for a short moment using WinAPI.ShutdownBlockReasonCreate and close all user apps by invoking CloseMainWindow() method. So therefore UE-V Agent would think as of normal application closing and sync the settings back before Windows exits and shuts down the UE-V tray application.

**What the tool does**

  * Runs as a almost zero footprint tray app in the background
  * Listens for Logoff and Shutdown Events
  * Answers first of all apps in the user space on such events
  * Stops the Shutdown / Logoff sequence for a short while
  * Displays a message in system tray
  * Closes the main window of form based, running applications (launched as the Current User)
  * Resumes Shutdown / Logoff Sequence

**Where can you get it?**

<a title="UE-V Logoff Helper Tool download" href="http://www.miru.ch/?attachment_id=1271" target="_blank">http://www.miru.ch/?attachment_id=1271</a>

&nbsp;

**How to use it / Install it**

Follow the instructions in the ReadMe file. Basically the Executable has to be run for each user session at logon. As it registers a new Event Source in Application Event Log, the install script or the executable have to be run with elevated privileges once.

**Known Limitations in Version 1.0**

  * Internet Explorer Settings are not synced if IE is open during logoff
  * If applications have an active &#8220;save-as&#8221; dialog the sync process might be interrupted
  * As by design, applications which are not form based (GUI less) don&#8217;t respond to CloseMainForm() method and are therefore not captured by the tool

&nbsp;