---
title: Azure-roltoewijzingen met Behulp van Azure CLI - Azure RBAC
description: Meer informatie over het bepalen tot welke resources gebruikers, groepen, service-principals of beheerde identiteiten toegang hebben met behulp van Azure CLI en op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).
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
ms.openlocfilehash: 2d30571b68ba7e38e9960d1e434cf7844f6be852
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780097"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Azure-roltoewijzingen in een lijst met Azure CLI

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] In dit artikel wordt beschreven hoe u roltoewijzingen vermeldt met behulp van Azure CLI.

> [!NOTE]
> Als uw organisatie beheerfuncties heeft uitbesteed aan een serviceprovider die gebruikmaakt van [gedelegeerd Azure-resourcebeheer,](../lighthouse/concepts/azure-delegated-resource-management.md)worden roltoewijzingen die zijn geautoriseerd door die serviceprovider hier niet weergegeven.

## <a name="prerequisites"></a>Vereisten

- [Bash in Azure Cloud Shell](../cloud-shell/overview.md) of [Azure CLI](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Roltoewijzingen voor een gebruiker weergeven

Gebruik az role assignment list om de roltoewijzingen voor een specifieke gebruiker [weer te geven:](/cli/azure/role/assignment#az_role_assignment_list)

```azurecli
az role assignment list --assignee {assignee}
```

Standaard worden alleen roltoewijzingen voor het huidige abonnement weergegeven. Als u roltoewijzingen voor het huidige abonnement en hieronder wilt weergeven, voegt u de `--all` parameter toe. Als u overgenomen roltoewijzingen wilt weergeven, voegt u de `--include-inherited` parameter toe.

In het volgende voorbeeld worden de roltoewijzingen vermeld die rechtstreeks aan de *\@ patlong-contoso.com* toegewezen:

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

Gebruik [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list)om de roltoewijzingen weer te geven die bestaan in een resourcegroepsbereik:

```azurecli
az role assignment list --resource-group {resourceGroup}
```

In het volgende voorbeeld worden de roltoewijzingen voor de *resourcegroep farm-sales* vermeld:

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

Gebruik az role assignment list om alle roltoewijzingen voor een abonnementsbereik [weer te geven.](/cli/azure/role/assignment#az_role_assignment_list) U kunt de abonnements-id vinden op de **blade** Abonnementen in de Azure Portal of u kunt az account [list gebruiken.](/cli/azure/account#az_account_list)

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

## <a name="list-role-assignments-for-a-management-group"></a>Lijst met roltoewijzingen voor een beheergroep

Gebruik az role assignment list om alle roltoewijzingen voor een beheergroepsbereik [weer te geven.](/cli/azure/role/assignment#az_role_assignment_list) Als u de beheergroeps-id wilt op halen, kunt u deze vinden op de **blade** Beheergroepen in de Azure Portal of kunt u az [account management-group list gebruiken.](/cli/azure/account/management-group#az_account_management_group_list)

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

## <a name="list-role-assignments-for-a-managed-identity"></a>Lijst met roltoewijzingen voor een beheerde identiteit

1. Haal de principal-id op van de door het systeem toegewezen of door de gebruiker toegewezen beheerde identiteit.

    U kunt az [ad sp list](/cli/azure/ad/sp#az_ad_sp_list) of az identity list gebruiken om de principal-id van een door de gebruiker toegewezen beheerde identiteit op te [halen.](/cli/azure/identity#az_identity_list)

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    U kunt az ad sp list gebruiken om de principal-id van een door het systeem toegewezen beheerde identiteit [op te halen.](/cli/azure/ad/sp#az_ad_sp_list)

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. Gebruik az role assignment list om de roltoewijzingen [weer te geven.](/cli/azure/role/assignment#az_role_assignment_list)

    Standaard worden alleen roltoewijzingen voor het huidige abonnement weergegeven. Als u roltoewijzingen voor het huidige abonnement en hieronder wilt weergeven, voegt u de `--all` parameter toe. Als u overgenomen roltoewijzingen wilt weergeven, voegt u de `--include-inherited` parameter toe.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Volgende stappen

- [Azure-rollen toewijzen met behulp van Azure CLI](role-assignments-cli.md)