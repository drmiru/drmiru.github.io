---
id: 4978
title: Data Lifecycle Management with Azure Storage
date: 2018-11-16T13:02:22+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=4978
permalink: /data-lifecycle-management-with-azure-storage/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Storage
tags:
  - Azure Storage
  - Data Lifecycle Management
  - Storage Tiering
---
## Introduction

Azure Storage provides the foundation for a lot of the services available on the Azure Cloud platform. In this post I&#8217;m going to walk you through the data lifecycle management features of Azure Storage Accounts. <span style="font-size: 12pt;"><br /> </span>

## Components of a Data Lifecycle Management

In a nutshell, DLM refers to a policy-driven approach that can be automated to take data through its useful life. But what is exactly the definition of data&#8217;s life? Imagine that data is captured and stored on an Azure  storage account. The new data will either be accessed frequently for further processing, reporting, analytics or some other use. Or, it will sit there for a long time and eventually become obsolete. The data may have logic and validations applied to it throughout either process. But at some point, it will come to the end of its useful life and be archived, purged, or both. This is where automatic data lifecycle management in Azure can help customers to optimize the size and costs of their storage accounts in the cloud. Azure Blob Storage lifecycle management (currently in public Preview) offers a rich, rule-based policy which you can use on GPv2 and Blob storage accounts to transition your data to their appropriate access tiers or expire at the end of its lifecycle. Lifecycle management policy helps you:

  * Transition blobs to a cooler storage tier (Hot to Cool, Hot to Archive, or Cool to Archive) to optimize for performance and cost
  * Delete blobs at the end of their lifecycles
  * Define rules to be executed once per day at the storage account level
  * Apply rules to containers or a subset of blobs (using prefixes as filters)

## Azure Storage Tiers

But first let&#8217;s have a word about the different tiers within Azure Storage / an Azure Storage Account. To support tiering, you need a Blob Storare General Purpose V2 (GPv2) Storage Account. However, if you have a GPv1 account, you can convert it easily to GPv2 by using the following methods:

**Azure CLI**

<pre class=""> az storage account update --access-tier Cool -n miruaccfoeus2 -g rg-stafailovertests-us</pre>

**PowerShell**

<pre class=""> Set-AzureRmStorageAccount -ResourceGroupName &lt;resource-group&gt; -AccountName &lt;storage-account&gt; -UpgradeToStorageV2</pre>

Here&#8217;s a short overview of the different tiers.

<div>
  <table style="border-collapse: collapse;" border="0">
    <colgroup> <col style="width: 179px;" /> <col style="width: 170px;" /> <col style="width: 121px;" /> <col style="width: 157px;" /> <col style="width: 157px;" /></colgroup> <tr style="background: #bdd6ee;">
      <td style="padding-left: 7px; padding-right: 7px; border: solid 0.5pt;">
        <strong>Tier</strong>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <strong>Tier Level</strong>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <strong>Intended Use</strong>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <strong>Use cases</strong>
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: solid 0.5pt; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        <strong>Access times</strong>
      </td>
    </tr>
    
    <tr>
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        Premium Storage (Preview)
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        Storage Account
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        High frequently access data require low access latencies
      </td>
      
      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
        -online transactions</p> 
        
        <p>
          -video rendering
        </p>
        
        <p>
          -static web content</td> 
          
          <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
            Micro- to Milliseconds
          </td></tr> 
          
          <tr>
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              Hot Storage
            </td>
            
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              Storage Account | Blob
            </td>
            
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              Frequently accessed data (default)
            </td>
            
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              -standard data processing
            </td>
            
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              Milliseconds
            </td>
          </tr>
          
          <tr>
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              Cold Storage
            </td>
            
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              Storage Account | Blob
            </td>
            
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              Infrequently accessed data, stored for at least 30 days
            </td>
            
            <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
              -media archive</p> 
              
              <p>
                -short term backups
              </p>
              
              <p>
                -DR
              </p>
              
              <p>
                -raw telemetry data</td> 
                
                <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                  Milliseconds
                </td></tr> 
                
                <tr>
                  <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: solid 0.5pt; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                    Archive Storage
                  </td>
                  
                  <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                    Blob
                  </td>
                  
                  <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                    Rarely or never accessed data stored for at least 180 days
                  </td>
                  
                  <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                    -archival sets</p> 
                    
                    <p>
                      -long term backup
                    </p>
                    
                    <p>
                      -compliance archive sets</td> 
                      
                      <td style="padding-left: 7px; padding-right: 7px; border-top: none; border-left: none; border-bottom: solid 0.5pt; border-right: solid 0.5pt;">
                        <15 hours
                      </td></tr> </tbody> </table> </div> 
                      
                      <h2>
                        Manual Tiering (changing access tier)
                      </h2>
                      
                      <p>
                        Changing the access tier can be performed on account or blob level, depending on the type of access tier. To change the access tier of a storage account from hot to cold:
                      </p>
                      
                      <p>
                        <strong>Azure CLI</strong>
                      </p>
                      
                      <pre class="">az storage account update --access-tier Cool -n miruaccfoeus2 -g rg-stafailovertests-us</pre>
                      
                      <p>
                        <strong>PowerShell</strong>
                      </p>
                      
                      <pre class="">Set-AzureRmStorageAccount -Name miruaccfoeus2 -ResourceGroupName rg-stafailovertests-us -AccessTier Cold -Force</pre>
                      
                      <p>
                        &nbsp;
                      </p>
                      
                      <p>
                        To change the access tier of a blobs older than 7 days (example)
                      </p>
                      
                      <p>
                        <strong>PowerShell</strong>
                      </p>
                      
                      <pre class="">#Set some constants
$stAccountName = "miruaccfoeus2"
$stContainerName = "demofiles"
$dtDaysRetention = 7
$stDesiredAccessTier = "Cool"


#Get storage account
$stAccountObj = Get-AzureRmStorageAccount | Where-Object {($_.StorageAccountName -eq $stAccountName) -and ($_.Kind -eq 'StorageV2' -or $_.Kind -eq 'BlobStorage')}
$stAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $stAccountObj.ResourceGroupName -Name $stAccountObj.StorageAccountName).Value[0]
$stAccountContext = New-AzureStorageContext -StorageAccountName $stAccountObj.StorageAccountName -StorageAccountKey $stAccountKey
$StContainers = Get-AzureStorageContainer -Context $stAccountContext

# Get all blobs of a specific container
$arrBlobs = @()
foreach ($iCont in $StorageContainers)
{
$arrBlobs += Get-AzureStorageBlob -Context $StAccountContext -Container $stContainerName
}

# Date Logic
$dtUTC = (Get-Date).ToUniversalTime()
$drRet = $dtUTC.AddDays(-$dtDaysRetention)

#Get matching Blobs
$arrBlobsMatch = $arrBlobs | Where-Object {$_.lastmodified.DateTime -le $dtDaysRetention}

# Change the Tier on the blobs
Foreach($iBlob in $arrBlobsMatch)
{
$iBlob.icloudblob.SetStandardBlobTier($stDesiredAccessTier)
}</pre>
                      
                      <h2>
                        Automatic, Policy based tiering
                      </h2>
                      
                      <p>
                        Now that we have seen how to manually change the access tier, it would be really cool to have an automatic tiering of data without the need of a scheduled script huh? Here is where the new feature &#8220;Azure Blob Storage Lifecycle Management&#8221;<br /> (by the time of writing this post the feature is in public preview, so do not use it in production).
                      </p>
                      
                      <p>
                        Azure Blob Storage lifecycle management (Preview) offers a rich, rule-based policy which you can use on GPv2 and Blob storage accounts to transition your data to their appropriate access tiers or expire at the end of its lifecycle.
                      </p>
                      
                      <p>
                        Lifecycle management policy helps to:<br /> Transition blobs to a cooler storage tier (Hot to Cool, Hot to Archive, or Cool to Archive) to optimize for performance and cost<br /> Delete blobs at the end of their lifecycles<br /> Define rules to be executed once per day at the storage account level<br /> Apply rules to containers or a subset of blobs (using prefixes as filters)<br /> To try this out in the preview phase, you first have to register the appropriate resource provider feature
                      </p>
                      
                      <p>
                        <strong>Azure CLI</strong>
                      </p>
                      
                      <pre class="">az feature register --namespace Microsoft.Storage --name DLM</pre>
                      
                      <p>
                        <strong>PowerShell</strong>
                      </p>
                      
                      <pre class="">Register-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage</pre>
                      
                      <p>
                        As this will take a little while, you can check the status of the process as well.
                      </p>
                      
                      <p>
                        <strong>Azure CLI</strong>
                      </p>
                      
                      <pre class="">az feature show --namespace Microsoft.Storage --name DLM</pre>
                      
                      <p>
                        <strong>PowerShell</strong>
                      </p>
                      
                      <pre class="">Get-AzureRmProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage</pre>
                      
                      <p>
                        Once the feature has been enabled we can create DLM policies.
                      </p>
                      
                      <p>
                        But first we have to install a <a href="https://www.powershellgallery.com/packages/AzureRM.Storage/5.0.3-preview">preview version</a> of the AzureRM.Storage PowerShell module. After we&#8217;ve done this, there are new commandlets available.
                      </p>
                      
                      <p>
                        So then, first we check if we have any available storage management policies assigned to the storage account.
                      </p>
                      
                      <pre class="">Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName rg-stafailovertests-us -StorageAccountName miruaccfoeus2</pre>
                      
                      <p>
                        If this is not the case we get the following error (IMHO, a warning message would be sufficient :-))
                      </p>
                      
                      <p>
                        <span style="color: #ff0000;">Get-AzureRmStorageAccountManagementPolicy : No ManagementPolicy found for account miruaccfoeus2</span><br /> <span style="color: #ff0000;">At line:1 char:1</span><br /> <span style="color: #ff0000;">+ Get-AzureRmStorageAccountManagementPolicy -ResourceGroupName rg-stafa &#8230;</span><br /> <span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span><br /> <span style="color: #ff0000;">+ CategoryInfo : CloseError: (:) [Get-AzureRmStor&#8230;anagementPolicy], CloudException</span><br /> <span style="color: #ff0000;">+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Management.Storage.GetAzureStorageAccountManagementPolicyCommand</span>
                      </p>
                      
                      <p>
                        Ok, let&#8217;s create and assign a new policy.
                      </p>
                      
                      <pre class="">#Define the Policy and the Rules
$rules = '{
  "version": "0.5",
  "rules": [ 
    {
      "name": "MIRUDemoDLM", 
      "type": "Lifecycle", 
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "demofiles/DLMFiles/docs" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 14 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 120 },
            "delete": { "daysAfterModificationGreaterThan": 1825 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}'</pre>
                      
                      <p>
                        Assign the Policy
                      </p>
                      
                      <pre class="">Set-AzureRmStorageAccountManagementPolicy -ResourceGroupName rg-stafailovertests-us -StorageAccountName miruaccfoeus2 -Policy $rules</pre>
                      
                      <p>
                        Now the output should look like this.
                      </p>
                      
                      <p>
                        <a href="http://www.miru.ch/wp-content/uploads/2018/11/storagemgmtpolrule-1.png"><img class="alignnone size-large wp-image-5008" src="http://www.miru.ch/wp-content/uploads/2018/11/storagemgmtpolrule-1-1024x440.png" alt="" width="1024" height="440" srcset="http://www.miru.ch/wp-content/uploads/2018/11/storagemgmtpolrule-1-1024x440.png 1024w, http://www.miru.ch/wp-content/uploads/2018/11/storagemgmtpolrule-1-300x129.png 300w, http://www.miru.ch/wp-content/uploads/2018/11/storagemgmtpolrule-1-768x330.png 768w, http://www.miru.ch/wp-content/uploads/2018/11/storagemgmtpolrule-1.png 1380w" sizes="(max-width: 1024px) 100vw, 1024px" /></a>
                      </p>
                      
                      <p>
                        With this we have now the following rules on the path demofiles/DLMFiles/docs assigned
                      </p>
                      
                      <ul>
                        <li>
                          Files, modified more than 14 days ago are moved to cool tier
                        </li>
                        <li>
                          Files, modified more then 120 days ago are archived to the archive tier
                        </li>
                        <li>
                          Files, modified more than 5 years are being deleted
                        </li>
                        <li>
                          Snaphots older than 90 days are removed as well
                        </li>
                      </ul>
                      
                      <p>
                        For a list of other possible rules see: <a href="https://docs.microsoft.com/en-us/azure/storage/common/storage-lifecycle-managment-concepts">https://docs.microsoft.com/en-us/azure/storage/common/storage-lifecycle-managment-concepts</a>
                      </p>
                      
                      <p>
                        <strong>Note:</strong> Lifecycle policy is executed once a day by the platform. Once a new policy is set, it can take up to 24 hours for actions such as tiering or deletion to be initiated and executed.
                      </p>
                      
                      <p>
                        &nbsp;
                      </p>
                      
                      <p>
                        That&#8217;s it so far. In a future post I&#8217;ll cover legal / WORM capabilities of Azure Storage, so stay tuned!
                      </p>
                      
                      <p>
                        &nbsp;
                      </p>
                      
                      <p>
                        &nbsp;
                      </p>