---
id: 726
title: Smart Card PIN pass-through not working with Windows 7 client on XenApp
date: 2012-03-27T15:16:30+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=726
permalink: /smart-card-pin-pass-through-not-working-with-windows-7-client-on-xenapp/
aktt_notify_twitter:
  - 'no'
categories:
  - Citrix
  - Security
  - Smart Card
  
  - XenApp
tags:
  - PIN pass-through
  - Smart Card
  - Windows 7
  - XenApp
---
**Issue description  
** 

While fighting with the Citrix Single Sign On Service (SSONSVR.exe) on Windows 7 using Smart Card based login I had a deeper look at the architecture of SSONSVR service. For those which are not familiar with this component of the Receiver / Online Plugin, it&#8217;s basically a Citrix client component which caches the current logon credentials on a Windows based endpoint to enable the pass-through logon feature of XenApp. If a user logs on with username / password, SSONSVR caches the credentials and passes them over to the XenApp server resp. to the WI or WI service site.  
Now, if a user logs on to his Windows 7 client using Smart Card interactive logon with PIN, SSONSVR.exe is not invoked and therefore PIN pass through to a XenApp host is not working. Users will receive a RDS logon screen where they have to click on their user account and enter the PIN, which is of course very annoying.

SSONSVR is implemented as a &#8220;Network Provider&#8221; and called by Winlogon via NPLogonNotify function**. <span style="color: #35383d; font-family: Arial; font-size: 9pt;">Upon a smart card logon the NPLogonNotify process is simply not invoked by Winlogon.exe anymore due to an architecture change of Winlogon within Vista / Windows 7 / 2008 R2.<br /> </span>**For more info about that see: [<span style="font-family: Arial; font-size: 9pt;">http://msdn.microsoft.com/en-us/library/bb905527.aspx</span>](http://msdn.microsoft.com/en-us/library/bb905527.aspx)<span style="color: #35383d;"><span style="font-family: Arial; font-size: 9pt;"> and </span><a href="http://support.citrix.com/article/CTX131223">http://support.citrix.com/article/CTX131223</a></span>

<span style="color: #35383d; font-family: Arial; font-size: 9pt;"><strong>Resolution<br /> </strong></span>

<span style="color: #35383d;"><span style="font-family: Arial; font-size: 9pt;">Add the following Registry key to your Windows 7 clients:<br /> </span><span style="color: #4f81bd;">Key: HKLM\Software\Microsoft\WindowsNT\CurrentVersion\Winlogon\Notify<br /> Value Name: SmartCardLogonNotify<br /> Value Type: REG_DWORD<br /> Value Data: 0x1<br /> </span></span>

<span style="color: #35383d; font-family: Arial; font-size: 9pt;">Logoff and log on using your smart card / PIN, and voilà, here is ssonsvr.exe or ssonsvr.exe*32 process running.<br /> To generally enable SSON and Smart Card PIN Pass Through a few more steps are required.<br /> </span>

  * <div>
      <span style="color: #35383d; font-family: Arial; font-size: 9pt;">Configure Receiver ADM Policy<br /> </span>
    </div>
    
      * <span style="color: #35383d; font-family: Arial; font-size: 9pt;">Enable local user name and password<br /> </span>
      * <span style="color: #35383d; font-family: Arial; font-size: 9pt;">Allow Smart Card Authentication<br /> </span>
      * <span style="color: #35383d; font-family: Arial; font-size: 9pt;">Enable pass-through authentication for PIN<br /> </span>
  * <span style="color: #35383d; font-family: Arial; font-size: 9pt;">Enable Smart Card or Smart Card with pass-through authentication on your WI site<br /> </span>
  * <span style="color: #35383d; font-family: Arial; font-size: 9pt;">Enable &#8220;Trust XML Service requests&#8221; on the XenApp policy<br /> </span>
  * <span style="color: #35383d; font-family: Arial; font-size: 9pt;">Install required Smart Card drivers and middleware components on the XenApp hosts if using a third party one.<br /> </span>

<span style="color: #35383d; font-family: Arial; font-size: 9pt;"><strong>Notice about PIN caching on Windows 7<br /> </strong>The Microsoft provided CSP does not support global PIN caching, only a &#8220;per process&#8221; PIN caching. This means, at the time Receiver starts up and each time Receiver or PNAgent is calling a published resource, the CSP asks for the PIN. This behavior is by design and to get rid of that, you have to use a third-party CSP / Middleware like ActivIdentity, which supports global PIN caching.</span>