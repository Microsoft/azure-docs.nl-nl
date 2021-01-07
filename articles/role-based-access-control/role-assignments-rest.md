---
title: Azure-roltoewijzingen toevoegen of verwijderen met behulp van de REST API-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van de REST API en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 05/06/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e4f230663e0eeddcf874c24e5041653f241f481c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964266"
---
# <a name="add-or-remove-azure-role-assignments-using-the-rest-api"></a>Azure-roltoewijzingen toevoegen of verwijderen met behulp van de REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] In dit artikel wordt beschreven hoe u rollen toewijst met behulp van de REST API.

## <a name="prerequisites"></a>Vereisten

Om roltoewijzingen toe te voegen of te verwijderen, hebt u het volgende nodig:

- Machtigingen voor `Microsoft.Authorization/roleAssignments/write` en `Microsoft.Authorization/roleAssignments/delete`, zoals [Beheerder van gebruikerstoegang](built-in-roles.md#user-access-administrator) of [Eigenaar](built-in-roles.md#owner)

## <a name="add-a-role-assignment"></a>Een roltoewijzing toevoegen

In azure RBAC kunt u een roltoewijzing toevoegen om toegang te verlenen. Als u een roltoewijzing wilt toevoegen, gebruikt u de roltoewijzingen [-rest API maken](/rest/api/authorization/roleassignments/create) en geeft u de beveiligingsprincipal, roldefinitie en bereik op. U moet toegang hebben tot de bewerking om deze API aan te roepen `Microsoft.Authorization/roleAssignments/write` . Van de ingebouwde rollen wordt alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruiker toegang](built-in-roles.md#user-access-administrator) verleend tot deze bewerking.

1. Gebruik de [roldefinities-lijst](/rest/api/authorization/roledefinitions/list) rest API of Zie [ingebouwde rollen](built-in-roles.md) om de id op te halen voor de roldefinitie die u wilt toewijzen.

1. Gebruik een GUID-hulp programma voor het genereren van een unieke id die wordt gebruikt voor de toewijzings-id van de rol. De id heeft de volgende indeling: `00000000-0000-0000-0000-000000000000`

1. Beginnen met de volgende aanvraag en hoofd tekst:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. Vervang *{Scope}* in de URI door het bereik voor de roltoewijzing.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

    In het vorige voor beeld is micro soft. web een resource provider die verwijst naar een App Service-exemplaar. U kunt ook andere resource providers gebruiken en het bereik opgeven. Zie [Azure-resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md) en ondersteunde [Azure resource provider-bewerkingen](resource-provider-operations.md)voor meer informatie.  

1. Vervang *{roleAssignmentId}* door de GUID-id van de roltoewijzing.

1. Vervang *{Scope}* in de hoofd tekst van de aanvraag door het bereik voor de roltoewijzing.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roledefinitionid hebben}* door de roldefinitie-id.

1. Vervang *{principalId}* door de object-id van de gebruiker, groep of service-principal die wordt toegewezen aan de rol.

De volgende aanvraag en hoofd tekst wijst de rol van [back-uplezer](built-in-roles.md#backup-reader) toe aan een gebruiker op het abonnements bereik:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

Hieronder ziet u een voor beeld van de uitvoer:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

## <a name="remove-a-role-assignment"></a>Roltoewijzing verwijderen

Als u in Azure RBAC de toegang wilt intrekken voor een rol, verwijdert u de roltoewijzing. Als u een roltoewijzing wilt verwijderen, gebruikt u de [roltoewijzingen rest API verwijderen](/rest/api/authorization/roleassignments/delete) . U moet toegang hebben tot de bewerking om deze API aan te roepen `Microsoft.Authorization/roleAssignments/delete` . Van de ingebouwde rollen wordt alleen de beheerder van de [eigenaar](built-in-roles.md#owner) en de [gebruiker toegang](built-in-roles.md#user-access-administrator) verleend tot deze bewerking.

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

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen weer geven met behulp van de REST API](role-assignments-list-rest.md)
- [Resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-naslaginformatie](/rest/api/azure/)
- [Aangepaste Azure-rollen maken of bijwerken met behulp van de REST API](custom-roles-rest.md)
