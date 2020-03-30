---
id: 3630
title: Troubleshooting SMA (Service Management Automation) – Part 2
date: 2014-12-20T00:07:04+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=3630
permalink: /troubleshooting-sma-service-management-automation-part-2/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Cloud and Datacenter Management
  - Microsoft
  - Powershell
  - Service Management Automation
  - System Center
tags:
  - Automation
  - Powershell
  - Service Management Automation
  - System Center 2012 R2
---
&nbsp;

&nbsp;

This is the 2nd part of a 3 blog post series on troubleshooting SMA.

See <a href="http://www.miru.ch/troubleshooting-sma-service-management-automation-part-1/" target="_blank">here for Part 1</a>

In this post, I&#8217;ll focus on troubleshooting job states and worker issues.

If you read <a href="http://www.miru.ch/troubleshooting-sma-service-management-automation-part-1/" target="_blank">Part 1</a>, then you should be aware how SMA distributes the jobs to the available workers.  
While this mechanism ensures fair sharing of runbook jobs it may also introduce some issues.

## 

## What&#8217;s the matter?

  * Workers with assigned jobs can go down, or have other serious problems preventing them from executing jobs
  * Jobs may hang in a queued or starting state because of an error described above

While there are runbooks, you don&#8217;t care to start them over and over again if they failed, others will be sensitive to states from connected interfaces like Service Manager. You really want to keep the invoked runbook and having it executed properly even if the assigned runbook worker has an issue. So how can you spot on runbooks being in a problem state?

To help me out with housekeeping and troubleshooting SMA, I&#8217;ve created a little Powershell Module (SMAHouseKeeping.psm1) to perform the following tasks:

  * List Jobs and their assignment to workers (can be helpful to identify a worker with problems)
  * Move Jobs from a worker to another worker (can be used to transfer queued or starting jobs to another worker)

**Where to get the module**

<https://gallery.technet.microsoft.com/Module-to-troubleshoot-and-ebafca16>

<strong style="font-size: 1.17em;">How to use the module</strong>

To use the module you need

  * to unzip and unblock the downloaded files
  * read/write access to the SMA DB

When importing the module you have to pass SQL Server and DB Name as Arguments for the module

## **Examples:**

### ****Importing the module

(prevent this module from autoloading)

<pre>Import-Module C:\Scripts\SMAHouseKeeping\SMAHouseKeeping.psm1 -ArgumentList SQLSERVER01,SMA</pre>

(where &#8220;SQLSERVER&#8221; is the SQL Host and &#8220;SMA&#8221; is the name of the SMA database)

### Listing job to worker assignments

<pre>Get-SMAJobAssignment -JobState suspended
Get-SMAJobAssignment -JobState queued
Get-SMAJobAssignment -JobState starting
Get-SMAJobAssignment -JobState stopped
Get-SMAJobAssignment -JobState running
Get-SMAJobAssignment -JobState completed</pre>

&nbsp;

### Moving queued jobs to another runbook worker

<pre>Move-SMAJobQueue -SourceWorker worker1 -TargetWorker worker2 -JobState queued</pre>

&nbsp;

### Canceling a stuck job

<pre>Set-SMAJobState -JobID 848ecc75-d5bc-4ef7-b6ac-9335b86a269b -JobState failed</pre>

&nbsp;

### And BTW&#8230;.

**THIS CODE IS MADE AVAILABLE AS IS, WITHOUT WARRANTY OF ANY KIND. THE ENTIRE   RISK OF THE USE OR THE RESULTS FROM THE USE OF THIS CODE REMAINS WITH THE USER**