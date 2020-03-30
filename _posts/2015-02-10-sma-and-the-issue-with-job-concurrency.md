---
id: 3743
title: SMA and the issue with job concurrency
date: 2015-02-10T00:54:11+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=3743
permalink: /sma-and-the-issue-with-job-concurrency/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Cloud and Datacenter Management
  - Powershell
  - Service Management Automation
  - System Center
tags:
  - Powershell
  - Service Management Automation
  - SMA
  - System Center 2012 R2
---
Job concurrency can be something you really want, or on the other hand something you really want to avoid. What if you want to enforce a particular runbook to have only a single job instance running at any given time? Unfortunately, SMA doesn&#8217;t have a built-in method to control runbook concurrency as Orchestrator 2012 / R2 had it on a per runbook base. My fellow co-worker Fulvio Ferrarini reached out if there&#8217;s a known work around and referred to the following discussions:

[<span style="color: #0563c1;">https://social.technet.microsoft.com/Forums/en-US/9db7d18f-65ec-4fc6-a2de-9c3f485d6640/sma-specific-runbook-concurrency-limits?forum=scogeneral</span>](https://social.technet.microsoft.com/Forums/en-US/9db7d18f-65ec-4fc6-a2de-9c3f485d6640/sma-specific-runbook-concurrency-limits?forum=scogeneral)

[<span style="color: #0563c1;">http://feedback.azure.com/forums/255259-azure-pack/suggestions/6201986-sma-runbook-concurrency</span>](http://feedback.azure.com/forums/255259-azure-pack/suggestions/6201986-sma-runbook-concurrency)

I tried the first method, but that didn&#8217;t work very well. The main reason for that? Well, querying the SMA Web Service for Jobs of a particular runbook, only returns jobs matching the following states:

  * Running
  * Failed
  * Suspended
  * Completed

The WS call **does not** return &#8220;queued&#8221; or  &#8220;starting&#8221; stated. So therefore we can&#8217;t guarantee that there&#8217;s no other runbook on the way to its execution.

The second method worked, but having a numeric counter without enhanced overlap protection seemed a bit to weak.

Therefore I searched for a more flexible and reliable method, although the possibilities are limited without tweaking the database. The <a href="https://gallery.technet.microsoft.com/SMA-Runbook-snippet-to-a15a159e" target="_blank">following code snippet on TechNet</a> can be used within any runbook you wish to protect from concurrency.

Some notes here&#8230;

  * Each protected runbook must have an equivalent SMA variable to record the job lD currently locking the others
  * The concurrency protection does work inside the runbook code and not on the job instance level. Although you can have multiple job instances running at the same time, you have to choose where to protect your runbook code from concurrency. On the other hand this gives you the flexibility to have some code in your runbook being executed, even in parallel, while other code is protected from concurrency.
  * You really have to make sure that you include the &#8220;free up&#8221; region at the end AND at any try/catch block or throw sequence, to ensure the lock is released even if a terminating error occurs. Which leads me to the last point&#8230;
  * Handle your errors and exceptions. Unhandled terminating errors can lead to mis-locked runbook jobs

&nbsp;

<span style="text-decoration: underline;">DISCLAIMER</span>  
None of the above methods guarantee that job instances of a runbook can&#8217;t be created and run at the very same time, but depending on which one you choose, you can dramatically minimize the likeliness for this occurrence.

&nbsp;

&nbsp;

&nbsp;