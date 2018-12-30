---
id: 659
title: How to create a Windows 7 Default Profile
date: 2011-09-21T21:11:54+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=659
permalink: /how-to-create-a-windows-7-default-profile/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
bfa_virtual_template:
  - hierarchy
categories:
  - Virtualization
---
Leaving the &#8220;Default&#8221; user profile as it is can hurt your VDI / RDS deployment, because Windows needs certain amount of time to create a new user profile from the initial default one. The Default has not been initialized, meaning e.g. the HKCU hive is not generated, active setup has not initialized components.

You can gain up to 25 seconds less for logons (depending on other parameters too of course)
  
There are several ways to create a new Default user profile on a Windows 7 client,

The most popular one is the official way from Microsoft documented here: <http://support.microsoft.com/kb/973289/en-us>

BUT&#8230;Personally I think that&#8217;s a bit too complex, we just want to copy another users profile to the default one or not? Here is another way which worked several times for me.

  1. Create a local user, log on with this user and make your changes to the profile as you have to
  2. Log off and log on with an administrator account (you may need to reboot first as sometimes the user&#8217;s hive is not completely unloaded at logoff)
  3. Open the registry editor and change the following value to match your model users profile
  
    **HKEY\_LOCAL\_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\Default**
  
    (Change %SystemDrive%\Users\**Default** to %SystemDrive%\Users\**yourlocaluser**)&nbsp;</p> 
    &#8211;> **If you want to Replace the original Default Profile, continue with step 4. Otherwise you&#8217;re already done.**</li> 
    
      * Open Computer Advanced Properties and navigate to &#8220;User Profiles&#8221;. Sekect  the **Default** profile and choose &#8220;Copy to&#8221;. Select %SystemDrive%\Users\**Default** as the destination and select &#8220;Everyone&#8221; as the permitted user to use the profile
      * Change the registry value back to %SystemDrive%\Users\**Default**
      * Open Regedit and load the ntuser.dat hive of the copied profile
      * Remove all entries referencing to &#8220;yourlocaluser&#8221; in the hive
      * Unload the hive</ol> 
    
    Be aware that this method is not officially supported by Microsoft. However I&#8217;ve used it a couple of times without running into any issues.
    
    &nbsp;
    
    &nbsp;
    
    &nbsp;
    
    &nbsp;