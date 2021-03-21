---
title: Gebeurtenis domeinen in Azure Event Grid
description: In dit artikel wordt beschreven hoe u gebeurtenis domeinen gebruikt voor het beheren van de stroom van aangepaste gebeurtenissen naar uw verschillende zakelijke organisaties, klanten of toepassingen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 46a50a8ecc50bd1b80efcba41228564df1c36c9f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198650"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Informatie over gebeurtenis domeinen voor het beheren van Event Grid onderwerpen

In dit artikel wordt beschreven hoe u gebeurtenis domeinen gebruikt voor het beheren van de stroom van aangepaste gebeurtenissen naar uw verschillende zakelijke organisaties, klanten of toepassingen. Gebruik gebeurtenis domeinen voor het volgende:

* Architecturen voor multi tenant gebeurtenissen op schaal beheren.
* Uw autorisatie en verificatie beheren.
* Partitioneer uw onderwerpen zonder ze afzonderlijk te beheren.
* Vermijd afzonderlijk publiceren naar elk van uw onderwerp-eind punten.

## <a name="event-domain-overview"></a>Overzicht van gebeurtenis domeinen

Een gebeurtenis domein is een beheer programma voor een groot aantal Event Grid-onderwerpen die betrekking hebben op dezelfde toepassing. U kunt dit beschouwen als een meta onderwerp dat duizenden afzonderlijke onderwerpen kan hebben.

Gebeurtenis domeinen maken beschikbaar voor u dezelfde architectuur die wordt gebruikt door Azure-Services (zoals Storage en IoT Hub) om hun gebeurtenissen te publiceren. Hiermee kunt u gebeurtenissen naar duizenden onderwerpen publiceren. Domeinen bieden u ook autorisatie en verificatie controle over elk onderwerp, zodat u uw tenants kunt partitioneren.

## <a name="example-use-case"></a>Voorbeeld van een toepassing
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Toegangsbeheer

Met een domein krijgt u een nauw keurige autorisatie en verificatie controle voor elk onderwerp via Azure op rollen gebaseerd toegangs beheer (Azure RBAC). U kunt deze rollen gebruiken om elke Tenant in uw toepassing te beperken tot alleen de onderwerpen waaraan u hen toegang wilt verlenen.

Azure RBAC in gebeurtenis domeinen werkt op dezelfde manier als [Managed Access Control](security-authorization.md) werkt in de rest van Event grid en Azure. Gebruik Azure RBAC om aangepaste roldefinities in gebeurtenis domeinen te maken en af te dwingen.

### <a name="built-in-roles"></a>Ingebouwde rollen

Event Grid heeft twee ingebouwde roldefinities om Azure RBAC gemakkelijker te maken voor het werken met gebeurtenis domeinen. Deze rollen zijn **EventGrid EventSubscription Inzender (preview)** en **EventGrid EventSubscription Reader (preview)**. U wijst deze rollen toe aan gebruikers die zich moeten abonneren op onderwerpen in uw gebeurtenis domein. U bereikt de roltoewijzing alleen voor het onderwerp dat gebruikers moeten abonneren op.

Zie [ingebouwde rollen voor Event grid](security-authorization.md#built-in-roles)voor meer informatie over deze rollen.

## <a name="subscribing-to-topics"></a>Abonneren op onderwerpen

Abonneren op gebeurtenissen in een onderwerp binnen een gebeurtenis domein is hetzelfde als het [maken van een gebeurtenis abonnement op een aangepast onderwerp](./custom-event-quickstart.md) of het abonneren op een gebeurtenis van een Azure-service.

### <a name="domain-scope-subscriptions"></a>Domein bereik abonnementen

In gebeurtenis domeinen kunnen abonnementen op domein bereik worden toegepast. Een gebeurtenis abonnement op een gebeurtenis domein ontvangt alle gebeurtenissen die naar het domein worden verzonden, ongeacht het onderwerp waarnaar de gebeurtenissen worden verzonden. De domein bereik abonnementen kunnen nuttig zijn voor beheer-en controle doeleinden.

## <a name="publishing-to-an-event-domain"></a>Publiceren naar een gebeurtenis domein

Wanneer u een gebeurtenis domein maakt, krijgt u een publicatie-eind punt te zien als u een onderwerp in Event Grid hebt gemaakt. 

Als u gebeurtenissen naar een onderwerp in een gebeurtenis domein wilt publiceren, moet u de gebeurtenissen naar het eind punt van het domein pushen op [dezelfde manier als voor een aangepast onderwerp](./post-to-custom-topic.md). Het enige verschil is dat u het onderwerp moet opgeven waarnaar de gebeurtenis moet worden geleverd.

Als u bijvoorbeeld de volgende matrix met gebeurtenissen publiceert, wordt gebeurtenis verzonden met `"id": "1111"` het onderwerp, `foo` terwijl de gebeurtenis met `"id": "2222"` zou worden verzonden naar het onderwerp `bar` :

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

Gebeurtenis domeinen verwerken het publiceren naar onderwerpen voor u. In plaats van gebeurtenissen te publiceren naar elk onderwerp dat u afzonderlijk beheert, kunt u al uw gebeurtenissen naar het eind punt van het domein publiceren. Event Grid zorgt ervoor dat elke gebeurtenis wordt verzonden naar het juiste onderwerp.

## <a name="limits-and-quotas"></a>Limieten en quota
Dit zijn de limieten en quota die betrekking hebben op gebeurtenis domeinen:

- 100.000 onderwerpen per gebeurtenis domein 
- 100 gebeurtenis domeinen per Azure-abonnement 
- 500 gebeurtenis abonnementen per onderwerp in een gebeurtenis domein
- 50 domein bereik abonnementen 
- 5.000 gebeurtenissen per seconde opname frequentie (in een domein)

Als deze limieten niet aansluiten bij u, kunt u het product team bereiken door een ondersteunings ticket te openen of door een e-mail te verzenden naar [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Prijzen
Gebeurtenis domeinen gebruiken dezelfde [prijzen voor bewerkingen](https://azure.microsoft.com/pricing/details/event-grid/) die alle andere functies in Event grid gebruiken.

Bewerkingen werken hetzelfde in gebeurtenis domeinen als in aangepaste onderwerpen. Elke ingang van een gebeurtenis in een gebeurtenis domein is een bewerking en elke bezorgings poging voor een gebeurtenis is een bewerking.



## <a name="next-steps"></a>Volgende stappen

* Zie [gebeurtenis domeinen beheren](./how-to-event-domains.md)voor meer informatie over het instellen van gebeurtenis domeinen, het maken van onderwerpen, het maken van gebeurtenis abonnementen en het publiceren van gebeurtenissen.
