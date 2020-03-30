---
id: 2228
title: SMA Workflows and Remoting, how to deal with Variable Scoping
date: 2014-02-04T22:56:47+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=2228
permalink: /sma-workflows-and-remoting-how-to-deal-with-variable-scoping/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Microsoft
  - Powershell
  - Service Management Automation
tags:
  - Powershell
  - Service Management Automation
---
At the moment I&#8217;m working a lot with SMA (Service Management Automation) in Windows Azure Pack. You you&#8217;re unfamiliar with this topic, I&#8217;d recommend to read my <a title="Service Management Automation (SMA) Whitepaper" href="http://www.miru.ch/service-management-automation-sma-whitepaper/" target="_blank">white paper about SMA</a>.

So what&#8217;s the deal with Powershell Workflows in SMA and Remoting? Well first you have to be aware that inside Powershell Workflows, you can&#8217;t run any command available to native Powershell Scripts. There are enhancements to workflows, but also limitations.

Let&#8217;s take the following non-working example.

<pre style="width: 1134px; height: 180px;">Workflow Get-VMReplicaStatus
{
    param(
    [Parameter(mandatory=$true)]
    $VMHost,
    [Parameter(mandatory=$true)]
    $VMName
    )
    Invoke-Command -ComputerName $VMHost -ScriptBlock {Get-VMReplication -VMName $USING:VMName}
}</pre>

Ooops..

<p style="color: #ff0000; font-family: Lucida Console; font-size: xx-small;">
  At line:10 char:5<br /> + Invoke-Command -ComputerName $VMHost -ScriptBlock {Get-VMReplication -VMName &#8230;<br /> + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~<br /> Cannot call the &#8216;Invoke-Command&#8217; command. Other commands from this module have been packaged as workflow activities, but this command was specifically excluded
</p>

Calling this workflow will throw an error, telling us that Invoke-Command is not supported inside a workflow as a native action. But hey.. how can I do Powershell Remoting then? The answer is called &#8220;InlineScripts&#8221;. Inline scripts are executed in a separated thread, we could also call it a sandbox. What happens in the sandbox, stays in the sandbox. But sometimes, when a CMDLET is not available natively in a workflow, we can use it as an alternative.

Ok, next try&#8230;

<pre style="width: 1204px; height: 234px;">Workflow Get-VMReplicaStatus
{
    param(
    [Paramater(mandatory=$true)]
    $VMHost,
    [Paramater(mandatory=$true)]
    $VMName
    )
    InlineScript
    {
       Invoke-Command -ComputerName $USING:VMHost -ScriptBlock {Get-VMReplication -VMName $USING:VMName}
    }
}</pre>

Oops again..

<span style="color: red; font-family: 'Georgia','serif'; font-size: 9pt; mso-fareast-font-family: 'Times New Roman'; mso-bidi-font-family: 'Times New Roman';">Cannot validate argument on parameter &#8216;VMName&#8217;. The argument is null or empty.<br /> Provide an argument that is not null or empty, and then try the command again.</span>

So, one step closer but still an error. The workflow claims that the Argument VMName is empty. But why? Because if we use Inline Scripts, $USING is reserved to pass variables of a higher scope (meaning the workflow body) to an Inline Script. So the variable $VMName never gets passed to the remote command on our VM Host. But hey.. of course we have a solution here as well. Those who dealed with Powershell 2.0 Remoting and passing variables into a remote session will be very well aware of this.

Next try&#8230;

<pre>Workflow Get-VMReplicaStatus
{
    param(
    [Paramater(mandatory=$true)]
    $VMHost,
    [Paramater(mandatory=$true)]
    $VMName
    )
    InlineScript
    {
        $VMName = $USING:VMName
        Invoke-Command -ComputerName $USING:VMHost -ScriptBlock {param($VMName) Get-VMReplication -VMName $VMName} -ArgumentList $VMName
    }
}</pre>

Et voilà! This works like a charm, but why?

First we re-scope the variable VMName, so it is available inside our inline script. The variable then gets passed as an argument to the remote command as it was necessary **before** Powershell 3.0

How to do it better..

<pre>Workflow Get-VMReplicaStatus
{
    param(
    [Paramater(mandatory=$true)]
    $VMHost,
    [Paramater(mandatory=$true)]
    $VMName
    )
    InlineScript
    {
        Get-VMReplication $USING:VMName
    } -PSComputerName $VMHost
}</pre>

The last example is a best practice for SMA workflows. Instead of using remote commands inside InlineScripts you should use the -PSComputerName as a parameter for the InlineScript itself.

Hope this helps with the transition to SMA and Powershell Workflows.

&nbsp;