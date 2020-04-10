---
id: 873
title: Debugging a hanging process
date: 2012-10-30T13:58:42+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=873
permalink: /debugging-a-hanging-process/
aktt_notify_twitter:
  - 'no'
categories:
  - Citrix
  - Debugging
  - Virtualization
tags:
  - Debug
  - hang
  - XenApp
---
In this article I wanted to cover a step by step documentation on &#8220;how to debug hung processes&#8221; using debugging tools for Windows.

<span style="font-size: 14pt; text-decoration: underline;"><strong>The problem case<br /> </strong></span>

Customer reported frozen or hanging sessions on XenApp 6.5 hosts, which seemed to occur randomly. The only thing which still worked was taskmgr. If we watched the other user processes, all seemed to be responsive as normal, but in fact they weren&#8217;t. I then asked the customer to try out which process might force the session to enter a hanging state by just closing one user session process after the other to find the point where the session starts to respond again as normal. After a short period of trial & error he found out that the explorer process itself seemed to be the root cause. I then asked him to create a process dump by just using the task manager (right click on the process | create dump file). With previous operating systems had to use Sysinternals procdump or ADPlus from Microsoft to create a process dump file. I have to admit that in lots of cases you might require a full memory dump to analyze complex hangs or deadlocks, but in this case I just tried the easy way first.

<span style="text-decoration: underline;"><strong>Starting the debugging session<br /> </strong></span>

  * If you haven&#8217;t done yet you should download and install Debugging tools for Windows here
  * Run Windbg, make sure you run the x64 version if the process dumped was a 64bit process
  * <div>
      Set the symbol server path, so debugger can download the required symbols
    </div>
    
      * Go to File <span style="font-family: Wingdings;">à</span> Symbol File path and enter the Citrix and Microsoft symbol servers, choose a local path for caching the symbols
      * SRV\*C:\Debug\Symbols\*http://ctxsym.citrix.com/symbols;SRV\*C:\Debug\Symbols\*http://msdl.microsoft.com/download/symbols
  * Go to File <span style="font-family: Wingdings;">à</span> Open Crash dump and select the process dump file you created recently
  * Wait until the debugger is connected and you see the command prompt: 0:000>

&nbsp;

<span style="text-decoration: underline;"><strong>Analyzing the stuff<br /> </strong></span>

Type: <span style="font-family: Consolas; font-size: 12pt; background-color: lightgrey;"><strong>!analyze –hang</strong></span>  
This is the quick and dirty way to find a possible root cause for a process / thread hang or deadlock

Type: <span style="font-family: Consolas; font-size: 12pt; background-color: lightgrey;"><strong>!locks</strong></span>  
This command shows critical sections in the process tree and suspicious threads currently holding locks. In my case I got the following output:

CritSec sechost!SccCritsec+0 at <span style="color: red;"><strong>000007fefe299378</strong></span>  
WaiterWoken No  
LockCount 4  
RecursionCount 1  
OwningThread <span style="color: red;"><strong>3918</strong></span>  
EntryCount 0  
ContentionCount 26b  
\*** Locked

The interesting parts of this output are the memory section marked red and the thread owning the lock(s) marked purple.

Note: Sometimes the !locks command seems not to work as expected, so you won&#8217;t get any useful results. You can also use the following command to capture potential locks:

**<span style="background-color: silver;">!cs -l</span>  
** 

Know I wanted to know what thread **3918** was exactly doing at this time.

<span style="color: #385623;">Type:</span>  
<span style="font-family: Consolas; font-size: 12pt; background-color: lightgrey;"><strong>~</strong></span>  
This shows up all threads with their unique ID and thread number.  
As we see here the thread owning the lock 3918 has ID 12.

0 Id: 465c.b780 Suspend: 0 Teb: 000007ff\`fffde000 Unfrozen

1 Id: 465c.8738 Suspend: 0 Teb: 000007ff\`fffdc000 Unfrozen  
2 Id: 465c.aba0 Suspend: 0 Teb: 000007ff\`fffd3000 Unfrozen  
3 Id: 465c.3e58 Suspend: 0 Teb: 000007ff\`fffae000 Unfrozen  
4 Id: 465c.5a9c Suspend: 0 Teb: 000007ff\`fffac000 Unfrozen  
5 Id: 465c.4044 Suspend: 0 Teb: 000007ff\`fffaa000 Unfrozend  
6 Id: 465c.5634 Suspend: 0 Teb: 000007ff\`fff9a000 Unfrozen  
7 Id: 465c.34d0 Suspend: 0 Teb: 000007ff\`fffa2000 Unfrozen  
8 Id: 465c.2ec4 Suspend: 0 Teb: 000007ff\`fff90000 Unfrozen  
9 Id: 465c.adfc Suspend: 0 Teb: 000007ff\`fff8e000 Unfrozen  
10 Id: 465c.5888 Suspend: 0 Teb: 000007ff\`fff8c000 Unfrozen  
11 Id: 465c.b014 Suspend: 0 Teb: 000007ff\`fff8a000 Unfrozen  
<span style="color: red;"><strong> 12 Id: 465c.3918 Suspend: 0 Teb: 000007ff`fff86000 Unfrozen</strong><br /> </span> 13 Id: 465c.8d68 Suspend: 0 Teb: 000007ff\`fff84000 Unfrozen  
14 Id: 465c.6f20 Suspend: 0 Teb: 000007ff\`fff82000 Unfrozen  
15 Id: 465c.32cc Suspend: 0 Teb: 000007ff\`fff80000 Unfrozen  
16 Id: 465c.5250 Suspend: 0 Teb: 000007ff\`fff7e000 Unfrozen  
17 Id: 465c.1860 Suspend: 0 Teb: 000007ff\`fff9e000 Unfrozen  
18 Id: 465c.b05c Suspend: 0 Teb: 000007ff\`fffd9000 Unfrozen  
19 Id: 465c.5244 Suspend: 0 Teb: 000007ff\`fffd7000 Unfrozen  
20 Id: 465c.242c Suspend: 0 Teb: 000007ff\`fffd5000 Unfrozen  
21 Id: 465c.755c Suspend: 0 Teb: 000007ff\`fffa8000 Unfrozen  
22 Id: 465c.af88 Suspend: 0 Teb: 000007ff\`fffa6000 Unfrozen

&nbsp;

Now we dig into the stack trace of this particular thread.

<span style="color: #385623;">Type:</span><span style="font-family: Consolas; font-size: 12pt; background-color: lightgrey;"><strong>~12 kv</strong></span>  
This generates a back trace for thread ID 12 which is our suspicious thread 3918.

Child-SP RetAddr : Args to Child : Call Siteput

<img src="http://www.miru.ch/wp-content/uploads/2012/11/103012_1158_Debuggingah12.png" alt="" align="left" /> <span style="font-size: 9pt;">00000000`05c0f158 000007fe`fd9e10dc : c0000022`c0000005 00000000`c0000141 00000000`05c0f340 00000000`0504491c : ntdll!NtWaitForSingleObject+0xa<br /> 00000000`05c0f160 000007fe`fe28ce95 : 00000000`ffffffff 000007fe`fe28445c 00000000`00000000 00000000`000007f8 : KERNELBASE!WaitForSingleObjectEx+0x79<br /> 00000000`05c0f200 000007fe`fe28c7ee : 00000000`07c58960 00000000`000007f8 00000000`00000000 00000000`00000000 : sechost!ScGetNotifyResults+0xc9<br /> 00000000`05c0f2d0 000007fe`fe28c736 : 00000000`07c58960 00000000`00000001 00000000`00000000 00000000`00000000 : sechost!ScCommonNotifyCallback+0x86<br /> 00000000`05c0f330 00000000`774e112b : 00000000`07c58960 00000000`00000000 00000000`00000000 000007fe`fe28c6e4 : sechost!ScApcNotifyCallback+0x52<br /> <span style="color: red;"><strong>00000000`05c0f360 00000000`774e18ca : 000007fe`fd9e1430 00000000`00003918 fe28c6e4`00000009 00000000`000007fe : ntdll!KiUserApcDispatcher+0x2b (TrapFrame @ 00000000`05c0f6c8)</strong></span><br /> 00000000`05c0f858 000007fe`fd9e1430 : 00000000`00003918 fe28c6e4`00000009 00000000`000007fe 07c58960`00000000 : ntdll!NtWaitForMultipleObjects+0xa<br /> 00000000`05c0f860 00000000`77392ce3 : 00000000`05c0f998 00000000`05c0f990 00000000`00000000 00000000`00000001 : KERNELBASE!WaitForMultipleObjectsEx+0xe8<br /> 00000000`05c0f960 00000000`77128f7d : 00000000`05c0fa20 00000000`ffffffff 00000000`00000000 00000000`000007e4 : kernel32!WaitForMultipleObjectsExImplementation+0xb3<br /> 00000000`05c0f9f0 00000000`771262b2 : 00000000`00000001 00000000`0012a608 00000000`00000000 00000000`0012a5a0 : user32!RealMsgWaitForMultipleObjectsEx+0x12a<br /> 00000000`05c0fa90 000007fe`fb6535be : 00000000`00000001 00000000`0012a608 00000000`00000000 00000000`00000000 : user32!MsgWaitForMultipleObjectsEx+0x46<br /> <span style="color: red;"><strong>00000000`05c0fad0 00000000`7738652d : 00000000`00000000 00000000`00000000 00000000`00000000 00000000`00000000 : MMDevAPI!CDeviceEnumerator::PnpNotificationThread+0x3d8<br /> </strong></span>00000000`05c0fc20 00000000`774bc521 : 00000000`00000000 00000000`00000000 00000000`00000000 00000000`00000000 : kernel32!BaseThreadInitThunk+0xd<br /> 00000000`05c0fc50 00000000`00000000 : 00000000`00000000 00000000`00000000 00000000`00000000 00000000`00000000 : ntdll!RtlUserThreadStart+0x1d<br /> </span>

This output showed me that the thread is waiting for an answer right after the MMDevAPI call, which it never gets. The final status is **NtWaitForSingleObject** initiated by the User mode dispatcher which had an exception while waiting for the MMDevAPI.

<span style="text-decoration: underline;"><strong>So what?<br /> </strong></span>

The function**  
<span style="color: red;">CDeviceEnumerator </span>**belongs to the API MMDevAPI which is a multimedia / audio API from Microsoft. So something calls the function to enumerate audio devices and the API is not able to return the results or a final exception. I thought that this might be the root cause.

I asked the customer if he has changed something either on client side or with XA audio redirection. He confirmed a new type of thin client which is audio capable and they enabled the audio redirection on XenApp recently.  
After disabling the audio redirection the issue did not occur any more. So we were able to narrow down the issue to have its root cause in the audio stack.

&nbsp;

<span style="text-decoration: underline;"><strong>Additional resources:<br /> </strong></span>

Windbg command reference: <http://windbg.info/doc/1-common-cmds.html>

THE reference for dump analysis: <http://www.dumpanalysis.org>

&nbsp;

  * In a next post I&#8217;ll cover a full memory dump analysis to find root causes for deadlocks and BSODs.