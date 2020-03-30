---
id: 960
title: How to update group membership without logoff / logon /restart
date: 2012-12-04T01:15:24+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=960
permalink: /how-to-update-group-membership-without-logoff-logon-restart/
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
tags:
  - Kerberos
---
This might be very useful for certain situations where you want to update a user&#8217;s or computer&#8217;s group membership without the need to re-logon / restart. The whole magic is behind the issued kerberos tickets after you logged on to a machine or a machine has been started. The tool &#8220;klist.exe&#8221; cannot only be used for troubleshooting to display the current issued TGT / TGS, it is also capable to purge all current tickets. The purge command results in a re-issuance of the tickets, as soon as the next auth or service request is taking place. Keep in mind that this method only works for services which authenticate via Kerberos. NTLM based authentication still requires a fresh logon with updated group membership token.

**<span style="text-decoration: underline;">To purge a user&#8217;s tickets:</span>**

<pre>klist purge</pre>

**<span style="text-decoration: underline;">To purge tickets of the local system account:</span>**  
Start a cmd or PoSH session with elevated privileges

<pre>klist -li 0:0x3e7 purge</pre>

klist is a tool that has been included by default since Vista/Server 2008.

If you have a Windows 2003 Server / XP then you&#8217;re required to download klist here:  
<http://www.microsoft.com/downloads/details.aspx?familyid=9d467a69-57ff-4ae7-96ee-b18c4790cffd&displaylang=en>

Be aware then the 2003/XP version of klist does not support purging directly the system accoun&#8217;s tickets. You can use psexec from sysinternals to launch an interactive command line as the system account (psexec -s -i cmd.exe) and then execute klist purge)

To generally control the lifetime of Kerberos tickets consider the following article.

<http://technet.microsoft.com/en-us/library/Dd277401.aspx>

&nbsp;

&nbsp;

&nbsp;