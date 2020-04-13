---
id: 5179
title: Accessing Azure KeyVault secrets from an Azure Function
date: 2020-03-23T07:56:02+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=5179
permalink: /accessing-azure-keyvault-secrets-from-an-azure-function/
bfa_virtual_template:
  - hierarchy
categories:
  - Powershell
  - Serverless
tags:
  - Azure
  - Function
  - KeyVault
  - Serverless
---
<figure class="wp-block-image"><img src="../images/2020/03/FunctionApp_loves_KeyVault.png" alt="" class="wp-image-5212" /></figure> 

Storing secrets, keys and certificates securely is not an optional discipline. Code, wherever it lands, must never include any clear text secrets. Azure Key Vault provides a secure way to store customer managed encryption keys, credentials and certificates.

Using Azure Functions you have several ways to access secrets from an Azure Key Vault. No matter which option you choose, the Function App needs access to the Key Vault. This is accomplished with a Key Vault Access Policy. I highly recommend to provision a MSI (managed service identity) for your Function App, so the access policy to read and list secrets in Key Vault can be assigned to the MSI.

### Option 1: Get Key Vault Secret at runtime of the function

In this example I&#8217;m going to show how to get a Key Vault secret via PowerShell.

{% highlight PowerShell %}
#Get Access Key for Eventhub Namespace from KeyVault
Write-Host "Getting secret: $eventHubKeySecret from key vault: $keyVaultName"
$eventHubkey = (Get-AzKeyVaultSecret -VaultName $keyVaultName -SecretName $eventHubKeySecret).SecretValueText
if (!$eventHubkey) {
    throw "Unable to get EventHub Access Key from KeyVault"
}
{% endhighlight %}


Key Vaults are billed based on API operations (eg. get key/secret). If your function is being called billions of times a month, you might have to think about the resulting costs. To limit the amount of key operations against your vault, you have another option:

### Option 2: Get Key Vault Secret on Function App startup using application settings

Another way is to reference the URI of the secret in the application settings of the function app, so the secrets get loaded, at startup time of the Function App.

{% highlight ruby %}
mySecret = @Microsoft.KeyVault(VaultName=myvault;SecretName=mysecret;SecretVersion=ec96f02080254f109c51a1f14cdb1931)
{% endhighlight %}

As any other app setting it can be referenced as an environment variable inside the function.
{% highlight ruby %}
$mySecret = $ENV:mySecret
{% endhighlight %}

A few restrictions apply with this method:

  * Only system managed MSI are supported
  * Key Vault Firewall settings, limiting network access (e.g. to the Function App) are currently not supported

### Conclusion

Both options provide a way to securely store and access KeyVault secrets and keys from within a Function App. As Always, it&#8217;s recommended to have an automatic rollover procedure for keys and secrets.