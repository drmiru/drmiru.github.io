---
id: 179
title: How to implement a file based locking mechanism within a powershell script
date: 2009-11-21T14:53:54+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=179
permalink: /how-to-implement-a-file-based-locking-mechanism-within-a-powershell-script/
categories:
  - Powershell
---
The following code enables a file based locking mechanism within a powershell script. It checks for existance of a lock file and checks if a running process exists, which is capturing this file. This can be used in situations, where a script is beeing launched paralell by another script or programm, but you want to have your script executed exclusively and serialized. 

<span style="font-family:Times New Roman; font-size:12pt">The following code enables a file based locking mechanism within a powershell script. It checks for existance of a lock file and checks if a running process exists, which is capturing this file. This can be used in situations, where a script is beeing launched parallel by another script or programm, but you want to have your script executed exclusively and serialized.<br /> </span>

<span style="font-family:Times New Roman; font-size:12pt"><br /></span><span style="color:#92d050; font-family:Courier New; font-size:10pt">#################################################<br />#Scriptname:     checklock.ps1<br />#Author:         Michael Rüefli (www.miru.ch)<br />#Date:           21.11.2009<br />#Description:    The code uses a file locking mechanism to ensure that the<br />#                script beeing started is running exclusively. Mostly used where<br />#                a powershell script is started parallel within a few seconds<br />#<br />#Usage:          Paste your code in the MAIN section and set the $lockfile paramater to match your needs<br />############################################<br /> </span>

<span style="color:#c4bc96; font-family:Courier New; font-size:10pt">$lockfile = &#8220;d:\lock.lck&#8221;<br />$lockstatus = 0<br />While ($lockstatus -ne 1)<br />{<br />If (Test-Path $lockfile)<br />{<br />echo &#8220;Lock file found!&#8221;<br />$pidlist = Get-content $lockfile<br />If (!$pidlist)<br />{<br />$PID | Out-File $lockfile<br />$lockstatus = 1<br />}<br />$currentproclist = Get-Process | ? { $_.id -match $pidlist }<br />If ($currentproclist)<br />{<br />echo &#8220;lockfile in use by other process!&#8221;<br />$rndwait = New-Object system.Random<br />$rndwait= $rndwait.next(1,4)<br />echo &#8220;Sleeping for $rndwait seconds&#8221;<br />Start-Sleep $rndwait<br />}<br />Else<br />{<br /> </span>

<span style="color:#c4bc96; font-family:Courier New; font-size:10pt">Remove-Item $lockfile -Force<br />$PID | Out-File $lockfile<br />$lockstatus = 1<br /> </span>

<span style="color:#c4bc96; font-family:Courier New; font-size:10pt">}<br />}<br />Else<br />{<br />$PID | Out-File $lockfile<br />$lockstatus = 1<br />}<br />}<br /> </span>

<span style="color:#c4bc96; font-family:Courier New; font-size:10pt">#———————————————————————————————–<br />## Main Script Part<br />## Here you can paste your code, in fact what the script has to do<br />echo &#8220;Hi, it seems that no other script is doing the same as me now.. so I can do my job exclusively&#8221;<br /> </span>

<span style="color:#c4bc96; font-family:Courier New; font-size:10pt">## End of Main Part<br />#———————————————————————————————–<br />#remove the lockfile<br />Remove-Item $lockfile –Force<br /> </span>

 

[<img src="http://www.miru.ch/wp-content/uploads/2010/01/112109-1453-howtoimplem1.jpg" alt="" border="0" />](http://images.google.ch/imgres?imgurl=http://www.gnr8.biz/images/blue_lock%2520main.jpg&imgrefurl=http://www.gnr8.biz/product_info.php%3Fproducts_id%3D304&usg=__zSEp805411nm-VqQyBYugBeRgwE=&h=432&w=432&sz=49&hl=de&start=2&tbnid=FZ87K5zi-ppJzM:&tbnh=126&tbnw=126&prev=/images%3Fq%3Dlock%26gbv%3D2%26hl%3Dde)<span style="color:#c4bc96; font-family:Courier New; font-size:10pt"><br /> </span>