---
id: 547
title: Tired of OSD scripting App-V packages? Try this out. Not just another App-V Powershell script
date: 2011-06-03T22:22:45+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=547
permalink: /tired-of-osd-scripting-app-v-packages-try-this-out-not-just-another-app-v-powershell-script/
aktt_notify_twitter:
  - 'no'
categories:
  - Application Virtualization
  - Powershell
  
---
How to create a App-V package that fits different environments and location based configurations? Well there are several ways to achieve that.

  * Scripting inside the OSD file by adding registry keys and values as well as environment variables. (but what about file editing / replacement?)
  * Creating a dedicated OSD file for each location / adaption of the package (but what about the package project not recognizing manual changes?)
  * Create a dedicated package for each use case / location (well if you have a boreout, that could save your live but not the customer&#8217;s money)

The best solution IMHO is to outsource the logic to a script which interprets a simple XML file. So here&#8217;s the way to get there.

Within the OSD file you just add a script (of course a Powershell one, what else?) to be called at the PRE-LAUNCH state of an App-V application launch. The script reads out an XML file and executes:

  * Registry mods
  * Find / Replace within text based files (INI etc.)
  * Copy files / folders

Download the solution <a href="../images/2011/06/AppConf.zip" target="_blank">here</a> (it includes a little readme which should help you to implement that. It&#8217;s a BETA folks, so please drop me an <a href="mailto:michael@miru.ch" target="_blank">email</a> if you find bugs or have feature requests.