---
id: 2914
title: A first look on SMA Studio
date: 2014-06-30T22:27:11+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=2914
permalink: /a-first-look-on-sma-studio/
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
  - Powershell
  - Service Management Automation
---
Although Service Management Automation is IMHO the future of automation and orchestration, it has some downsides compared to the graphical editor capabilities of System Center Orchestrator. I assume that the next version of SMA, will address at least some of the missing features. In the meantime the community starts developing own solutions to make SMA runbook management a bit easier, especially when dealing with lot of runbooks and workflow code.

SMA Studio is an open source IDE tool written by [Marcus Westin](https://twitter.com/mawestin) providing additional features for SMA runbook management. It is currently in Beta status, but already provides some added value. In this post I&#8217;m quickly going through the main features and how you can simplify your SMA runbook management leveraging the tool. Keep in mind that it is a Beta, so you might want to wait for a final version or test it well, before using it in production.

## Prerequisites

  * Windows 7 or higher
  * .NET Framework 4.5
  * SMA deployment
  * Executing user must be SMA Admin

## Features at a glance

  * Editing, publishing and kicking of SMA runbooks without the need of Windows Azure Pack admin portal
  * Syntax highlighting
  * Integrated versioning
  * Version comparison

## How it works

The tool leverages the oData structure of the SMA web service API. SMA itself already provides a simple integrated versioning. Each time you save / publish a runbook it gets saved as a new version within the SMA database. Unfortunately SMA does not come with any source control tool on the end user side yet. Here&#8217;s where SMA Studio jumps in. Creating a new runbook is as easy as editing an existing one. Clicking the &#8220;save&#8221; button, saves the runbook to the cache folder within the program directory. Checking it back in, publishes a new version.

## Get Started

After downloading the tool, you have to extract the content and unblock the files.

<pre>Get-ChildItem C:\Downloads\SMAStudio2014-0.2.1-preview | Unblock-File</pre>

To start the tool, just execute ![](http://www.miru.ch/wp-content/uploads/2014/06/063014_2034_Afirstlooko1.png)

Running it for the first time, asks for the SMA web service url.

<img alt="" src="http://www.miru.ch/wp-content/uploads/2014/06/063014_2034_Afirstlooko2.png" width="439" height="364" />

This setting is saved to &#8220;**settings.xml**&#8221; within the program folder.

<img alt="" src="http://www.miru.ch/wp-content/uploads/2014/06/063014_2034_Afirstlooko3.png" width="903" height="616" />

The tool organizes the runbooks in folders corresponding to the tags you submitted to your runbooks, which is very nice. Also SMA assets can be managed out of SMA Studio. It does not provide management of SMA connection asset objects yet.

Comparing the current to a recent version is as easy as checking in and out, or creating a new one.

<img alt="" src="http://www.miru.ch/wp-content/uploads/2014/06/063014_2034_Afirstlooko4.png" width="898" height="512" />

&nbsp;

## Quick summary

For me the most important reasons why to have a look at the tool are:

  * GUI based runbook management where installation of Windows Azure Pack Admin components is not an option or planned
  * The easy to use versioning and comparison features
  * <div>
      Better overview and grouping of runbooks through the folder structuring
    </div>
    
    &nbsp;</li> </ul> 
    
    However there are some things I missed while working with SMA Studio so far.
    
      * Editing the runbooks is not very comfortable, as the tool does not provide the full set of other IDE tools, such as Powershell_ISE (Autocompletion, Intellisense, etc.)
      * The tool does not differentiate between a test version and the published one. Checking in, always updates both versions
      * Executing runbooks with parameters has some bugs (reported on GitHub)
    
    So far, I think I&#8217;m going to use the tool in the wild as soon as it hits the final version because of the versioning and comparison features.
    
    ## How to get the tool
    
    SMA Studion can be downloaded here: <http://www.sekurbit.se/>
    
    &nbsp;