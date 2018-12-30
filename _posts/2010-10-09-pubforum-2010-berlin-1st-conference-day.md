---
id: 263
title: 'PubForum 2010 Berlin &#8211; 1st conference day'
date: 2010-10-09T10:53:35+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=263
permalink: /pubforum-2010-berlin-1st-conference-day/
categories:
  - Virtualization
tags:
  - PubForum Citrix VDI AccessGateway NetScaler Profiles
---
Some known and also a lot uf unknown faces I see here at PubForum. Because of Synergy we&#8217;ll have a lot of Cirix people onsite, which will be a value add to the community I think.
  
After a short keynote from Alex Jushin the sessions start with the session of Markus Klein and Oliver Lomberg from Citrix Systems
  
The lates news from Synergy are brought to the commubnity.

**XenDesktop  5- the future**

  * Consolidated admin consoles
  
    &#8211; Desktop Director (operate the environment)
  
    &#8211; Desktop Studio (setup and configure the environment)
  * Tighter PS integration
  * One-click RemoteAssistance based on MS standard technology /not to say session shadowing)
  * Intelligent QoS (virtual channels can be optionally split up to different tcp streams, eg. different ports for printing and audio streams)
  * New print driver solution using UPD (seperates text and graphics within the EMF output stream and caches bitmap data on local client cache)
  * New WebInterface 5.4
  * XenClient /Type1 bare metal hypervisor to seperate also the local client desktop from it&#8217;s hardware)

**XenServer 5.6**
  
&#8211; fully integrated distributed virtual switch
  
&#8211; IntelliCache for storage optimization
  
&#8211; Delegated web-based management console

**OpenCloud**
  
-Bridge (transparency integration of public clouds)
  
-Apps /seamless SSO integration of public cloud apps)
  
-VMlogix (Orchestration of clouds)

**Session #2 Access Gateway &#8211; The Next Generation by Jay Tomlin**

Access Gateway 5 / VPX

  * Access Gateway VPX supports now vSphere
  * Appliance Failover with session synchronization
  * Multiple Logon Points
  
    &#8211; Basic (Replaces CSG/-> requires no user license
  
    &#8211; SmartAccess (Full PVN and other functions)
  * SmartGroups (Authentication / Authorization combinations, Policies)
  * Configuration Snapshots including go back and forward of AG versions
  * Multiple username authentication (where 2nd Factor username is different from AD user for example)
  * Clustering / Load Balancing via Access Controller Cluster
  * Extemded Endpoint Analysis Add-On from OPSWAT

Access Gateway Beyond 2011

  * Smart Card
  * Appliance Chaining
  * Smart Access for PNAgent like connections
  * Take the AG features and port it to NetScaler Kernel (run the same appliance core on Netscaler)

**Session #3 Visions about desktop agility &#8211; by Shawn Bass**

  * cloud computing ( we know that already &#8212; ASP, Hosting servers)
  * Security, don&#8217;t block, just ensure control and compliance
  * Be aware of App portfolio for win7 migrations
  * VDI &#8211; create a central desktop, provide apps across diff. datacenter with XenApp

**Session   #4 &#8211; Appsense User Virtualization from Appsense**

  * User installed apps
  
    &#8211; Isolated and virtualized from basic OS (kind of sandboxing user installed apps)
  
    &#8211; CTP version is working on an online base (installed apps are stored on a fileshare by the strata agent)

**Session #5 Helge Klein about profile basics, issues and solutions**

He outlines the pain in the a** topics we are all dealing with in a way everyone can follow, even if he doesn&#8217;t know anything about profiles (are there really some people out? .-) Here are some topics I&#8217;d like to outline&#8230;

  * Is a mandatory profile more secure?
  
    &#8211; not on a multiuser env like RDS or XenApp because you assign full access to the ntuser.man, and as soon the HKCU gets mapped at logon the user has access to other users hive to.. with full access 🙁
  * Can I use multiple profiles to a user (eg. for XenApp Silos)
  
    &#8211; yes you can deal with variables within the user profile path in AD
  
    &#8211; But.. should we do that? It makes sense in terms of plattform isolations or terminal server silos
  * Can / Should I use the same profile on 32 and 64 system?
  
    &#8211; yes, you can, but you shouldn&#8217;t because the profile could contain hard coded references to the program files directory
  * AppData redirection? Cool or pain in the a**?
  
    &#8211; Applications need to access the file server a lot of times
  
    &#8211; Apps could have issues with redirected appdata
  
    &#8211; Shawn Bass corrects my own comment regarding VDI environments, and he is absolutely right.
  
    Accesses to fileshare via SMB are causing much more load on the file server thn it would on a block level storage if C: drive is located on a shared storage
  * How to troubleshoot profiles?
  
    &#8211; userenv debug has gone since Vista :-(&#8230; so that&#8217;s a really big bull\****!

**Session #6 Aidan Finn about Win 7 Sp1 / 2008 R2 SP1 / Hyper-V / Dynamic Memory**

  * Dynamic Memory for Hyper-V
  
    Not an over commitment, but more or less the optimization of memeory allocation to virtual machines.  You can set startup and maximumamount for a VM. Also ballooning is supported as VMware does. &#8211;> Memory for the parent partition has to be reserved using a registry key to protect from memory overload of Vms with dynamic memory.

 

**Session #7 SMS Passcode**
  
(yes #6 I missed) while checking SEPAGO profile migratiuon tool 🙂

While we&#8217;re trying Sasha&#8217;s white russians&#8230;
  
Lars shows the functionality of SMS passcode on a Androis touchpad, very easy to use 2-factor authentication indeed.

Gus Pinto later on talks about the feature of receiver in terms of the iOS

After a greate dine @ the italian we had a cool networking night.