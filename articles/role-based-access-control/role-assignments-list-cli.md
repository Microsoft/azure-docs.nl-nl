---
title: Azure-roltoewijzingen weer geven met Azure CLI-Azure RBAC
description: Meer informatie over hoe u kunt bepalen welke resources gebruikers, groepen, service-principals of beheerde identiteiten hebben toegang tot het gebruik van Azure CLI en op rollen gebaseerd toegangs beheer (Azure RBAC).
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
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: cc64e314a8acb035736df0521987cb78a7297326
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556924"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure-roltoewijzingen weer geven met Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] In dit artikel wordt beschreven hoe u roltoewijzingen kunt weer geven met behulp van Azure CLI.

> [!NOTE]
> Als uw organisatie uitbestede beheer functies heeft voor een service provider die gebruikmaakt van [Azure delegated resource management](../lighthouse/concepts/azure-delegated-resource-management.md), worden roltoewijzingen die door die service provider worden toegestaan, hier niet weer gegeven.

## <a name="prerequisites"></a>Vereisten

- [Bash in azure Cloud shell](../cloud-shell/overview.md) of [Azure cli](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Roltoewijzingen voor een gebruiker weergeven

Als u de roltoewijzingen voor een specifieke gebruiker wilt weer geven, gebruikt u de [lijst AZ Role Assignment](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee {assignee}
```

Standaard worden alleen roltoewijzingen voor het huidige abonnement weer gegeven. Als u roltoewijzingen voor het huidige abonnement en hieronder wilt weer geven, voegt u de `--all` para meter toe. Als u overgenomen roltoewijzingen wilt weer geven, voegt u de `--include-inherited` para meter toe.

In het volgende voor beeld worden de roltoewijzingen weer gegeven die rechtstreeks aan de *patlong \@ contoso.com* -gebruiker zijn toegewezen:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>Roltoewijzingen voor een resourcegroep opvragen

Als u de roltoewijzingen wilt weer geven die voor komen in een bereik van een resource groep, gebruikt u de [lijst AZ Role Assignment](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group {resourceGroup}
```

In het volgende voor beeld worden de roltoewijzingen voor de resource groep *Pharma-Sales* weer gegeven:

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>Roltoewijzingen weergeven voor een abonnement

Gebruik [AZ Role Assignment List](/cli/azure/role/assignment#az-role-assignment-list)om alle roltoewijzingen in een abonnements bereik weer te geven. Als u de abonnements-ID wilt ophalen, kunt u deze vinden op de Blade **abonnementen** in de Azure portal of kunt u [AZ account list](/cli/azure/account#az-account-list)gebruiken.

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

Voorbeeld:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>Roltoewijzingen voor een beheer groep weer geven

Gebruik [AZ Role Assignment List](/cli/azure/role/assignment#az-role-assignment-list)om alle roltoewijzingen in een beheer groeps bereik weer te geven. Als u de beheer groep-ID wilt ophalen, kunt u deze vinden op de Blade **beheer groepen** in de Azure portal of u kunt [AZ Account Management-Group List](/cli/azure/account/management-group#az-account-management-group-list)gebruiken.

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

Voorbeeld:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Roltoewijzingen voor een beheerde identiteit weer geven

1. Haal de principal-ID op van de door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteit.

    Als u de principal-ID van een door de gebruiker toegewezen beheerde identiteit wilt ophalen, kunt u [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list) of [AZ ID List](/cli/azure/identity#az-identity-list)gebruiken.

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    Als u de principal-ID van een door het systeem toegewezen beheerde identiteit wilt ophalen, kunt u [AZ AD SP List](/cli/azure/ad/sp#az-ad-sp-list)gebruiken.

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. Als u de roltoewijzingen wilt weer geven, gebruikt u de [lijst AZ Role Assignment](/cli/azure/role/assignment#az-role-assignment-list).

    Standaard worden alleen roltoewijzingen voor het huidige abonnement weer gegeven. Als u roltoewijzingen voor het huidige abonnement en hieronder wilt weer geven, voegt u de `--all` para meter toe. Als u overgenomen roltoewijzingen wilt weer geven, voegt u de `--include-inherited` para meter toe.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Volgende stappen

- [Azure-rollen toewijzen met behulp van Azure CLI](role-assignments-cli.md)