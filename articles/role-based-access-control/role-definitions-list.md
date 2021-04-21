---
title: Azure-roldefinities weergeven - Azure RBAC
description: Leer hoe u ingebouwde en aangepaste Azure-rollen kunt gebruiken met Azure Portal, Azure PowerShell, Azure CLI of REST API.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/26/2021
ms.author: rolyon
ms.openlocfilehash: b285755d24cdbf1f8ef06eb850fc218a00734f16
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771727"
---
# <a name="list-azure-role-definitions"></a>Azure-roldefinities weergeven

Een roldefinitie is een verzameling machtigingen die kunnen worden uitgevoerd, zoals lezen, schrijven en verwijderen. Het wordt meestal gewoon een rol genoemd. [Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](overview.md) heeft meer dan 120 [ingebouwde](built-in-roles.md) rollen of u kunt uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe u een lijst maakt met de ingebouwde en aangepaste rollen die u kunt gebruiken om toegang te verlenen tot Azure-resources.

Zie Machtigingen voor beheerdersrollen in Azure Active Directory voor een overzicht van [de Azure Active Directory.](../active-directory/roles/permissions-reference.md)

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Een lijst met alle rollen maken

Volg deze stappen om alle rollen in de Azure Portal.

Als u een bijgewerkte functie-ervaring wilt bekijken, bekijkt u het tabblad **Rollen (preview),** dat momenteel in openbare preview is. Op **het tabblad Rollen (preview)** wordt dezelfde lijst met rollen weergegeven als het tabblad **Rollen** met een aantal extra functies. U kunt beide rollen tabblad gebruiken om te werken met uw rollen, maar als u aangepaste rollen maakt of verwijdert, moet u de pagina mogelijk handmatig vernieuwen om de meest recente wijzigingen weer te geven.

#### <a name="roles"></a>[Rollen](#tab/roles/)

1. Klik in Azure Portal op **Alle services** en selecteer vervolgens een bereik. U kunt bijvoorbeeld **Beheergroepen,** **Abonnementen,** **Resourcegroepen** of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op **het tabblad** Rollen voor een lijst met alle ingebouwde en aangepaste rollen.

   U kunt het aantal gebruikers en groepen zien dat is toegewezen aan elke rol in het huidige bereik.

   ![Lijst met rollen](./media/role-definitions-list/roles-list-current.png)

#### <a name="roles-preview"></a>[Rollen (preview)](#tab/roles-preview/)

1. Klik in Azure Portal op **Alle services** en selecteer vervolgens een bereik. U kunt bijvoorbeeld **Beheergroepen,** **Abonnementen,** **Resourcegroepen** of een resource selecteren.

1. Klik op de specifieke resource.

1. Klik op **Toegangsbeheer (IAM)**.

1. Klik op **het tabblad Rollen (preview)** om een lijst met alle ingebouwde en aangepaste rollen weer te geven.

   ![Lijst met rollen met preview-ervaring](./media/role-definitions-list/roles-list.png)

1. Als u de machtigingen voor een bepaalde rol wilt zien, klikt u in **de kolom Details** op de **koppeling** Weergeven.

    Er wordt een deelvenster Machtigingen weergegeven.

1. Klik op **het tabblad Machtigingen** om de machtigingen voor de geselecteerde rol weer te geven en te doorzoeken.

   ![Rolmachtigingen met preview-ervaring](./media/role-definitions-list/role-permissions.png)

---

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Een lijst met alle rollen maken

Als u alle rollen in Azure PowerShell, gebruikt [u Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition)

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Een roldefinitie in een lijst zetten

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om de details van een specifieke rol weer te geven.

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Een roldefinitie in JSON-indeling opsmaken

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om een rol in JSON-indeling weer te geven.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Machtigingen van een roldefinitie opsnachtigingen

Gebruik [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition)om de machtigingen voor een specifieke rol weer te geven.

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>Een lijst met alle rollen maken

Gebruik az role definition list om alle rollen in Azure CLI [weer te geven.](/cli/azure/role/definition#az_role_definition_list)

```azurecli
az role definition list
```

In het volgende voorbeeld worden de naam en beschrijving van alle beschikbare roldefinities vermeld:

```azurecli
az role definition list --output json --query '[].{roleName:roleName, description:description}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role"
  },

  ...

]
```

In het volgende voorbeeld worden alle ingebouwde rollen vermeld.

```azurecli
az role definition list --custom-role-only false --output json --query '[].{roleName:roleName, description:description, roleType:roleType}'
```

```json
[
  {
    "description": "Can manage service and the APIs",
    "roleName": "API Management Service Contributor",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Can manage service but not the APIs",
    "roleName": "API Management Service Operator Role",
    "roleType": "BuiltInRole"
  },
  {
    "description": "Read-only access to service and APIs",
    "roleName": "API Management Service Reader Role",
    "roleType": "BuiltInRole"
  },
  
  ...

]
```

### <a name="list-a-role-definition"></a>Een roldefinitie in een lijst zetten

Gebruik az role definition list om de details van een [rol weer te geven.](/cli/azure/role/definition#az_role_definition_list)

```azurecli
az role definition list --name {roleName}
```

In het volgende voorbeeld wordt de *roldefinitie Inzender* vermeld:

```azurecli
az role definition list --name "Contributor"
```

```json
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action",
          "Microsoft.Blueprint/blueprintAssignments/write",
          "Microsoft.Blueprint/blueprintAssignments/delete"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-permissions-of-a-role-definition"></a>Machtigingen van een roldefinitie op een lijst zetten

In het volgende voorbeeld worden alleen de *acties en* *notActions van* de rol *Inzender* vermeld.

```azurecli
az role definition list --name "Contributor" --output json --query '[].{actions:permissions[0].actions, notActions:permissions[0].notActions}'
```

```json
[
  {
    "actions": [
      "*"
    ],
    "notActions": [
      "Microsoft.Authorization/*/Delete",
      "Microsoft.Authorization/*/Write",
      "Microsoft.Authorization/elevateAccess/Action",
      "Microsoft.Blueprint/blueprintAssignments/write",
      "Microsoft.Blueprint/blueprintAssignments/delete"
    ]
  }
]
```

In het volgende voorbeeld worden alleen de acties van de rol *Inzender voor virtuele machines* vermeld.

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Authorization/*/read",
    "Microsoft.Compute/availabilitySets/*",
    "Microsoft.Compute/locations/*",
    "Microsoft.Compute/virtualMachines/*",
    "Microsoft.Compute/virtualMachineScaleSets/*",
    "Microsoft.Compute/disks/write",
    "Microsoft.Compute/disks/read",
    "Microsoft.Compute/disks/delete",
    "Microsoft.DevTestLab/schedules/*",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
    "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

    ...

    "Microsoft.Storage/storageAccounts/listKeys/action",
    "Microsoft.Storage/storageAccounts/read",
    "Microsoft.Support/*"
  ]
]
```

## <a name="rest-api"></a>REST-API

### <a name="list-role-definitions"></a>Lijst met roldefinities weergeven

Als u roldefinities wilt weergeven, gebruikt [u de](/rest/api/authorization/roledefinitions/list) REST API. Als u de resultaten wilt verfijnen, geeft u een bereik en een optioneel filter op.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. Vervang in de URI *{scope}* door het bereik waarvoor u de roldefinities wilt weergeven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |

    In het vorige voorbeeld is microsoft.web een resourceprovider die verwijst naar een App Service exemplaar. U kunt ook andere resourceproviders gebruiken en het bereik opgeven. Zie Azure-resourceproviders en [-typen en](../azure-resource-manager/management/resource-providers-and-types.md) ondersteunde bewerkingen voor [Azure-resourceproviders voor meer informatie.](resource-provider-operations.md)  
     
1. Vervang *{filter} door* de voorwaarde die u wilt toepassen om de lijst met roldefinities te filteren.

    > [!div class="mx-tableFixed"]
    > | Filter | Description |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Bevat roldefinities voor het opgegeven bereik en eventuele subbereiken. |
    > | `$filter=type+eq+'{type}'` | Bevat roldefinities van het opgegeven type. Het type rol kan `CustomRole` of `BuiltInRole` zijn. |

De volgende aanvraag bevat aangepaste roldefinities op abonnementsbereik:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=type+eq+'CustomRole'
```

Hieronder ziet u een voorbeeld van de uitvoer:

```json
{
    "value": [
        {
            "properties": {
                "roleName": "Billing Reader Plus",
                "type": "CustomRole",
                "description": "Read billing data and download invoices",
                "assignableScopes": [
                    "/subscriptions/{subscriptionId1}"
                ],
                "permissions": [
                    {
                        "actions": [
                            "Microsoft.Authorization/*/read",
                            "Microsoft.Billing/*/read",
                            "Microsoft.Commerce/*/read",
                            "Microsoft.Consumption/*/read",
                            "Microsoft.Management/managementGroups/read",
                            "Microsoft.CostManagement/*/read",
                            "Microsoft.Billing/invoices/download/action",
                            "Microsoft.CostManagement/exports/*"
                        ],
                        "notActions": [
                            "Microsoft.CostManagement/exports/delete"
                        ]
                    }
                ],
                "createdOn": "2020-02-21T04:49:13.7679452Z",
                "updatedOn": "2020-02-21T04:49:13.7679452Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId1}",
            "type": "Microsoft.Authorization/roleDefinitions",
            "name": "{roleDefinitionId1}"
        }
    ]
}
```

### <a name="list-a-role-definition"></a>Een roldefinitie in een lijst zetten

Als u de details van een specifieke rol wilt weergeven, gebruikt u de functiedefinities [- Get](/rest/api/authorization/roledefinitions/get) of Role Definitions - Get [By Id](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    Voor een roldefinitie op mapniveau kunt u deze aanvraag gebruiken:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Vervang *{scope}* binnen de URI door het bereik waarvoor u de roldefinitie wilt gebruiken.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |
     
1. Vervang *{roleDefinitionId} door* de roldefinitie-id.

De volgende aanvraag bevat de [roldefinitie Lezer:](built-in-roles.md#reader)

```http
GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7?api-version=2015-07-01
```

Hieronder ziet u een voorbeeld van de uitvoer:

```json
{
    "properties": {
        "roleName": "Reader",
        "type": "BuiltInRole",
        "description": "Lets you view everything, but not make any changes.",
        "assignableScopes": [
            "/"
        ],
        "permissions": [
            {
                "actions": [
                    "*/read"
                ],
                "notActions": []
            }
        ],
        "createdOn": "2015-02-02T21:55:09.8806423Z",
        "updatedOn": "2019-02-05T21:24:35.7424745Z",
        "createdBy": null,
        "updatedBy": null
    },
    "id": "/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
}
```

## <a name="next-steps"></a>Volgende stappen

- [Ingebouwde Azure-rollen](built-in-roles.md)
- [Aangepaste Azure-rollen](custom-roles.md)
- [Azure-roltoewijzingen vermelden met behulp van Azure Portal](role-assignments-list-portal.md)
- [Azure-rollen toewijzen met behulp van de Azure Portal](role-assignments-portal.md)
