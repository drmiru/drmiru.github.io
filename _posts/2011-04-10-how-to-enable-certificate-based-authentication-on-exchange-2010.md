---
id: 439
title: How to enable certificate based authentication on Exchange 2010
date: 2011-04-10T10:17:44+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=439
permalink: /how-to-enable-certificate-based-authentication-on-exchange-2010/
aktt_notify_twitter:
  - 'no'
categories:
  - Exchange
  - Microsoft
  
tags:
  - Certificate OWA IIS
---
This article describes all steps to activate certificate based authentication to Outlook Web Application on Exchange 2010 / IIS 7.We assume here that you already have configured the SSL server certificate for the CAS server and the user accessing OWA has a valid user certificate for authentication installed.

  * Install certificate based auth feature

>   * Start a powershell console
>   * <pre>Import-Module ServerManager</pre>
> 
>   * <pre>Add-WindowsFeature web-client-auth</pre>

  *  <span style="font-size: x-small;">Open IIS Manager and highlight the server name in the left hand pane</span> 
      * <span style="font-size: x-small;">Double click the Authentication icon in the middle pane.</span>
      * <span style="font-size: x-small;">Right click on Active Directory Client Certificate Authentication and select Enable.</span>
      * <span style="font-size: x-small;">Select the OWA virtual directory in the left pane under the Default Web Site.</span>
      * <span style="font-size: x-small;">Double click on the SSL Settings icon.</span>
      * <span style="font-size: x-small;">Set the Client Certificates radio button to Require.</span>
      * <span style="font-size: x-small;">Click Apply.</span>

  * Unlock the IIS feature for certificate authentication  
    Run appcmd from %windir%\system32\inetsrv

>   * <pre><span style="font-size: x-small;"><em>appcmd unlock config /section:clientCertificateMappingAuthentication</em></span></pre>
> 
>   * <pre><span style="font-size: x-small;"><em>appcmd set config "Default Web Site/OWA" -section:clientCertificateMappingAuthentication /enabled:true</em></span></pre>

<span style="font-size: x-small;"><br /> </span>

  * <span style="font-size: x-small;">Configure the OWA virtual directory from the Exchange Management shell. This will turn off forms based authentication, set Windows Authentication and Basic Authentication to false. Users will be required to present a certificate to access OWA after this step.</span>

<span style="font-size: x-small;"><br /> </span>

>   * <pre><span style="font-size: x-small;"><em>set-owavirtualdirectory  -identity "server-name\OWA (Default Web Site)"  -WindowsAuthentication:$false -BasicAuthentication:$false  FormsAuthentication:$False</em></span></pre>
> 
>   * <pre><span style="font-size: x-small;"><em> </em><em>IISreset /noforce</em></span></pre>

> Opening the OWA url should now ask for the certificate
> 
> [<img class="alignnone size-thumbnail wp-image-446" title="certverify" src="../images/2011/04/certverify1-150x150.gif" alt="" width="150" height="150" />](http://www.miru.ch/?attachment_id=446)

>![](file:///C:/Users/mrueefli/AppData/Local/Temp/moz-screenshot.png) 

 <span style="font-size: x-small;"></span>