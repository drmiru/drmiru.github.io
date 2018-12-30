---
id: 932
title: Not just another „wait for network“ script
date: 2012-11-19T13:50:21+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=932
permalink: /not-just-another-wait-for-network-script/
aktt_notify_twitter:
  - 'no'
s2mail:
  - 'yes'
categories:
  - Appsense
  - Powershell
  - User Virtualization
  - Virtualization
tags:
  - Appsense
  - Powershell
---
Using Appsense Environment Manager to copy files at computer startup trigger can lead to headaches, because the action is executed by default without respect to the network connection / domain connection status. Therefore I created an intelligent Powershell script which can be used as a reusable condition prior to the copy jobs.

It does basically the following:

  * Checks if a physical Ethernet adapter with an active layer-2 connection is present
  * If so, the script tries to connect to the current domain controller by using the RootDSE
  * If the DC is not reachable after a few tries, the script exits with return code 1, which equals &#8220;condition false&#8221;, otherwise it returns 1, which equals to &#8220;condition true&#8221;
  * Sets an environment variable &#8220;_EMPNetworkStatus&#8221; with either status &#8220;LANConnect&#8221; or &#8220;disconnected&#8221;
  * Writes the state to the application event log

<pre> ################################################################################################################
# ScriptName: WaitForNetwork.ps1
# Author: Michael Rüefli / INSERTO AG
# Date: 30.05.2012
# Version: 1.01
# Description: Script checks if any physical ethernet adapter is connected and tries to reach current DC
#              The script writes to application eventlog
################################################################################################################
function writeevent() {
if ($args[0] -eq "-h") {
            Write-Host "usage: writeevent &lt;event source&gt; &lt;event type:Information,Warning,Error &lt;event id&gt; &gt; &lt;event text&gt;"
            break
}
#count the arguments
if ($args.count -lt 4)
{
            write-host "Error calling writeevent. Too few arguments. use option -h to see help" -foregroundcolor "red"
            break
}
trap 
{ # this installs an error handler for the function
            write-host "Error writing to eventlog:" $_.Exception.GetType().FullName + $_.Exception.Message
            continue; # exiting a trap with "continue" says to continue on the next line of the script
}

$source = $args[0]
$type = $args[1]
$eventid = $args[2]
$msg = $args[3]
#Create the source, if it does not already exist.
if(![System.Diagnostics.EventLog]::SourceExists($source))
{
            [System.Diagnostics.EventLog]::CreateEventSource($source,'Application')
             $logfile = 'Application'
}
else 
{
            $logfile = [System.Diagnostics.EventLog]::LogNameFromSourceName($source,'.')
             #write-host "Source exists and is registered in the $logfile Log"
}
 #Check if Event Type is correct
               switch ($type) 
               { 
                  "Information" {} 
                  "Warning" {} 
                  "Error" {} 
                  default {"Event type is invalid";exit}
               }
               $log = New-Object System.Diagnostics.EventLog 
               $log.set_log($logfile) 
               $log.set_source($source)
               $log.WriteEntry($msg,$type,$eventid)
}

#Check if the condition has already been run
If ($env:_EMPNetworkStatus -eq "LANConnect")
{
            write-output "Network connection is ready: Check has already been run"
            writeevent "WaitForNetworkScript" Information 2 "Network connection is ready: Check has already been run"
            exit 0
}

#Get All physical network adapter which are connected
$connected_physical_nics = Get-WmiObject Win32_NetworkAdapter | `
where {$_.AdapterType -eq "Ethernet 802.3" `
-and $_.PNPDeviceID -match 'PCI' `
-and $_.NetConnectionStatus -eq 2} 

#If an adapter is found, we assume client is in corporate network
If ($connected_physical_nics)
{
            #Get the current DC
    $currentDC = ([ADSI]“LDAP://RootDSE”).dnshostname
    #test connection to DC
    $i = 0
    While (!$connrst)
    {
        $connrst = Test-Connection -computername $currentDC -Count 1 -ea SilentlyContinue
        $i += 1
        If (!$connrst -and $i -ge 25)
        {
            #Timeout, we exit
            write-output "Timeout, we exit the script"
                                   writeevent "WaitForNetworkScript" Warning 1 "Timeout, network connection not ready: Error while trying to reach DC: $currentDC"
            exit 1
        }
        start-sleep -s 1
    }
    write-output "Network connection is ready: connected DC: $currentDC"
            writeevent "WaitForNetworkScript" Information 0 "Network connection is ready: connected DC: $currentDC"
            [ENVIRONMENT]::SetEnvironmentVariable("_EMPNetworkStatus", "LANConnect", "MACHINE")
    exit 0
}
Else
{
    write-warning "Network is disconnected"
    writeevent "WaitForNetworkScript" Warning 3 "Network is disconnected"
            [ENVIRONMENT]::SetEnvironmentVariable("_EMPNetworkStatus", "Disconnected", "MACHINE")
    exit 1
}</pre>