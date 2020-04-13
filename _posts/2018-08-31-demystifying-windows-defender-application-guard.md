---
id: 4955
title: Demystifying Windows Defender Application Guard
date: 2018-08-31T15:30:56+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4955
permalink: /demystifying-windows-defender-application-guard/
bfa_virtual_template:
  - hierarchy
categories:
  - Application Virtualization
  - Security
  
tags:
  - Container
  - Defender
  - Windows 10
---
Windows Defender Application Guard (Application Guard) was introduced with Windows 10 build 1709  and is designed to protect from current and future attacks against internet exposed applications such as Edge or Office applications. WDAG leverages Microsoft virtualization and hardware isolation technology, in fact Hyper-V containers to isolate applications from the operating system. Hyper-V containers seem to be an ideal solution for this as the have a more secure virtualization abstraction layer than classic container.

[<img class="aligncenter wp-image-4957 size-full" src="../images/2018/08/containers.png" alt="" width="926" height="416" srcset="../images/2018/08/containers.png 926w, ../images/2018/08/containers-300x135.png 300w, ../images/2018/08/containers-768x345.png 768w" sizes="(max-width: 926px) 100vw, 926px" />](../images/2018/08/containers.png)

For more information on Hyper-V containers see here:

<https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/hyperv-container>

&nbsp;

## Windows Defender Application Guard in Depth

* * *

<span style="color: #ff0000;">Disclaimer: The following details have been discovered by myself and may not be 100% accurate, based on process tracking and reverse engineering my assumptions could be wrong. However, this is what I&#8217;ve found out so far.</span>

* * *

WDAG is an optional component to install / activate on Windows 10 Enterprise and Professional. To enable the feature (which requires a reboot):

<pre class="">Enable-WindowsOptionalFeature -FeatureName Windows-Defender-ApplicationGuard -Online</pre>

After reboot you&#8217;ll notify that your available user memory has significantly decreased, which is because of a new hidden Hyper-V container instance beeing spun up behind the scenes. As soon as new application guard windows are started the memory footprint of the corresponding vmmem.exe process will increase significantly. I&#8217;ve observed up to 1.3GB working set with just a few web pages opened. Vmmem reflects the memory used inside a virtual machine or in this case here, the Hyper-V Container.

[<img class="size-large wp-image-4960 alignnone" src="../images/2018/08/wdag_container-1024x60.png" alt="" width="1024" height="60" srcset="../images/2018/08/wdag_container-1024x60.png 1024w, ../images/2018/08/wdag_container-300x18.png 300w, ../images/2018/08/wdag_container-768x45.png 768w, ../images/2018/08/wdag_container.png 1327w" sizes="(max-width: 1024px) 100vw, 1024px" />](../images/2018/08/wdag_container.png)

You won&#8217;t find this container instance using regular management tools such as docker client, because it is hidden from the management layer. However as any other windows container it is managed by the &#8220;Container Manager Service&#8221;. Protected applications (currently Edge exclusively) are started within this container while they are seamlessly presented on the host operating system using a tiny version of the RDP client. So very simplified, think of a remote app, launched in a container, presented to the host OS via RDP.

[<img class="aligncenter size-large wp-image-4968" src="../images/2018/08/wdag_container-1-1024x398.png" alt="" width="1024" height="398" srcset="../images/2018/08/wdag_container-1-1024x398.png 1024w, ../images/2018/08/wdag_container-1-300x117.png 300w, ../images/2018/08/wdag_container-1-768x298.png 768w, ../images/2018/08/wdag_container-1.png 1228w" sizes="(max-width: 1024px) 100vw, 1024px" />](../images/2018/08/wdag_container-1.png)

Depending on the mode (standalone or enterprise), Browser Broker get&#8217;s called manually or automatically based on the entered URL / address. Windows Defender Guard Manager talks to the Container Manager Service which initiates the launch of a new Edge Browser instance inside the pre-launched container. A special RDP Client (HvsiRdpClient) displays content and allows KVM interaction to the edge app running inside the Hyper-V Container.

## Standalone vs. Enterprise Mode

There are two working modes for WDAG. The difference is quite simple. The enterprise mode automatically detects safe versus unsafe URLs, based on group policy settings and launches the url within a guarded Edge process inside the WDAG container. In contrary, with standalone mode, the user has to explicitly launch a new guarded window/tab. Enterprise mode requires Windows 10 Enterprise SKU.

[<img class="size-medium wp-image-4969 aligncenter" src="../images/2018/08/launch_wdag_edge-300x179.png" alt="" width="300" height="179" srcset="../images/2018/08/launch_wdag_edge-300x179.png 300w, ../images/2018/08/launch_wdag_edge-768x459.png 768w, ../images/2018/08/launch_wdag_edge-1024x613.png 1024w, ../images/2018/08/launch_wdag_edge.png 1202w" sizes="(max-width: 300px) 100vw, 300px" />](../images/2018/08/launch_wdag_edge.png)

## How to configure Windows Defender Application Guard

Configuration of WDAG settings can be done via GPO (requires enterprise SKU) or via settings application.

[<img class="alignnone wp-image-4973 size-large" src="../images/2018/08/WDAG_settings_GPO-1024x376.png" alt="" width="1024" height="376" srcset="../images/2018/08/WDAG_settings_GPO-1024x376.png 1024w, ../images/2018/08/WDAG_settings_GPO-300x110.png 300w, ../images/2018/08/WDAG_settings_GPO-768x282.png 768w, ../images/2018/08/WDAG_settings_GPO.png 1439w" sizes="(max-width: 1024px) 100vw, 1024px" />](../images/2018/08/WDAG_settings_GPO.png) [<img class="alignnone wp-image-4974 size-large" src="../images/2018/08/WDAG_settings_gui-1024x796.png" alt="" width="1024" height="796" srcset="../images/2018/08/WDAG_settings_gui-1024x796.png 1024w, ../images/2018/08/WDAG_settings_gui-300x233.png 300w, ../images/2018/08/WDAG_settings_gui-768x597.png 768w, ../images/2018/08/WDAG_settings_gui.png 1199w" sizes="(max-width: 1024px) 100vw, 1024px" />](../images/2018/08/WDAG_settings_gui.png)

&nbsp;

## Summary

Windows Defender Application Guard protects the operating system from malicious software as it runs the application inside a Hyper-V Container. Using this technology you can now safely speak of a **real** &#8220;sand boxed Browser&#8221;. Enterprise organisations can keep a high level of security and control, while still allowing users to access non-trusted websites through the isolated environment enriching the overall user experience. Although there is some interaction between the host OS and the app running inside the container, it&#8217;s very secure by default, as no file, process, registry or RPC access is allowed with the &#8220;mother&#8221; OS. Files can be printed out if configured by the admin, but not saved outside the container environment. Copy and paste (RDP Clip only) can also be enabled as well as keeping settings and cookies for the containerized browser. Very nice move, we&#8217;ll see what other types of applications will leverage this technology in the future.