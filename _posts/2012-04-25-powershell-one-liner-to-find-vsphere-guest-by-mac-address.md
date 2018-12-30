---
id: 740
title: Powershell one-liner to find vSphere guest by MAC address
date: 2012-04-25T21:27:58+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=740
permalink: /powershell-one-liner-to-find-vsphere-guest-by-mac-address/
aktt_notify_twitter:
  - 'no'
categories:
  - Powershell
  - VMware
tags:
  - MAC Address
  - Powershell
  - vSphere
---
Somebody has stolen an IP address which belongs to one of your vSphere guests and you have an IP conflict reported in Windows Event Log? Just note the suspicious MAC and execute the following statement within vSphere PowerCLI

<pre>Get-VM | Get-NetworkAdapter | ? {$_.MacAddress -match '<strong>00:50:56:AF:00:61</strong>'} | % {get-vm -Id (($_.Id).split('/')[0])}</pre>

&nbsp;

&nbsp;