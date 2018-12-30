---
id: 2713
title: My wish list for SMA vNext
date: 2014-06-01T22:57:42+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=2713
permalink: /my-wish-list-for-sma-vnext/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Cloud and Datacenter Management
  - Service Management Automation
tags:
  - Automation
  - SMA
  - vNext
---
Automating the world of datacenters and infrastructures has always been one of my passions. Starting with Service Management Automation (SMA), the capabilities have been extended in very cool ways. While still, SC Orchestrator is used heavily to automate IT processes, SMA is IMHO the little twinkle star. Released as a Version 1.0 together with Windows Azure Pack last year, the way Microsoft goes with automation in SMA has significantly changed the game. SMA is purely based on PowerShell Workflows. This brings some great enhancements like direct interoperability with DSC (Desired State Configuraton). PowerShell Workflows are rock solid, do support checkpoints and can be re-used even outside of SMA. Of course there are also some things you might miss in the current version of SMA when you compare it to SC Orchestrator.

I expect some updates to be delivered during the vNext wave, however here&#8217;s my current with list for SMA vNext.

  * Multi Tenancy for SMA Admin Portal
  * Delegation for Assets and Runbooks to Tenants and Admins
  * Integration into Tenant Admin Portal as seen on Microsoft Azure
  * Bulk Import / Export of Runbooks (currently delivered by SMART)
  * Selection of specific Runbook Worker when executing / calling Runbooks
  * SMA Automation native Module Integration in PowerShell_ISE to better support external Runbook development (currently delivered by a community module)
  * Nested variable passing to PS Remote Sessions within inline scripts ($USING can&#8217;t actually be reused)
  * More granular Schedule Options, higher frequencies

I definitely bet on SMA. It&#8217;s a cool product and leverages my PowerShell effort of the last 8 years. However I&#8217;d love to see some of the points on my wish list to appear in the next version.

If you&#8217;re not aware of SMA, I&#8217;d recommend my SMA Whitepaper available <a href="http://gallery.technet.microsoft.com/Service-Management-fcd75828" target="_blank">on TechNet Gallery</a>.

Feel free to post comments or drop me an email with additional feature requests, so I can include it in this post.

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;