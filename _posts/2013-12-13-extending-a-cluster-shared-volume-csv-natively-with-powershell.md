---
id: 1995
title: Extending a Cluster Shared Volume (CSV) natively with Powershell
date: 2013-12-13T01:04:23+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=1995
permalink: /extending-a-cluster-shared-volume-csv-natively-with-powershell/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Failover Cluster
  - Microsoft
  - Powershell
tags:
  - Clustering
  - CSV
  - Powershell
---
In one of my recent <a href="http://www.miru.ch/automating-online-extension-of-a-csv-using-powershell/" target="_blank">posts</a>, I showed how to extend  a CSV using Powershell and DISKPART. As I&#8217;m constantly trying to avoid using &#8220;legacy&#8221; command line tools, I decided to find a way around DISKPART.Therefore I was able to create a Powershell Function to extend a CSV on a Windows Failover Cluster.

As you might already know, the operation has to be performed on the Coordinator node, the CSV owner. The function helps you here too.

  * Searches for the current owner node of the CSV
  * Checks if a WinRM session can be established to the owner node and creates a persisent Remote Powershell Session
  * Get&#8217;s the physical disk and partition ID from the CSV
  * Extends the CSV to it&#8217;s maximum available extend

Prerequisites:

  * Server 2008 R2 or higher
  * Powershell 3.0
  * Failover Clustering Powershell Module (RSAT-Clustering-Powershell)
  * The physical LUN has already be extended on your Storage Space, SAN, NAS

And here it is&#8230;

<pre>Function Extend-csv
{
    [CmdletBinding(SupportsShouldProcess=$true)]
    param
    (
    [Parameter(Mandatory=$True)]
    [STRING]$csvname,
    [Parameter(Mandatory=$True)]
    [STRING]$clustername    
    )

    #Check for Failover Cluster Powershell Module Presence
    If (!(Get-Module FailoverClusters)) {write-warning "Install Failover Clustering Powershell Module first!";break}

    #Get the owner of the CSV and some ID info
    $csv = Try {Get-ClusterSharedVolume -Cluster $clustername  -ErrorAction STOP | ? {$_.Name -eq "$csvname"} } Catch {}
    If (!$csv) {write-warning "No CSV with Name $csvname found on Cluster $clustername";break}
    $csv_owner = $csv.OwnerNode.name
    $csv_id = $csv.id

    #Establish a new WinRM Session with the CSV owner node
    $session = Try {New-PSSession -ComputerName $csv_owner -ErrorAction STOP} Catch {Write-Warning "Error while trying to establish a WinRM Session to Host: $csv_owner";break}

    #Execute the commands on the remote host (CSV Owner)
    Invoke-Command -Session $session -Scriptblock {
        #Rescan the storage
        Update-HostStorageCache;

        #Get the Disk ID of the CSV corresponding LUN
        $csv_disk_id = (Get-ItemProperty "HKLM:\Cluster\Resources\$USING:csv_id\Parameters").DiskIdGuid;

        #Query the physical Disk
        $physicaldisk = Get-Disk | ? {$_.Guid -eq $csv_disk_id};

        #Get the partition
        $partition = Get-Partition -Disk $physicaldisk | ? {$_.Type -ne 'Reserved'}

        #Resize the Partition
        $newsize = $partition.Size + $physicaldisk.LargestFreeExtent
        $newsizeGB = ($newsize / 1024 / 1024 / 1024)
        Try
        {
            Resize-Partition -DiskNumber $partition.DiskNumber -PartitionNumber $partition.PartitionNumber -Size $newsize -ErrorAction STOP -ErrorVariable EXTENDERR;
        }
        Catch
        {
            Write-Warning "Invalid Size or no free space to extend the volume";
        }
        If (!$EXTENDERR) {Write-Output "Cluster Shared Volume: $csvname on Cluster: $clustername has been successfully extended to $newsizeGB GB"}

    }

    #Cleaning Up the Powershell Remote Session
    Remove-PSSession $session.ID;
}</pre>