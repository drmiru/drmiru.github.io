---
id: 510
title: Bulk unregister / register VMs on vSphere
date: 2011-05-20T11:42:05+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=510
permalink: /bulk-unregister-register-vms-on-vsphere/
aktt_notify_twitter:
  - 'no'
categories:
  
---
If you want to unregister all VMs on a particular vSphere Cluster / Host you&#8217;re pretty done using the GUI, registering all VMs on a datastore could take you to the &#8220;hell of clicking masters&#8221; depending on the amount of VMs to register. I created two powershell functions which do that stuff for you.

Usage:

<address>
  unregisterallvm &#8220;datacenter-A&#8221; &#8220;Cluster-A10-LAB&#8221;
</address>

<address>
  registerallvm &#8220;vcenter.domain.com&#8221; &#8220;datacenter-A&#8221; &#8220;Cluster-A10-LAB&#8221; &#8220;Datastore-SAN-001&#8221;
</address>

<address>
</address>

<address>
  And here is the code for copy &#8216;n pasting:<br />
</address>

<pre>Function unregisterallvm([STRING]$datacentername,[STRING]$clustername)
{
 $vms = Get-DataCenter $datacentername | Get-Cluster $clustername | get-vm
 Foreach ($vm in $vms)
 {
   $vmname = $vm.name
   Write-Host "Unregistering VM: $vmname from Cluster $clustername"
   Remove-VM -VM $vmname -DeleteFromDisk:$false -Confirm:$false -RunAsync
 }
}

Function registerallvm([STRING]$vCenterHost,[STRING]$datacentername,[STRING]$clustername,[STRING]$datastorename)
{
 $datastore = Get-Datacenter $datacentername | Get-Datastore | ? {$_.name -match $datastorename}
 $datastoreshortname = $datastore.Name
 $ResourcePool = Get-Cluster -Name $clustername | Get-ResourcePool | Get-View
 $vmFolder = Get-View (Get-Datacenter -Name $datacentername | Get-Folder -Name "vm").id
 $vmdirs = (dir "vmstores:\$vCenterHost@443\$datacentername\$datastoreshortname\")
 Foreach ($f in $vmdirs)
 {
   $vmname = $f.Name
   $checkreg = (Get-Cluster $clustername | Get-VM | ? { $_.name -match $vmname})
   If (!$checkreg)
   {
     "Registering VM: $vmname on: $clustername \ $datastoreshortname"
     $vmFolder.RegisterVM_Task("[$datastoreshortname]/$vmname/$vmname.vmx", $vmname, $false, $ResourcePool.MoRef, $null)   
   }
   Else
   {
     "VM: $vmname is already registered with the same name. Skipping...."
   }
 }
}

</pre>

Of course you need to place this functions in your script before you&#8217;re calling them and you need PowerCLI snapin registered and have a connection to vCenter using:

<pre>connect-viserver -server "vcenter.domain.com" 
</pre>