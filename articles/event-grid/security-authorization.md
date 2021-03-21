---
title: Beveiliging en verificatie Azure Event Grid
description: Beschrijving van Azure Event Grid en de concepten ervan.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: e9bcf00e832e4deaaf9c5f81ba5af51609a1c412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601037"
---
# <a name="authorizing-access-to-event-grid-resources"></a>Toegang tot Event Grid-resources autoriseren
Met Azure Event Grid kunt u het toegangs niveau dat aan verschillende gebruikers wordt gegeven, beheren om verschillende **beheer bewerkingen** uit te voeren, zoals abonnementen op lijst gebeurtenissen, nieuwe maken en sleutels genereren. Event Grid maakt gebruik van Azure op rollen gebaseerd toegangs beheer (Azure RBAC).

> [!NOTE]
> EventGrid biedt geen ondersteuning voor Azure RBAC voor het publiceren van gebeurtenissen naar Event Grid onderwerpen of domeinen. Gebruik een Shared Access Signature SAS-sleutel of-token om clients te verifiëren die gebeurtenissen publiceren. Zie [Publishing-clients verifiëren](security-authenticate-publishing-clients.md)voor meer informatie. 

## <a name="operation-types"></a>Bewerkings typen
Voer de volgende Azure CLI-opdracht uit voor een lijst met bewerkingen die worden ondersteund door Azure Event Grid: 

```azurecli-interactive
az provider operation show --namespace Microsoft.EventGrid
```

Met de volgende bewerkingen wordt mogelijk geheime informatie geretourneerd, waardoor de normale Lees bewerkingen worden gefilterd. Het is raadzaam de toegang tot deze bewerkingen te beperken. 

* Micro soft. EventGrid/eventSubscriptions/getFullUrl/Action
* Micro soft. EventGrid/topics/Listkeys ophalen/Action
* Micro soft. EventGrid/topics/regenerateKey/Action


## <a name="built-in-roles"></a>Ingebouwde rollen
Event Grid biedt de volgende drie ingebouwde rollen. 

De Inzender rollen Event Grid-abonnement en Event Grid-abonnement zijn bedoeld voor het beheren van gebeurtenis abonnementen. Ze zijn belang rijk bij het implementeren van [gebeurtenis domeinen](event-domains.md) omdat ze gebruikers de machtigingen geven die ze nodig hebben om zich te abonneren op onderwerpen in uw gebeurtenis domein. Deze rollen zijn gericht op gebeurtenis abonnementen en verlenen geen toegang voor acties zoals het maken van onderwerpen.

Met de rol Event Grid Inzender kunt u Event Grid resources maken en beheren. 


| Rol | Beschrijving |
| ---- | ----------- | 
| [Event Grid-abonnements lezer](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-reader) | Hiermee beheert u Event Grid bewerkingen voor gebeurtenis abonnementen. |
| [Inzender van Event Grid-abonnement](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor) | Hiermee kunt u Event Grid gebeurtenis abonnementen lezen. |
| [Inzender Event Grid](../role-based-access-control/built-in-roles.md#eventgrid-contributor) | Hiermee kunt u Event Grid-resources maken en beheren. |


> [!NOTE]
> Selecteer links in de eerste kolom om naar een artikel te navigeren met meer informatie over de rol. Zie [dit artikel](../role-based-access-control/quickstart-assign-role-user-portal.md)voor instructies over het toewijzen van gebruikers of groepen aan RBAC-rollen.


## <a name="custom-roles"></a>Aangepaste rollen

Als u machtigingen wilt opgeven die afwijken van de ingebouwde rollen, kunt u aangepaste rollen maken.

Hier volgen enkele voor beelden van Event Grid roldefinities waarmee gebruikers verschillende acties kunnen uitvoeren. Deze aangepaste rollen verschillen van de ingebouwde rollen omdat ze bredere toegang verlenen dan alleen gebeurtenis abonnementen.

**EventGridReadOnlyRole.jsop**: alleen alleen-lezen bewerkingen toestaan.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.js**: beperkte post acties toestaan, maar geen verwijderings acties toestaan.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.jsop**: alle gebeurtenis raster acties toestaan.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

U kunt aangepaste rollen maken met [Power shell](../role-based-access-control/custom-roles-powershell.md), [Azure cli](../role-based-access-control/custom-roles-cli.md)en [rest](../role-based-access-control/custom-roles-rest.md).



### <a name="encryption-at-rest"></a>Versleuteling 'at rest'

Alle gebeurtenissen of gegevens die door de Event Grid-Service naar schijf worden geschreven, worden versleuteld met een door micro soft beheerde sleutel, zodat deze op rest versleuteld is. Bovendien is de maximale tijds duur dat gebeurtenissen of gegevens bewaard 24 uur in acht te komen in de [Event grid beleid voor opnieuw proberen](delivery-and-retry.md). Event Grid verwijdert automatisch alle gebeurtenissen of gegevens na 24 uur, of de TTL van de gebeurtenis, afhankelijk van wat kleiner is.

## <a name="permissions-for-event-subscriptions"></a>Machtigingen voor gebeurtenis abonnementen
Als u een gebeurtenis-handler gebruikt die geen webhook is (zoals een Event Hub of een wachtrij opslag), moet u schrijf toegang hebben voor die bron. Met deze machtigingen kunt u voor komen dat een niet-geautoriseerde gebruiker gebeurtenissen naar uw resource verzendt.

U moet beschikken over de machtiging **micro soft. EventGrid/EventSubscriptions/write** voor de resource die de bron van de gebeurtenis is. U hebt deze machtiging nodig omdat u een nieuw abonnement op het bereik van de resource schrijft. De vereiste resource wijkt af van de vraag of u zich abonneert op een systeem onderwerp of een aangepast onderwerp. Beide typen worden beschreven in deze sectie.

### <a name="system-topics-azure-service-publishers"></a>Systeem onderwerpen (Azure service Publishers)
Als u niet de eigenaar of Inzender van de bron resource bent, moet u voor systeem onderwerpen toestemming hebben om een nieuw gebeurtenis abonnement te schrijven op het bereik van de resource die de gebeurtenis publiceert. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Als u zich bijvoorbeeld wilt abonneren op een gebeurtenis in een opslag account met de naam **myacct**, hebt u de machtiging micro soft. EventGrid/EventSubscriptions/write nodig voor: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Aangepaste onderwerpen
Voor aangepaste onderwerpen hebt u toestemming nodig voor het schrijven van een nieuw gebeurtenis abonnement in het bereik van het event grid-onderwerp. De indeling van de resource is: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Als u zich bijvoorbeeld wilt abonneren op een aangepast onderwerp met de naam **mytopic**, hebt u de machtiging micro soft. EventGrid/EventSubscriptions/write nodig voor: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`



## <a name="next-steps"></a>Volgende stappen

* Zie [About Event grid](overview.md) voor een inleiding tot Event grid.
