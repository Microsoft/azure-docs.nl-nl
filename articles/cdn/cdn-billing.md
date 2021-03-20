---
title: Wat is Azure CDN facturering? Microsoft Docs
description: Meer informatie over de facturerings structuur voor inhoud die wordt gehost door Azure Content Delivery Network, met inbegrip van facturerings regio's, leverings kosten en voor het beheren van kosten.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: allensu
ms.openlocfilehash: aa2f00a732a3978524fc017481285859c9535387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018678"
---
# <a name="understanding-azure-cdn-billing"></a>Inzicht in facturering voor Azure CDN

Deze veelgestelde vragen bevat een beschrijving van de facturerings structuur voor inhoud die wordt gehost door Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Wat is een facturerings regio?
Een facturerings regio is een geografisch gebied dat wordt gebruikt om te bepalen welke tarief in rekening wordt gebracht voor de levering van objecten van Azure CDN. De huidige facturerings zones en de bijbehorende regio's zijn als volgt:

- Zone 1: Noord-Amerika, Europa, Midden-Oosten en Afrika

- Zone 2: Azië en Stille Oceaan (inclusief Japan)

- Zone 3: Zuid-Amerika

- Zone 4: Australië en Nieuw-Zeeland

- Zone 5: India

Zie [Azure CDN pop-locaties per regio](./cdn-pop-locations.md)voor meer informatie over pop-regio's (Point-of-Presence). Een POP die zich in Mexico bevindt, bevindt zich bijvoorbeeld in de Noord-Amerika regio en is daarom opgenomen in zone 1. 

Zie [Content Delivery Network prijzen](https://azure.microsoft.com/pricing/details/cdn/)voor meer informatie over Azure CDN prijzen.

## <a name="how-are-delivery-charges-calculated-by-region"></a>Hoe worden de leverings kosten berekend op basis van de regio?
De Azure CDN facturerings regio is gebaseerd op de locatie van de bron server die de inhoud levert aan de eind gebruiker. De bestemming (fysieke locatie) van de client wordt niet beschouwd als de facturerings regio.

Als een gebruiker die zich in Mexico bevindt bijvoorbeeld een aanvraag afgeeft en deze aanvraag wordt verwerkt door een server die zich in een Verenigde Staten POP bevindt als gevolg van peering of verkeers omstandigheden, wordt de facturerings regio de Verenigde Staten.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Wat is een factureer bare Azure CDN trans actie?
Elke HTTP (S)-aanvraag die bij het CDN eindigt, is een factureer bare gebeurtenis. Dit omvat alle antwoord typen: geslaagd, mislukt of andere. Verschillende antwoorden kunnen echter verschillende verkeers bedragen genereren. Bijvoorbeeld, *304 niet gewijzigd* en andere antwoorden met alleen kopteksten genereren weinig verkeer omdat ze een kleine reactie op de header zijn; fout Reacties (bijvoorbeeld *404 niet gevonden*) zijn factureer bare, maar maken een kleine kosten vanwege de nettolading van het kleine antwoord.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Wat andere Azure-kosten zijn gekoppeld aan Azure CDN gebruiken?
Het gebruik van Azure CDN heeft ook enkele gebruiks kosten in rekening gebracht voor de services die als oorsprong voor uw objecten worden gebruikt. Deze kosten zijn doorgaans een kleine fractie van de totale kosten voor het CDN-gebruik.

Als u Azure Blob Storage gebruikt als oorsprong voor uw inhoud, worden ook de volgende opslag kosten in rekening gebracht voor cache opvullingen:

- Werkelijk gebruikte GB: de daad werkelijke opslag van uw bron objecten.

- Trans acties: als dat nodig is om de cache te vullen.

- Overdrachten in GB: de hoeveelheid gegevens die wordt overgedragen om de CDN-caches op te vullen.

> [!NOTE]
> Vanaf 2019 oktober, als u Azure CDN van micro soft gebruikt, zijn de kosten voor gegevens overdracht van oorsprong die worden gehost in azure naar CDN Pop's gratis. Azure CDN van Verizon en Azure CDN van Akamai zijn onderhevig aan de tarieven die hieronder worden beschreven.

Zie [Azure Storage facturering – band breedte, trans acties en capaciteit](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)voor meer informatie over Azure Storage facturering.

Als u gebruikmaakt van *gehoste service levering*, worden er kosten in rekening gebracht:

- Azure-reken tijd: de reken instanties die fungeren als de oorsprong.

- Azure Compute Transfer: de gegevens overdracht van de reken instanties om de Azure CDN-caches op te vullen.

Als uw client aanvragen voor byte bereik gebruikt (ongeacht de oorspronkelijke service), zijn de volgende overwegingen van toepassing:

- Een *byte-bereik aanvraag* is een factureer bare trans actie op het CDN. Wanneer een client een byte-bereik aanvraag uitgeeft, is deze aanvraag voor een subset (bereik) van het object. Het CDN reageert alleen met een gedeeltelijk deel van de aangevraagde inhoud. Deze gedeeltelijke reactie is een factureer bare trans actie en de overdrachts hoeveelheid is beperkt tot de grootte van het bereik antwoord (plus kopteksten).

- Wanneer een aanvraag alleen binnenkomt voor een deel van een object (door een byte-bereik header op te geven), haalt het CDN het volledige object op in de cache. Als gevolg hiervan, zelfs als de factureer bare trans actie van het CDN voor een gedeeltelijk antwoord is, kan de factureer bare trans actie van de oorsprong de volledige grootte van het object omvatten.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Hoeveel overdrachts activiteit wordt uitgevoerd om de cache te ondersteunen?
Telkens wanneer een CDN-POP de cache moet invullen, wordt er een aanvraag naar de oorsprong voor het object dat in de cache wordt opgeslagen. Als gevolg hiervan wordt de oorsprong een factureer bare trans actie op elke cache-Misser. Het aantal missers in de cache is afhankelijk van een aantal factoren:

- Hoe kan de inhoud in de cache worden opgeslagen: als de inhoud een hoge TTL-waarde (time-to-Live)/Expiration heeft en regel matig wordt geopend zodat deze populair blijft in de cache, wordt de grootste deel van de belasting verwerkt door het CDN. Een typische verhouding in de cache-treffer is ruim meer dan 90%, wat betekent dat minder dan 10% van client aanvragen moet worden teruggestuurd naar de oorsprong, hetzij voor een cache-Missing of het vernieuwen van objecten.

- Hoeveel knoop punten moet het object laden: telkens wanneer een knoop punt een object van de oorsprong laadt, wordt er een factureer bare trans actie gemaakt. Als gevolg hiervan resulteert meer globale inhoud (toegankelijk vanaf meer knoop punten) in meer factureer bare trans acties.

- TTL-invloed: een hogere TTL voor een object houdt in dat deze van de oorsprong minder vaak moet worden opgehaald. Dit betekent ook dat clients, zoals browsers, het object langer in de cache kunnen opslaan, waardoor de trans acties naar het CDN kunnen worden verminderd.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Welke oorspronkelijke Services komen in aanmerking voor gratis gegevens overdracht met Azure CDN van micro soft? 
Als u een van de volgende Azure-Services gebruikt als uw CDN-oorsprong, worden er geen kosten in rekening gebracht voor gegevens overdracht van de oorsprong naar de CDN-Pop's. 

- Azure Storage
- Azure Media Services
- Azure Virtual Machines
- Virtual Network
- Load Balancer
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Azure-app-service
- Azure Functions
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Azure Data Explorer
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL Database
- Azure SQL Managed Instance
- Azure Cache voor Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hoe kan ik mijn kosten het meest effectief beheren?
Stel de langst mogelijke TTL in voor uw inhoud.