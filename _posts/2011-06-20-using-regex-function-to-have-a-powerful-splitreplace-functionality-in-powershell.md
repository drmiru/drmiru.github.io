---
id: 600
title: Using regex function to have a powerful split/replace functionality in Powershell
date: 2011-06-20T22:52:11+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=600
permalink: /using-regex-function-to-have-a-powerful-splitreplace-functionality-in-powershell/
aktt_notify_twitter:
  - 'no'
categories:
  - Powershell
  - Scripting
  
tags:
  - Powershell Split Replace RegEx
---
Powershell provides easy replace and split method to manipulate text/string values. They are easy to use but&#8230;if you want to replace content only on subsequent positions or split a string using a pattern containing more than a single character, you&#8217;ll fail.

Let&#8217;s take two examples.  
Replace the last IP digit

<pre>$a = 'The IP Adress with the number 100 is: 192.168.1.100'
$a.replace('100','101')</pre>

if you just had to replace the IP digit but not the text before, you have a problem.

The same for split.

<pre>$a = 'This is a combined string :which we want to split from here: and the show begins:'
$a.split('from here:')</pre>

The split method does only allow a single character as a split string.

To enhance this two methods take these two regex examples as a reference.

<pre>[Regex]::Replace('192.168.1.200', '\d{1,3}$','201')
[Regex]::Split('http://server1/dir/subdir/','\bhttp://')</pre>

As you can see, the regex pattern function works also for split and replace methods within complex string content