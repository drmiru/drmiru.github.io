---
id: 570
title: Installing Hyper-V on Server Core 2008 R2, a journey into the abyss of CLI administration
date: 2011-11-06T21:43:55+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=570
permalink: /installing-hyper-v-on-server-core-2008-r2-a-journey-into-the-abyss-of-cli-administration/
aktt_notify_twitter:
  - 'no'
categories:
  - Hyper-V
  - Microsoft
  - Powershell
  
tags:
  - Hyper-V
  - Powershell
  - Server Core
---
This week i decided to take myself into a Hyper-V installation running on a Server 2008 R2 SP1 Core. To share my experience and to conserve my own knowledge I&#8217;d like to share my personal experiences in this blog post.

# The start over

For each of you who haven&#8217;t seen a core server after initial image deploy, I&#8217;ve captured the scree after changing the admin password.

<img src="../images/2011/06/060611_1944_InstallingH13.png" alt="" width="510" height="384" /> 

## Configuring Networking

List interfaces and note down the IDX numbers

<span style="font-family: Consolas;"><span style="background-color: silver;">Netsh interface ipv4 show interfaces</span><br /> </span>

Set the IP addess for the management interface

<span style="font-family: Consolas; background-color: silver;">Netsh interface ipv4 set address name=&#8221;21&#8243; address=10.2.1.140 mask=255.255.255.0 gateway=10.2.1.1<br /> </span>

Set the DNS Servers

<span style="font-family: Consolas;"><span style="background-color: silver;">Netsh interface ipv4 add dnsserver name=&#8221;21&#8243; address=10.2.1.160 index=1<br /> Netsh interface ipv4 add dnsserver name=&#8221;21&#8243; address=10.2.1.161 index=2</span><br /> </span>

Rename the host

<span style="font-family: Consolas; background-color: silver;">Netdom renamecomputer localhost /NewName:SHYPVC01<br /> </span>

Join the host to a domain

<span style="font-family: Consolas; background-color: silver;">Netdom join localhost /miru.lab /userD:<username> /passwordD:<password><br /> </span>

Enable remote management

<span style="font-family: Consolas; background-color: silver;">Cscript \windows\system32\scregedit.wsf /ar 0<br /> </span>

<span style="font-family: Consolas; background-color: silver;">Netsh advfirewall set currentprofile settings remotemanagement enable<br /> </span>

Install Powershell

<span style="font-family: Consolas; background-color: silver;">start /w ocsetup NetFx2-ServerCore<br /> </span>

<span style="font-family: Consolas; background-color: silver;">start /w ocsetup MicrosoftWindowsPowerShell<br /> </span>

Enable Powershell Remoting

Start powershell from C:\windows\system32\windowspowershell\v1.0\

<span style="font-family: Consolas; background-color: silver;">Enable-PSRemoting<br /> </span>

Install Hyper-V Role

<span style="font-family: Consolas; background-color: silver;">start /w ocsetup Microsoft-Hyper-V<br /> </span>

So from now on, you&#8217;ll be much more comfortable using SCVMM and all the stuff of RSAT to remotely manage your core host. Server core requires you less patching and the possibility of getting rid of curious windows admins thinking &#8220;ehh. let&#8217;s have a look what that server does&#8221; and logging in just for fun by clicking around fancy GUIs. Me personally I think the core installation will become more important with Server 8, because there are dozens of new modules and thousands of new CMDLETS making it way better to manage a core instance via PowerShell.

More details about how to manage a Server core instance in a later article.

&nbsp;

&nbsp;

&nbsp;