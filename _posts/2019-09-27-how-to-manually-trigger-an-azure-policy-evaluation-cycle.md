---
id: 5132
title: How to manually trigger an Azure Policy Evaluation Cycle
date: 2019-09-27T08:08:11+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=5132
permalink: /how-to-manually-trigger-an-azure-policy-evaluation-cycle/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Microsoft
  
tags:
  - Azure
  - Governance
  - Policy
---
 

Azure Policy is a comprehensive tool to control and govern your azure environments. Whether you want to audit or prevent things from happening in your azure subscriptions, there are plenty of built-in policies you can customize according to your needs. Now I often get the question when and where policies get evaluated once created.

Once a policy or policy initiative has been assigned to a scope (Management Group, Subscription or Resource Group), it usually takes up to 30 minutes to take effect. The policy then evaluates compliance state against all underlying resources (for policies with audit effect). Same applies for changes to existing policy assignments.

While policy effect &#8220;deny&#8221; prevents affected deployments at submission time on ARM level, recurring policy evaluation against compliance state occurs then every 24 hours.

If you want to initiate a manual instant policy evaluation cycle, you can leverage the ARM REST APIs. Here&#8217;s an example to trigger an evaluation cycle on subscription level.

<pre class="wp-block-code"><code>$subscriptionId = "&lt;your subscription id goes here>"
$uri = "https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview"
$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Tenant.Id)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}
Invoke-RestMethod -Method Post -Uri $uri -UseBasicParsing -Headers $authHeader
</code></pre>

And a similar example to trigger the eval cycle on a specific resource group.

<pre class="wp-block-code"><code>$subscriptionId = "&lt;your subscription id goes here>"
$resourceGroupName = "&lt;your resource group goes here>"
$uri = "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview"
$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Tenant.Id)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}
Invoke-RestMethod -Method Post -Uri $uri -UseBasicParsing -Headers $authHeader
</code></pre>

A happy governance day!