---
title: Azure-roltoewijzingen weer geven met behulp van de REST API-Azure RBAC
description: Meer informatie over hoe u kunt bepalen welke resources gebruikers, groepen, service-principals of beheerde identiteiten hebben toegang tot het gebruik van de REST API en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.topic: how-to
ms.date: 02/27/2021
ms.author: rolyon
ms.openlocfilehash: 9780902a1c5f4a711e1abffa6b508c28efe269ac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735877"
---
# <a name="list-azure-role-assignments-using-the-rest-api"></a>Azure-roltoewijzingen weer geven met behulp van de REST API

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] In dit artikel wordt beschreven hoe u roltoewijzingen kunt weer geven met behulp van de REST API.

> [!NOTE]
> Als uw organisatie uitbestede beheer functies heeft voor een service provider die gebruikmaakt van [Azure delegated resource management](../lighthouse/concepts/azure-delegated-resource-management.md), worden roltoewijzingen die door die service provider worden toegestaan, hier niet weer gegeven.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="list-role-assignments"></a>Lijst met roltoewijzingen weergeven

In azure RBAC kunt u de roltoewijzingen weer geven om toegang te krijgen. Als u roltoewijzingen wilt weer geven, gebruikt u een van de [roltoewijzingen-lijst rest-](/rest/api/authorization/roleassignments/list) api's. Als u uw resultaten wilt verfijnen, geeft u een bereik en een optioneel filter op.

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de roltoewijzingen wilt weer geven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |

    In het vorige voor beeld is micro soft. web een resource provider die verwijst naar een App Service-exemplaar. U kunt ook andere resource providers gebruiken en het bereik opgeven. Zie [Azure-resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md) en ondersteunde [Azure resource provider-bewerkingen](resource-provider-operations.md)voor meer informatie.  
     
1. Vervang *{filter}* door de voor waarde die u wilt Toep assen om de roltoewijzings lijst te filteren.

    > [!div class="mx-tableFixed"]
    > | Filter | Beschrijving |
    > | --- | --- |
    > | `$filter=atScope()` | Hier worden roltoewijzingen voor alleen het opgegeven bereik weer gegeven, met inbegrip van de roltoewijzingen in subbereiken. |
    > | `$filter=assignedTo('{objectId}')` | Hier worden roltoewijzingen voor een opgegeven gebruiker of Service-Principal weer gegeven.<br/>Als de gebruiker lid is van een groep die een roltoewijzing heeft, wordt die roltoewijzing ook weer gegeven. Dit filter is transitief voor groepen. Dit betekent dat als de gebruiker lid is van een groep en die groep lid is van een andere groep die een roltoewijzing heeft, die roltoewijzing ook wordt vermeld.<br/>Dit filter accepteert alleen een object-ID voor een gebruiker of een service-principal. U kunt geen object-ID door geven voor een groep. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Hier worden roltoewijzingen weer gegeven voor de opgegeven gebruiker of Service-Principal en bij het opgegeven bereik. |
    > | `$filter=principalId+eq+'{objectId}'` | Hier worden roltoewijzingen voor een opgegeven gebruiker, groep of Service-Principal weer gegeven. |

De volgende aanvraag bevat alle roltoewijzingen voor de opgegeven gebruiker bij het abonnements bereik:

```http
GET https://management.azure.com/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()+and+assignedTo('{objectId1}')
```

Hieronder ziet u een voor beeld van de uitvoer:

```json
{
    "value": [
        {
            "properties": {
                "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
                "principalId": "{objectId1}",
                "scope": "/subscriptions/{subscriptionId1}",
                "createdOn": "2019-01-15T21:08:45.4904312Z",
                "updatedOn": "2019-01-15T21:08:45.4904312Z",
                "createdBy": "{createdByObjectId1}",
                "updatedBy": "{updatedByObjectId1}"
            },
            "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "{roleAssignmentId1}"
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

- [Azure-rollen toewijzen met behulp van de REST API](role-assignments-rest.md)
- [Azure REST API-naslaginformatie](/rest/api/azure/)
