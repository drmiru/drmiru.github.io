---
id: 674
title: Exchange 2010 Administrative Group Name decrypted
date: 2011-10-28T20:21:40+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=674
permalink: /exchange-2010-administrative-group-name-decrypted/
aktt_notify_twitter:
  - 'no'
categories:
  
---
I&#8217;m sure almost everyone dealing with upgrading an existing Exchange Org to MS Exchange 2010 wondered about the name of the administrative group created automatically under the configuration container.

It&#8217;s name (FYDIBOHF23SPDLT) looks interesting and by MS you shouldn&#8217;t change it in any way. But where does that name come from? If you look back a couple of years, there was a cool movie called &#8220;2001 &#8211; space odyssey&#8221;. A smart but fool computer named HAL had full control over the star ship. **HAL** is a crypt name too and if you count each letter always **-1** in the alphabet you&#8217;ll receive &#8212; guess what? **I B M** 🙂

So what about the name of the E2K10 admin group? Starting with Exchange 2007, they had to find a name, which would be really unique and no one has already chosen because in fact E2K7 and E2K10 don&#8217;t use the administrative group concept any more, but you need one if you upgrade from E2K3 for coexistence purposes. The MS Exchange team had a couple of ideas, one was creating a unique GUID. But would that be good to read and to administer? Probably not.

So if we take the name and proceed the same way as with the old school **HAL**&#8230;

**FYDIBOHF23SPDLT**

means..

**EXCHANGE12ROCKS**

For me personally, it rocks too 😉