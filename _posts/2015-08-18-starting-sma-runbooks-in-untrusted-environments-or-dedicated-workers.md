---
id: 4247
title: Starting SMA Runbooks in untrusted environments or dedicated workers
date: 2015-08-18T23:54:00+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4247
permalink: /starting-sma-runbooks-in-untrusted-environments-or-dedicated-workers/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Azure Automation
  - Cloud and Datacenter Management
  - Microsoft
  - Service Management Automation
  - System Center
tags:
  - Azure
  - Powershell
  - Service Management Automation
---
### The unpleasant question

While deploying Service Management Automation out in the field over the last 2 years, I&#8217;m more and more confronted with an unpleasant question:

_How can we choose the runbook worker, which is going to execute a particular runbook?_

### Why would you need that?

If you have a multi-forest environment without trusts and/or an environment with lots of DMZs and other sensitive network zones, you might not be allowed to connect from your regular runbook workers to any of the sensitive target systems. Orchestrator had (still has) the option to execute a particular runbook on a runbook worker of choice.

&nbsp;

[<img class="alignleft  wp-image-4253" src="http://www.miru.ch/wp-content/uploads/2015/08/SMA-Multi-Env-Worker.png" alt="SMA Multi Env Worker" width="508" height="375" srcset="http://www.miru.ch/wp-content/uploads/2015/08/SMA-Multi-Env-Worker.png 671w, http://www.miru.ch/wp-content/uploads/2015/08/SMA-Multi-Env-Worker-300x221.png 300w" sizes="(max-width: 508px) 100vw, 508px" />](http://www.miru.ch/wp-content/uploads/2015/08/SMA-Multi-Env-Worker.png)[
  
](http://www.miru.ch/wp-content/uploads/2015/08/SMA-Multi-Env-Worker.png) 

&nbsp;

&#8211;> Well, the short answer is: You can&#8217;t do that with SMA without creating multiple SMA instances.

###  But wait, there&#8217;s more.

The long answer is: You can&#8217;t do it out of the box with the current version. However I wanted to know if there might be a &#8220;dirty&#8221; way to achieve that. As I did some reverse engineering on how SMA runbook jobs are fired, the SQL traces showed interesting results. There are mainly two stored procedures being executed, when SMA web service triggers a runbook start.

  *  [sma].[core].[createjob]
  
    (creates a new job entry and a new job context entry)
  * [sma].[queues].[message]
  
    (creates a new message to inform the worker about new job)

Therefore I created a little function which takes the required parameters to execute the two procedures via T-SQL. Along with other functions created under the former name &#8220;SMAHouseKeeping&#8221; I&#8217;ve put the runbook calling function into a module called c**SMATools** which can be <a href="https://gallery.technet.microsoft.com/Module-to-troubleshoot-and-ebafca16" target="_blank">downloaded from TechNet</a>**.**

To have a SMA runbook job executed on a specific worker you simply have to provide the following parameters.

<pre>Invoke-cSMARunbook -RunbookName "My-Runbook" -WorkerName "MyWorker" -WebServiceEndpoint https://MySMAWebService</pre>

To call a runbook with input parameters, you have to provide them in XML string format.

<pre>Invoke-cSMARunbook -RunbookName a_masta -WorkerName smaw002 -RunbookParameters '&lt;Parameters&gt;&lt;Parameter name="message" value="Hello World" isValueNull="0"/&gt;&lt;/Parameters&gt;' -WebServiceEndpoint https://sma01</pre>

### 

### Any other, &#8220;supported&#8221;, way?

Yes, there is one. If you&#8217;re using Azure Automation you might want to use the concept of &#8220;hybrid Workers&#8221;, which does basically mean, you&#8217;re authoring and calling your runbooks on your public Azure Automation account, but the runbook code itself is executed on a &#8220;on premises&#8221; worker. Using this architecture you can put one or more worker in a runbook server group. When calling a runbook, you&#8217;re able to specify the target runbook group. Having multiple groups enables you to differentiate where code is executed at rest. See <a href="https://azure.microsoft.com/en-us/documentation/articles/automation-hybrid-runbook-worker/" target="_blank">here</a> for more information about Hybrid Runbook Worker scenario with Azure Automation.

&nbsp;

**Disclaimer**

THE MODULE CODE AND ANY RELATED INFORMATION ARE PROVIDED “AS IS” WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED OR IMPLIED,
  
INCLUDING BUT NOT LIMITED TO THE IMPLIED WARRANTIES OF MERCHANTABILITY AND/OR FITNESS FOR A PARTICULAR PURPOSE. BE AWARE THAT MANIPULATING THE SMA DATABASE DIRECTLY IS ABSOLUTELY UNSUPPORTED AND IS COMPLETELY ON YOUR OWN RISK.

&nbsp;

&nbsp;