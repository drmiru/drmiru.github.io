---
id: 5204
title: Azure Load Balancer and global vNet peering
date: 2020-03-07T12:53:13+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=5204
permalink: /azure-load-balancer-and-global-vnet-peering/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Microsoft
tags:
  - Azure
  - LoadBalancer
  - Network
---
 

Just coming back from a strange troubleshooting session where an application running on an AKS Cluster was not responding anymore after a network architecture change. It turned out quickly, that the root cause had to be searched in the recent configuration change. The change included a new routing configuration in the customers Azure global network. The AKS cluster&#8217;s network has been peered with a new hub network. The hub network is located in SwitzerlandNorth region whereas the AKS application vnet is located in WestEurope. So we have created a so called cross-region or &#8220;global&#8221; vnet peering. <figure class="wp-block-image">

<img src="http://www.miru.ch/wp-content/uploads/2020/03/globalvnetpeering.png" alt="" class="wp-image-5206" srcset="http://www.miru.ch/wp-content/uploads/2020/03/globalvnetpeering.png 709w, http://www.miru.ch/wp-content/uploads/2020/03/globalvnetpeering-300x168.png 300w" sizes="(max-width: 709px) 100vw, 709px" /> <figcaption>image source: Microsoft</figcaption></figure> 

After changing the routes everything but the AKS&#8217;s load balancer frontend IP was reachable from on-premises.

Long story short &#8211; The AKS cluster was using the basic SKU of Azure Load Balancer which does not support global vNet peering. 

More information:  
<https://docs.microsoft.com/en-us/azure/load-balancer/concepts-limitations> 

<https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview>



Hope this prevents some headaches

Happy peering everyone!