---
id: 5134
title: Why you really want to protect your terraform state file
date: 2019-09-27T17:13:46+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=5134
permalink: /why-you-really-want-to-protect-your-terraform-state-file/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - DevOps
  - Virtualization
tags:
  - IaC
  - terraform
---
I&#8217;ve been using terraform for Azure IaC in various customer projects the last months. One thing which is really critical is to have a proper terraform backend configuration. As long as you&#8217;re the only one working with a specific terraform configuration you only have to care about your own state file. But for production environments, where deployments are usually triggered by a CI/CD pipeline, you&#8217;re no longer handling with local state files and have to store them on a cloud storage. For Azure, it&#8217;s obviously an Azure Storage Account where you put your state file inside a blob container.

Here&#8217;s an example of a partial terraform backend configuration.

{% highlight ruby %}
terraform {
  backend "azurerm" {
    storage_account_name  = "myiacblobstorage"
    container_name        = "terraform"
    key                   = "terraform.myProject.tfstate"
    access_key           = ""
  }
}
{% endhighlight %}

The access key is provided as a parameter on execution, usually gathered from an azure key vault at runtime of the pipeline.

<blockquote class="wp-block-quote">
  <p>
    Wait.. what&#8217;s this statefile (.tfstate) exactly for?
  </p>
</blockquote>

The state file is used by Terraform so it can map the real status of your infrastructure to the configuration applied by your Terraform templates. On every terraform validate/plan/apply cycle the state file gets updated so it reflects the current state of the configured Azure environment (like a mirror). The file (something.tfstate) is stored locally (by default) within the .terraform folder if you do not configure the backend (as shown above). Prior to any operation, Terraform does a refresh to update the state with the real infrastructure. If you lose this statefile, terraform will create a fresh, empty state file, which would result in all defined objects to be recreated. This will obviously fail as they already exist. The worst case here, terraform will delete and recreate all your defined resources. Theoretically there&#8217;s a way to recover from a lost state file by using the &#8220;terraform import&#8221; method. But as you have to import any single resource, this can become a nightmare if your project contains several resources.

## Considerations for handling the state file

  * Store the state file on an Azure Storage Account and every DevOps engineer working on the same project should always reflect this file in the backend configuration
  * Protect the file or the upper blob container with proper SAS tokens
  * Activate soft-delete option on your storage account for easy recovery
  * Automatically create snapshots of your state file(s) after each successful terraform apply run (ideally within the release pipeline). I&#8217;ve added an example how to achieve that using PowerShell below

{% highlight ruby %}
$storageAccountName = "myiacblobstorage"
$ContainerName = "terraform"
$BlobName = "terraform.myproject.tfstate" 

$storageAccount = Get-AzStorageAccount | Where-Object {$_.StorageAccountName -eq $storageAccountName}

$blob = Get-AzStorageBlob -Context $context -Container $ContainerName -Blob $BlobName
$snap = $blob.ICloudBlob.CreateSnapshot()
{% endhighlight %}

Hope this helps to prevent issues with your terraform journey.