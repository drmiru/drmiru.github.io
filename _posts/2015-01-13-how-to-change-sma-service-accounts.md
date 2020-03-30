---
id: 3669
title: How to change SMA Service Accounts
date: 2015-01-13T19:36:31+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=3669
permalink: /how-to-change-sma-service-accounts/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Cloud and Datacenter Management
  - Microsoft
  - Service Management Automation
  - System Center
tags:
  - Service Management Automation
  - SMA
  - System Center 2012 R2
---
A SMA (Service Management Automation) infrastructure has typically two different service accounts being used for running the basic environment.

**Runbook Service Account  
** This account is used to execute the Runbook Worker Service, to compile and launch the workflows (runbooks)

**Web Service App Pool Account  
** The account is used as the web application pool identity of SMA REST web service

&nbsp;

## Changing the Runbook Service Account

  * Stop all &#8220;Runbook Service&#8221; services on any runbook workers
  * Assign the new user account to all &#8220;Runbook Service&#8221; services
  * Change the registry key  
    HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\ServiceManagementAutomation\RunbookWorker\ServiceAccount  accordingly  
    (_although I&#8217;ve found that it works without changing this value, I&#8217;d still recommend to do it so_)
  * Add the new user to the local group  &#8220;Performance Monitor Users&#8221; on each runbook worker, remove the old account
  * Add the user as a new SQL login on the SQL server hosting the SMA database and assign &#8220;dbwowner&#8221; rights on the SMA database
  * Start all &#8220;Runbook Service&#8221; services on any runbook workers

&nbsp;

## Changing the app pool service account

  * Stop the SMA IIS application pool on any node hosting the SMA web service
  * Change the SMA app pool identity on any node hosting the SMA web service
  * Add the the new user to the local group  &#8220;Performance Monitor Users&#8221; on any node hosting the SMA web service, remove the old account
  * Start the IIS application pool

###  General permission considerations

Neither to the runbook service account, nor to the app pool account you should assign extended privileges. The service accounts does not require special permissions, beside the membership of the local Performance Monitor Users group and the right to &#8220;logon as a service&#8221;. To work with permissions, use SMA credential assets instead and tend to use different credentials for different target systems and services. This prevents you having a &#8220;all in one master hero service account&#8221;.

Keep calm and automate the world&#8230;..