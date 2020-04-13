---
id: 432
title: Exchange 2010 SP1 Rollup 2 available
date: 2011-03-18T22:27:54+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=432
permalink: /exchange-2010-sp1-rollup-1-available/
aktt_notify_twitter:
  - 'no'
categories:
  
---
The rollup can be downloaded <a href="http://www.microsoft.com/downloads/en/details.aspx?FamilyID=0059546f-f43d-4c7e-8367-81c6d5642bf3&displaylang=en" target="_blank">here</a>.

To apply the update follow this order:

  * Edge transport servers
  * Client access servers
  * Hub transport servers
  * UM servers
  * Mailbox servers

If you have DAGs follow this procedure to upgrade all DAG members. (Thanks to <a href="http://howdouc.blogspot.com/" target="_blank">Tim Harrington</a> for original post)

Exchange 2010 SP1 provides some new scripts to help with performing maintenance on DAG members.  To apply the update to a DAG member, the following steps should be taken:

**Step 1** &#8211; From the Exchange installation path (in my case C:\Program Files\Microsoft\Exchange Server\V14\Scripts\), run: _.\StartDagServerMaintenance.ps1 –ServerName “MBXServerName”_

[<img title="RU-DAG-StartMaint-markup" src="http://lh6.ggpht.com/_7DQiUObw70M/TLMwBjnwSgI/AAAAAAAAARU/5fIpPjudFeA/RUDAGStartMaintmarkup_thumb2.jpg?imgmax=800" border="0" alt="RU-DAG-StartMaint-markup" width="500" height="75" />](http://lh5.ggpht.com/_7DQiUObw70M/TLMwBdBUTII/AAAAAAAAARQ/pYLEWQOjlhE/s1600-h/RUDAGStartMaintmarkup4.jpg)

This script will:

  * Run Suspend-MailboxDatabaseCopy for each database copy hosted on the DAG member
  * Pauses the node in the cluster, which prevents it from being and becoming the PAM
  * Sets the value of the DatabaseCopyAutoActivationPolicy parameter on the DAG member to Blocked
  * Moves all active databases currently hosted on the DAG member to other DAG members

**Step 2** – Install the update rollup as described above

**Step 3** – From the Exchange installation path (in my case C:\Program Files\Microsoft\Exchange Server\V14\Scripts\), run: _.\StopDagServerMaintenance.ps1 –ServerName “MBXServerName”_

[<img title="RU-DAG-StopMaint-markup" src="http://lh6.ggpht.com/_7DQiUObw70M/TLMwCcwV-UI/AAAAAAAAARc/iYgrEJKL2wQ/RUDAGStopMaintmarkup_thumb1.jpg?imgmax=800" border="0" alt="RU-DAG-StopMaint-markup" width="508" height="56" />](http://lh6.ggpht.com/_7DQiUObw70M/TLMwCBz6haI/AAAAAAAAARY/d8ceYqNtxLU/s1600-h/RUDAGStopMaintmarkup3.jpg)

This script will:

  * Run Resume-MailboxDatabaseCopy for each database copy hosted on the DAG member
  * Resumes the node in the cluster, which it enables full cluster functionality for the DAG member
  * Sets the value of the DatabaseCopyAutoActivationPolicy parameter on the DAG member to Unrestricted

**Step 4** – (Optional) Use the _RedistributeActiveDatabases.ps1_ script to re-balance the active database copies across the DAG