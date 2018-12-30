---
id: 999
title: Why UE-V is not ready yet for volatile VDI envionments
date: 2013-04-01T20:50:56+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=999
permalink: /why-ue-v-is-not-ready-yet-for-volatile-vdi-envionments/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Microsoft
  - User Virtualization
tags:
  - UE-V
  - User Profiles
---
&nbsp;

**UPDATE! Read the article about my new tool**: <a href="http://www.miru.ch/2013/06/ue-v-logoff-helper-tool/" target="_blank">UE-V Logoff Helper</a>

In a recent [post](http://www.miru.ch/2012/11/microsoft-uev-1-0-uncovered-part-one/) I blogged about UE-V, it&#8217;s architecture and functions. As the first productive deployments have started I came along a weird logoff behavior.

Together with MS Premier Support and the developers we where able to narrow down the issue to be a &#8220;as by design&#8221; behavior, which makes it useless IMHO for volatile VDI environments. When I say volatile, I mean VDI desktops or session based desktops where user changes are not persistent, because of mandatory profiles and/or pooled desktops being reset at user logoff.

UE-V does not honor running applications while a user logoff is initiated. In fact UE-V does not save back settings of running applications to the central settings location on the file share. The injected dll and the agent take care of syncing the changes locally. So next time the application is started and stopped, settings are saved back to the central store again. This is of course useless for volatile environments.

While  I still see UE-V as a cool alternative to traditional roaming profiles, I have to stop all my planned UE-V projects and wait for a version 1.1 to fix this behavior. In the meanwhile I continue to push Appsense as the favorite solution.