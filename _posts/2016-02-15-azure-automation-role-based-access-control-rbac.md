---
id: 4551
title: Azure Automation Role based Access Control (RBAC)
date: 2016-02-15T23:20:09+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4551
permalink: /azure-automation-role-based-access-control-rbac/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Azure
  - Azure Automation
  - Cloud and Datacenter Management
tags:
  - Automation
  - Azure
  - RBAC
---
<span style="font-family: Calibri Light; font-size: 12pt;">Since December 24<sup>th</sup>, Azure Automation supports RBAC. We really asked Microsoft to implement this enhancement, as lots of sensitive information and code may live within an Azure Automation account. Assets are used to store credentials, variables, connection objects. As you might have more and more contributors to the system, you probably don&#8217;t want to share all your credential objects to anyone writing automation runbooks. Before RBAC, the only way was to create multiple Automation accounts, but of course this introduced other difficulties.<br /> </span>

<span style="font-family: Calibri Light; font-size: 12pt;">Now, here&#8217;s where you access the RBAC component.<br /> </span>

![](../content/images/2015/12/Automation-RBAC-start-300x241.png) <span style="font-family: Times New Roman; font-size: 12pt;"><br /> </span>

![](../content/images/2015/12/Automation-RBAC-users-300x223.png) <span style="font-family: Times New Roman; font-size: 12pt;"><br /> </span>

<span style="font-family: Calibri Light; font-size: 12pt;">We have 5 different roles, to which we can assign users.   <em><br /> </em></span>

<div>
  <table style="border-collapse: collapse;" border="0">
    <colgroup> <col style="width: 172px;" /> <col style="width: 200px;" /> <col style="width: 2px;" /> <col style="width: 141px;" /> <col style="width: 98px;" /> <col style="width: 0px;" /></colgroup> <tr style="height: 2px; background: #00b0f0;">
      <td style="border-top: solid 0.5pt; border-left: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" rowspan="2" valign="middle">
        <span style="color: white; font-family: Calibri Light; font-size: 12pt;">Role</span>
      </td>
      
      <td style="border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: none; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="color: white; font-family: Calibri Light; font-size: 12pt;">Permissions</span>
      </td>
      
      <td style="border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; padding: 1px;" colspan="4">
      </td>
    </tr>
    
    <tr style="height: 18px;">
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;"> Runbooks</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: none; padding: 1px;">
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Resources</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Roles / Access</span>
      </td>
    </tr>
    
    <tr style="height: 18px;">
      <td style="border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Owner</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;"> Edit</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: none; padding: 1px;">
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Edit</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Edit</span>
      </td>
    </tr>
    
    <tr style="height: 18px;">
      <td style="border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Contributor</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;"> Edit</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: none; padding: 1px;">
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Use within Runbooks</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">&#8212;</span>
      </td>
    </tr>
    
    <tr style="height: 18px;">
      <td style="border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Reader</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;"> Read</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: none; padding: 1px;">
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;"> Read</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;"> Read</span>
      </td>
    </tr>
    
    <tr style="height: 18px;">
      <td style="border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Automation Operator</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;"> start / stop /suspend / resume</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: none; padding: 1px;">
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;"> &#8212;</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;"> &#8212;</span>
      </td>
    </tr>
    
    <tr style="height: 18px;">
      <td style="border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">User Access Administrator</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">&#8212;</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: none; padding: 1px;">
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">&#8212;</span>
      </td>
      
      <td style="border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt; padding: 1px 7px 1px 7px;" valign="middle">
        <span style="font-family: Calibri Light; font-size: 12pt;">Edit</span>
      </td>
    </tr>
  </table>
</div>

<span style="font-family: Calibri Light; font-size: 12pt;">Key component behind the scenes is of course ARM (<a href="https://azure.microsoft.com/en-us/documentation/articles/resource-group-overview/">Azure Resource Manager</a>).<br /> </span>

<span style="font-family: Calibri Light; font-size: 12pt;">Although you can&#8217;t define different access levels on single objects, you have the possibility to delegate Asset-Management to a security department, while still be able to have more Runbook authors contributing to your automation solutions.<br /> </span>

<span style="font-family: Calibri Light; font-size: 12pt;"><br /> </span>