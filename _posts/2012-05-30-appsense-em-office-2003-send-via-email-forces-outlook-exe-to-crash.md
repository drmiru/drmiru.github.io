---
id: 785
title: 'Appsense EM / Office 2003 &#8211; &#8220;send via email&#8221; forces Outlook.exe to crash'
date: 2012-05-30T09:23:49+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=785
permalink: /appsense-em-office-2003-send-via-email-forces-outlook-exe-to-crash/
aktt_notify_twitter:
  - 'no'
categories:
  - Appsense
tags:
  - Appsense
  - Office
  - Outlook
  - Personalization
---
Assuming that you followed Appsense <a href="http://www.miru.ch/wp-content/uploads/2012/05/Office-Personalization.docx" target="_blank">best practice for Office</a> personalization Settings, you might still have an issue with Office (Outlook) when using the &#8220;send via email&#8221; function out of 3rd party applications like (Acrobat Reader, IE, etc.)

To address that issue, add the following key for each user on the logon trigger (so it exists outside of the Office PM bubble).

[HKEY\_CURRENT\_USER\Software\Microsoft\Office\11.0\Outlook]  
&#8220;LastUILanguage&#8221;=dword:00000407

The value can be any valid language.