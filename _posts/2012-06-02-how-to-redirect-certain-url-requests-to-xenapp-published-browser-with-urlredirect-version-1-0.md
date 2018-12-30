---
id: 791
title: 'How to redirect certain URL requests to XenApp published browser with &#8220;URLRedirect&#8221; version 1.0'
date: 2012-06-02T12:10:01+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=791
permalink: /how-to-redirect-certain-url-requests-to-xenapp-published-browser-with-urlredirect-version-1-0/
aktt_notify_twitter:
  - 'yes'
aktt_tweeted:
  - "1"
categories:
  - Application Virtualization
  - Citrix
  - Powershell
  - Scripting
  - Virtualization
  - XenApp
tags:
  - Published Application
  - Receiver
  - Redirect
  - URL
  - XenApp
---
**Starting point**

I recently had a customer asking me how to configure client to server content redirection on XenApp 6.5. After listening to his requirements I noticed that plain client to server redirection will not fit all the reqs. He requested that if users click on URLs or links within documents or applications, the request should be forwarded to a published browser on a XenApp farm. A filter based on URLs should be available to decide whether a URL request will be served by the published browser rather than with the local browser. The solution should work with both current Citrix Receiver types (Native and Enterprise PNagent).

**The Solution**

I created a small solution called &#8220;URLRedirect&#8221; which does exactly that. The solution consists of the following components:

  * A reg file which creates a new custom application named <span style="color: #0000ff;">CTX.HTTP</span> type in HKEY\_CLASSES\_ROOT
  * two reg files which set the default program for opening http/https requests to <span style="color: #0000ff;">CTX.HTTP</span> application
  * A powershell script which contains the main intelligence to launch the URL either via local or published browser
  * A text file to include / exclude URLs

&nbsp;

**Installation / Configuration**

As this is version 1.0, there are still some things to do manually :-), I promise that I&#8217;ll create an installer / uninstaller  some day

  * Download URLRedirect <a title="URLRedirect download" href="http://www.miru.ch/downloads/URLRedirect.zip" target="_blank">HERE</a>
  * Extract the zip to a folder on the client of your choice (if you choose **<span style="color: #008000;">C:\Scripts\URLRedirect</span>** there is not much customization for you)
  * Edit the <span style="color: #008000;"><strong>URLRedirect.ps1</strong></span> Powershell script to fit your needs (static constants)
  * <a title="how-to-bulk-unblock-files-in-windows-7" href="http://nicholasrogoff.wordpress.com/2010/09/01/how-to-bulk-unblock-files-in-windows-7-or-server-2008/" target="_blank">Unblock</a> the Powershell script and set the PS <a href="http://technet.microsoft.com/en-us/library/dd347628.aspx" target="_blank">execution policy</a> to &#8220;RemoteSigned&#8221;, as a more secure alternative you can also <a href="http://www.hanselman.com/blog/SigningPowerShellScripts.aspx" target="_blank">sign</a> the Script.
  * Edit the **<span style="color: #008000;">HKCR_CTX.HTTP.reg</span>** file (adapt the path to the powershell script  where you extracted it)
  * Edit the <span style="color: #008000;"><strong>urls.txt</strong></span> to either define which URLs should be included or excluded from being redirected (depending on what you set for $filtermode in the PS script)
  * Apply the **<span style="color: #008000;">HKCR_CTX.HTTP.reg</span>** as an administrative user
  * Apply **<span style="color: #008000;">HKCU_shell_http.reg</span>** and **<span style="color: #008000;">HKCU_shell_https.reg</span>** to all users using any profile management or scripting solution

**Disclaimer**

The solution has been successfully tested with Receiver 3.2 and ReveiverEnterprise 3.2 on Windows 7 together with XenApp 6.5 and Web Interface 5.4, but it may also work for lower versions. Currently it does NOT work with PNAgent together with Citrix StoreFront Services.
  
The solution is provided as is without any warranty or support. You use it at your own risk.

**Bug Reporting**

If you notice any bugs or if you have nice ideas or feature requests, tweet me @DRMIRU, or write me an email to: michael(at)miru.ch

**
  
**