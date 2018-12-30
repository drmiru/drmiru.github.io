---
id: 372
title: Crash Dump Analysis
date: 2010-12-15T23:05:31+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=372
permalink: /crash-dump-analysis/
aktt_notify_twitter:
  - 'no'
categories:
  - Virtualization
---
The traditional Windows BSOD has turned into a rarely occuring phenomen, sometimes you might see still see it. Didn&#8217;t you crossed your hands over your head and thought&#8230; What the f\*** shall I do with this information on the bluescreen?
  
I&#8217;ve collected some useful links which will help you to get a knowledge of analyzing crash dumps (system and application crashes). Finally it shows you which component was responsible to crash your system / application.
  
An important note here:
  
MOST of user executed applications can&#8217;t be responsible for a system crash cause they are executed in the user mode of the operating system memory. So mainly kernel mode sw/hw drivers are causing your winbox to show a BSOD. Instead of asking your users, &#8220;which application did you start before it crashed?&#8221; you better have a look into the following pages and make yourself fit in basics of crash dump analysis. A special thank here to Mark Russinovich, Dmitri Vostokov and Lalit Kaushal for sharing their tremendous knowledge about this stuff.

The mother of all crash dump analysis pages by dmitri [http://www.dumpanalysis.org/
  
](http://www.dumpanalysis.org/) A nice 90min webcast about how to become a basic analyzer by Mark <http://preview.tinyurl.com/dumpanalysis>
  
Troubleshooting XenApp / XenDesktop by Lalit <http://support.citrix.com/servlet/KbServlet/download/25126-102-648711/Debugging%20XenApp%20and%20XenDesktop%20issues.ppt>

** **