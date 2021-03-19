---
title: Een lijst met Azure deny-toewijzingen maken met behulp van de REST API-Azure RBAC
description: Meer informatie over het weer geven van Azure deny-toewijzingen voor gebruikers, groepen en toepassingen met behulp van de REST API en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2f835c270930734bf9963a7c7c3168b873eddaf6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84791908"
---
# <a name="list-azure-deny-assignments-using-the-rest-api"></a>Azure deny-toewijzingen weer geven met behulp van de REST API

Met [Azure deny-toewijzingen](deny-assignments.md) kunnen gebruikers specifieke Azure-resource acties uitvoeren, zelfs als een roltoewijzing deze toegang verleent. In dit artikel wordt beschreven hoe u toewijzingen voor weigeren kunt weer geven met behulp van de REST API.

> [!NOTE]
> U kunt niet rechtstreeks uw eigen weigerings toewijzingen maken. Zie [Azure deny-toewijzingen](deny-assignments.md)voor meer informatie over hoe weigerings toewijzingen worden gemaakt.

## <a name="prerequisites"></a>Vereisten

Als u informatie wilt ophalen over een weiger toewijzing, hebt u het volgende nodig:

- `Microsoft.Authorization/denyAssignments/read` toestemming, die deel uitmaakt van de meeste [ingebouwde rollen van Azure](built-in-roles.md).

## <a name="list-a-single-deny-assignment"></a>Een enkele weigerings toewijzing weer geven

1. Begin met de volgende aanvraag:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments/{deny-assignment-id}?api-version=2018-07-01-preview
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de weigerings toewijzingen wilt weer geven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Abonnement |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{deny-Assignment-id}* door de id van de weigerings toewijzing die u wilt ophalen.

## <a name="list-multiple-deny-assignments"></a>Meerdere weigerings toewijzingen weer geven

1. Begin met een van de volgende aanvragen:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview
    ```

    Met optionele para meters:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Vervang *{Scope}* in de URI door het bereik waarvoor u de weigerings toewijzingen wilt weer geven.

    > [!div class="mx-tableFixed"]
    > | Bereik | Type |
    > | --- | --- |
    > | `subscriptions/{subscriptionId}` | Abonnement |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resourcegroep |
    > | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Resource |

1. Vervang *{filter}* door de voor waarde die u wilt Toep assen om de lijst met geweigerde toewijzingen te filteren.

    > [!div class="mx-tableFixed"]
    > | Filter | Beschrijving |
    > | --- | --- |
    > | (geen filter) | Een lijst met alle weigerings toewijzingen op, boven en onder het opgegeven bereik. |
    > | `$filter=atScope()` | Een lijst met weigerings toewijzingen voor het opgegeven bereik en hierboven. Omvat niet de weigerings toewijzingen in subbereiken. |
    > | `$filter=assignedTo('{objectId}')` | Een lijst met weiger toewijzingen voor de opgegeven gebruiker of Service-Principal.<br/>Als de gebruiker lid is van een groep die een weiger toewijzing heeft, wordt de weigerings toewijzing ook vermeld. Dit filter is transitief voor groepen. Dit betekent dat als de gebruiker lid is van een groep en die groep lid is van een andere groep die een weiger toewijzing heeft, de weigerings toewijzing ook wordt vermeld.<br/>Dit filter accepteert alleen een object-ID voor een gebruiker of een service-principal. U kunt geen object-ID door geven voor een groep. |
    > | `$filter=atScope()+and+assignedTo('{objectId}')` | Geeft een lijst van geweigerde toewijzingen voor de opgegeven gebruiker of Service-Principal en bij het opgegeven bereik. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Een lijst met geweigerde toewijzingen met de opgegeven naam. |
    > | `$filter=principalId+eq+'{objectId}'` | Een lijst met geweigerde toewijzingen voor de opgegeven gebruiker, groep of Service-Principal. |

## <a name="list-deny-assignments-at-the-root-scope-"></a>Lijst met geweigerde toewijzingen in het hoofd bereik (/)

1. Breid uw toegang uit, zoals wordt beschreven in [toegang verhogen om alle Azure-abonnementen en-beheer groepen te beheren](elevate-access-global-admin.md).

1. Gebruik de volgende aanvraag:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/denyAssignments?api-version=2018-07-01-preview&$filter={filter}
    ```

1. Vervang *{filter}* door de voor waarde die u wilt Toep assen om de lijst met geweigerde toewijzingen te filteren. U moet een filter opgeven.

    > [!div class="mx-tableFixed"]
    > | Filter | Beschrijving |
    > | --- | --- |
    > | `$filter=atScope()` | Lijst weigeren alleen toewijzingen voor het hoofd bereik. Omvat niet de weigerings toewijzingen in subbereiken. |
    > | `$filter=denyAssignmentName+eq+'{deny-assignment-name}'` | Weiger toewijzingen met de opgegeven naam. |

1. Verwijder verhoogde toegang.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure deny-toewijzingen](deny-assignments.md)
- [Toegang verhogen om alle Azure-abonnementen en beheergroepen te beheren](elevate-access-global-admin.md)
- [Azure REST API-naslaginformatie](/rest/api/azure/)
