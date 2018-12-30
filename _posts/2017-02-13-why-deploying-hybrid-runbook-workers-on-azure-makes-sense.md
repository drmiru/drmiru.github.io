---
id: 4824
title: Why deploying Hybrid Runbook Workers on Azure makes sense
date: 2017-02-13T22:09:34+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4824
permalink: /why-deploying-hybrid-runbook-workers-on-azure-makes-sense/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure Automation
  - Hybrid Worker
  - Powershell
  - Service Management Automation
tags:
  - Azure
  - Azure Automation
  - Hybrid Workers
---
A &#8220;Hybrid Runbook Worker&#8221; is basically an extension the the MMA (Microsoft Management Agent), meant to execute Azure Automation Runbooks on premises. For a general introduction I&#8217;m referring to the following documentation:

<https://docs.microsoft.com/en-us/azure/automation/automation-hybrid-runbook-worker>

[<img class="alignleft size-full wp-image-4830" src="http://www.miru.ch/wp-content/uploads/2017/02/automation-hbw.png" alt="automation-hbw" width="806" height="504" srcset="http://www.miru.ch/wp-content/uploads/2017/02/automation-hbw.png 806w, http://www.miru.ch/wp-content/uploads/2017/02/automation-hbw-300x188.png 300w, http://www.miru.ch/wp-content/uploads/2017/02/automation-hbw-768x480.png 768w" sizes="(max-width: 806px) 100vw, 806px" />](http://www.miru.ch/wp-content/uploads/2017/02/automation-hbw.png)

I really like the concept to have a single point of control for either Cloud- Hybrid- or on-premises automation. Additionally Azure Automation, as it&#8217;s a Cloud Service, it has more features, more frequent updates and enhancements that the on premises System Center Suite.

So while I was thinking about the Hybrid Worker scenarios and some requirements I have in ongoing projects, I though about deploying Azure VMs as Hybrid Runbook Workers instead of using the &#8220;shared&#8221; ones could be a solution for some of them.

**But Wait..**. <span style="color: #ff0000;">Deploying Azure VMs and use them as Azure Automation Hybrid Runbook Workers? Is he nuts?</span>

[<img class="alignleft size-medium wp-image-4837" src="http://www.miru.ch/wp-content/uploads/2017/02/nuts-300x200.jpg" alt="nuts" width="300" height="200" srcset="http://www.miru.ch/wp-content/uploads/2017/02/nuts-300x200.jpg 300w, http://www.miru.ch/wp-content/uploads/2017/02/nuts.jpg 524w" sizes="(max-width: 300px) 100vw, 300px" />](http://www.miru.ch/wp-content/uploads/2017/02/nuts.jpg)

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

So here&#8217;s why..

### Fair share principle of Azure Workers

Runbook jobs running more than 3 hours, will be suspended/resumed or restarted from scratch, depending on  the type of the runbook. After 3 subsequent events like this they are kind of &#8220;discarded&#8221;.
  
[See here](https://docs.microsoft.com/en-us/azure/automation/automation-runbook-execution#fairshare) for more information about &#8220;fair share&#8221; in Azure Automation.

### Scalability

If you have thousands of jobs executed each day, performance of the shared workers in azure can become an issue, especially if you use a lot of nested Workflow based runbooks. Hybrid Workers are added to Hybrid Worker Groups and follow the same &#8220;static random&#8221; queuing mechanism, as SMA workers do. Scaling up your workers (memory / CPU / IOPs) is only possible with Hybrid Workers.

### Outbound Network Connections / Special Binaries

You can&#8217;t install anything on a Azure Runbook Worker, neither can you connect from a runbook executed on a AA Worker to external networks or into a virtual machine running in one of your network security groups. Another reason are Modules, PowerShell SnapIns or even custom management consoles you might need to deploy for automation purposes. You can only import PowerShell Modules into your Azure Automation account which follow the requirements documented [here](https://docs.microsoft.com/en-us/azure/automation/automation-integration-modules).

### When to use the default Cloud bases Workers?

If those three use cases do not apply for you, I&#8217;d recommend to use the Azure Workers for the following reasons:

  * No additional cost for the workers running as Azure VMs
  * Any imported custom modules are automatically distributed, which is not the case for Hybrid Workers at the time writing
  * Less complexity

&nbsp;