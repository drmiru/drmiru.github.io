---
id: 1133
title: Single Sign On in RDS 2012 demystified
date: 2013-06-24T22:40:59+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1133
permalink: /single-sign-on-in-rds-2012-demystified/
aktt_notify_twitter:
  - 'yes'
s2mail:
  - 'yes'
aktt_tweeted:
  - "1"
categories:
  - Desktop Virtualization
  - Kerberos
  - Microsoft
  - RDS
  - Security
  - Smart Card
  - Virtualization
tags:
  - Authentication
  - RDS
  - Server 2012
  - SSO
---
<span style="font-size: 10pt;">Server 2012 RDS has been a huge game changer for shared hosted desktops as well as for hosted VDI deployments. The capabilities you get out of the box fit the requirements of a lot companies I&#8217;d say, and when I say a lot I don&#8217;t mean all. There are still uses cases where Citrix has to be in place and I feel that being still a good point because it keeps the innovation cycle up and running.<br /> </span>

<span style="font-size: 10pt;">However in this post I&#8217;m going to cover a topic that arises in almost every RDS project.<br /> </span>

  * <span style="font-size: 10pt;">Does RDS / Connection Broker / RDS Web / RDS Gateway support Single Sign On authentication?<br /> </span>
  * <span style="font-size: 10pt;">What with Remote Apps and different collections?<br /> </span>
  * <span style="font-size: 10pt;">What about Smart Card?<br /> </span>

**The definition of the term &#8220;SSO&#8221;<span style="font-size: 10pt;"><br /> </span>**Single Sign On can be <span style="font-size: 10pt;">implemented in several ways, but what I&#8217;m talking about here is the fact, the user has to enter his credentials only once at the primary contact device (endpoint). Subsequent logons will then be authenticated via the original credential set, which can be user name and password or PIN if using Smart Card. The credential forwarding occurs on Kerberos / CredSSP level, so no freaky agent behind the scenes filling in some user credentials.<br /> </span>

**Out of the box behaviour  
** 

<span style="font-size: 10pt;">Depending on how you design the solution the entry point for the user might be different. But however, I&#8217;m going to cover all possible pitfalls.<br /> By default the procedure to start a remote app or desktop is as follows:</span>**  
** 

  * <span style="font-size: 10pt;">User logs on to client computer providing his domain credentials / PIN<br /> </span>
  * <span style="font-size: 10pt;">User launches the RDWeb website<br /> </span>
  * <span style="font-size: 10pt;">User enters domain credentials / PIN (with cert based auth)<br /> </span>
  * <span style="font-size: 10pt;">User launches app / desktop<br /> </span>
  * <span style="font-size: 10pt;">User confirms untrusted rdp file signing message<br /> </span>
  * <span style="font-size: 10pt;">If Smart Cards are used, another PIN prompt to complete<br /> </span>

<span style="font-size: 10pt;">There are way too much authentication points and click steps until the user reaches the app / desktop. And you might be annoyed by pop-up boxes like these….<br /> </span>

![](http://www.miru.ch/wp-content/uploads/2013/06/062413_2332_SingleSignO1.png) <span style="font-size: 10pt;"><br /> <img src="http://www.miru.ch/wp-content/uploads/2013/06/062413_2332_SingleSignO2.png" alt="" /><img src="http://www.miru.ch/wp-content/uploads/2013/06/062413_2332_SingleSignO3.png" alt="" /><br /> </span>

&nbsp;

**Components (tiers) involved  
** 

![](http://www.miru.ch/wp-content/uploads/2013/06/062413_2332_SingleSignO4.png) **  
** 

While the RDWeb site is using Kerberos for SSO, subsequent authentication processes use CredSSP by providing default or fresh credentials depending on the authentication mechanism at the web tier.

**How to configure it correctly  
** 

<span style="font-size: 10pt;">There are basically four steps to complete<br /> </span>

  * <span style="font-size: 10pt;">Issue and assign a valid SSL certificate to RDS Gateways, RD Web Servers and RD Connection Brokers<br /> </span>
  * <span style="font-size: 10pt;">Provide the certificate&#8217;s thumbprint within GPO to enable trusted rdp file signing<br /> </span>
  * <span style="font-size: 10pt;">Enable Web SSO on the RD Web Servers<br /> </span>
  * <span style="font-size: 10pt;">Create GPO for Credential Delegation Settings<br /> </span>
  * <span style="font-size: 10pt;">Add the RDWeb URL to IE&#8217;s &#8220;Local Intranet&#8221; Zone<br /> </span>

<span style="font-size: 10pt;">…how to get there?<br /> </span>

**1. Issue a valid SSL certificate and assign it to the server components<span style="font-size: 10pt;"><br /> </span>**I&#8217;m not covering how to request and issue a SSL certificate here, you might know that already, I&#8217;m sure. But keep in mind the Key Usage must contain &#8220;Server Authentication&#8221;.<span style="font-size: 10pt;"><br /> </span>

<span style="font-size: 10pt;">Assign the certificate for connection broking, rdp file-signing and web access<br /> <img src="http://www.miru.ch/wp-content/uploads/2013/06/062413_2332_SingleSignO5.png" alt="" /><br /> </span>

**2. Enable SSO on Web tier**<span style="font-size: 10pt;"><br /> This step requires a bit more work including web config file editing. Be sure to create a backup of the files before screwing them up <span style="font-family: Wingdings;">J</span><br /> </span>

![](http://www.miru.ch/wp-content/uploads/2013/06/062413_2332_SingleSignO6.png) <span style="font-size: 10pt;"><br /> </span>

<span style="font-size: 10pt;">Edit: <span style="color: #5b9bd5;"><strong>%SYSTEMROOT%\Web\RDWeb\pages\web.config</strong></span><br /> </span>

<span style="font-size: 10pt;"><strong>Find the Authentication key and change it from:<br /> </strong></span>

<span style="font-family: Consolas; font-size: 10pt;"><strong><span style="color: red;"><!&#8211; </span><authentication mode=&#8221;Windows&#8221;/> <span style="color: red;">&#8211;></span><br /> </strong></span>

<span style="font-family: Consolas; font-size: 10pt;"><authentication mode=&#8221;Forms&#8221;><br /> </span>

<span style="font-family: Consolas; font-size: 10pt;"><forms loginUrl=&#8221;default.aspx&#8221; name=&#8221;TSWAAuthHttpOnlyCookie&#8221; protection=&#8221;All&#8221; requireSSL=&#8221;true&#8221; /><br /> </span>

<span style="font-family: Consolas; font-size: 10pt;"></authentication><br /> </span>

<span style="font-size: 10pt;">To:<br /> </span>

<span style="color: red; font-family: Consolas; font-size: 10pt;"><strong><authentication mode=&#8221;Windows&#8221;/><br /> </strong></span>

<span style="font-family: Consolas; font-size: 10pt;"><span style="color: red;"><strong><!&#8211;</strong></span><authentication mode=&#8221;Forms&#8221;><br /> </span>

<span style="font-family: Consolas; font-size: 10pt;"><forms loginUrl=&#8221;default.aspx&#8221; name=&#8221;TSWAAuthHttpOnlyCookie&#8221; protection=&#8221;All&#8221; requireSSL=&#8221;true&#8221; /><br /> </span>

<span style="font-family: Consolas; font-size: 10pt;"></authentication> <span style="color: red;"><strong>&#8211;></strong></span><br /> </span>

&nbsp;

<span style="font-size: 10pt;"><strong>Comment out the module and security sections:<br /> </strong></span>

<span style="font-family: Consolas; font-size: 10pt;"><system.webServer><br /> </span>

<span style="font-family: Consolas; font-size: 10pt;"><br /> <strong><span style="color: red;"><!&#8211;</span><br /> </strong><modules runAllManagedModulesForAllRequests=&#8221;true&#8221;><br /> </span>

 <span style="font-family: Consolas; font-size: 10pt;"><remove name=&#8221;FormsAuthentication&#8221; /><br /> </span>

 <span style="font-family: Consolas; font-size: 10pt;"><add name=&#8221;RDWAFormsAuthenticationModule&#8221; type=&#8221;Microsoft.TerminalServices.Publishing.Portal.FormAuthentication.TSFormsAuthentication&#8221; /><br /> </span>

 <span style="font-family: Consolas; font-size: 10pt;"></modules> <strong><span style="color: red;">&#8211;></span><br /> </strong></span>

<span style="font-family: Consolas; font-size: 10pt;"><span style="color: red;"><br /> <strong><!&#8211;</strong></span> <security><br /> </span>

 <span style="font-family: Consolas; font-size: 10pt;"><authentication><br /> </span>

 <span style="font-family: Consolas; font-size: 10pt;"><windowsAuthentication enabled=&#8221;true&#8221; /><br /> </span>

 <span style="font-family: Consolas; font-size: 10pt;"><anonymousAuthentication enabled=&#8221;false&#8221; /><br /> </span>

 <span style="font-family: Consolas; font-size: 10pt;"></authentication><br /> </span>

 <span style="font-family: Consolas; font-size: 10pt;"></security> <span style="color: red;"><strong>&#8211;></strong><br /> </span></span>

 <span style="font-family: Consolas; font-size: 10pt;"><httpRedirect enabled=&#8221;true&#8221; /><br /> </span>

 <span style="font-family: Consolas; font-size: 10pt;"></system.webServer><br /> </span>

&nbsp;

<span style="font-size: 10pt;">Edit: <span style="color: #5b9bd5;"><strong>%SYSTEMROOT%\Web\RDWeb\Pages\en-us\Default.aspx</strong><br /> </span></span>

<span style="font-size: 10pt;">Change the following line to specify the private mode by default:<br /> </span>

<span style="font-family: Consolas; font-size: 10pt;">//<br /> // Page Variables<br /> //<br /> public string sHelpSourceServer, sLocalHelp, sRDCInstallUrl, strWorkspaceName;<br /> public Uri baseUrl, stylesheetUrl, renderFailCssUrl;<br /> <span style="color: red;"><strong>public bool bShowPublicCheckBox = false, bPrivateMode = true, bRTL = false;<br /> </strong></span></span>

&nbsp;

**3. Enable trusted rdp file signing for clients  
** 

<span style="font-size: 10pt;">Get the certificate thumbprint on your Connection Broker via Powershell<br /> </span>

<pre><span style="font-family: Consolas; font-size: 10pt;">Get-Childitem CERT:\LocalMachine\My </span></pre>

![](http://www.miru.ch/wp-content/uploads/2013/06/062413_2332_SingleSignO7.png) <span style="font-size: 10pt;"><br /> </span>

<span style="font-size: 10pt;">Copy the thumbprint of the corresponding certificate to clipboard<br /> </span>

<span style="font-size: 10pt;">Edit your clients GPO to include the thumbprint and to trust signed rdp files with this certificate<br /> </span>

![](http://www.miru.ch/wp-content/uploads/2013/06/062413_2332_SingleSignO8.png) <span style="font-size: 10pt;"><br /> </span>

&nbsp;

**3. Enable Smart Card Support  
** 

Enabling Smart Card Authentication is not a big deal as it has to be done only in the web tier. All other components support smart card based authentication by default.  
Again, keep in mind that Microsoft does not provide any kind of PIN pass-through component yet, as Citrix does. As the user reaches the endpoint (RD Session or VDI Desktop), an additional PIN prompt will appear.  
Additionally, if your CSP does not support global PIN caching, but only process based caching, the PIN has to be entered twice, first at RDWeb site, second at the RDP session layer.  
To enable Certificate based authentication on the RDWeb tier, complete the following on the IIS installation of your RDWeb Server:

![](http://www.miru.ch/wp-content/uploads/2013/06/062413_2332_SingleSignO9.png) 

<span style="font-size: 10pt;">Also ensure that if using a 3<sup>rd</sup> party CSP, it has to be installed on all tiers.<br /> </span>

&nbsp;

**Unsupported / non-working scenarios  
** 

  * <span style="font-size: 10pt;">SSO does not work if the clients are not domain joined<br /> </span>
  * <span style="font-size: 10pt;">Smart Card PIN pass-through is not supported for RD sessions<br /> </span>
  * <span style="font-size: 10pt;">Smart card SSO at RDWeb portal without a PIN prompt is only working when CSP (smart card middleware) supports global PIN caching. Most providers only support &#8220;per process&#8221; caching.</span>