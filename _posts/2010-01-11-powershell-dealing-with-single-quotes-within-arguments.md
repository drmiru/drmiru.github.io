---
id: 190
title: 'Powershell: Dealing with single quotes within arguments'
date: 2010-01-11T22:08:42+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=190
permalink: /powershell-dealing-with-single-quotes-within-arguments/
categories:
  - Powershell
  - Scripting
---
A run in a very special issue when I tried to pass arguments to a powershell script if one of the arguments contained single quotes within the argument string like &#8220;this is damn &#8216;quote&#8217; inside the arg!!&#8221; Your powershell script would then think that these are separated arguments. You won&#8217;t get the hell out of this sh*t. One possible solution is to place the &#8220;wracky&#8221; argument at the end, in fact use is as last argument. Install the <a title="Powershell Community Extensions" href="http://pscx.codeplex.com/Release/ProjectReleases.aspx?ReleaseId=1615" target="_blank">Powershell Community Extensions </a>and use the **join-string** cmdlet. Here&#8217;s an example.

<span style="color:#92d050; font-family:Courier New"># save this code as a ps script and use two arguments to pass to the script.<br /> # use single quotes within the 2nd argument, eg.    &#8220;This is the &#8216;2nd&#8217; Argument&#8221;<br /> </span>

<span style="color:#ddd9c3; font-family:Courier New">$user=$args[0]<br /> $msg=@()<br /> $i = 1 for ( $i ; $i -lt $args.count; $i++ )<br /> {<br /> $msg += $args[$i]<br /> }<em><br /> </em></span>

<span style="color:#ddd9c3; font-family:Courier New">$msg = ($msg | join-string -separator &#8221; &#8220;)<br /> write-host &#8216;The Script Arguments where now: &#8220;&#8216;$user'&#8221; &#8220;&#8216;$msg'&#8221;&#8216;<br /> </span>

![](http://www.miru.ch/wp-content/uploads/2010/01/011110-2123-powershelld11.jpg)