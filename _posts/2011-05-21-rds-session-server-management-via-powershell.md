---
id: 525
title: RDS Session / Server Management via Powershell
date: 2011-05-21T11:43:43+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=525
permalink: /rds-session-server-management-via-powershell/
aktt_notify_twitter:
  - 'no'
categories:
  - Citrix
  - RDS
---
Once upon a time there was a cool wrapper dll for accessing TS session info from scripts and .NET programs without accessing the complex and poorly documented WTS API. The dll was called WTSadmin.dll. Unfortunately this assembly does not work any more on 2008 R2 RDS. Googling and binging sent me to a project named <a href="http://code.google.com/p/cassia/" target="_blank">Cassia</a>, which is in fact another wrapper for the WTS API. And theeeeeere.. is a Powershell module using the Cassia wrapper. It can be downloaded <a href="http://archive.msdn.microsoft.com/PSTerminalServices" target="_blank">here</a>.

I prefer to use the zip file and copy the module folder into %systemroot%\system32\WindowsPowerShell\V1.0\Modules

After that you can import the module manually or using a profile.

<pre>import-module  PSTerminalServices</pre>

And here is a little example which extracts the client IP address of the current user&#8217;s session (also works for ICA sessions of course)

<pre>$username = $env:USERNAME
$session = Get-TSSession | ? { $_.UserName -match $username} | select *
If ($session)
{
   $clientip = $session.ClientIPAddress.ToString()
   write-host "Client IP is: $clientip"
}

</pre>

If you want to manage the RDS Configuration you have to import the RemoteDesktopServices module (built in after RDS role install)

<pre>import-module RemoteDesktopServices

</pre>

After importing the module you can browse the RDS configuration as a file system.

Getting config items:

<pre>ls RDS:\RDSConfiguration

</pre>

Adding a license server

<pre>cd RDS:\RDSConfiguration\LicensingSettings\SpecifiedLicenseServers</pre>

<pre>New-Item -name licensehost.domain.com

</pre>

More info about RDS:\ provider <a href="http://blogs.msdn.com/b/powershell/archive/2009/01/09/managing-remote-desktop-services-aka-terminal-services-using-windows-powershell.aspx" target="_blank">here</a>

<pre></pre>