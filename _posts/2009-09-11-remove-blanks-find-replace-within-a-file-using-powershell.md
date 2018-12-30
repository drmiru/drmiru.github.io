---
id: 165
title: Remove blanks, find replace within a file using powershell
date: 2009-09-11T21:08:16+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=165
permalink: /remove-blanks-find-replace-within-a-file-using-powershell/
categories:
  - Powershell
tags:
  - File Operations
  - Powershell
  - Scripting
---
These two small Powershell functions could save an admins live at daily work 🙂

<pre>#---------------------------------------------------------------------------------------
Function find_replace($file,$findstr,$replacestr)
{
	$input =  get-content $file
	$output = $input -replace($findstr,$replacestr)
	set-content $file $output
}
#---------------------------------------------------------------------------------------</pre>

Example:
  
**find_replace &#8220;C:\test.txt&#8221; &#8220;blaa&#8221; &#8220;fooo&#8221;**

<pre>#---------------------------------------------------------------------------------------
Function trim($file,$mode)
{
	$input = Get-Content $file
	$output = $input | where {$_ -notmatch "^$" }
	If ($mode -eq 'includeblanks')
	{
		$output = $output -replace(' ','')
	}
	Set-Content $file $output
}
#---------------------------------------------------------------------------------------</pre>

Example 1:**
  
trim &#8220;C:\test.txt&#8221;
  
** (removes empty lines in the file)****

**Example 2:
  
trim &#8220;C:\test.txt&#8221; includeblanks
  
** (removes also blank chars)**
  
**