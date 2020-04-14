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


> "who is accessing shared files, from a classified library? Can we get those events into Log Analytics?"


This is where the REST API of MCAS (Microsoft Cloud App Security) can jump in. We'll see how in a minute. But first, let's see how we can access the API. To access the API you have to generate an API key first. This is done through the admin portal of MCAS.


- Go to: https://portal.cloudappsecurity.com
- In the upper right corner, click the settings button
- Navigate to Security Extentions / API tokens
- Create a new API token (note it down somewhere safe, I prefer an Azure KeyVault)
- Note the MCAS URL (refer to the API documentation link) -> e.g. yourtenantname.eu2.portal.cloudappsecurity.com

### PowerShell Example

{% highlight ruby %}
$mcasUrl = 'yourtenantname.eu2.portal.cloudappsecurity.com'

#Secret for Workspace key
Write-Host "Getting secret: $workSpaceKeySecret from key vault: $keyVaultName"
$workSpaceKey = (Get-AzKeyVaultSecret -VaultName $keyVaultName -SecretName $workSpaceKeySecret).SecretValueText
if (!$workSpaceKey) {
    throw "[!]ERROR: Unable to get Secret from KeyVault"
}

Function Convert-ToUnixDate ($PSdate) {
    $epoch = [timezone]::CurrentTimeZone.ToLocalTime([datetime]'1/1/1970')
    [math]::round((New-TimeSpan -Start $epoch -End $PSdate).TotalSeconds)
}

#Create auth header params for Sentinel API Access
$authHeader = @{
    'Content-Type'='application/x-www-form-urlencoded'
    'Authorization'='Token ' + $mcasApiKey
}

#Create filter for API Query
$dateStart = (Convert-ToUnixDate (((get-date).addhours(-$searchTimeFrameHours)).ToUniversalTime())) * 1000
$filter = @"
{
    "filters": {
        "activity.eventType": {
            "eq": ["EVENT_CATEGORY_ACCEPT_SHARE_FILE"]
        },
        "created" : {
            "gte" : "$dateStart"
        }
    }
}
"@

$result = Invoke-WebRequest -Method Post -Uri "https://$mcasUrl/api/v1/activities/" -Headers $authHeader -body $filter
$sharings = ($result.Content -creplace 'Level','Level_2' -creplace 'EventName','EventName_2' | ConvertFrom-Json -depth 50).data
$shareReport = @()
Foreach ($sharedObj in $sharings) {
    $shareInfo = [PSCUSTOMOBJECT]@{
        Description = $sharedObj.description
        Operation = $sharedObj.eventTypeName
        Severity = $sharedObj.severity
        Collaborator=($sharedObj | select -ExpandProperty user).userName
        CreatedDateTime =  ([datetimeoffset]::FromUnixTimeMilliseconds(1000 * ((($sharedObj.created).toString()).substring(0,10) + "." + (($sharedObj.created).toString()).substring(10,3)))).DateTime
        UserId = $sharedObj.rawDataJson | select -ExpandProperty UserId
        ResolvedUser = $sharedObj.resolvedActor.name
        UserKey = $sharedObj.rawDataJson | select -ExpandProperty UserKey
        SiteUrl = $sharedObj.rawDataJson | select -ExpandProperty SiteUrl
        SourceFileName = $sharedObj.rawDataJson | select -ExpandProperty SourceFileName
        SourceRelativeUrl = $sharedObj.rawDataJson | select -ExpandProperty SourceRelativeUrl
        FileUrl = $sharedObj.description_metadata | select -ExpandProperty target_object
        ObjectId = $sharedObj.rawDataJson | select -ExpandProperty ObjectId
        AccessLocation = $sharedObj.location
        AppName = $sharedObj.appName        
    }
    $shareReport += $shareInfo
}

{% endhighlight %}

### Ingest data into log analytics

Now as we got the Information, we need to ingest the data into Log Analytics. There are various PowerShell modules available from the community. I just wanted to show you the plain code as an example here.

{% highlight ruby %}

#Function to create a new authorization using the workspace key
 Function Build-Signature {
    param(
        $customerId,
        $sharedKey,
        $date,
        $contentLength,
        $method,
        $contentType,
        $resource
    )
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource    
    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)    
    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash

    return $authorization
}
 
#Function to post the json event data 
Function Set-LogAnalyticsData {
    param(
        $customerId,
        $sharedKey,
        $body,
        $logType
    )

    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature  -customerId $customerId -sharedKey $sharedKey -date $rfc1123date -contentLength $contentLength `
    -fileName $fileName `
    -method $method `
    -contentType $contentType `
    -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"
    
    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }
    
    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode 
}


#Get TenantId
$tenantId = (get-azcontext).Tenant.Id

#Get Log Analytics Workspace
$workSpace = (Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $workspaceResourceGroup)
$workspaceId = $workSpace.ResourceId
$customerId = ($workSpace.CustomerId).ToString()
if (!$workspaceId) {
    throw "[!] Workspace cannot be found. Please try again"
} else {
    Write-Host -ForegroundColor Green "[-] Workspace $WorkspaceName connected"
}

#Send data to log analytics data collector API
$body = $shareReport | convertto-json
Set-LogAnalyticsData -customerId $customerId -sharedKey $workspaceKey -body $body -logType "MCAS_CL"

{% endhighlight %}

It will take around 5 minutes to propagate the new custom log data into your workspace. The logs will then be searchable by the type: "MCAS_CL".

## Summary

In this post I showed you how to:
- Get Secrets from Azure Key Vault
- Get Information from MCAS API
- Ingest the Information into a Log Analytics Workspace

You can get the full code as an time triggered Azure Function on [github.com/drmiru](https://github.com/drmiru/AzureFunctions/tree/master/MCAS).