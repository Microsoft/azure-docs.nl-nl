---
title: Overzicht van Azure Service Bus beperking | Microsoft Docs
description: Overzicht van Service Bus beperking van de lagen standaard en Premium.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 436f9a40269f7eea4e31b55b9657d38849876eb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85340949"
---
# <a name="throttling-operations-on-azure-service-bus"></a>Beperkings bewerkingen op Azure Service Bus

De systeem eigen oplossingen van de Cloud bieden een begrip van onbeperkte resources die kunnen worden geschaald met uw werk belasting. Hoewel dit principe meer waar is in de Cloud dan bij on-premises systemen, zijn er nog steeds beperkingen in de Cloud.

Deze beperkingen kunnen ertoe leiden dat aanvragen voor client toepassingen in de lagen Standard en Premium worden beperkt, zoals wordt beschreven in dit artikel. 

## <a name="throttling-in-azure-service-bus-standard-tier"></a>Beperking in Azure Service Bus Standard-laag

De laag Azure Service Bus Standard werkt als een multi tenant-installatie met een prijs model voor betalen per gebruik. Hier zijn meerdere naam ruimten in hetzelfde cluster die de toegewezen resources delen. De Standard-laag is de aanbevolen keuze voor ontwikkel-, test-en QA-omgevingen en productie systemen met een lage door voer.

In het verleden had Azure Service Bus zeer beperkende limieten die strikt afhankelijk zijn van het resource gebruik. Er is echter een kans om de beperkings logica te verfijnen en voorspel bare beperkings gedrag te bieden aan alle naam ruimten die deze resources delen.

In een poging om te zorgen voor eerlijk gebruik en distributie van resources voor alle Azure Service Bus standaard naam ruimten die dezelfde resources gebruiken, is de beperkings logica gewijzigd in op Credit gebaseerd.

> [!NOTE]
> Het is belang rijk te weten dat de beperking ***niet nieuw*** is voor Azure service bus of een native Cloud service.
>
> Op basis van krediet beperking is het verfijnen van de manier waarop verschillende naam ruimten resources delen in een multi tenant-omgeving met een standaard laag en zodoende redelijk gebruik kunnen maken door alle naam ruimten die de resources delen.

### <a name="what-is-credit-based-throttling"></a>Wat is op krediet gebaseerd beperkende beperkingen?

Op Credit gebaseerde beperking beperkt het aantal bewerkingen dat kan worden uitgevoerd op een bepaalde naam ruimte in een specifieke tijds periode. 

Hieronder ziet u de werk stroom voor op krediet gebaseerde beperking- 

  * Aan het begin van elke periode geven we een bepaald aantal tegoeden aan elke naam ruimte.
  * Bewerkingen die worden uitgevoerd door de client toepassingen van de afzender of ontvanger worden meegeteld bij deze tegoeden (en afgetrokken van de beschik bare tegoeden).
  * Als de tegoeden zijn uitgeput, worden volgende bewerkingen beperkt tot het begin van de volgende tijds periode.
  * Tegoed wordt aan het begin van de volgende tijds periode aangevuld.

### <a name="what-are-the-credit-limits"></a>Wat zijn de krediet limieten?

De krediet limieten zijn momenteel per seconde ingesteld op ' 1000 '.

Niet alle bewerkingen zijn gelijk gemaakt. Dit zijn de kosten voor de tegoeden van elk van de bewerkingen- 

| Bewerking | Tegoed kosten|
|-----------|-----------|
| Gegevens bewerkingen (Send, SendAsync, receive, ReceiveAsync, Peek) |1 tegoed per bericht |
| Beheer bewerkingen (maken, lezen, bijwerken, verwijderen voor wacht rijen, onderwerpen, abonnementen, filters) | 10 tegoed |

> [!NOTE]
> Houd er rekening mee dat wanneer u naar een onderwerp verzendt, elk bericht wordt geëvalueerd op basis van filter (s) voordat het op het abonnement beschikbaar wordt gesteld.
> Elke filter evaluatie telt ook op basis van de krediet limiet (d.w.z. 1 tegoed per filter evaluatie).
>

### <a name="how-will-i-know-that-im-being-throttled"></a>Hoe weet ik dat ik een beperking heb?

Wanneer de client toepassings aanvragen worden beperkt, wordt de onderstaande server reactie ontvangen door uw toepassing en geregistreerd.

```
The request was terminated because the entity is being throttled. Error code: 50009. Please wait 2 seconds and try again.
```

### <a name="how-can-i-avoid-being-throttled"></a>Hoe kan ik voor komen dat ze worden beperkt?

Met gedeelde resources is het belang rijk dat u een billijk gebruik afdwingt voor verschillende Service Bus naam ruimten die deze resources delen. Bandbreedte beperking zorgt ervoor dat elke piek in een enkele workload geen andere werk belastingen op dezelfde resources heeft om te worden beperkt.

Zoals verderop in het artikel wordt vermeld, is er geen risico om te worden beperkt omdat de client-Sdk's (en andere Azure PaaS-aanbiedingen) het standaard beleid voor opnieuw proberen ingebouwd. Alle vertraagde aanvragen worden opnieuw uitgevoerd met exponentiële uitstel en gaan uiteindelijk door wanneer de tegoeden worden aangevuld.

Het begrijpen van sommige toepassingen kan gevoelig zijn om te worden beperkt. In dat geval kunt u het beste [uw huidige service bus Standard-naam ruimte migreren naar Premium](service-bus-migrate-standard-premium.md). 

Bij de migratie kunt u toegewezen resources toewijzen aan uw Service Bus-naam ruimte en de resources op de juiste wijze schalen als er een piek in uw werk belasting is en de kans dat deze wordt beperkt, wordt verminderd. Daarnaast kunt u de resources die zijn toegewezen aan uw naam ruimte omlaag schalen als uw werk belasting wordt beperkt tot normale niveaus.

## <a name="throttling-in-azure-service-bus-premium-tier"></a>Beperking in Azure Service Bus Premium-laag

De [Azure service bus Premium](service-bus-premium-messaging.md) -laag wijst toegewezen resources, in termen van Messa ging-eenheden, toe aan elke naam ruimte-instelling door de klant. Deze speciale resources bieden voorspel bare door Voer en latentie en worden aanbevolen voor hoge door Voer of gevoelige systemen voor productie kwaliteit.

Daarnaast kunnen klanten met de Premium-laag ook hun doorvoer capaciteit opschalen wanneer deze pieken in de werk belasting ervaren.

### <a name="how-does-throttling-work-in-service-bus-premium"></a>Hoe werkt beperking voor Service Bus Premium?

Met exclusieve resource toewijzing voor Service Bus Premium wordt beperking uitsluitend bepaald door de beperkingen van de resources die aan de naam ruimte zijn toegewezen.

Als het aantal aanvragen meer is dan de huidige resources kunnen service, worden de aanvragen beperkt.

### <a name="how-will-i-know-that-im-being-throttled"></a>Hoe weet ik dat ik een beperking heb?

Er zijn verschillende manieren om de beperking van Azure Service Bus Premium te identificeren. 
  * **Vertraagde aanvragen** worden weer gegeven op de [Azure monitor aanvraag gegevens](service-bus-metrics-azure-monitor.md#request-metrics) om te bepalen hoeveel aanvragen zijn beperkt.
  * Hoog **CPU-gebruik** geeft aan dat de huidige resource toewijzing hoog is en dat aanvragen mogelijk worden beperkt als de huidige werk belasting niet vermindert.
  * Hoog **geheugen gebruik** geeft aan dat de huidige resource toewijzing hoog is en dat aanvragen mogelijk worden beperkt als de huidige werk belasting niet vermindert.

### <a name="how-can-i-avoid-being-throttled"></a>Hoe kan ik voor komen dat ze worden beperkt?

Aangezien de Service Bus Premium-naam ruimte al toegewezen resources heeft, kunt u de kans verkleinen door het aantal Messa ging-eenheden dat aan uw naam ruimte is toegewezen, te verg Roten in de gebeurtenis (of anticiperen) van een piek in de werk belasting.

U kunt omhoog/omlaag schalen door [runbooks](../automation/automation-create-alert-triggered-runbook.md) te maken die kunnen worden geactiveerd door wijzigingen in de bovenstaande metrische gegevens.

## <a name="faqs"></a>Veelgestelde vragen

### <a name="how-does-throttling-affect-my-application"></a>Hoe beïnvloedt de beperking mijn toepassing?

Wanneer een aanvraag wordt beperkt, houdt dit in dat de service bezet is omdat er meer aanvragen worden uitgevoerd dan voor de resources zijn toegestaan. Als dezelfde bewerking na enkele ogen blikken opnieuw wordt uitgevoerd, kan de aanvraag worden gehonoreerd zodra de service de huidige werk belasting heeft door lopen.

Omdat beperking het verwachte gedrag van een native Cloud service is, hebben we de logica voor opnieuw proberen in de Azure Service Bus SDK zelf gemaakt. De standaard instelling is ingesteld op automatisch opnieuw proberen met een exponentiële uitschakeling om ervoor te zorgen dat de aanvraag niet telkens wordt beperkt.

De standaard logica voor opnieuw proberen wordt toegepast op elke bewerking.

### <a name="does-throttling-result-in-data-loss"></a>Is er sprake van een beperking van gegevens verlies?

Azure Service Bus is geoptimaliseerd voor persistentie, zorgen we ervoor dat alle gegevens die naar Service Bus worden verzonden, worden opgeslagen in de opslag voordat de service het succes van de aanvraag bevestigt.

Als de aanvraag is voltooid (bevestigd) door Service Bus, betekent dit dat de aanvraag door Service Bus is verwerkt. Als Service Bus een ' NACK ' retourneert (fout), betekent dit dat Service Bus de aanvraag niet heeft kunnen verwerken en de client toepassing de aanvraag opnieuw moet uitvoeren.

Wanneer een aanvraag echter wordt beperkt, betekent dit dat de service de aanvraag nu niet kan accepteren en verwerken vanwege beperkingen van de resource. Dit betekent niet dat er gegevens verloren zijn gegaan **,** omdat Service Bus eenvoudigweg de aanvraag niet heeft bekeken. In dit geval moet u, afhankelijk van het standaard beleid voor opnieuw proberen van de Service Bus SDK, ervoor zorgen dat de aanvraag uiteindelijk wordt verwerkt.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende geavanceerde onderwerpen voor meer informatie en voor beelden van het gebruik van Service Bus Messa ging:

* [Overzicht van Service Bus berichten](service-bus-messaging-overview.md)
* [Snelstart: Berichten verzenden en ontvangen met behulp van Azure Portal en .NET](service-bus-quickstart-portal.md)
* [Zelfstudie: Voorraad bijwerken met Azure Portal en onderwerpen/abonnementen](service-bus-tutorial-topics-subscriptions-portal.md)

