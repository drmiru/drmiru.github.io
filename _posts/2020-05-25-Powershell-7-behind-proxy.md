---
id: 5208
title: PowerShell 7 behind corporate proxy server
date: 2020-05-25T19:10:34+02:00
author: Michael Rüefli
layout: post
permalink: /PowerShell-7-behind-proxy/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Microsoft
  - PowerShell
tags:
  - PowerShell
  - PowerShellCore
---
## A legacy problem - refactored
Using modern Devtools can be challenging if you need to connect through a corporate proxy server. Not all frameworks and tools have a proper support for all proxy scenarios.

PowerShell 7, as the new combined world of classic PowerShell and PowerShell Core 6.x is based on the .NET Core Framework. So basic rules to manage web connections behind a corporate proxy apply as usual.
You might run into one or more of the following errors (not limited to) behind a proxy server:
- Azure PowerShell CMDLETs fail
- Web-related CMDLETs like Invoke-Restmethod or Invoke-Webrequest fail
- Download/Installation of PowerShell Modules fail
- PowerShell does not show a default Repository (PSGallery)

I'd suggest to use either the AllUsers or the CurrentUser PowerShell Profile for the required, few lines of code. For more information about PowerShell profiles see: [about_profiles](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles?view=powershell-7)

### PowerShell Core 6.x / PowerShell 7.x
```PowerShell
$proxyUri = 'http://proxy.domain.com:port'
[System.Net.Http.HttpClient]::DefaultProxy = New-Object System.Net.Webproxy($proxyUri, $true)
[System.Net.Http.HttpClient]::DefaultProxy.Credentials = [System.Net.CredentialCache]::DefaultNetworkCredentials
```

The above snippet will use the logged on user's credentials being forwarded to the proxy server (if the proxy requires it though). If you want to use a special credential you can use the following snippet:
```PowerShell
$credential = Get-Credential
$proxyUri = 'http://proxy.domain.com:port'
[System.Net.Http.HttpClient]::DefaultProxy = New-Object System.Net.Webproxy($proxyUri, $true)
[System.Net.Http.HttpClient]::DefaultProxy.Credentials = $credential
```

Hope this helps.