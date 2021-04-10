---
title: Azure-Event Hubs-.NET-uitzonde ringen
description: In dit artikel vindt u een lijst met Azure Event Hubs .NET Messa ging-uitzonde ringen en voorgestelde acties.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 09/23/2020
ms.author: spelluru
ms.openlocfilehash: af2d820fd255d041ade21a00d7d7a9ac6b12fcba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745899"
---
# <a name="eventhubsexception---net"></a>EventHubsException-.NET
Er wordt een **EventHubsException** geactiveerd wanneer een bewerking die specifiek is voor Event hubs, een probleem heeft veroorzaakt, met inbegrip van fouten in de service en specifiek voor de client. 

## <a name="exception-information"></a>Uitzonderings gegevens
De uitzonde ring omvat de volgende contextuele informatie die u kan helpen bij het leren van de context van de fout en de relatieve ernst. 

- **IsTransient**: geeft aan of de uitzonde ring wordt beschouwd als herstelbaar of niet. Als er sprake is van een tijdelijke, is het juiste beleid voor opnieuw proberen al toegepast en zijn er geen nieuwe pogingen gedaan.
- **Reden**: biedt een aantal bekende redenen voor de fout die de hoofd oorzaak kan categoriseren en verduidelijkt. Deze redenen zijn bedoeld om het Toep assen van uitzonderings filtering en andere logica voor het inspecteren van de tekst van een uitzonderings bericht niet ideaal te maken. Enkele belang rijke oorzaken voor fouten zijn:
    - **Client gesloten**: treedt op wanneer een event hub-client die al is gesloten of verwijderd. We raden u aan de toepassings code te controleren om ervoor te zorgen dat objecten van de Event Hubs-client bibliotheek worden gemaakt en gesloten in het gewenste bereik.
    - **Time-out van service**: geeft aan dat de Event hubs-service niet binnen de verwachte hoeveelheid tijd reageert op een bewerking. Dit probleem kan zijn veroorzaakt door een tijdelijk netwerk probleem of een service probleem. De aanvraag is al dan niet met de Event Hubs-service voltooid. de status is niet bekend. Het is raadzaam om de huidige status te verifiëren en het opnieuw te proberen als dat nodig is.
    - **Quotum overschreden**: geeft aan dat er te veel actieve Lees bewerkingen zijn voor één consumer groep. Deze limiet is afhankelijk van de laag van de Event Hubs naam ruimte en is mogelijk nodig om naar een hogere laag te verplaatsen. Een alternatief zou zijn om extra consumenten groepen te maken en ervoor te zorgen dat het aantal lees bewerkingen van de consument voor elke groep binnen de limiet valt. Zie [Azure Event hubs quota's en limieten](event-hubs-quotas.md)voor meer informatie.
    - De **bericht grootte is overschreden**: gebeurtenis gegevens als Maxi maal toegestane grootte voor zowel een afzonderlijke gebeurtenis als een batch gebeurtenissen. Het bevat de gegevens van de gebeurtenis en eventuele bijbehorende meta gegevens en systeem overhead. Om deze fout op te lossen, vermindert u het aantal gebeurtenissen dat in een batch is verzonden of vermindert u de grootte van de gegevens die in het bericht zijn opgenomen. Als de grootte limieten kunnen worden gewijzigd, raadpleegt u [Azure Event hubs quota's en limieten](event-hubs-quotas.md) voor specifieke informatie.
    - **Gebruikers verbinding verbroken**: een consument heeft de verbinding met de Event hub-service van het event hub-exemplaar verbroken. Normaal gesp roken treedt het probleem op wanneer een consument met een hoger eigenaars niveau eigenaar is van een partitie en een combi natie van Consumer groepen.
    - Kan de **resource niet vinden**: de Event hubs-service kan geen resource vinden, zoals een event hub, consumenten groep of partitie. Mogelijk is het verwijderd of is er een probleem met de Event Hubs service zelf.

## <a name="handling-exceptions"></a>Afhandeling van uitzonderingen
U kunt op verschillende manieren reageren op een specifieke fout reden voor de **EventHubException**  . Een manier is het Toep assen van een uitzonderings filter component als onderdeel van het blok catch.

```csharp
try
{
    // Read events using the consumer client
}
catch (EventHubsException ex) where 
    (ex.Reason == EventHubsException.FailureReason.ConsumerDisconnected)
{
    // Take action based on a consumer being disconnected
}
```

## <a name="next-steps"></a>Volgende stappen
Er zijn andere uitzonde ringen die worden beschreven in het [verouderde artikel](event-hubs-messaging-exceptions.md). Sommige van deze zijn alleen van toepassing op de verouderde Event Hubs .NET-client bibliotheek.