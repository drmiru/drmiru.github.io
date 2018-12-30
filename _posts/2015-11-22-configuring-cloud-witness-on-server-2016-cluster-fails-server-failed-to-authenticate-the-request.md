---
id: 4496
title: 'Configuring Cloud Witness on Server 2016 Cluster fails: Server failed to authenticate the request'
date: 2015-11-22T12:19:14+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4496
permalink: /configuring-cloud-witness-on-server-2016-cluster-fails-server-failed-to-authenticate-the-request/
bfa_virtual_template:
  - hierarchy
s2mail:
  - 'yes'
categories:
  - Azure
  - Cloud and Datacenter Management
  - Failover Cluster
  - Microsoft
tags:
  - Azure
  - Cloud Witness
  - Failover Cluster
---
While preparing my Demo Environment for the next TechNet Event, in fact re-bulding it with Technical Preview 4, I stumbled over an error while configuring the Cloud Witness for the storage clusters. Cloud Witness is a new feature of Server 2016. To have a Quorum for your clusters you can now leverage a file share resource on your Azure Storage Account. See <a href="http://blogs.msdn.com/b/clustering/archive/2014/11/14/10572766.aspx" target="_blank">here</a> for more information about <a href="http://blogs.msdn.com/b/clustering/archive/2014/11/14/10572766.aspx" target="_blank">Cloud Witness</a>.

However, I have configured this functionality now quite a few times, but this time I got the following error:

<span style="color: #ff0000;">Set-ClusterQuorum : Server failed to authenticate the request. Make sure the value of Authorization header is formed correctly including the</span>
  
 <span style="color: #ff0000;">signature.</span>
  
 <span style="color: #ff0000;">RequestId:e1258a19-0001-0020-720b-2596d2000000</span>
  
 <span style="color: #ff0000;">Time:2015-11-22T09:56:01.1548088Z</span>
  
 <span style="color: #ff0000;">At line:1 char:1</span>
  
 <span style="color: #ff0000;">+ Set-ClusterQuorum -Cluster $Cluster1 -CloudWitness -AccountName drmir &#8230;</span>
  
 <span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span>
  
 <span style="color: #ff0000;">+ CategoryInfo          : NotSpecified: (:) [Set-ClusterQuorum], ClusterCmdletException</span>
  
 <span style="color: #ff0000;">+ FullyQualifiedErrorId : Set-ClusterQuorum,Microsoft.FailoverClusters.PowerShell.SetClusterQuorumCommand</span>

The reason for this error to occur is so simple, that I really thought if it is worth to create a post, but on the other hand it could help people to save time while digging in the wrong corner.

The root cause of this was a time sync issue between my Cluster nodes and Azure. In other words, I messed up with NTP configuration on my Lab Domain Controller. After I fixed the timing, the command run as expected.

<pre>Set-ClusterQuorum -Cluster yourcluster -CloudWitness -AccountName yourstorageaccount -AccessKey YouWontgetmyKeyHereofCourse:-)=='</pre>

Inaccurate time settings can lead to other errors interacting with your Azure Storage Account as well, as it does with your on-premises services using Kerberos of course.

Hope this helps!

&nbsp;