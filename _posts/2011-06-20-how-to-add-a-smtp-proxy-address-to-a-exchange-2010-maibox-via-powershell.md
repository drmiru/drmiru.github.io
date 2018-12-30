---
id: 592
title: How to add a smtp proxy address to a Exchange 2010 maibox via Powershell
date: 2011-06-20T21:53:44+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=592
permalink: /how-to-add-a-smtp-proxy-address-to-a-exchange-2010-maibox-via-powershell/
aktt_notify_twitter:
  - 'no'
categories:
  - Exchange
  - Powershell
  - Scripting
tags:
  - Exchange2010 Powershell
---
This is a Powershell snippet to add a smtp proxy address to an existing mailbox in a Exchange 2010 environment.
  
It must run on a machine with installed Exchange 2010 Management Shell.

<pre>#Load the Exchange 2010 SnapIns and establish a management session
Add-PSSnapin Microsoft.Exchange.Management.PowerShell.E2010
 . $env:ExchangeInstallPath\bin\RemoteExchange.ps1 | Out-Null
 Connect-ExchangeServer -auto

#Add Exchange Proxy Address to users mailbox
$mbx = get-mailbox "JSmith"
$col_proxyaddr = $mbx.EmailAddresses
$col_proxyaddr.Add("jimbo@domain.com")
set-mailbox "JSmith" -EmailAddresses $col_proxyaddr</pre>