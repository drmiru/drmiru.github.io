---
id: 4614
title: WMF 4.0 / .Net Framework Bug brakes Variable Scoping in Inlinescripts
date: 2016-04-07T21:35:40+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4614
permalink: /wmf-4-0-net-framework-bug-brakes-variable-scoping-in-inlinescripts/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Powershell
  - Scripting
  - Service Management Automation
tags:
  - Powershell
  - Service Management Automation
  - WinRM
---
In an earlier post I showed <a href="http://www.miru.ch/sma-workflows-and-remoting-how-to-deal-with-variable-scoping/" target="_blank">how-to-deal-with-variable-scoping</a> inside PowerShell Workflows to be usable within InlineScript blocks. While the $USING way is simple and easy it conflicts with proper error handling inside InlineScripts.

First of all, the credits for filing the bug on connect, and writing first <a href="http://www.powershellmagazine.com/2015/09/14/why-you-should-install-wmf-5-0-production-preview-on-your-sma-runbook-workers/" target="_blank">blog posts </a>go to fellow MVP and PowerShell geeks <a href="https://twitter.com/trondhindenes" target="_blank">Trond Hindenes</a> and <a href="http://www.powershellmagazine.com/2015/09/14/why-you-should-install-wmf-5-0-production-preview-on-your-sma-runbook-workers/" target="_blank">Ben Gelens</a>.

I recently came across multiple SMA runbook issues, not limited to single customer, but it seemed to me quite randomly. And the worst: No one changed the Runbook code and they suddenly stopped to work. After investigating quiet a reasonable amount of time I notized that the variable rescoping within Inlinescripts ($USING:) was not working any more in some situations. That&#8217;s were I came across the blog post from Ben Gelens. I was able to reproduce the error in different ways. The root cause as Ben states correctly is $ErrorActionPreference variable which forces a bug in WF compilation, **AND only** if set to value &#8220;stop&#8221;. Setting $erroractionpreference = &#8220;stop&#8221; is something I often use inside PowerShell Workflows or regular PS Scripts, as it enables try catch statements on a script level. I&#8217;m pretty sure the bug was introduced by an update to the .NET / WMF components, but at this point in time I wasn&#8217;t able to figure out which KB could be the one in question.

### Now when and where does this bug hit you?

  * On the WinRM Client Side (e.g. SMA Runbook Worker)
  * When using multiple InlineScript blocks in combination with WinRM to the same remote host inside a single workflow
  * When using nested runbooks with the above combinations

### Preventing methods / Workarounds

  * Avoiding multiple InlineScript blocks within a single workflow to the same remote host
  * Avoiding $ErrorActionPreference = &#8220;Stop&#8221; inside the InlineScript blocks, use &#8220;-ErrorAction Stop&#8221; statements for CMDLETs where needed
  * Update SMA Runbook Workers with WMF5.0 _(DISCLAIMER: Even this seems to work quite fine, it&#8217;s not yet officially supported by the Automation Team inside MSFT)_

&nbsp;

I&#8217;m happy to hear any other workarounds and thoughts.