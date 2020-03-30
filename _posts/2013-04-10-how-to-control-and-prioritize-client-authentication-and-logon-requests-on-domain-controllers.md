---
id: 1042
title: How to control and prioritize Client authentication and logon requests on Domain Controllers
date: 2013-04-10T10:44:06+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1042
permalink: /how-to-control-and-prioritize-client-authentication-and-logon-requests-on-domain-controllers/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Active Directory
  - Microsoft
tags:
  - Active Directory
---
Each domain joined Windows client locates an appropriate Domain Controller using a component called &#8220;DCLocator&#8221; as part of the NETLOGON service.

Refer to the following [article](http://support.microsoft.com/kb/314861/en-us) for detailed information (strongly recommended)

Now the question arises, if there is a logic which domain controller responds first to queries from the DCLocator. Based on my experiences the following facts counts into play.<span style="font-size: 12pt;"><br /> </span>

  * The OS version of the DC (e.g. a 2012 DC responds generally before a 2003 DC)
  * The responsiveness (mainly based on HW resources)
  * The ordering within the DNS SRV records (\_ldap / \_gc)
  * **The priority and weight values for the SRV records  
** 

There are multiple scenarios where you might have to limit LDAP requests to one or more of your Domain Controllers. The following are the most common ones:

&nbsp;

  * DC holding the PDC Emulator is more loaded than other DCs (causing logon performance issues in large user environments)
  * Upgrading / Installing new Domain Controllers as part of an AD upgrade (causing logons to occur on the new DC by dwfault)

&nbsp;

The NETLOGON service has two parameters which control the response-behavior to LDAP requests: [**LdapSrvWeigth**](http://technet.microsoft.com/en-us/library/cc957291.aspx) and [**LdapSrvPriority**  ](http://technet.microsoft.com/en-us/library/cc957290.aspx)**  
** 

By default, each DC has a priority of 0 and a weight of 100. The weight can be used to prefer particular DCs with the same priority.

If the priority value is set, it takes precedence over the weight value.

  * **Domain controllers with the highest weight and the lowest priority are most likely to be contacted  
** 

#### <span style="font-family: Calibri;">To modify the behavior, just create two REG_DWORD values (LdapSrvWeight and LdapSrvPriority) under the Key:<br /> <span style="color: #5b9bd5;">HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\Netlogon\Parameters</span><br /> </span>

#### <span style="font-family: Calibri;">Choose a value between 0 and 65335<br /> </span>

  * #### <span style="font-family: Calibri;">Restart the NETLOGON Service, and watch the SRV records in DNS, they should be updated shortly with the new values.<br /> </span>

#### <span style="font-family: Calibri;">The following table illustrates a configuration example<br /> </span>

<div>
  <table style="border-collapse: collapse;" border="0">
    <colgroup> <col style="width: 75px;" /> <col style="width: 76px;" /> <col style="width: 132px;" /> <col style="width: 132px;" /></colgroup> <tr style="background: #dbdbdb;">
      <td style="padding-left: 7px; padding-right: 7px; border: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">Name</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">Priority</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">Weight</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">Effective Order</span>
        </h4>
      </td>
    </tr>
    
    <tr>
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">DC1</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">10</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">50</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">2</span>
        </h4>
      </td>
    </tr>
    
    <tr>
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">DC2</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">10</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">100</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">1</span>
        </h4>
      </td>
    </tr>
    
    <tr>
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">DC3</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;">100</span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <h4>
          <span style="font-family: Calibri;"></span>
        </h4>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
      </td>
    </tr>
  </table>
</div>

#### <span style="font-family: Calibri;">Additional resources<br /> </span>

How DNS Support for Active Directory Works <http://technet.microsoft.com/en-us/library/cc759550.aspx>