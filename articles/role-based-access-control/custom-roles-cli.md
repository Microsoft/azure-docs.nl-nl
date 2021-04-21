---
title: Aangepaste Azure-rollen maken of bijwerken met behulp van Azure CLI - Azure RBAC
description: Meer informatie over het maken, bijwerken of verwijderen van aangepaste Azure-rollen met behulp van Azure CLI en op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: d3d05ba65e0d3918f1651c36cd17700ebf74de76
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778333"
---
# <a name="create-or-update-azure-custom-roles-using-azure-cli"></a>Aangepaste Azure-rollen maken of bijwerken met behulp van Azure CLI

> [!IMPORTANT]
> Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als de [ingebouwde rollen van Azure](built-in-roles.md) niet voldoen aan de specifieke behoeften van uw organisatie, kunt u uw eigen aangepaste rollen maken. In dit artikel wordt beschreven hoe u aangepaste rollen kunt maken, maken, bijwerken of verwijderen met behulp van Azure CLI.

Zie Zelfstudie: Een aangepaste Azure-rol maken met behulp van Azure CLI voor een [stapsgewijse zelfstudie over](tutorial-custom-role-cli.md)het maken van een aangepaste rol.

## <a name="prerequisites"></a>Vereisten

Als u aangepaste rollen wilt maken, hebt u het volgende nodig:

- Machtigingen voor het maken van aangepaste rollen, zoals [Eigenaar](built-in-roles.md#owner) of [Administrator voor gebruikerstoegang](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) of [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>Aangepaste rollen opvragen

Gebruik az role definition list om aangepaste rollen weer te geven die beschikbaar zijn [voor toewijzing.](/cli/azure/role/definition#az_role_definition_list) In het volgende voorbeeld worden alle aangepaste rollen in het huidige abonnement vermeld.

```azurecli
az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
```

```json
[
  {
    "roleName": "My Management Contributor",
    "type": "CustomRole"
  },
  {
    "roleName": "My Service Reader Role",
    "type": "CustomRole"
  },
  {
    "roleName": "Virtual Machine Operator",
    "type": "CustomRole"
  }
]
```

## <a name="list-a-custom-role-definition"></a>Een aangepaste roldefinitie in een lijst zetten

Gebruik az role definition list om een aangepaste roldefinitie [weer te geven.](/cli/azure/role/definition#az_role_definition_list) Dit is dezelfde opdracht die u zou gebruiken voor een ingebouwde rol.

```azurecli
az role definition list --name {roleName}
```

In het volgende voorbeeld wordt de *roldefinitie Operator van* virtuele machine vermeld:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

In het volgende voorbeeld worden alleen de acties van de rol *Operator van virtuele machine* vermeld:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json --query '[].permissions[0].actions'
```

```json
[
  [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ]
]
```

## <a name="create-a-custom-role"></a>Een aangepaste rol maken

Gebruik az role definition create om een aangepaste [rol te maken.](/cli/azure/role/definition#az_role_definition_create) De roldefinitie kan een JSON-beschrijving zijn of een pad naar een bestand met een JSON-beschrijving.

```azurecli
az role definition create --role-definition {roleDefinition}
```

In het volgende voorbeeld wordt een aangepaste rol gemaakt met de *naam Operator voor virtuele machines.* Met deze aangepaste rol wordt toegang toegewezen aan alle leesbewerkingen van de resourceproviders *Microsoft.Compute,* *Microsoft.Storage* en *Microsoft.Network* en wordt toegang toegewezen om virtuele machines te starten, opnieuw op te starten en te bewaken. Deze aangepaste rol kan worden gebruikt in twee abonnementen. In dit voorbeeld wordt een JSON-bestand als invoer gebruikt.

vmoperator.jsaan

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Een aangepaste rol bijwerken

Als u een aangepaste rol wilt bijwerken, gebruikt u [eerst az role definition list om](/cli/azure/role/definition#az_role_definition_list) de roldefinitie op te halen. Ten tweede moet u de gewenste wijzigingen aanbrengen in de roldefinitie. Gebruik tot slot [az role definition update om](/cli/azure/role/definition#az_role_definition_update) de bijgewerkte roldefinitie op te slaan.

```azurecli
az role definition update --role-definition {roleDefinition}
```

In het volgende voorbeeld wordt de *bewerking Microsoft.Insights/diagnosticSettings/* toegevoegd aan en wordt een beheergroep toegevoegd aan voor de aangepaste rol `Actions` `AssignableScopes` *Virtuele-machineoperator.* Het toevoegen van een beheergroep aan `AssignableScopes` is momenteel in de preview-fase.

vmoperator.jsop

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Een aangepaste rol verwijderen

Als u een aangepaste rol wilt verwijderen, gebruikt [u az role definition delete.](/cli/azure/role/definition#az_role_definition_delete) Als u de rol wilt opgeven die u wilt verwijderen, gebruikt u de rolnaam of de rol-id. Gebruik az role definition list om de [rol-id te bepalen.](/cli/azure/role/definition#az_role_definition_list)

```azurecli
az role definition delete --name {roleNameOrId}
```

In het volgende voorbeeld wordt de aangepaste *rol Operator* voor virtuele machines verwijderd.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Een aangepaste Azure-rol maken met Azure CLI](tutorial-custom-role-cli.md)
- [Aangepaste Azure-rollen](custom-roles.md)
- [Bewerkingen van Azure-resourceproviders](resource-provider-operations.md)