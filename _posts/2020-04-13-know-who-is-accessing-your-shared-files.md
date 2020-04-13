---
id: 5206
title: Know who is accessing externally shared files using Microsoft Cloudapp Security (MCAS) API
date: 2020-04-13T12:53:13+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=5206
permalink: /Know-who-is-accessing-externally-shared-files-using-Microsoft-Cloudapp-Security/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Microsoft
tags:
  - Azure
  - Cloud App Security
  - MCAS
---
People are sharing files using Teams, Sharepoint or OneDrive for Business and that's ok. That's one of the collaboration feature these tools where built for. But what if people are sharing files externally, how can you track who is effectively accessing the shared files?

So first things first, I'm not going to cover the details of sharing policies of the above tools in this post, it is a topic for another discussion. Let's assume you have configured the sharing policy in a semi-open way, so users can only share documents with guests already existing in your tenant.

At some point in time, your manager, client or the security officer asks you to create a report of all externally shared files. While this can be done using different reporting features in the o365 Admin Portals, it might be more interesting to know, which file is effectively accessed by who. Even more interesting could be the following question:


> "who is accessing shared files, from a classified library?"


This is where the REST API of MCAS (Microsoft Cloud App Security) can jump in. We'll see how in a minute. But first, let's see how we can access the API. To access the API you have to generate an API key first. This is done through the admin portal of MCAS.


- Go to: https://portal.cloudappsecurity.com
- In the upper right corner, click the settings button
- Navigate to Security Extentions / API tokens
- Create a new API token (note it down somewhere safe, I prefer an Azure KeyVault)
