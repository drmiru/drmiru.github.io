---
id: 5184
title: When your Azure Policy interferes with Azure Network Watcher
date: 2019-12-13T01:24:18+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=5184
permalink: /when-your-azure-policy-interferes-with-azure-network-watcher/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - DevOps
  - Governance
  - Microsoft
tags:
  - Azure
  - Blueprints
  - Network
---
Network Watcher is a regional service that enables you to monitor and diagnose conditions at a network scenario level in, to, and from Azure. Scenario level monitoring enables you to diagnose problems at an end-to-end network level view. Network diagnostic and visualization tools available with Network Watcher help you understand, diagnose, and gain insights to your network in Azure. Network Watcher is enabled through the creation of a Network Watcher resource. This resource allows you to utilize Network Watcher capabilities. 

By default, a resource group called &#8220;NetworkWatcherRG&#8221; is created automatically, the first time you create a virtual network in your subscription. This might or might not be a desired behavior, especially when you govern your environment using Azure Policies.

I recently came across an annoying issue with Azure Network Watcher when having Azure Policies in place that required certain tags to be submitted on resource / resource group creation.

When trying to enable the NetworkWatcher for a new region, the request got denied:  


> <font color="red"> Resource 'NetworkWatcherRG' was disallowed by policy </font>


The reason for this, the Network Watcher is a hidden resource, but still it is a resource which has to live inside a resource group. Microsofts automatically created a resource group with the name &#8220;NetworkWatcherRG&#8221;. So first, the name of the resource group is not compliant with my naming standards, secondly I&#8217;m enforcing certain tags to be set, when a resource group is created. That&#8217;s why this failed.

## How to make it the &#8220;compliant&#8221; way.

### 1. Disable automatic creation of Network Watcher

<p class="has-background has-luminous-vivid-amber-background-color">
  Disclaimer! Once you opted out of the automatic creation, you have to contact support to have it back &#8220;opted in&#8221;
</p>

**PowerShell**
{% highlight ruby %}
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
{% endhighlight %}

**Azure CLI**

{% highlight ruby %}az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
{% endhighlight %}

### 2. Automate Network Watcher instance creation

Whatever process and tools you choose, ensure every subscription gets his resource group for the network watcher instances. I&#8217;d recommend to choose the resource group where you put your other network core resources in, such as network gateways and firewalls. I&#8217;m using Azure Blueprints to ensure, every new subscription get&#8217;s the basic resources and policies assigned.<figure class="wp-block-image">

<img src="../images/2019/12/networkwatcher02.png" alt="" class="wp-image-5186" srcset="../images/2019/12/networkwatcher02.png 906w, ../images/2019/12/networkwatcher02-300x43.png 300w, ../images/2019/12/networkwatcher02-768x109.png 768w" sizes="(max-width: 906px) 100vw, 906px" /> </figure> 

You can find an example blueprint to achieve this in my Github repo <a href="https://github.com/drmiru/AzureBlueprints/tree/master/blueprints/NetworkWatcher" target="_blank" rel="noreferrer noopener" aria-label="here (opens in a new tab)">here</a>