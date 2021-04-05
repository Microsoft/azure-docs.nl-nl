---
title: Azure Service Bus Messa ging-entiteiten automatisch door sturen
description: In dit artikel wordt beschreven hoe u een Azure Service Bus wachtrij of een abonnement koppelt aan een andere wachtrij of een onderwerp.
ms.topic: article
ms.date: 01/20/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 80bef52d568130fa800a1da661f4867abb3df02c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678985"
---
# <a name="chaining-service-bus-entities-with-autoforwarding"></a>Service Bus entiteiten koppelen met autoforwarding

Met de functie voor het *door sturen* van service bus kunt u een wachtrij of abonnement koppelen aan een andere wachtrij of een onderwerp dat deel uitmaakt van dezelfde naam ruimte. Wanneer automatisch door sturen is ingeschakeld, verwijdert Service Bus berichten die in de eerste wachtrij of dit abonnement (bron) worden geplaatst en worden deze in de tweede wachtrij of het onderwerp (bestemming) geplaatst. Het is nog steeds mogelijk om rechtstreeks een bericht naar de doel entiteit te verzenden.

> [!NOTE]
> De laag basis van Service Bus biedt geen ondersteuning voor de functie voor automatisch door sturen. De standaard-en Premium-laag bieden ondersteuning voor de functie. Zie [Service Bus prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor verschillen tussen deze lagen.

## <a name="using-autoforwarding"></a>Autoforwarden gebruiken

U kunt autoforwarding inschakelen door de eigenschappen [QueueDescription. ForwardTo][QueueDescription.ForwardTo] of [SubscriptionDescription. ForwardTo][SubscriptionDescription.ForwardTo] in te stellen voor de [QueueDescription][QueueDescription] -of [SubscriptionDescription][SubscriptionDescription] -objecten voor de bron, zoals in het volgende voor beeld:

```csharp
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

De doel entiteit moet bestaan op het moment dat de bron entiteit wordt gemaakt. Als de doel entiteit niet bestaat, retourneert Service Bus een uitzonde ring wanneer u wordt gevraagd om de bron entiteit te maken.

U kunt automatisch door sturen gebruiken om een afzonderlijk onderwerp uit te schalen. Service Bus beperkt het [aantal abonnementen op een bepaald onderwerp](service-bus-quotas.md) tot 2.000. U kunt extra abonnementen bieden door onderwerpen van het tweede niveau te maken. Zelfs als u niet gebonden bent aan de Service Bus beperking van het aantal abonnementen, kan het toevoegen van een tweede niveau aan onderwerpen de algehele door Voer van uw onderwerp verbeteren.

![Diagram van een scenario voor het door sturen van berichten met een bericht dat is verwerkt via een onderwerp met bestellingen dat kan worden ondergebracht in een van de drie onderwerpen op het tweede niveau.][0]

U kunt ook autoforwarding gebruiken om de bericht afzenders van ontvangers te loskoppelen. Denk bijvoorbeeld aan een ERP-systeem dat bestaat uit drie modules: order verwerking, voorraad beheer en beheer van klant relaties. Elk van deze modules genereert berichten die in een bijbehorend onderwerp in de wachtrij worden geplaatst. Anne en Robert zijn verkoop medewerkers die geïnteresseerd zijn in alle berichten die betrekking hebben op hun klanten. Als u deze berichten wilt ontvangen, maakt Anne en Bob elk een persoonlijke wachtrij en een abonnement op elk van de ERP-onderwerpen waarmee automatisch alle berichten worden doorgestuurd naar de wachtrij.

![Diagram van een scenario voor het door sturen van een bewerking waarbij drie verwerkings modules berichten verzenden via drie overeenkomende onderwerpen naar twee afzonderlijke wacht rijen.][1]

Als Anne op vakantie gaat, wordt de persoonlijke wachtrij in plaats van het ERP-onderwerp opgevuld. In dit scenario, omdat een vertegenwoordiger geen berichten heeft ontvangen, hebben geen van de ERP-onderwerpen het quotum bereikt.

> [!NOTE]
> Als de functie voor automatisch door sturen is ingesteld, wordt de waarde voor AutoDeleteOnIdle op **zowel de bron-als de bestemming** automatisch op de maximum waarde van het gegevens type geconfigureerd.
> 
>   - Op de bron zijde fungeert autoforwarding als een ontvangst bewerking. De bron waarvoor het instellen van de instellingen voor het door sturen van de computer is, is nooit echt ' inactief '.
>   - Aan de kant van de bestemming wordt dit gedaan om ervoor te zorgen dat er altijd een bestemming is om het bericht door te sturen naar.

## <a name="autoforwarding-considerations"></a>Aandachtspunten voor het door sturen van fouten

Als de doel entiteit te veel berichten verzamelt en het quotum overschrijdt, of als de doel entiteit is uitgeschakeld, voegt de bron entiteit de berichten toe aan de [wachtrij met onbestelbare](service-bus-dead-letter-queues.md) meldingen totdat er ruimte is in het doel (of als de entiteit opnieuw is ingeschakeld). Deze berichten blijven Live in de wachtrij met onbestelbare meldingen, dus u moet ze expliciet ontvangen en verwerken vanuit de wachtrij voor onbestelbare brieven.

Wanneer u afzonderlijke onderwerpen koppelt aan een samengesteld onderwerp met veel abonnementen, is het raadzaam dat u over een beperkt aantal abonnementen beschikt op het onderwerp van het eerste niveau en veel abonnementen op de onderwerpen van het tweede niveau. Een voor beeld: een onderwerp van het hoogste niveau met 20 abonnementen, dat elk wordt gekoppeld aan een onderwerp van het tweede niveau met 200-abonnementen, voor een hogere door voer dan een onderwerp van het hoogste niveau met 200-abonnementen, is elk gekoppeld aan een onderwerp op het tweede niveau met 20 abonnementen.

Service Bus één bewerking per doorgestuurd bericht. Bijvoorbeeld: een bericht verzenden naar een onderwerp met 20 abonnementen, dat elk is geconfigureerd voor het door sturen van berichten naar een andere wachtrij of een ander onderwerp, wordt gefactureerd als 21 bewerkingen als alle abonnementen op het eerste niveau een kopie van het bericht ontvangen.

Als u een abonnement wilt maken dat is gekoppeld aan een andere wachtrij of een ander onderwerp, moet de maker van het abonnement over **beheer** machtigingen beschikken voor zowel de bron-als de doel entiteit. Voor het verzenden van berichten naar het bron onderwerp zijn alleen **Verzend** machtigingen vereist voor het bron onderwerp.

Maak geen keten die meer dan 4 hops overschrijdt. Berichten die meer dan 4 hops overschrijden, zijn onbestelbaar.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor gedetailleerde informatie over het door sturen van gegevens:

* [ForwardTo][QueueDescription.ForwardTo]
* [QueueDescription][QueueDescription]
* [SubscriptionDescription][SubscriptionDescription]

Zie voor meer informatie over het verbeteren van de prestaties van Service Bus. 

* [Aanbevolen procedures voor prestatie verbeteringen met behulp van Service Bus Messa ging](service-bus-performance-improvements.md)
* [Gepartitioneerde bericht entiteiten][Partitioned messaging entities].

[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto#Microsoft_ServiceBus_Messaging_QueueDescription_ForwardTo
[SubscriptionDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.forwardto#Microsoft_ServiceBus_Messaging_SubscriptionDescription_ForwardTo
[QueueDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[SubscriptionDescription]: /dotnet/api/microsoft.servicebus.messaging.queuedescription
[0]: ./media/service-bus-auto-forwarding/IC628631.gif
[1]: ./media/service-bus-auto-forwarding/IC628632.gif
[Partitioned messaging entities]: service-bus-partitioning.md
