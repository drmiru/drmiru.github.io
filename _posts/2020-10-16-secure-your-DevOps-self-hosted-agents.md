---
id: 5209
title: Secure your Azure DevOps agents and pipelines
date: 2020-10-16T18:53:34+02:00
author: Michael Rüefli
layout: post
permalink: /secure-devops-agents/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Microsoft
  - DevOps
  - Security

tags:
  - Security
  - DevOps
---
## Starting point
Azure DevOps provides several ways to host your pipelines. While the public AzureDevops service provides hosted and self-hosted agent functionality, Azure DevOps Server (formely TFS), only provides the self-hosted variant.
I personally prefer to work with the public service for the following reasons:

- no infrastructure and application to maintain
- secure access via Azure AD / MFA / Conditional Access
- features and available extensions are much more up to date by the nature of it's SaaS architecture

## Differences regarding the agents
While the microsoft hosted agents are maintained by Microsoft, self-hosted agents have to be managed. Self-hosted agents are basically virtual machines running Windows or Linux OS with the Azure DevOps Agent installed. It is also possible to spin up containers with the DevOps agent running inside.

## Costs
Self-hosted agents have higher costs, just by the fact that you need to spin up and run virtual machines permanently.
Regarding the parllel job / pipeline executions, it does not matter which type you choose. Only Azure DevOps Server (on-premises) does not care how many agents are executing tasks in parallel.

## Which agent type to choose?
If you're not running the on-premises version, there are only a few reasons why to go for self-hosted agents:

- Specific HW requirements (eg. sizing, performance)
- Access to private network endpoints required
- State persistence between different jobs / pipelines
- Complex build/release tools which take just too long to be installed during pipeline runtime

## What about security?
There are several areas you need to consider to DevOps chain secure and not a target for any attacker.

### General guidelines

- Don't use high privileged identities for service connections
- Limit the scope of service connections, use dedicated service connections for different subscriptions or even resource groups where possible
- Use certificates rather than secrets to login with service principals. See the chapter "prevent credential caching" below for more information
- Use Azure Key Vault instead of secured variables inside Azure DevOps to securely store/retrieve secrets, keys and certificates
- Do not log/output secrets to the console or a log file
- Watch the audit log for malicious actions: https://dev.azure.com/"org_name"/_settings/audit


#### For self-hosted agents
- Use different agent / agent pools for different projects, where security and seggregation of duty is required
- Make sure you implement a cleanup process to regularly cleanup temporary build/release data on the agents build folder
- Don't use high privileged accounts to run the agent service
- Don't rely on PATH settings, always use fully qualified paths to call your executables

## Pay attention to cached credentials!
![whaaat](../images/2020/shockedfry.jpg)

Using Azure PowerShell or AzureCLI has an effect I call: "the good, the bad and the ugly". Both tools cache your credentials and access tokens in the user profile of the user executing the az login or connect-azaccount. You don't want cached credentials luring around, not on your self-hosted agents nor on the microsoft hosted ones.
If you use service principal based login, AzureRmContext.json in %userprofile%/.Azure even contains the secret used to log in!
For more details see this post from Mikhail: [How Azure CLI Manages Your Access Tokens](https://mikhail.io/2019/07/how-azure-cli-manages-access-tokens/)

#### What now?
---------

For Azure PowerShell
--------------------
If you use Azure PowerShell, there's an easy way to prevent credential caching in AzureRmContext.json. Depending on the type of the agent you want to change the scope for this. Possible values are: Process | CurrentUser

```PowerShell
Disable-AzContextAutoSave -Scope Process | out-null
```

Be aware that this does not affect the cached tokens, which are still there.
If you want to clean already cached credentials & tokens:
```PowerShell
Set-Location $ENV:USERPROFILE\.Azure
get-childitem -Path .\ | Remove-item -Recurse -Force
```

For Azure CLI
-------------
Azure CLI does not provide a method to disable credential caching. So make sure you remove the contents of the .\Azure directory from the agent's profile on self-hosted agents, as the very last step in your pipeline tasks. Also make sure the task runs whether the precedessor-steps did fail or not.


--> Hope this helps to make your CI/CD environment a bit more secure :-)
