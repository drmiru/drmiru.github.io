---
id: 5209
title: What if - you just need to connect a virtual network interface 
date: 2020-06-17T19:10:34+02:00
author: Michael Rüefli
layout: post
permalink: /PowerShell-7-behind-proxy/
bfa_virtual_template:
  - hierarchy
categories:
  - Azure
  - Microsoft
  - DevOps
  - Networking
tags:
  - RBAC
  - ARM
---
## Starting point
Larger enterprises might have more complex requirements controlling network traffic in Azure as well as maintaining the "least privilege" principle. Most of them are using a so called "hub-spoke" architecture, where the hub network is placed within a dedicated "core subscription". Also other virtual networks are seperated into dedicated subscriptions or just resource groups.

## Issue description
What if you want to delegate the permission to create virtual machines within a Dev/Test subscription to certain users. As you might guess, they also need other permissions than just on the microsoft.compute resource provider. Depending on the architecture, the are not allowed to create a virtual network, and are required to use an existing one.
The vnet might sit within another resource group, where the user does not have contributor rights.
By default the user needs at least "Network Contributor" role to attach the VM's network interface to a virtual subnet. With this role, he also gains a lot of other permissions, in fact, all permissions to change the vNet and subnet configuration.

To fight this, we can create a custom RBAC role and assign it to the affected user on the corresponding resource group containing the virtual subnet, where the vNIC is going to be attached at VM creation.

## How to do it

- First, we create a new RBAC role definition
- Create the following json file (make sure you change the scope ids accordingly)

```javascript
{
  "Name":"SubnetUser",
  "description":"Can attach network interfaces to subnets",
  "actions":[
      "Microsoft.Resources/subscriptions/resourceGroups/read",
      "Microsoft.Network/virtualNetworks/read",
      "Microsoft.Network/virtualNetworks/subnets/join/action"
    ],
  "assignableScopes": [
      "/providers/Microsoft.Management/managementGroups/<myManagementGroup>",
      "/subscriptions/00000000-0000-0000-0000-000000000000"
    ]
}
```
- Save it as SubnetUser.json

- Import the definition

```PowerShell
az role definition create --role-definition .\SubnetUser.json
```

- Assign the role to the user on he appropriate scope (e.g. the resource group containing the managed network)

```PowerShell
az role assignment create --assignee-object-id $aadObj.objectId --assignee-principal-type Group --role $row.RoleName --scope $scopeId
```