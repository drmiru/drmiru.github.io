---
id: 5077
title: Using Azure DevOps Pipelines with Azure Automation
date: 2019-02-15T23:45:35+02:00
author: Michael Rüefli
layout: post
guid: http://www.miru.ch/?p=5077
permalink: /using-azure-devops-pipelines-with-azure-automation/
bfa_virtual_template:
  - hierarchy
image: /wp-content/uploads/2019/02/2019-02-15-22_49_09-AutomationLab-CD-Pipelines.png
categories:
  - Azure Automation
  - DevOps
  - Microsoft
tags:
  - Azure
  - Azure Automation
  - DevOps
  - Powershell
---
Azure Automation is a robust, cross platform and powerful automation engine for script based process-automation. If you are unfamiliar with Azure Automation, have a look at the official [docs here](https://docs.microsoft.com/en-us/azure/automation/automation-intro).

### Source Control

Azure Automation has native support for GitHub and Azure DevOps (vsoGit) repositories used as source control for runbooks. The basic setup of source control integration is a quite simple and easy step. However, the concept behind this out of the box functionality might not fit a CI/CD concept. Azure Automation source control sync jobs are built to sync code changes either automatically or manually into the runbooks container. To learn about the basic steps to activate source control see the [docs here](https://docs.microsoft.com/en-us/azure/automation/source-control-integration).

While this might fit basic requirements, I recently had a project where the customer had the requirement to integrate runbook code changes into a CI/CD pipeline. As he is already using Azure DevOps pipelines the way to the new solution was pretty easy.



So, let&#8217;s see how this is built.

#### Step by Step Procedure

  1. De-activate auto sync (we don&#8217;t want to sync the runbooks automatically on code change commits)
  2. Create a new script runbook (Start-DevOpsSourceControlSync), which initiates the manual synchronization. We will call the runbook at a later stage from the CD pipeline

<pre class="wp-block-code"><code>param(
    [Parameter(mandatory=$false)]
    [string]$connectionName = "AzureRunAsConnection",

    [Parameter(mandatory=$false)]
    [string]$automationAccountName = "d-aut-automationlab-01",

    [Parameter(mandatory=$false)]
    [string]$resourceGroupName = "rgr-automationdemo-d"
)
$errorActionPreference = "stop"

# Import Modules
Import-Module az.Accounts
Import-Module az.Automation

# Get Azure Run As Connection Name
$connectionName = "AzureRunAsConnection"
# Get the Service Principal connection details for the Connection name
$servicePrincipalConnection = Get-AutomationConnection -Name $connectionName         

# Logging in to Azure AD with Service Principal
"Logging in to Azure AD..."
Connect-AzAccount -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

# Get Automation Account Object
$automationAccount = Get-AzAutomationAccount -Name $automationAccountName -ResourceGroupName $resourceGroupName 

#start source control sync job
$sc = Get-AzAutomationSourceControl -AutomationAccountName $automationAccount.AutomationAccountName -ResourceGroupName $automationAccount.ResourceGroupName 
$syncJob = Start-AzAutomationSourceControlSyncJob -SourceControlName $sc.Name -AutomationAccountName $automationAccount.AutomationAccountName -ResourceGroupName $automationAccount.ResourceGroupName 
$syncJobResult = Get-AzAutomationSourceControlSyncJob -SourceControlName $sc.Name -JobId $syncJob.SourceControlSyncJobId -AutomationAccountName $automationAccount.AutomationAccountName -ResourceGroupName $automationAccount.ResourceGroupName
# Get Sync Job Status, wait for completion 
while ($syncJobResult.ProvisioningState -eq 'New' -or $syncJobResult.ProvisioningState -eq 'Running'){
    $syncJobResult = Get-AzAutomationSourceControlSyncJob -SourceControlName $sc.Name -JobId $syncJob.SourceControlSyncJobId -AutomationAccountName $automationAccount.AutomationAccountName -ResourceGroupName $automationAccount.ResourceGroupName
    write-output "waiting for sync job to complete"
    start-sleep -Seconds 3
}
$syncJobResult
</code></pre>

4. Create Service Connection  
<figure class="wp-block-image">

<img src="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_16_55-Service-connection_-ARM-AutomationDemo-Pipelines.png" alt="" class="wp-image-5081" srcset="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_16_55-Service-connection_-ARM-AutomationDemo-Pipelines.png 911w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_16_55-Service-connection_-ARM-AutomationDemo-Pipelines-300x158.png 300w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_16_55-Service-connection_-ARM-AutomationDemo-Pipelines-768x405.png 768w" sizes="(max-width: 911px) 100vw, 911px" /> </figure> <figure class="wp-block-image"><img src="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_23_38-Service-connection_-ARM-AutomationDemo-Pipelines.png" alt="" class="wp-image-5082" srcset="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_23_38-Service-connection_-ARM-AutomationDemo-Pipelines.png 602w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_23_38-Service-connection_-ARM-AutomationDemo-Pipelines-290x300.png 290w" sizes="(max-width: 602px) 100vw, 602px" /></figure> 

5. Create Build Pipeline<figure class="wp-block-image">

<img src="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_27-AutomationLab-CI-Azure-DevOps-Services-1024x441.png" alt="" class="wp-image-5084" srcset="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_27-AutomationLab-CI-Azure-DevOps-Services-1024x441.png 1024w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_27-AutomationLab-CI-Azure-DevOps-Services-300x129.png 300w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_27-AutomationLab-CI-Azure-DevOps-Services-768x331.png 768w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_27-AutomationLab-CI-Azure-DevOps-Services.png 1386w" sizes="(max-width: 1024px) 100vw, 1024px" /> </figure> <figure class="wp-block-image"><img src="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_48-AutomationLab-CI-Azure-DevOps-Services-1024x399.png" alt="" class="wp-image-5085" srcset="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_48-AutomationLab-CI-Azure-DevOps-Services-1024x399.png 1024w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_48-AutomationLab-CI-Azure-DevOps-Services-300x117.png 300w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_48-AutomationLab-CI-Azure-DevOps-Services-768x299.png 768w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_28_48-AutomationLab-CI-Azure-DevOps-Services.png 1225w" sizes="(max-width: 1024px) 100vw, 1024px" /></figure> 

6. Create Release Pipeline  
<figure class="wp-block-image">

<img src="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_29_51-AutomationLab-CD-Pipelines.png" alt="" class="wp-image-5086" srcset="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_29_51-AutomationLab-CD-Pipelines.png 655w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_29_51-AutomationLab-CD-Pipelines-300x201.png 300w" sizes="(max-width: 655px) 100vw, 655px" /> </figure> <figure class="wp-block-image"><img src="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_30_53-AutomationLab-CD-Pipelines-1024x355.png" alt="" class="wp-image-5087" srcset="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_30_53-AutomationLab-CD-Pipelines-1024x355.png 1024w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_30_53-AutomationLab-CD-Pipelines-300x104.png 300w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_30_53-AutomationLab-CD-Pipelines-768x266.png 768w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_30_53-AutomationLab-CD-Pipelines.png 1597w" sizes="(max-width: 1024px) 100vw, 1024px" /></figure> <figure class="wp-block-image"><img src="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_19_00-AutomationLab-CD-Pipelines-1024x446.png" alt="" class="wp-image-5083" srcset="http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_19_00-AutomationLab-CD-Pipelines-1024x446.png 1024w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_19_00-AutomationLab-CD-Pipelines-300x131.png 300w, http://www.miru.ch/wp-content/uploads/2019/02/2019-02-15-22_19_00-AutomationLab-CD-Pipelines-768x335.png 768w" sizes="(max-width: 1024px) 100vw, 1024px" /><figcaption>Add the following inline code</figcaption></figure> 

<pre class="wp-block-code"><code>#Define constants
$automationAccountName = "d-aut-automationlab-01"
$resourceGroupName = "rgr-automationdemo-d"

#Get automation account object
$automationAccount = Get-AzureRMAutomationAccount -Name $automationAccountName -ResourceGroupName $resourceGroupName 

#start sync runbook
$job = Start-AzureRMAutomationRunbook -Name "Start-DevOpsSourceControlSync" -AutomationAccountName $automationAccount.AutomationAccountName -ResourceGroupName $automationAccount.ResourceGroupName -Parameters $runbookParameters 

# Get Job Status, wait for completion or failure
$jobResult = Get-AzureRMAutomationJob -Id $job.JobId -AutomationAccountName $automationAccount.AutomationAccountName -ResourceGroupName $automationAccount.ResourceGroupName
while ($jobResult.Status -eq 'New' -or $jobResult.Status -eq 'Running'){
    $jobResult = Get-AzureRMAutomationJob -Id $job.JobId -AutomationAccountName $automationAccount.AutomationAccountName -ResourceGroupName $automationAccount.ResourceGroupName
    write-output "waiting for job to complete"
    start-sleep -Seconds 3
}
$jobResult.Status
</code></pre>

As you can see, we now have a fully integrated CI/CD pipeline, everytime someone pushes code changes to our master branch. Needless to note that in production environments you&#8217;d have multiple stages for the deployments and branch policy for master branch, preventing direct pushes. But for the purpose of this demo I wanted to keep the steps as simple as possible.



Hope provides some ideas&#8230;

Cheers

Michael