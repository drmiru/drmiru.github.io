---
id: 460
title: Save and restore user certificates using Powershell
date: 2011-05-05T21:24:30+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=460
permalink: /save-and-restore-user-certificates-using-powershell/
aktt_notify_twitter:
  - 'yes'
aktt_tweeted:
  - "1"
categories:
  - Powershell
  - Scripting
  - Security
  - Virtualization
tags:
  - Appsense Certificates Powershell
---
After a lot of unsuccessful tries (even with Appsense Personalization Manager it&#8217;s hard to save and restore user certificates, if AppData is not redirected), I found a valuable way to export and import user certificates at Logoff / Logon.

<span style="text-decoration: underline;">Prerequisites:</span>

  * Powershell 2.0 installed
  * Execution Policy must be set accordingly
  * Users logging on / off must have the permission to execute scripts of course

These two scripts do the job.

**ExportCert.ps1**

  * Exports all user certificates to a destination folder in a PKCS12 format (.pfx)
  * Accepts filters based on issuer attributes
  * Exports User or Machine Certificates from local cert store

Usage:

<pre>Powershell.exe ./ExportCert.ps1 &lt;destinationpath&gt; &lt;certstore&gt;  [optional &lt;filter&gt;]
</pre>

Example:

<pre>Powershell.exe C:\Scripts\ExportCert.ps1 H:\Profile\Certs CurrentUser DC=Microsoft</pre>

This command exports all user certificates from CurrentUser store to H:\Profile\Certs and excludes certificates where issuer attribute contains &#8220;DC=Microsoft&#8221;

**ImportCert.ps1**

  * Imports all Certificates within a given folder into User or Machine cert store

Usage:

<pre>Powershell.exe ./ImportCert.ps1 &lt;sourcepath&gt; &lt;certstore&gt;
</pre>

Example:

<pre>Powershell.exe C:\Scripts\ImportCert.ps1 H:\Profile\Certs CurrentUser</pre>

This command imports all user certificates into the  CurrentUser store to H:\Profile\Certs

**Integrating the scripts into a Appsense Environment Manager Config**

As currently the Appsense EM does not support Powershell scripts as custom actions, we have to wrap the call of powershell scripts around a simple VBscript. If you download the scripts <a href="../images/2011/05/PS-CertMgmt.zip" target="_blank">here</a>, you&#8217;ll find usable examples for how to call the Import / Export Scripts at logon / logoff using EM custom actions.

Special Notes:

  * The scripts are provided as they are without any warranty. Use at your own risk
  * The password for private key protection of the PFX is not that strong as it has to be automatically set on import, but I&#8217;m currently thinking about other possibilities.
  * Any bugs or feature requests are welcome

<a href="../images/2011/05/PS-CertMgmt.zip" target="_blank"><img class="alignleft size-thumbnail wp-image-466" title="download-button" src="../images/2011/05/download-button-150x150.jpg" alt="" width="75" height="75" /></a>