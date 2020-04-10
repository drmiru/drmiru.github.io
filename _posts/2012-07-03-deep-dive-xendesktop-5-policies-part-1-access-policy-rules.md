---
id: 811
title: 'Deep Dive &#8211; XenDesktop 5 Policies: Part 1 &#8211; Access Policy Rules'
date: 2012-07-03T23:52:42+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=811
permalink: /deep-dive-xendesktop-5-policies-part-1-access-policy-rules/
aktt_notify_twitter:
  - 'no'
categories:
  - Citrix
  - Desktop Virtualization
  - Virtualization
  - XenDesktop
tags:
  - Access Policy Rules
  - Powershell
  - XenDesktop
  - XenDesktop 5
---
XenDesktop 5.x provides very flexible and fine grained access policy rules to control resource access to broker desktops or applications. This post covers the basics behind the functionalities and provides a lot of examples for advanced configuration of Access Policy Rules. Advanced rules have to be configured with Powershell, so get your PS console ready!

<span style="font-size: 12pt;"><strong>Background</strong></span>  
XenDesktop access policies define the rules used to control access of users to Desktop / Application resources in the site. Access checks are based on the attributes of users&#8217; connections from their devices to the XenDesktop site. The access policy comprises a set of rules while each rule contains a set of connection filters, resource filters, and access right grants.

[<img class="alignleft size-medium wp-image-835" title="AccessPolicyRules" src="http://www.miru.ch/wp-content/uploads/2012/07/AccessPolicyRules-273x300.jpg" alt="" width="385" height="422" srcset="http://www.miru.ch/wp-content/uploads/2012/07/AccessPolicyRules-273x300.jpg 273w, http://www.miru.ch/wp-content/uploads/2012/07/AccessPolicyRules-933x1024.jpg 933w, http://www.miru.ch/wp-content/uploads/2012/07/AccessPolicyRules.jpg 1405w" sizes="(max-width: 385px) 100vw, 385px" />](http://www.miru.ch/wp-content/uploads/2012/07/AccessPolicyRules.jpg)

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

&nbsp;

As a general rule keep in mind that if include filters are enabled at least one rule must apply. Exclude filters take precedence even if an include rule matches. Also the users must have an active entitlement to the desktop group.

<span style="font-size: 12pt;"><strong><br /> </strong></span>**Connection Filters**  
Connection filters always have an include and exclude form that can be individually enabled or disabled. By default, include filters are enabled and empty, and exclude filters are disabled. For a rule to be considered when access policies are evaluated, at least one connection include filter must be enabled.

**Resource Filters**  
Resource filters except application filters have an include and exclude form that can be individually enabled or disabled. The application filter has only an include form. By default, include filters are empty and enabled, and exclude filter are disabled. For an access policy rule to expose any resources, it must have at least one include filter that is both enabled and non-empty. **Smart Access Filter Tags** Each filter tag is a combination of a AG vServer and the applied access policy. Smart Access Filters work with AGEE  and AG STD in combination with an Access controller.

<span style="font-size: 12pt;"><strong> </strong></span>**Using the GUI within XenDesktop Studio**  
The GUI is very limited and can only be used to configure basic rules as (any connection / all connections through access gateway / specific connections through access gateway)

**<span style="font-size: 12pt;">Using Powershell</span>  
** Be aware that, after modifying an Access Policy Rule with Powershell, the policy can&#8217;t be edited anymore using the GUI.

![](http://www.miru.ch/wp-content/uploads/2012/07/APR-GUI-error-300x47.jpg) 

By default, each Desktop Group gets two Access Policy Rules, when the Desktop Group is created. Let&#8217;s say the Desktop Group is named VDI\_WIN7, the following Policies will be created automatically: VDI\_WIN7\_Direct (policy for all direct connections) VDI\_WIN7_AG (policy for all connections via Access Gateway) You can either edit these policies or unassign them from the Desktop Group, which I would prefer to have a fall back to the default settings if you crack up the settings via Powershell.  
Here are the steps to create and assign two new custom policies to a paricular Desktop Group named VDI_WIN7

<span style="text-decoration: underline;">1. Display current Access Policies</span>

<pre>Get-BrokerAccessPolicyRule | ? {$_.Name -match 'VDI_WIN7'}</pre>

<span style="text-decoration: underline;">2. Unassign current Access Policies</span>

<pre>Get-BrokerAccessPolicyRule | ? {$_.Name -match 'VDI_WIN7'} | Set-BrokerAccessPolicyRule -IncludedDesktopGroups @()</pre>

<span style="text-decoration: underline;">3. Create two new access policies, one for direct access and another for AG access, differentiate Access by AD Groups</span> <span style="text-decoration: underline;"></span> <span style="text-decoration: underline;"></span>

<pre>$dg = Get-BrokerDesktopGroup VDI_WIN7<span style="font-family: Consolas;">
New-BrokerAccessPolicyRule -Name "LAB VDI User Remote" -IncludedUserFilterEnabled $true -IncludedDesktopGroupFilterEnabled $true –IncludedUsers "MIRULAB\GS-CAG-VDI-User" -IncludedDesktopGroups $dg -AllowedProtocols 'HDX' -AllowedConnections ViaAG</span><span style="font-family: Consolas;">
New-BrokerAccessPolicyRule -Name "LAB VDI User Internal" -IncludedUserFilterEnabled $true -IncludedDesktopGroupFilterEnabled $true –IncludedUsers "MIRULAB\GS-VDI-User" -IncludedDesktopGroups $dg -AllowedProtocols 'HDX' -AllowedConnections NotViaAG
</span></pre>

<span style="font-size: 12pt;"><strong>Other examples<br /> </strong></span>

<span style="text-decoration: underline;">Modify an existing Access Policy to allow any user to access the &#8220;VDI_WIN7&#8221; Desktop Group via AGEE as long as he accesses via vServer &#8220;cag.domain.com&#8221; with applied session policy &#8220;session-policy-lab&#8221;</span>

<pre>New-BrokerAccessPolicyRule -Name "LAB VDI User Remote" -AllowedUsers AnyAuthenticated -IncludedSmartAccessFiltersEnabled $true -IncludedSmartAccessTags "cag.domain.com:session-policy-lab"</pre>

<span style="text-decoration: underline;">Mofify an existing Access Policy to allow a specific user group to access the VDI_WIN7 Desktop Group via direct connection as long as he accesses from Client Subnet &#8220;10.2.2.0/24&#8221;</span>**  
** 

<pre><span style="font-family: Consolas;">Set-BrokerAccessPolicyRule "LAB VDI User" -IncludedUsers "MIRULAB\GS-CAG-Thin-Access" -IncludedClientIPFilterEnabled $true -IncludedClientIPs "10.2.1.0/24"
</span></pre>

<span style="text-decoration: underline;">Add another Desktop Group to an existing Access Policy</span>**  
** 

<pre><span style="font-family: Consolas;">$dg = Get-DesktopGroup VDI_XP_Pooled
Set-BrokerAccessPolicyRule "LAB VDI User" –AddIncludedDesktopGroups $dg</span><strong>
</strong></pre>

**Exporting / Importing Access Policy Rules**  
You can easily export and re-import Access Policy Rules with Powershell which can also save your day by creating a backup before manipulating the objects.

<span style="text-decoration: underline;">Exporting all Access Policy Rules</span> <span style="font-family: Consolas;"></span>

<pre><span style="font-family: Consolas;">Get-BrokerAccessPolicyRule | Export-Clixml C:\Backup\AccessPolicyRules.xml
</span></pre>

<span style="text-decoration: underline;">Importing Access Policy Rules from a previous backup</span> <span style="font-family: Consolas;"></span>

<pre><span style="font-family: Consolas;">Get-BrokerAccessPolicyRule | Remove-BrokerAccessPolicyRule
Import-Clixml C:\Backup\AccessPolicyRules.xml | New-BrokerAccessPolicyRule
</span></pre>

**Complete CMDLET documentation**

<a href="http://support.citrix.com/static/kc/CTX127254/help/Get-BrokerAccessPolicyRule.html" target="_blank">Get-BrokerAccessPolicyRule</a><a href="http://support.citrix.com/static/kc/CTX127254/help/Set-BrokerAccessPolicyRule.html" target="_blank"></a>

<a href="http://support.citrix.com/static/kc/CTX127254/help/Set-BrokerAccessPolicyRule.html" target="_blank">Set-BrokerAccessPolicyRule</a>

<a href="http://support.citrix.com/static/kc/CTX127254/help/New-BrokerAccessPolicyRule.html" target="_blank">New-BrokerAccessPolicyRule</a>

<a href="http://support.citrix.com/static/kc/CTX127254/help/Remove-BrokerAccessPolicyRule.html" target="_blank">Remove-BrokerAccessPolicyRule</a>

<a href="http://support.citrix.com/static/kc/CTX127254/help/Rename-BrokerAccessPolicyRule.html" target="_blank">Rename-BrokerAccessPolicyRule</a>