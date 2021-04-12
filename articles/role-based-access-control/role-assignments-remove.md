---
title: Azure-roltoewijzingen verwijderen-Azure RBAC
description: Meer informatie over het verwijderen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van Azure Portal, Azure PowerShell, Azure CLI of REST API.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.openlocfilehash: 7a3e4853d6dffa7eb2c5cf80846f6f1bd6beba03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100561379"
---
# <a name="remove-azure-role-assignments"></a>Azure-roltoewijzingen verwijderen

[Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../../articles/role-based-access-control/overview.md) is het autorisatiesysteem om de toegang tot Azure-resources te beheren. Als u de toegang van een Azure-resource wilt verwijderen, verwijdert u een roltoewijzing. In dit artikel wordt beschreven hoe u rollen toewijzingen verwijdert met behulp van de Azure Portal, Azure PowerShell, Azure CLI en REST API.

## <a name="prerequisites"></a>Vereisten

Als u roltoewijzingen wilt verwijderen, hebt u het volgende nodig:

- `Microsoft.Authorization/roleAssignments/delete`machtigingen, zoals beheerder of [eigenaar](../../articles/role-based-access-control/built-in-roles.md#owner) van [gebruikers toegang](../../articles/role-based-access-control/built-in-roles.md#user-access-administrator)

## <a name="azure-portal"></a>Azure Portal

1. Open **toegangs beheer (IAM)** in een bereik, zoals de beheer groep, het abonnement, de resource groep of de resource, waar u de toegang wilt verwijderen.

1. Klik op **het tabblad roltoewijzingen om** alle roltoewijzingen in dit bereik weer te geven.

1. Schakel in de lijst met roltoewijzingen het selectievakje in naast de beveiligings-principal met de roltoewijzing die u wilt verwijderen.

   ![Roltoewijzing geselecteerd om te worden verwijderd](./media/role-assignments-remove/rg-role-assignments-select.png)

1. Klik op **Verwijderen**.

   ![Bericht bij verwijderen van roltoewijzing](./media/role-assignments-remove/remove-role-assignment.png)

1. Klik op **Ja** om te bevestigen dat u de roltoewijzing inderdaad wilt verwijderen.

    Als u een bericht ziet dat overgenomen roltoewijzingen niet kunnen worden verwijderd, probeert u een roltoewijzing te verwijderen uit een onderliggend bereik. Open toegangs beheer (IAM) bij het bereik waaraan de rol is toegewezen en probeer het opnieuw. Een snelle manier om toegangs beheer (IAM) in het juiste bereik te openen, is door de kolom **bereik** te bekijken en op de koppeling naast **(overgenomen)** te klikken.

   ![Bericht voor toewijzing van rol verwijderen voor overgenomen roltoewijzingen](./media/role-assignments-remove/remove-role-assignment-inherited.png)

## <a name="azure-powershell"></a>Azure PowerShell

In Azure PowerShell verwijdert u een roltoewijzing met behulp van [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment).

In het volgende voor beeld wordt de toewijzing van de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) uit de *patlong \@ contoso.com* -gebruiker voor de resource groep *Pharma-Sales* verwijderd:

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Hiermee verwijdert u de rol van [lezer](built-in-roles.md#reader) uit de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 op abonnements bereik.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Hiermee verwijdert u de rol van [facturerings lezer](built-in-roles.md#billing-reader) van de *Alain \@ example.com* -gebruiker in het bereik van de beheer groep.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Als u het volgende fout bericht wordt weer gegeven: ' de opgegeven informatie is niet toegewezen aan een roltoewijzing ', zorg ervoor dat u ook `-Scope` de `-ResourceGroupName` para meters of opgeeft. Zie [problemen met Azure RBAC oplossen](troubleshooting.md#role-assignments-with-identity-not-found)voor meer informatie.

## <a name="azure-cli"></a>Azure CLI

In azure CLI verwijdert u een roltoewijzing met behulp van [AZ Role Assignment delete](/cli/azure/role/assignment#az_role_assignment_delete).

In het volgende voor beeld wordt de toewijzing van de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) uit de *patlong \@ contoso.com* -gebruiker voor de resource groep *Pharma-Sales* verwijderd:

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

Hiermee verwijdert u de rol van [lezer](built-in-roles.md#reader) uit de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 op abonnements bereik.

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

Hiermee verwijdert u de rol van [facturerings lezer](built-in-roles.md#billing-reader) van de *Alain \@ example.com* -gebruiker in het bereik van de beheer groep.

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="rest-api"></a>REST-API

In de REST API verwijdert u een roltoewijzing met behulp van [roltoewijzingen-verwijderen](/rest/api/authorization/roleassignments/delete).

1. Haal de roltoewijzings-id (GUID) op. Deze id wordt geretourneerd wanneer u de roltoewijzing voor het eerst maakt of u kunt deze ophalen door de roltoewijzingen weer te geven.

1. Begin met de volgende aanvraag:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

1. Vervang *{Scope}* in de URI door het bereik voor het verwijderen van de roltoewijzing.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roleAssignmentId}* door de GUID-id van de roltoewijzing.

Met de volgende aanvraag verwijdert u de opgegeven roltoewijzing bij het abonnements bereik:

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

Hieronder ziet u een voor beeld van de uitvoer:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:24.5379478Z",
        "updatedOn": "2020-05-06T23:55:24.5379478Z",
        "createdBy": "{createdByObjectId1}",
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="arm-template"></a>ARM-sjabloon

Er is geen manier om een roltoewijzing te verwijderen met behulp van een Azure Resource Manager sjabloon (ARM-sjabloon). Als u een roltoewijzing wilt verwijderen, moet u andere hulpprogram ma's gebruiken, zoals de Azure Portal, Azure PowerShell, Azure CLI of REST API.

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen vermelden met behulp van Azure Portal](role-assignments-list-portal.md)
- [Toewijzingen van Azure-roltoewijzingen weer geven met Azure PowerShell](role-assignments-list-powershell.md)
- [Problemen met Azure RBAC oplossen](troubleshooting.md)
