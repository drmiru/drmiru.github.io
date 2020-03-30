---
id: 3957
title: Performing Maintenance on busy SMA Runbook Workers
date: 2015-05-01T00:09:38+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=3957
permalink: /performing-maintenance-on-busy-sma-runbook-workers/
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
  - Service Management Automation
  - SMA
---
### <span style="font-size: 1.5em;">Starting Point</span>

In one of my current projects we use SMA as the main automation engine for infrastructure general IT process automation. Sometimes the four runbook workers have more than 100 concurrent jobs running. While some of the jobs complete after 1-2 minutes, others run for hours up to 1 day, depending on the overall process behind. Lot of the SMA runbooks are triggered through SMA connector via Service Manager. So behind the runbooks are SCSM SRs firing of the runbooks , while the runbooks themselves sometimes again, wait for a SCSM workflow step to finish until they continue, or call the next runbook. However, the Runbook servers are busy almost all the time.

### The Challenge

If we have to perform maintenance on the workers, how can we ensure that we don&#8217;t kill active jobs?

### The Background

To decide for options here we have to highlight some facts around the Runbook worker&#8217;s principles.

  * Runbook Workers pick up the jobs based on static random queue numbers, assigned to each job
  * Once a job is assigned to a particular worker, the assignment will not change
  * Runbook workers have a drain time for running jobs when service is stopped regularly (default is 5min.)
  * During drain time, workers don&#8217;t pick up new jobs, but finish the current running ones, as long as they finish within the drain time limit
  * When Runbook service is stopped, jobs which where still running after drain time limit will be restarted from scratch when the service is back up again
  * Runbook jobs containing checkpoints will be restarted from the last successful checkpoint
  * When a worker is restarted without a graceful shutdown of the Runbook service first, drain time is skipped and ignored, rest of the behavior is same as above

###  A Solution

First we set the drain time high enough, so we can be pretty sure to have a &#8220;free&#8221; worker when the service is shut down. We can do this using the SMA PowerShell module. (in this example 8 hours)

<pre>Set-SmaAdminConfiguration -DrainTimeInSeconds 28800 -Force -WebServiceEndpoint <a href="https://smaweb">https://smaweb</a></pre>

The following script is just an example on how the update / patch procedure could look like..

<pre>Workflow Restart-SMAWorker 
{     
    $VerbosePreference = "continue"
    InlineScript {         
        Import-Module SMAHouseKeeping     
    }

    #Get all Workers     
    [string[]]$AllWorkers = (Get-SmaRunbookWorkerDeployment -WebServiceEndpoint <a href="https://smaweb).ComputerName">https://smaweb).ComputerName</a>

    #Get Least Busy Worker     
    $MaxConcurrency = $MaxConcurrency -1     
    [string[]]$LeastBusyWorker = (Get-SMAJobAssignment -JobState running | Group-Object WorkerName | Sort-Object Count).Name
    InlineScript {             
        $VerbosePreference = "continue"             
        Write-Verbose "Stopping Runbook Service on: $USING:LeastBusyWorker"
        Stop-Service RBSVC

        #---Put your custom Patch / SW Update Sequence here...

        #---End custom Patch Sequence

    } -PSComputerName $LeastBusyWorker
}</pre>

###  Some final notes

  * To make the above script working, you&#8217;ll need my SMAHouseKeeping PowerShell module, which is available on <a href="https://gallery.technet.microsoft.com/Module-to-troubleshoot-and-ebafca16" target="_blank">TechNet here</a>.
  * Using checkpoints inside master / long running runbooks can be key for several reasons. So I&#8217;d recommend to create manual checkpoints using the command &#8220;checkpoint-workflow&#8221; as often as possible, while knowing that each checkpoint requires it&#8217;s space inside the SMA database.
  * Rebooting a Runbook worker without checking for any running runbook first, is evil 😉

&nbsp;