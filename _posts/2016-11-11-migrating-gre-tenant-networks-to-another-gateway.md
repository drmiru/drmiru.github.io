---
id: 4793
title: Migrating GRE Tenant Networks to another Gateway
date: 2016-11-11T02:01:16+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4793
permalink: /migrating-gre-tenant-networks-to-another-gateway/
bfa_virtual_template:
  - hierarchy
categories:
  - Cloud and Datacenter Management
  - Hyper-V
  - Powershell
  - Virtualization
tags:
  - Network Virtualization
  - NVGRE
  - SDN
---
At some point in time every SDN deployment hits it&#8217;s scale limit. In Microsofts SDNv1, a single NVGRE gateway can serve up to 100 tenant virtual networks and 200-300Mbit/s throughput. To scale out the network virtualization deployment, additional gateways or gateway clusters have to be deployed. But how can we migrate existing virtualized tenant VM networks from one gateway to another?

[<img class="alignleft wp-image-4798" src="../images/2016/11/gre1-1024x527.png" alt="gre1" width="563" height="290" srcset="../images/2016/11/gre1-1024x527.png 1024w, ../images/2016/11/gre1-300x154.png 300w, ../images/2016/11/gre1-768x395.png 768w" sizes="(max-width: 563px) 100vw, 563px" />](http://www.miru.ch/migrating-gre-tenant-networks-to-another-gateway/gre1/)

  * There are a couple of steps are required to make this happen
  * Remove NAT Rules
  * Remove VPN Connection definitions and routed subnets
  * Remove NAT connections
  * Re-Add all of the above on the new gateway

I&#8217;ve created a little, which does that all automatically. All NAT connections and Rules (including multiple public IP addresses) and VPN settings are transfered to the new GRE gateway device.

[<img class="alignleft size-large wp-image-4796" src="../images/2016/11/move-grevmnet-1024x218.png" alt="move-grevmnet" width="1024" height="218" srcset="../images/2016/11/move-grevmnet-1024x218.png 1024w, ../images/2016/11/move-grevmnet-300x64.png 300w, ../images/2016/11/move-grevmnet-768x164.png 768w, ../images/2016/11/move-grevmnet.png 1467w" sizes="(max-width: 1024px) 100vw, 1024px" />](http://www.miru.ch/migrating-gre-tenant-networks-to-another-gateway/move-grevmnet/)

You can find the code on [my github repository](https://github.com/drmiru/Powershell/blob/master/SCVMM/Move-VMNetVirtualizationConfig.ps1)

As a short disclaimer here:

You are using the script at your very own risk. I don&#8217;t take any responsibility for messed up HNV deployments. 🙂