---
id: 3868
title: Guidance for a SCVMM / Hyper-V deployment in a locked down, multi-forest environment
date: 2015-03-24T22:18:39+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=3868
permalink: /guidance-for-a-scvmm-hyper-v-deployment-in-a-locked-down-multi-forest-environment/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Cloud and Datacenter Management
  - Hyper-V
  - Kerberos
  - Security
  - System Center
  
tags:
  - Active Directory
  - Cross-Forest
  - SCVMM
  - Security
  - System Center
---
This is a &#8220;lessons learned&#8221; post and a follow up to an earlier post on &#8220;[SCVMM in multi-forest environments](http://www.miru.ch/scvmm-in-multi-forest-environments/)&#8221; to keep others away from &#8220;trial and error&#8221; when integrating SCVMM with Hyper-V in a secured environment. So what does &#8220;secured&#8221; and &#8220;locked down&#8221; mean in this context? Let me first describe the environment and use case a bit. The options to change the architecture below where limited due to customer&#8217;s internal security regulations.

So you might ask, why don&#8217;t you just enable &#8220;forest wide&#8221; authentication and why don&#8217;t you place VMM into the same network security zone as the Hyper-V hosts? Well humm.. just because I was not allowed to <span style="font-family: Wingdings;">J</span>. So I searched for documentation on this particular scenario **-> fail**.

Multi-forest in general is documented, but not the specific trust authentication mode. Additionally the [required ports for SCVMM documentation](https://technet.microsoft.com/en-us/library/gg710871.aspx) is not 100% complete. Enough said… let&#8217;s see how to get this thing up and running.<span style="font-size: 12pt;"><br /> </span>

### Environment

![](../images/2015/03/032415_2016_Guidancefor1.png) 

### 

&nbsp;

  * SCVMM in Forest A (Service Forest)
  * Hyper-V Nodes in Forest B (Fabric Forest)
  * Two-Way Trust with &#8220;selective authentication&#8221;
  * SCVMM and Hyper-V Hosts in different VLANs, segregated by Layer 7 Firewall

### Selective forest authentication in a nutshell

Enabling this option on the forest trust properties requires any user / group in the trusting forest to be granted explicitly on computer objects with the &#8220;allowed to authenticate&#8221; permission on the trusted forest.  
For detailed information on that see the following TechNet documentation: <https://technet.microsoft.com/en-us/library/cc755844%28v=ws.10%29.aspx>

### Issues to solve

<div>
  <table style="border-collapse: collapse;" border="0">
    <colgroup> <col style="width: 28px;" /> <col style="width: 356px;" /> <col style="width: 312px;" /></colgroup> <tr style="background: #00b0f0;">
      <td style="padding-left: 7px; padding-right: 7px; border: solid 0.5pt;">
        <span style="color: white; font-size: 10pt;"><strong> #</strong></span>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <span style="color: white; font-size: 10pt;"><strong>Issue</strong></span>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <span style="color: white; font-size: 10pt;"><strong>Solution</strong></span>
      </td>
    </tr>
    
    <tr>
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <span style="font-size: 10pt;">1</span>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <span style="font-size: 10pt;">Unable to verify RunAs credentials account for Host Management with error: ID 405<br /> </span></p> 
        
        <p>
          <img alt="" src="../images/2015/03/032415_2016_Guidancefor2.png" /></td> 
          
          <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
            <span style="font-size: 10pt;">Grant the RunAs account from Forest B &#8220;allowed to authenticate&#8221; permission on the SCVMM computer object in Forest A</span>
          </td></tr> 
          
          <tr>
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              <span style="font-size: 10pt;">2</span>
            </td>
            
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              <span style="font-size: 10pt;">Unable to add Hyper-V Hosts and Clusters with error:<br /> </span></p> 
              
              <p>
                &nbsp;
              </p>
              
              <p>
                <span style="color: #c00000;"><em>Unable to connect via WMI. Hostname: hv001.forestA.local<br /> Server &#8216;hv001.forestA.local&#8217; does not exist or access denied. Check firewall settings.<br /> The RPC server is unavailable. (Exception from HRESULT: 0x800706BA)</em></span></td> 
                
                <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                  <span style="font-size: 10pt;">The TechNet documentation on &#8220;<a href="https://technet.microsoft.com/en-us/library/gg710871.aspx">required ports for SCVMM</a>&#8221; does mention the following ports for SCVMM Agent installation:<br /> </span></p> 
                  
                  <p>
                    <span style="font-size: 10pt;">TCP 445<br /> </span>
                  </p>
                  
                  <p>
                    <span style="font-size: 10pt;">TCP 135<br /> </span>
                  </p>
                  
                  <p>
                    <span style="font-size: 10pt;">TCP 139<br /> </span>
                  </p>
                  
                  <p>
                    <span style="font-size: 10pt;">TCP 80<br /> </span>
                  </p>
                  
                  <p>
                    &nbsp;
                  </p>
                  
                  <p>
                    <span style="font-size: 10pt;">Assuming that no firewall admin in the world will allow RPC dynamic ports >1024 from SCVMM to all Hyper-V nodes. we have <a href="https://support.microsoft.com/en-us/kb/154596">to fix the RPC port range</a>. In this example I used: 51000-51500, and request the firewall rule accordingly<br /> </span>
                  </p>
                  
                  <p>
                    &nbsp;
                  </p>
                  
                  <p>
                    <span style="color: #ffc000; font-size: 10pt;"><strong>Note! <span style="color: black;">The endpoint mapper (tcp 135) and the RPC port range must also be opened to any CNO (Cluster named Object) resp. to it&#8217;s IP address<span style="color: #ffc000;"><br /> </span></span></strong></span></td> </tr> 
                    
                    <tr>
                      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                        <span style="font-size: 10pt;">3</span>
                      </td>
                      
                      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                        <span style="font-size: 10pt;">Unable to add Hyper-V Hosts and Clusters with error:<br /> </span></p> 
                        
                        <p>
                          <img alt="" src="../images/2015/03/032415_2016_Guidancefor3.png" /></td> 
                          
                          <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                            <ul>
                              <li>
                                <span style="font-size: 10pt;">Grant the SCVMM Service account from Forest A &#8220;allowed to authenticate&#8221; permission on all Hyper-V hosts in Forest B<br /> </span>
                              </li>
                              <li>
                                <span style="font-size: 10pt;">Grant the SCVMM Service account from Forest A &#8220;read&#8221; permission in Forest B<br /> </span>
                              </li>
                              <li>
                                <span style="font-size: 10pt;">Allow LDAP/LDAPs connection from SCVMM in Forest A to all Domain Controllers in<br /> Forest B<br /> </span>
                              </li>
                              <li>
                                <span style="font-size: 10pt;">Ensure the selected RunAs Account has local Administrator permissions on all Hyper-V hosts<br /> </span>
                              </li>
                              <li>
                                <span style="font-size: 10pt;">Ensure name resolution works between both forests</span>
                              </li>
                            </ul>
                          </td></tr> 
                          
                          <tr>
                            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                              <span style="font-size: 10pt;">4</span>
                            </td>
                            
                            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                              <span style="font-size: 10pt;">Cluster Service suddenly stops on certain nodes after some days of operation. Trying to restart the service results in error:<br /> </span></p> 
                              
                              <p>
                                <span style="color: #c00000;"><em>RPC Endpoint mapper / no endpoint available</em></span></td> 
                                
                                <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                                  <span style="font-size: 10pt;">Increase the RPC port range to a minimum of 200 ports. Cluster-intercommunication also requires RPC ports. Depending on other management tasks and interfaces the documented minimum range of 100 ports can become too small.</span>
                                </td></tr> </tbody> </table> </div> 
                                
                                <p>
                                  &nbsp;
                                </p>
                                
                                <h3>
                                  In short you need to…
                                </h3>
                                
                                <ul>
                                  <li>
                                    Configure all Hyper-V Hosts to use a fixed range for RPC Ports starting somewhere from > 5000 (depending on the existing ports in the firewall ruleset)
                                  </li>
                                  <li>
                                    Configure the firewall ruleset accordingly
                                  </li>
                                  <li>
                                    Grant the Host Management RunAs account from Forest B &#8220;allowed to authenticate&#8221; permission on the SCVMM computer object in Forest A
                                  </li>
                                  <li>
                                    Grant the SCVMM Service account from Forest A &#8220;allowed to authenticate&#8221; permission on all Hyper-V hosts and CNOs in Forest B
                                  </li>
                                  <li>
                                    Grant the SCVMM Service account from Forest A &#8220;read&#8221; permission in Forest B
                                  </li>
                                  <li>
                                    Allow LDAP/LDAPs connection from SCVMM in Forest A to all Domain Controllers in<br /> Forest B
                                  </li>
                                  <li>
                                    Ensure the selected Host Management RunAs Account has local Administrator permissions on all Hyper-V hosts
                                  </li>
                                  <li>
                                    Ensure name resolution works between both forests
                                  </li>
                                </ul>
                                
                                <p>
                                  &nbsp;
                                </p>
                                
                                <p>
                                  Hope this helps and saves some nerves…. Mine are gone for today 😉
                                </p>
                                
                                <p>
                                  &nbsp;
                                </p>