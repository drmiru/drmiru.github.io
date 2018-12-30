---
id: 29
title: How to dot-source a powershell script
date: 2009-03-13T21:53:16+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=29
permalink: /how-to-dot-source-a-powershell-script/
categories:
  - Powershell
tags:
  - Powershell
---
Have you ever been interested to include (to dot source) another powershell script into your own one?

It&#8217;s as simpe as this post: just decide where to include the script (typically at the beginning).
  
Add the following line:

. C:\somepath\somepowershellscript.ps1

that&#8217;s all. you now have access to all functions defined in the source script.
  
Use this method to include my &#8220;<a href="http://www.miru.ch/downloads/scripts/globalfunctions.ps1" target="_blank">globalfunctions.ps1</a>&#8221; which includes a lot of useful functions&#8230; and it&#8217;s even growing all days 🙂