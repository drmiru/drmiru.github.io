---
id: 1094
title: How to execute 64-Bit Powershell Scripts in System Center Orchestrator
date: 2013-05-26T13:45:18+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1094
permalink: /how-to-execute-64-bit-powershell-scripts-in-system-center-orchestrator/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Microsoft
  - Powershell
  - System Center
tags:
  - Orchestrator
  - Powershell
  - SCVMM
---
I came along a weird issue when trying to automate a SCVMM Hyper-V Bare metal Deployment using System Center Orchestrator 2012 SP1. As the System Center Integration Pack for SCVMM is focused on virtual machine automation tasks, I had to create my own Powershell scripts.

<span style="font-size: 12pt; text-decoration: underline;"><strong>The Difficulty<br /> </strong></span>

System Center Orchestrator is built in x86 architecture (yes.. still the 2012 SP1 version I&#8217;m using on a Server 2012). Therefore the built-in Script execution action executes the PoSh code in x86 Powershell environment. This is not very useful for x64 based management modules or snapIns like SCVMM, Exchange 2010/2013 or SCCM. Trying to execute a SCVMM CMDLET like in the example here:

![](http://www.miru.ch/wp-content/uploads/2013/05/052613_1142_Howtoexecut1.png) 

..ends with an Error (CMDLET is unknown)

![](http://www.miru.ch/wp-content/uploads/2013/05/052613_1142_Howtoexecut2.png) 

This happens because SCVMM PoSh modules are not available in x86 Powershell environments.

<span style="font-size: 12pt; text-decoration: underline;"><strong>The Workarounds<br /> </strong></span>

There are basically three different workarounds I&#8217;m currently aware of. If you know other or better ones please let me know.

  1. **Use Run Program Action  
** 

![](http://www.miru.ch/wp-content/uploads/2013/05/052613_1142_Howtoexecut3.png) 

<p style="margin-left: 36pt;">
  <ol>
    <li>
      <strong>Run the code inside a x64-Bit Powershell environment<br /> </strong>
    </li>
  </ol>
  
  <p>
    <img src="http://www.miru.ch/wp-content/uploads/2013/05/052613_1142_Howtoexecut4.png" alt="" />
  </p>
  
  <p>
    &nbsp;
  </p>
  
  <ol>
    <li>
      <strong>Use ExecutePS Integration Pack 1.1 from Charlesjoy on <a href="http://orchestrator.codeplex.com/releases/view/76101">CodePlex</a></strong><br /> <a href="http://orchestrator.codeplex.com/releases/view/76101">http://orchestrator.codeplex.com/releases/view/76101</a>
    </li>
  </ol>
  
  <p>
    &nbsp;
  </p>
  
  <p>
    <strong>Which one would I choose?</strong>
  </p>
  
  <p>
    Well for simple tasks I&#8217;d choose option 2 because it doesn&#8217;t require additional IP installation and knowledge. For more sophisticated tasks I&#8217;ll take the ExecutePS IP which is the Ferrari IMHO to execute Powershell code inside System Center Orchestrator.
  </p>