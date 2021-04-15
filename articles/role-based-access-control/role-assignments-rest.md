---
title: Azure-rollen toewijzen met behulp van REST API - Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van de REST API en op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 3baf44a4240b23b41ce2e80dc22dbda4c7d0672a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363713"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>Azure-rollen toewijzen met behulp van de REST API

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] In dit artikel wordt beschreven hoe u rollen toewijst met behulp van REST API.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Een Azure-rol toewijzen

Als u een rol wilt toewijzen, gebruikt [u](/rest/api/authorization/roleassignments/create) de REST API roltoewijzingen maken en geeft u de beveiligingsprincipaal, roldefinitie en het bereik op. Als u deze API wilt aanroepen, moet u toegang hebben tot de `Microsoft.Authorization/roleAssignments/write` bewerking. Van de ingebouwde rollen krijgen alleen [eigenaar en](built-in-roles.md#owner) beheerder van [gebruikerstoegang](built-in-roles.md#user-access-administrator) toegang tot deze bewerking.

1. Gebruik de [lijst met](/rest/api/authorization/roledefinitions/list) roldefinities REST API zie [Ingebouwde](built-in-roles.md) rollen om de id op te halen voor de roldefinitie die u wilt toewijzen.

1. Gebruik een GUID-hulpprogramma om een unieke id te genereren die wordt gebruikt voor de roltoewijzings-id. De id heeft de volgende indeling: `00000000-0000-0000-0000-000000000000`

1. Begin met de volgende aanvraag en de volgende body:

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

1. Vervang *{scope}* binnen de URI door het bereik voor de roltoewijzing.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

    In het vorige voorbeeld is microsoft.web een resourceprovider die verwijst naar een App Service-exemplaar. Op dezelfde manier kunt u ook andere resourceproviders gebruiken en het bereik opgeven. Zie Azure-resourceproviders en [-typen](../azure-resource-manager/management/resource-providers-and-types.md) en ondersteunde bewerkingen voor [Azure-resourceproviders voor meer informatie.](resource-provider-operations.md)  

1. Vervang *{roleAssignmentId}* door de GUID-id van de roltoewijzing.

1. Vervang in de aanvraag body *{scope}* door het bereik voor de roltoewijzing.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Beheergroep |
    > | `subscriptions/{subscriptionId1}` | Abonnement |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Resource |

1. Vervang *{roleDefinitionId} door* de roldefinitie-id.

1. Vervang *{principalId}* door de object-id van de gebruiker, groep of service-principal die aan de rol wordt toegewezen.

Met de volgende aanvraag en de volgende body wordt de rol [Van back-uplezer](built-in-roles.md#backup-reader) toegewezen aan een gebruiker op abonnementsbereik:

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

Hieronder ziet u een voorbeeld van de uitvoer:

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

### <a name="new-service-principal"></a>Nieuwe service-principal

Als u een nieuwe service-principal maakt en onmiddellijk probeert een rol toe te wijzen aan die service-principal, kan die roltoewijzing in sommige gevallen mislukken. Als u bijvoorbeeld een nieuwe beheerde identiteit maakt en vervolgens probeert een rol toe te wijzen aan die service-principal, kan de roltoewijzing mislukken. De reden voor deze fout is waarschijnlijk een replicatievertraging. De service-principal wordt in één regio gemaakt; De roltoewijzing kan echter plaatsvinden in een andere regio die de service-principal nog niet heeft gerepliceerd.

Als u dit scenario wilt aanpakken, gebruikt u de [functietoewijzingen - maken REST API](/rest/api/authorization/roleassignments/create) en stelt u de eigenschap in op `principalType` `ServicePrincipal` . U moet ook de `apiVersion` instellen op `2018-09-01-preview` of hoger.

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen met behulp van de REST API](role-assignments-list-rest.md)
- [Resources implementeren met Resource Manager-sjablonen en Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API-naslaginformatie](/rest/api/azure/)
- [Aangepaste Azure-rollen maken of bijwerken met behulp van de REST API](custom-roles-rest.md)
