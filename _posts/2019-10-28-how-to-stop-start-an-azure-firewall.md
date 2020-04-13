---
id: 5119
title: How to stop | start an Azure Firewall
date: 2019-10-28T09:10:34+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=5119
permalink: /how-to-stop-start-an-azure-firewall/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Microsoft
  - Powershell
tags:
  - Firewall
  - Hybrid Network
  - Security
---
Azure Firewall is a fully managed, stateful layer 7 firewall. In contrary to classic NVA based concepts, there is no need to care about scale and throughput because all of this is managed by Azure in the background. I always recommend it to customers, unless there are specific killer reasons to still use NVA based firewalls. As you might know, deploying highly available NVA solutions hurts big time, because you need load balancers on both, the internal and external NVA interface.

If you want to know more about the differences between NVAs and Azure Firewall I&#8217;d recommend the following articles:

<https://azure.microsoft.com/en-us/blog/azure-firewall-and-network-virtual-appliances/>

<http://aidanfinn.azurewebsites.net/?p=21469>

### Saving costs for Dev/Test Environments

Azure Firewall is not for free, of course. It can hurt your budget for test and development environments, even if you have zero amount of network traffic. Although a comparable setup with 2 NVAs may cost even more, depending on the VM size and the additional product license, it would be nice to save costs for the Azure Firewall for &#8220;off-use&#8221; times, right? To save money I&#8217;ve created **a PowerShell script**, which can stop (deallocate) and start an Azure Firewall instance. The script is based on the methods described in the following docs article. <https://docs.microsoft.com/en-us/azure/firewall/firewall-faq> 

**Using the script has the following advantages:**

  * It saves Firewall Information prior to the stop action (information is saved to a custom tag on the object). You&#8217;ll need the info when starting the instance again. The script reads it from the tag
  * Scenarios with multiple attached public IP addresses

**Note:**  
Stopping / deallocating the firewall goes pretty fast. However starting the instance takes as long as spinning up a new instance. (I&#8217;m pretty sure this is what happens behind the scenes)

You can find the source code on <a href="https://github.com/drmiru/AzurePowerShell/tree/master/scripts/AzureFirewall" target="_blank" rel="noreferrer noopener" aria-label="my GitHub here (opens in a new tab)">my GitHub here</a>.