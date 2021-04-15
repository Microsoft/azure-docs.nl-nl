---
title: Gebeurtenisdomeinen in Azure Event Grid
description: In dit artikel wordt beschreven hoe u gebeurtenisdomeinen gebruikt om de stroom van aangepaste gebeurtenissen naar uw verschillende zakelijke organisaties, klanten of toepassingen te beheren.
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 32c06ac55f667ec9807c7952127c2cf0f0384024
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374706"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Gebeurtenisdomeinen begrijpen voor het beheren van Event Grid onderwerpen

In dit artikel wordt beschreven hoe u gebeurtenisdomeinen gebruikt om de stroom van aangepaste gebeurtenissen naar uw verschillende zakelijke organisaties, klanten of toepassingen te beheren. Gebeurtenisdomeinen gebruiken voor het volgende:

* Eventing-architecturen voor meerdereten op schaal beheren.
* Uw autorisatie en verificatie beheren.
* Partitioneren van uw onderwerpen zonder dat u ze afzonderlijk beheert.
* Voorkom dat u afzonderlijk publiceert naar elk van uw onderwerp-eindpunten.

## <a name="event-domain-overview"></a>Overzicht van gebeurtenisdomeinen

Een gebeurtenisdomein is een beheerprogramma voor grote aantallen Event Grid onderwerpen met betrekking tot dezelfde toepassing. U kunt het zien als een metaonderwerp dat duizenden afzonderlijke onderwerpen kan hebben.

Gebeurtenisdomeinen bieden u dezelfde architectuur die wordt gebruikt door Azure-services zoals Storage en IoT Hub om hun gebeurtenissen te publiceren. Met deze informatie kunt u gebeurtenissen publiceren naar duizenden onderwerpen. Domeinen bieden u ook autorisatie- en verificatiebeheer voor elk onderwerp, zodat u uw tenants kunt partitioneren.

## <a name="example-use-case"></a>Voorbeeld van een toepassing
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Toegangsbeheer

Met een domein krijgt u via op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) een fijnkeurige autorisatie en verificatiecontrole voor elk onderwerp. U kunt deze rollen gebruiken om elke tenant in uw toepassing te beperken tot alleen de onderwerpen waar u hen toegang toe wilt verlenen.

Azure RBAC in gebeurtenisdomeinen [](security-authorization.md) werkt op dezelfde manier als beheerd toegangsbeheer in de rest van Event Grid en Azure. Gebruik Azure RBAC om aangepaste roldefinities te maken en af te dwingen in gebeurtenisdomeinen.

### <a name="built-in-roles"></a>Ingebouwde rollen

Event Grid heeft twee ingebouwde roldefinities om Azure RBAC gemakkelijker te maken om met gebeurtenisdomeinen te werken. Deze rollen zijn **EventGrid EventSubscription Contributor (Preview)** en **EventGrid EventSubscription Reader (Preview)**. U wijst deze rollen toe aan gebruikers die zich moeten abonneren op onderwerpen in uw gebeurtenisdomein. U beperkt de roltoewijzing alleen tot het onderwerp waar gebruikers zich op moeten abonneren.

Zie Ingebouwde rollen voor [Event Grid.](security-authorization.md#built-in-roles)

## <a name="subscribing-to-topics"></a>Abonneren op onderwerpen

Abonneren op gebeurtenissen in een onderwerp binnen een gebeurtenisdomein is hetzelfde als het maken van een gebeurtenisabonnement voor een aangepast onderwerp of het abonneren op een gebeurtenis vanuit een Azure-service. [](./custom-event-quickstart.md)

> [!IMPORTANT]
> Domeinonderwerp wordt beschouwd als **een automatisch beheerde** resource in Event Grid. U kunt een gebeurtenisabonnement maken op het domeinonderwerpbereik zonder het domeinonderwerp te maken. In dit geval maakt Event Grid automatisch het domeinonderwerp namens u. U kunt er natuurlijk nog steeds voor kiezen om het domeinonderwerp handmatig te maken. Met dit gedrag kunt u zich zorgen maken over één resource minder wanneer u te maken hebt met een groot aantal domeinonderwerpen. Wanneer het laatste abonnement op een domeinonderwerp wordt verwijderd, wordt het domeinonderwerp ook verwijderd, ongeacht of het domeinonderwerp handmatig is gemaakt of automatisch is gemaakt. 

### <a name="domain-scope-subscriptions"></a>Domeinbereikabonnementen

Gebeurtenisdomeinen maken ook domeinbereikabonnementen mogelijk. Een gebeurtenisabonnement op een gebeurtenisdomein ontvangt alle gebeurtenissen die naar het domein worden verzonden, ongeacht het onderwerp waar de gebeurtenissen naar worden verzonden. Domeinbereikabonnementen kunnen nuttig zijn voor beheer- en controledoeleinden.

## <a name="publishing-to-an-event-domain"></a>Publiceren naar een gebeurtenisdomein

Wanneer u een gebeurtenisdomein maakt, krijgt u een publicatie-eindpunt dat vergelijkbaar is met als u een onderwerp hebt gemaakt in Event Grid. 

Als u gebeurtenissen wilt publiceren naar een onderwerp in een gebeurtenisdomein, pusht u de gebeurtenissen op dezelfde manier naar het eindpunt van het domein als [voor een aangepast onderwerp.](./post-to-custom-topic.md) Het enige verschil is dat u het onderwerp moet opgeven waar u de gebeurtenis aan wilt leveren.

Als u bijvoorbeeld de volgende matrix met gebeurtenissen publiceert, wordt er een gebeurtenis met naar het onderwerp verzonden terwijl de gebeurtenis met `"id": "1111"` naar het onderwerp wordt `foo` `"id": "2222"` `bar` verzonden:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Gebeurtenisdomeinen verwerken publicatie naar onderwerpen voor u. In plaats van gebeurtenissen te publiceren naar elk onderwerp dat u afzonderlijk beheert, kunt u al uw gebeurtenissen publiceren naar het eindpunt van het domein. Event Grid zorgt ervoor dat elke gebeurtenis naar het juiste onderwerp wordt verzonden.

## <a name="limits-and-quotas"></a>Limieten en quota
Dit zijn de limieten en quota met betrekking tot gebeurtenisdomeinen:

- 100.000 onderwerpen per gebeurtenisdomein 
- 100 gebeurtenisdomeinen per Azure-abonnement 
- 500 gebeurtenisabonnementen per onderwerp in een gebeurtenisdomein
- 50 domeinbereikabonnementen 
- 5000 gebeurtenissen per seconde opnamefrequentie (in een domein)

Als deze limieten niet geschikt zijn voor u, opent u een ondersteuningsticket of stuurt u een e-mail naar [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Prijzen
Gebeurtenisdomeinen gebruiken dezelfde prijzen [voor bewerkingen](https://azure.microsoft.com/pricing/details/event-grid/) die alle andere functies in Event Grid gebruiken.

Bewerkingen werken hetzelfde in gebeurtenisdomeinen als in aangepaste onderwerpen. Elk ingress van een gebeurtenis naar een gebeurtenisdomein is een bewerking en elke bezorgingspoging voor een gebeurtenis is een bewerking.



## <a name="next-steps"></a>Volgende stappen

* Zie Gebeurtenisdomeinen beheren voor meer informatie over het instellen van [gebeurtenisdomeinen,](./how-to-event-domains.md)het maken van onderwerpen, het maken van gebeurtenisabonnementen en het publiceren van gebeurtenissen.
