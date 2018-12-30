---
id: 254
title: 'VDI &#8230; and the most common errors and misunderstandings'
date: 2010-09-29T22:16:30+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=254
permalink: /vdi-and-the-most-common-errors-and-misunderstandings/
categories:
  - Desktop Virtualization
tags:
  - VDI XenDesktop
---
Designing and deploying a virtual desktop environment can be a pai\* in the a\** if you do not watch carefully the most common keypoints, errors and misunderstandings.

  * A virtual desktop is not cheaper than a physical one if you calculate the investments
  * You still need to manage your endpoint even if it&#8217;s a thin client. If you plan to use thin clients, keep an eye on the central management at least.
  * Because you have to deliver the desktop via one of the well known remote display protocols (RDP, ICA, PCoIP) you also have to deal with common usability issues as with traditional server based computing. If your recent SBC project did fail, you have a good chance that your VDI project will fail too. So check the reasons why SBC dit not succeed first.
  * A virtual desktop is not generally faster than a local workstation only because it runs on high performant server hardware
  * Carefully check your requirements (why do we need to deploy virtual desktops, what are the exact use cases, who are the stakeholders of such a project)
  * Check if you have ressource intensive applications and identify the business criticality
  * watch the skills of your administrators
  
    keep in mind that your admins have to deal with a couple  of different technologies such as:
  
    &#8211; server / client hypervisors
  
    &#8211; network and storage design / scaling
  
    &#8211; application delivery / virtualization / streaming
  
    &#8211; user environment and profile management
  
    You could think, ok, no big deal, we had these topics since we are born.. but trust me in a VDI environment, everything is a kind of different.
  * choose the right VDI broker product which suits your requirements read the ultimate comparison matrix <a href="http://www.miru.ch/?p=240" target="_self">here</a>
  * deploy a profile management solution
  * deploy a application virtualization solution (tradidional SW deployment will kill your nervs in a pooled VDI environment)
  * Don&#8217;t calculate with a base image for all users of 1v CPU and 512MB RAM, come on, your users aren&#8217;t just starting wordpad and adobe reader in real life, or are they?
  * Take your time and optimize your &#8220;golden image&#8221;
  * If you are using XenDesktop just check these docs. They cover a lot of knowledge to keep in mind before you click the install button.
  
    <a href="https://community.citrix.com/kits/#/kit/1067009" target="_blank">https://community.citrix.com/kits/#/kit/1067009</a>
  
    <a href="http://support.citrix.com/article/CTX126190" target="_blank">http://support.citrix.com/article/CTX126190</a>
  
    <a href="http://community.citrix.com/display/ocb/2010/09/21/Desktop+Virtualization+From+the+User+Perspective" target="_blank">http://community.citrix.com/display/ocb/2010/09/21/Desktop+Virtualization+From+the+User+Perspective</a>
  
    <a href="https://www1.gotomeeting.com/register/625937032" target="_blank">https://www1.gotomeeting.com/register/625937032</a>

If you do not agree with these findings feel free to comment.

Michael