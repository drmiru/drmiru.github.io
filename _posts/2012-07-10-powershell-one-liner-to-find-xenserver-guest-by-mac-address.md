---
id: 854
title: Powershell one-liner to find XenServer guest by MAC address
date: 2012-07-10T12:21:18+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=854
permalink: /powershell-one-liner-to-find-xenserver-guest-by-mac-address/
aktt_notify_twitter:
  - 'no'
categories:
  - Citrix
  - Powershell
  - Scripting
  - XenServer
tags:
  - MAC
  - Powershell
  - XenServer
---
In a previous post I showed a Powershell one-liner to <a href="http://www.miru.ch/2012/04/powershell-one-liner-to-find-vsphere-guest-by-mac-address/" target="_blank">find a vSphere client based on a MAC address</a> search string. This time I&#8217;ll do the same for Citrix XenServer.

<pre>$vif = get-xenserver:VIF | ? { $_.MAC -match "ce:e2:b7:56:85:7f"};(Get-XenServer:VIF.VM -VIF $vif.uuid).name_label</pre>

Of course you have to install the <a href="http://community.citrix.com/download/attachments/38633496/XenServerPSSnapIn-6.0.0-1.msi?version=1" target="_blank">XenServer PoSH SDK</a> and connect to a XenServer / Pool prior issuing the command above.

&nbsp;

&nbsp;