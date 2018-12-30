---
id: 203
title: iSCSI multipathing with vSphere 4
date: 2010-03-26T21:27:13+00:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=203
permalink: /iscsi-multipathing-with-vsphere-4/
categories:
  - Virtualization
tags:
  - iSCSI MPIO vSphere4 Multipathing
---
After having esx 3.x deployed, we noticed quiet fast that network based storage can&#8217;t be multipathed and load-balanced really. There are a small number of tricks using LACP, channeling.. but&#8230; This does not help at all. The following article describes in detail, why VI 3.x can&#8217;t load balance the IP based storage traffic.

<a href="http://virtualgeek.typepad.com/virtual_geek/2009/01/a-multivendor-post-to-help-our-mutual-iscsi-customers-using-vmware.html" target="_blank">http://virtualgeek.typepad.com/virtual_geek/2009/01/a-multivendor-post-to-help-our-mutual-iscsi-customers-using-vmware.html</a>

Introduced among vSphere 4, the iSCSI stack has been pimped not too bad! It does support MPIO as we know that from FC storage adapters.

Here is the how to article from Duncan.

<a href="http://www.yellow-bricks.com/2009/03/18/iscsi-multipathing-with-esxcliexploring-the-next-version-of-esx/" target="_blank">http://www.yellow-bricks.com/2009/03/18/iscsi-multipathing-with-esxcliexploring-the-next-version-of-esx/</a>

I tested this in my lab using openfiler as iSCSI Target, and it works perfectly!! The IO is balanced over two vmkernels in fact. As long as openfiler also supports multiple tcp sessions to the same target lun, ESX 4.x does it too. The round robin feature which directs each 1000 IOs to another available path is also officially supported in vSphere 4. Consider to manually split up your paths, as the round robin setting does in fact not use more than one path parallel (1000 IOs -> path1, 1000 IOs -> path 2 and back again)

[<img class="alignnone size-thumbnail wp-image-204" title="openfiler-iscsi-mp" src="http://www.miru.ch/wp-content/uploads/2010/03/openfiler-iscsi-mp-150x150.gif" alt="openfiler-iscsi-mp" width="150" height="150" />](http://www.miru.ch/wp-content/uploads/2010/03/openfiler-iscsi-mp.gif)

And here&#8217;s another leading edge presentation from Nicholas Travers (EMC)
  
<a href="http://www.miru.ch/wp-content/uploads/2010/03/storage-best-practices.pdf" target="_blank">http://www.miru.ch/wp-content/uploads/2010/03/storage-best-practices.pdf</a>