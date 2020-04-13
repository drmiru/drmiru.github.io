---
id: 1009
title: How the Kerberos token size can affect WinRM and other Kerberos based services
date: 2013-04-02T21:42:26+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1009
permalink: /how-the-kerberos-token-size-can-affect-winrm-and-other-kerberos-based-services/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
bfa_virtual_template:
  - hierarchy
categories:
  - Kerberos
  - Microsoft
  - Security
  - Smart Card
tags:
  - Kerberos
  - Token size
  - WinRM
---
![](../images/2013/04/040213_1942_HowtheKerbe1.jpg)

It&#8217;s like a little but really mean ghost which seems to follow me over all my IT career, it&#8217;s called &#8220;kerberos token&#8221;. I&#8217;ve debugged a lot of kerberos errors the last years, but the overall evergreen is a parameter called &#8220;MaxTokenSize&#8221;. Depending on the Windows OS version it has a default value of 12&#8217;000 bytes(WinXP,Vista,Win7) and 48&#8217;000 bytes (Win8). The kerberos token size grows depending on the following facts:

  * Amount of direct and indirect Domain group memberships
  * Whether user has or has not a SID history and if so, the number of entries
  * Authentication method (username/password or multifactor like Smart Cards)
  * User is enabled for Kerberos delegation

You can &#8220;calculate&#8221; or in other, better words, &#8220;estimate&#8221; the kerberos token size with the following formula. (Quoted from: [KB327825](http://support.microsoft.com/kb/327825/en-us))

<span style="color: black; font-size: 10pt;"><em>TokenSize = 1200 + 40d + 8s</em><br /> </span>

<span style="color: black; font-size: 10pt;"><em>This formula uses the following values:</em><br /> </span>

  * <span style="color: black; font-size: 10pt;"><em>d: The number of domain local groups a user is a member of plus the number of universal groups outside the user&#8217;s account domain that the user is a member of plus the number of groups represented in security ID (SID) history.</em><br /> </span>
  * <span style="color: black; font-size: 10pt;"><em>s: The number of security global groups that a user is a member of plus the number of universal groups in a user&#8217;s account domain that the user is a member of.</em><br /> </span>
  * <span style="color: black; font-size: 10pt;"><em>1200: The estimated value for ticket overhead. This value can vary, depending on factors such as DNS domain name length, client name, and other factors.</em><br /> </span>

<span style="font-size: 14pt;"><strong>Things that may occur, if the size exceeds the OS defined values: </strong><br /> </span>

  * Domain Join does not work (&#8220;<span style="color: red;">not enough storage to perform this operation</span>&#8220;, and other possible weird error messages)
  * Kerberos delegation fails
  * Access and single sign on to kerberos enabled services fails and NTLM fall back is showing a logon pop up box
  * WinRM / Remote Powershell sessions fail to establish with the following error: &#8220;<span style="color: red;">The WinRM client cannot process the request. It cannot determine the content type of the HTTP response from the destination computer</span>&#8220;

<span style="font-size: 12pt;"> </span><span style="font-size: 14pt;"><strong>How to increase the default values?</strong></span>

(Requires a reboot after modification)  
The supported max value is 65335. However you should consider methods to reduce the token size, rather than use the max value everywhere.

**<span style="text-decoration: underline;">For basic services</span>  
** Add the following Registry Key to all your Windows clients and servers

<pre>New-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\LSA\Kerberos\Parameters -Name "MaxTokenSize" -Value 65335 -PropertyType "DWORD"</pre>

**<span style="text-decoration: underline;">For WinRM / Remote Powershell and other HTTP based services</span>  
** Add the following Registry value to all your servers

<pre>New-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Services\HTTP\Parameters -Name "MaxFieldLength" -Value 65335 -PropertyType "DWORD" New-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Services\HTTP\Parameters -Name "MaxRequestBytes" -Value 40000 -PropertyType "DWORD"</pre>

&nbsp;

<span style="font-size: 12pt;"><strong>Other useful resources:</strong></span>

<span style="font-size: 10pt;">(Quoted from ShaneC33&#8217;s Blog)<br /> </span>

<span style="font-size: 10pt;">How to use Group Policy to add the MaxTokenSize registry entry to multiple computers<br /> </span>

[<span style="color: blue; font-size: 10pt; text-decoration: underline;">http://support.microsoft.com/kb/938118/EN-US</span>](http://support.microsoft.com/kb/938118/EN-US)<span style="font-size: 10pt;"><br /> </span>

<span style="font-size: 10pt;">New resolution for problems with Kerberos authentication when user belongs to many groups<br /> <a href="http://support.microsoft.com/kb/327825"><span style="color: blue; text-decoration: underline;">http://support.microsoft.com/kb/327825</span></a><br /> </span>

<span style="font-size: 10pt;">&#8220;HTTP 400 &#8211; Bad Request (Request Header too long)&#8221; error in Internet Information Services (IIS)<br /> <a href="http://support.microsoft.com/default.aspx?scid=kb;EN-US;2020943"><span style="color: blue; text-decoration: underline;">http://support.microsoft.com/default.aspx?scid=kb;EN-US;2020943</span></a><br /> </span>

<span style="font-size: 10pt;">Users who are members of more than 1,015 groups may fail logon authentication<br /> <a href="http://support.microsoft.com/kb/328889/"><span style="color: blue; text-decoration: underline;">http://support.microsoft.com/kb/328889/</span></a><br /> </span>

<span style="font-size: 10pt;">Group Policy may not be applied to users belonging to many groups<br /> <a href="http://support.microsoft.com/kb/263693/"><span style="color: blue; text-decoration: underline;">http://support.microsoft.com/kb/263693/</span></a><br /> </span>

&nbsp;

<span style="font-size: 12pt;"><br /> </span>