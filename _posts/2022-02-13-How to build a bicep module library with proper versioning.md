---
id: 5211
title: How to build a bicep module library with proper versioning
date: 2022-02-13T17:00:00+02:00
author: Michael Rüefli
layout: post
permalink: /how-to-build-a-bicep-module-library/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Microsoft
  - DevOps
  - ARM

tags:
  - DevOps
  - SecDevOps
  - InfrastructureAsCode
---

In this post I'm going to show how to create a bicep module library with a proper versioning and CI integration.

## Why create an own module library
Despite the fact that you find a lot of useful templates in bicep repository on Github, you may want to create your own reference library for your organization or customers. Using modules makes your bicep templates much more reusable and modular

<b>Different Possibilities</b>
So let's talk about how to create a module library
There are different ways to achieve this, each with it's pros and cons.
- Dedicated Git repository, with Git submodules
- Azure Template Spec
- Azure Container Registry

In this post I'm focusing on the ACR. Usually I'm using an Azure Container Registry to store and pull container images for AKS or ACI. But since bicep version xx, an ACR can be used to store bicep templates. This allows us to store bicep modules centrally and with a proper versioning using artifact tags.

## Getting started
1. Bicep Version
First you need to make sure, you have the correct bicep version installed on your dev machine. I'm using the bicep extension of azure cli.
```bash
az bicep version
az bicep upgrade
```

2. Create Git Repository
Create a new Git Repo in Azure DevOps, Github or any other compatible platform

3. Create a proper folder structure in the Repo
Create subfolders, matching the resource provider name or the resource type. Although this is an optional step, it helps organizing your modules
Such as:
- Compute
- Network
- Security
- Storage 

..and so on.

4. Create a container registry
Create a container registry where you want to store the modules. As an example I've added a bicep template.
```yaml
param name string
param location string

@allowed([
  'Basic'
  'Standard'
  'Premium'
])
param sku string
@allowed([
  'Enabled'
  'Disabled'
])
param publicNetworkAccess string


resource acr 'Microsoft.ContainerRegistry/registries@2020-11-01-preview' = {
  name: name
  location: location
  identity:{
    type:'SystemAssigned'    
  }
  sku:{
    name:sku
  }
  properties:{
    publicNetworkAccess:publicNetworkAccess
    zoneRedundancy: 'Disabled'    
  }
}

output registryId string = acr.id
```

<b>Deploy the registry</b>
```
az deployment group create --resource-group <your RG> --name bicepacr --template-file .\myAcr.bicep --parameters name=myacr001 --location=westeurope
```
<b>Assign push permissions to your service principal</b>
```
az role assignment create --assignee <object id of your app registration> --role "ACR Push" --scope <registryId>
```
<b>Assign pull permissions to your devops engineers</b>
```
az role assignment create --assignee <object id of your AAD group> --role "ACR Pull" --scope <registryId>
```


5. Create bicep config file
- In the root directory of the repo create a file named: bicepconfig.json
- Edit the file and add the following lines
```yaml
{
  "moduleAliases": {
      "br": {
        "myModules": {
            "registry": "myacr001.azurecr.io",
            "modulePath": "bicep/modules"
        }
      }
  }
}
```
This creates an alias, which can be used in master template for referencing the modules in the registry.

7. Create CI pipeline
Create a CI pipeline for continous build and push of the modules.
```yaml

trigger:
- "*"

variables:
  isMain: $[eq(variables['Build.SourceBranch'], 'refs/heads/main')]
  registryLoginSrv: myacr001.azurecr.io
  serviceConnection: bicepbuild-connection
  buildOnlychangedModules: 'true'

jobs:
  - job: Build
    displayName: Build and publish Bicep Modules
    pool:
      vmImage: 'ubuntu-latest'

    steps:
    - task: AzureCLI@2
      displayName: 'Get changed files'
      inputs:
        azureSubscription: $(serviceConnection)
        scriptType: 'pscore'
        scriptLocation: 'inlineScript'
        failOnStandardError: true
        inlineScript: |
          Write-Host "BuildOnlyChangedModules: $(buildOnlychangedModules)"
          $targetfolder = "$(Build.StagingDirectory)" + "/"
          function CopyFiles{
              param( [string]$source )

              $target = $targetfolder + $source

              New-Item -Force $target
              copy-item $source $target -Force
          }

          If ("$(buildOnlychangedModules)" -eq 'true') {
            $changes = git diff --name-only --relative --diff-filter AMR HEAD^ HEAD .
          }
          else {
            $changes = Get-ChildItem $(Build.SourcesDirectory) -Filter "*.bicep" -Recurse
          }
          $changes
          if ($changes -is [string]){ 
            CopyFiles $changes 
          }
          else {
              if ($changes -is [array])
              {       
                  foreach ($change in $changes)
                  { 
                    CopyFiles $change 
                  }
              }
          }


    - task: AzureCLI@2
      displayName: 'Build Bicep Files'
      inputs:
        azureSubscription: $(serviceConnection)
        scriptType: 'pscore'
        scriptLocation: 'inlineScript'
        failOnStandardError: true
        inlineScript: |
          az bicep install
          az bicep version
          $bicepFiles = Get-ChildItem $(Build.StagingDirectory) -Filter "*.bicep" -Recurse
          Foreach ($file in $bicepFiles) {
            write-host "Building: $($file.FullName)"
            az bicep build --file $file.FullName
          }

    - task: AzureCLI@2
      condition: and(succeeded(), eq(variables.isMain, 'true'))
      displayName: 'Push Bicep Files to Registry'
      inputs:
        azureSubscription: $(serviceConnection)
        scriptType: 'pscore'
        scriptLocation: 'inlineScript'
        failOnStandardError: true
        inlineScript: |
          az bicep install
          az bicep version
          $bicepFiles = Get-ChildItem $(Build.StagingDirectory) -Filter "*.bicep" -Recurse
          Foreach ($file in $bicepFiles) {
            $folderName = (($file.FullName -split '/')[-2]).tolower()
            $fileName = (Split-Path -Path $file.FullName -LeafBase).tolower() 
            $targetRepository = "/bicep/modules/$folderName/$fileName"
            write-host "pushing: $fileName with Version: $(Build.BuildNumber)"
            az bicep publish --file $file.FullName --target br:$(registryLoginSrv)$($targetRepository):$(Build.BuildNumber)
          }
```

This pipeline will...
- Build the changed bicep files at each commit
- Push the changed bicep files to the registry on each pull request to the main branch, using the build number as a version tag

Make sure you protect the main branch of your repo and add a build validation check. With this you can make sure, only successfully compiled modules land in your registry.

8. Use the modules in your main bicep files
```yaml
targetScope = 'subscription'
var subscriptionId string = '9c8c3766-89ae-4acb-9c23-5f1deb8a70e7'

resource myRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'myTestResourceGroup'
  scope:subscription(subscriptionId)
}


module uidAgic 'br/myModules:security/usermanagedidentity:20220125.3' = {
  scope: myRG
  name: 'UserManagedIdentityAGIC'
  params: {
    identityName: 'd-umi-aks-agic'
    location: location
  }
}

```

As always, if you have comments, questions or you find bugs. Feel free to reach out via Github / Twitter or LinkedIn.
