---
id: 580
title: 'XenClient: unlock a locked VM'
date: 2011-06-13T14:38:56+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=580
permalink: /xenclient-unlock-a-locked-vm/
aktt_notify_twitter:
  - 'no'
categories:
  - Citrix
  - Virtualization
tags:
  - XenClient locked VM
---
If a XenClient VM which is registered to a synchronizer couln&#8217;t contact the synchronizer for a while it will be marked as &#8220;locked&#8221; on the XenClient. The default lease time for &#8220;offline&#8221; VMs is 14 days.

The only way is to manually override the state using the following procedure.

Open a XenClient shell by pressing Ctrl+Shift+T

<pre>xec-vm</pre>

<pre>UUID=`xec-vm --name &lt;locked_vm_name&gt; get uuid`</pre>

<pre>db-write /vm/${UUID}/backend/state/operation READY</pre>

<pre>Press Ctrl + q to refresh Citrix Receiver for XenClient</pre>

The lease time can be configured while assigning  a image to a user within the synchronizer management interface