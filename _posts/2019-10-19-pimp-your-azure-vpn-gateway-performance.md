---
id: 5148
title: Pimp your Azure VPN Gateway Performance
date: 2019-10-19T17:50:47+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=5148
permalink: /pimp-your-azure-vpn-gateway-performance/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Microsoft
tags:
  - Azure
  - Hybrid Network
  - Performance
---
<figure class="wp-block-image"><img src="../content/images/2019/10/speed-300x300.png" alt="" class="wp-image-5157" srcset="../content/images/2019/10/speed-300x300.png 300w, ../content/images/2019/10/speed-150x150.png 150w, ../content/images/2019/10/speed.png 512w" sizes="(max-width: 300px) 100vw, 300px" /></figure> 

A lot of customers are using Azure VPN Gateways to create a hybrid connectivity with Azure and their on-premises data center. Beside Azure Express Route, it&#8217;s a widely adopted and robust solution. Azure VPN Gateways are highly available by default and depending on the SKU even zone redundant and active/active. Whereas active/active configurations require BGP, active passive configurations can use static routes.

The performance of the VPN Gateways hardly depends on two factors (beside the ISP connectivity of course), the SKU (size) and the IPSec policy settings.

### SKU (Size)

Depending on the SKU size your Azure VPN gateway can handle more or less connections and overall bandwidth. 

<table class="wp-block-table">
  <tr>
    <td>
      <strong>SKU</strong>
    </td>
    
    <td>
      <strong>S2S Connections</strong>
    </td>
    
    <td>
      <strong>Aggregated Throughput</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Basic</strong>
    </td>
    
    <td>
      10
    </td>
    
    <td>
      100 Mbps
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>VpnGw1</strong>
    </td>
    
    <td>
      30
    </td>
    
    <td>
      650 Mbps
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>VpnGw2</strong>
    </td>
    
    <td>
      30
    </td>
    
    <td>
      1 Gbps
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>VpnGw3</strong>
    </td>
    
    <td>
      30
    </td>
    
    <td>
      1.25 Gbps
    </td>
  </tr>
</table>

Note, that the max throughput is cumulative for all connections, not for each connection.

### IPSec Configuration

When configuring S2S-VPN to Azure using 3rd party firewall or routers, you have to pay attention to the IPsec parameters. The profile has to match on both ends, otherwise the tunnel won&#8217;t come up or you loose 100% of the packets. On the Azure VPN gateway, you can create a custom IPSec connection profile at creation time of the connection or afterwards. Each connection can have a single IPSec profile attached. IKE/IPSec encryption-, and hashing-algorhythms are crucial from a performance perspective. According to the <a rel="noreferrer noopener" aria-label="Microsoft docs (opens in a new tab)" href="https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways" target="_blank">Microsoft docs</a>, GCMAES256 is the best choice. I can confirm that according to my own tests. The following results where produced using a S2S-VPN Configuration between &#8220;westeurope&#8221; and &#8220;eastus2&#8221; regions. For the performance tests I used the <a rel="noreferrer noopener" aria-label="ntttcp utility (opens in a new tab)" href="https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769" target="_blank">ntttcp utility</a>. I was not able to exactly reach the max values as stated in the docs, but as mentioned, the gateways where spread over continents, so a fair amount of latency (~180ms) kicked in.

<table class="wp-block-table">
  <tr>
    <td>
      <strong>VPN GW</strong><br /><strong>Sku</strong>
    </td>
    
    <td>
      <strong>IKE/IPSec </strong><br /><strong>Encryption</strong>
    </td>
    
    <td>
      <strong>IKE/IPSec</strong><br /><strong>Hashing</strong>
    </td>
    
    <td>
      <strong>Max. Throughput </strong><br /><strong>(Mbps)</strong> <br /><em>average after 5 measures</em>
    </td>
  </tr>
  
  <tr>
    <td>
      VpnGw1
    </td>
    
    <td>
      DES3
    </td>
    
    <td>
      SHA256
    </td>
    
    <td>
      125
    </td>
  </tr>
  
  <tr>
    <td>
      VpnGw1
    </td>
    
    <td>
      AES256
    </td>
    
    <td>
      SHA256
    </td>
    
    <td>
      220
    </td>
  </tr>
  
  <tr>
    <td>
      VpnGw1
    </td>
    
    <td>
      GCMAES256
    </td>
    
    <td>
      GCMAES256
    </td>
    
    <td>
      300*
    </td>
  </tr>
  
  <tr>
    <td>
      VpnGw2
    </td>
    
    <td>
      DES3
    </td>
    
    <td>
      SHA256
    </td>
    
    <td>
      125
    </td>
  </tr>
  
  <tr>
    <td>
      VpnGw2
    </td>
    
    <td>
      AES256
    </td>
    
    <td>
      SHA256
    </td>
    
    <td>
      480
    </td>
  </tr>
  
  <tr>
    <td>
      VpnGw2
    </td>
    
    <td>
      GCMAES256
    </td>
    
    <td>
      GCMAES256
    </td>
    
    <td>
      650*
    </td>
  </tr>
  
  <tr>
    <td>
      VpnGw3
    </td>
    
    <td>
      DES3
    </td>
    
    <td>
      SHA256
    </td>
    
    <td>
      130
    </td>
  </tr>
  
  <tr>
    <td>
      VpnGw3
    </td>
    
    <td>
      AES256
    </td>
    
    <td>
      SHA256
    </td>
    
    <td>
      550
    </td>
  </tr>
  
  <tr>
    <td>
      VpnGw3
    </td>
    
    <td>
      GCMAES256
    </td>
    
    <td>
      GCMAES256
    </td>
    
    <td>
      880*
    </td>
  </tr>
</table>

*according to the default performance observed if no IPSec policy was attached, I assume GCMAES256 is the default mode 

### How to configure IPSec policies for your Azure VPN gateways?

IPSec policies are mapped on connection level. Each connection can have a dedicated IPSec policy. In the following example I&#8217;m showing how to configure and map the policies using Azure CLI, whereas PowerShell is another option of course.

**Create and attach IPSec Policy**

<pre class="wp-block-code"><code>az network vpn-connection ipsec-policy add --connection-name myconn1 --resource-group myRG --ipsec-encryption AES256 --ipsec-integrity SHA256 --ike-encryption AES256 --ike-integrity SHA256 --dh-group DHGroup14 --pfs-group PFS2048 --sa-lifetime 600 --sa-max-size 102400000</code></pre>

**Remove IPSec Policy (back to defaults)**

<pre class="wp-block-code"><code>az network vpn-connection ipsec-policy clear --connection-name myconn1 --resource-group myRG</code></pre>

### What now?

Now it&#8217;s time to check out the options on your firewall / vpn device and check the available option for IPSec Tunnel encryption and hashing mechanisms so you get the max out of your VPN gateway.