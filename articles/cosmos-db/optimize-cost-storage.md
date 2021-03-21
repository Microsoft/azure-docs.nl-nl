---
title: Opslagkosten optimaliseren in Azure Cosmos DB
description: In dit artikel wordt uitgelegd hoe u opslag kosten beheert voor de gegevens die zijn opgeslagen in Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 92bd3ff925080def4b2f074d07e662dfdbdbee01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93080847"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Opslagkosten optimaliseren in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB biedt onbeperkte opslag en door voer. In tegens telling tot door Voer, die u moet inrichten/configureren in uw Azure Cosmos-containers of-data bases, wordt de opslag gefactureerd op basis van een verbruiks basis. U wordt alleen gefactureerd voor de logische opslag die u gebruikt en u hoeft geen opslag vooraf te reserveren. Storage wordt automatisch omhoog en omlaag geschaald op basis van de gegevens die u toevoegt of verwijdert aan een Azure Cosmos-container.

## <a name="storage-cost"></a>Opslagkosten

Opslag wordt gefactureerd met de eenheid van GB. Lokale SSD-back-upopslag wordt gebruikt door uw gegevens en indexering. De totale gebruikte opslag ruimte is gelijk aan de opslag die is vereist voor de gegevens en indexen die worden gebruikt in alle regio's waar u Azure Cosmos DB gebruikt. Als u een Azure Cosmos-account globaal in drie regio's repliceert, betaalt u voor de totale opslag kosten in elk van deze drie regio's. Zie [capaciteits planner](https://www.documentdb.com/capacityplanner) voor het maken van een schatting van uw opslag vereiste. De kosten voor opslag in Azure Cosmos DB zijn $0,25 GB per maand, zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor de meest recente updates. U kunt waarschuwingen instellen om de opslag te bepalen die wordt gebruikt door de Azure Cosmos-container om uw opslag te bewaken, zie het artikel [monitor Azure Cosmos DB](./monitor-cosmos-db.md)).

## <a name="optimize-cost-with-item-size"></a>Kosten optimaliseren met item grootte

Azure Cosmos DB verwacht dat de grootte van het item 2 MB of minder is voor optimale prestaties en kosten voordelen. Als u een item nodig hebt om meer dan 2 MB aan gegevens op te slaan, kunt u het schema van het item opnieuw ontwerpen. In zeldzame gevallen kan het schema niet opnieuw worden ontworpen, kunt u het item splitsen in subitems en deze logisch koppelen met een algemene id (ID). Alle Azure Cosmos DB functies werken consistent door deze logische id te verankeren.

## <a name="optimize-cost-with-indexing"></a>Kosten optimaliseren met indexeren

Standaard worden de gegevens automatisch geïndexeerd, waardoor de totale hoeveelheid verbruikte opslag kan worden verg root. U kunt echter aangepast index beleid Toep assen om deze overhead te verminderen. Automatische indexering die niet is afgestemd op het beleid is ongeveer 10-20% van de grootte van het item. Door het verwijderen of aanpassen van het index beleid betaalt u geen extra kosten voor schrijf bewerkingen en hebt u geen extra doorvoer capaciteit nodig. Zie [indexeren in azure Cosmos DB](index-policy.md) voor het configureren van aangepaste indexerings beleidsregels. Als u al eerder met relationele data bases hebt gewerkt, kunt u zien dat "alles indexeren" betekent dat de opslag of een hogere versie wordt verdubbeld. In Azure Cosmos DB, in het mediaan geval is het echter veel lager. In Azure Cosmos DB is de opslag overhead van de index meestal laag (10-20%) zelfs bij automatische indexering, omdat deze is ontworpen voor een geringe opslag capaciteit. Door het indexerings beleid te beheren, kunt u het bereik van de index footprint en query prestaties op een meer verfijnde manier beheren.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimaliseer kosten met tijd tot Live en wijzigings invoer

Wanneer u de gegevens niet meer nodig hebt, kunt u deze probleemloos verwijderen uit uw Azure Cosmos-account met behulp van [time to Live](time-to-live.md), [feed Change](change-feed.md) of kunt u de oude gegevens migreren naar een ander gegevens archief, zoals Azure Blob Storage of Azure Data Warehouse. Met time to Live of TTL kunt Azure Cosmos DB automatisch items uit een container verwijderen na een bepaalde tijds periode. Standaard kunt u time to Live instellen op container niveau en de waarde per item overschrijven. Nadat u de TTL hebt ingesteld op een container of op item niveau, verwijdert Azure Cosmos DB deze items automatisch na de periode vanaf het moment dat ze voor het laatst zijn gewijzigd. Met behulp van Change feed kunt u gegevens migreren naar een andere container in Azure Cosmos DB of naar een extern gegevens archief. De migratie neemt nul in beslag en wanneer u klaar bent met de migratie, kunt u de levens duur van de Azure Cosmos-container verwijderen of zo configureren dat deze actief is.

## <a name="optimize-cost-with-rich-media-data-types"></a>Kosten optimaliseren met uitgebreide media gegevens typen 

Als u uitgebreide media typen wilt opslaan, bijvoorbeeld Video's, afbeeldingen enzovoort, hebt u een aantal opties in Azure Cosmos DB. Een optie is om deze uitgebreide media typen op te slaan als Azure Cosmos-items. Er is een limiet van 2 MB per item, en u kunt deze limiet vermijden door het gegevens item in meerdere subitems te koppelen. U kunt ze ook opslaan in Azure Blob Storage en de meta gegevens gebruiken om ernaar te verwijzen vanuit uw Azure Cosmos-items. Er zijn een aantal voor delen en nadelen met deze aanpak. Met de eerste benadering krijgt u de beste prestaties in de loop van de latentie, doorvoer-Sla's en kant-en-klare algemene distributie mogelijkheden voor de uitgebreide media gegevens typen naast uw reguliere Azure Cosmos-items. De ondersteuning is echter beschikbaar voor een hogere prijs. Door media op te slaan in Azure Blob-opslag, kunt u de totale kosten verlagen. Als latentie kritiek is, kunt u Premium Storage gebruiken voor Rich Media-bestanden waarnaar wordt verwezen vanuit Azure Cosmos-items. Dit is geïntegreerd met CDN voor installatie kopieën van de Edge-server tegen lagere kosten om de geo-beperking te omzeilen. De vervolg keuzelijst met dit scenario is dat u met twee services-Azure Cosmos DB en Azure Blob-opslag moet omgaan, waardoor de operationele kosten kunnen worden verhoogd. 

## <a name="check-storage-consumed"></a>Verbruikte opslag controleren

Om het opslag verbruik van een Azure Cosmos-container te controleren, kunt u een HEAD uitvoeren of aanvragen ophalen voor de container en de `x-ms-request-quota` en de `x-ms-request-usage` kopteksten controleren. Als u werkt met de .NET SDK, kunt u ook de eigenschappen [DocumentSizeQuota](/previous-versions/azure/dn850325(v%3Dazure.100))en [DocumentSizeUsage](/previous-versions/azure/dn850324(v=azure.100)) gebruiken om de gebruikte opslag ruimte op te halen.

## <a name="using-sdk"></a>SDK gebruiken

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Volgende stappen

Daarna kunt u meer te weten komen over cost Optimization in Azure Cosmos DB met de volgende artikelen:

* Meer informatie over het [optimaliseren voor ontwikkeling en testen](optimize-dev-test.md)
* Meer informatie over [uw Azure Cosmos DB factuur](understand-your-bill.md)
* Meer informatie over het [optimaliseren van doorvoer kosten](optimize-cost-throughput.md)
* Meer informatie over [het optimaliseren van de kosten van lees-en schrijf bewerkingen](optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten van query's](./optimize-cost-reads-writes.md)
* Meer informatie over [het optimaliseren van de kosten voor Azure Cosmos-accounts met meerdere regio's](optimize-cost-regions.md)
