---
id: 713
title: Redirect AGEE VPN users to a different Web Interface
date: 2011-12-17T00:16:48+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=713
permalink: /redirect-agee-vpn-users-to-a-different-web-interface/
aktt_notify_twitter:
  - 'no'
categories:
  - Citrix
  - Netscaler
  
tags:
  - Netscaler redirect web interface
---
<div>
  This post describes how to use the Netscaler responder feature to redirect users to a different Web Interface Site on the same web server if they are connected via VPN (AG plugin).
</div>

<div>
  <strong>Case description / Problem</strong>
</div>

<div>
  You have multiple Web Interface sites on your internal web servers which are load balanced using Netscaler. Users are connecting using a VPN Smart Access Portal on your AGEE. You want to prevent users from changing the Web Interface URL from eg. <a href="https://wi.corp.com/Citrix/internalWI">https://wi.corp.com/Citrix/internalWI</a> to <a href="https://wi.corp.com/Citrix/internalWI">https://wi.corp.com/Citrix/AdminWI</a> while connected via a non-clientless AGEE policy.
</div>

<div>
  <strong>Solution Description</strong>
</div>

  * Create a responder action and a responder policy to redirect requests always to /internalWI if request is coming from AGEE.
  * Bind the policy to the virtual server which is load balancing your WI servers

<div>
  <strong>Commands on the internal Netscaler</strong>
</div>

<div>
  <span style="font-size: small;"> </span>
</div>

<pre>add responder action "redirect_to_internal_wi" redirect "\"https://wi.corp.com/internalWI\"" -bypassSafetyCheck YES</pre>

<pre>add responder policy pol-redirect-wi "HTTP.REQ.URL.CONTAINS(\"/adminWI\") && CLIENT.IP.SRC.EQ(10.10.1.95)" "redirect_to_internal_wi"</pre>

<pre>bind lb vserver wi_vserver -policyName pol-redirect-wi -priority 100 -gotoPriorityExpression END</pre>

<div>
  Use the AGEE&#8217;s SNIP or MIP as the source address on the policy expression filter.
</div>