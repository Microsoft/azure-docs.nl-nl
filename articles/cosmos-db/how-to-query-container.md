---
title: Query's uitvoeren op containers in Azure Cosmos DB
description: Meer informatie over het opvragen van containers in Azure Cosmos DB met behulp van in-Partition en kruis partitie query's
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 5cd90e994e620960e0d974ef7609a67f8a5eb58b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448539"
---
# <a name="query-an-azure-cosmos-container"></a>Query’s uitvoeren op een Azure Cosmos-container
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt uitgelegd hoe u query's kunt uitvoeren op containers (collecties, grafieken, tabellen) in Azure Cosmos DB. In het bijzonder wordt uitgelegd hoe in-Partition-en kruis partitie query's werken in Azure Cosmos DB.

## <a name="in-partition-query"></a>In-partitiequery

Wanneer u gegevens opvraagt uit containers en de query een partitie sleutel filter heeft opgegeven, wordt de query automatisch geoptimaliseerd door Azure Cosmos DB. De query wordt doorgestuurd naar de [fysieke partities](partitioning-overview.md#physical-partitions) die overeenkomen met de partitie sleutel waarden die zijn opgegeven in het filter.

Denk bijvoorbeeld aan de onderstaande query met een gelijkheids filter op `DeviceId` . Als deze query wordt uitgevoerd op een container die is gepartitioneerd op `DeviceId` , wordt met deze query gefilterd op één fysieke partitie.

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Net als bij het vorige voor beeld wordt deze query ook gefilterd op één partitie. Het toevoegen van het extra filter op `Location` heeft deze wijziging niet:

```sql
SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Hier volgt een query met een bereik filter op de partitie sleutel en wordt het bereik niet toegepast op één fysieke partitie. Als u een query in de partitie wilt maken, moet de query een gelijkheids filter hebben dat de partitie sleutel bevat:

```sql
SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Partitieoverkoepelende query

De volgende query heeft geen filter op de partitie sleutel ( `DeviceId` ). Daarom moet het uitwaaieren naar alle fysieke partities waar het wordt uitgevoerd op de index van elke partitie:

```sql
SELECT * FROM c WHERE c.Location = 'Seattle`
```

Elke fysieke partitie heeft een eigen index. Wanneer u een query op meerdere partities uitvoert op een container, voert u daarom een query uit *per* fysieke partitie. Azure Cosmos DB worden automatisch resultaten verzameld over verschillende fysieke partities.

De indexen in verschillende fysieke partities zijn onafhankelijk van elkaar. Er is geen globale index in Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Parallelle partitieoverkoepelende query

De Cosmos DB-SDK's 1.9.0 en hoger bieden ondersteuning voor opties voor parallelle query-uitvoering. Met parallelle partitieoverkoepelende query's kunt u query's met lage latentie uitvoeren op meerdere partities.

U kunt parallelle queryuitvoering beheren door de volgende parameters af te stemmen:

- **MaxConcurrency**: Hiermee stelt u het maximum aantal gelijktijdige netwerk verbindingen met de partitie van de container in. Als u deze eigenschap instelt op `-1` , wordt de mate van parallelle uitvoering beheerd door de SDK. Als de  `MaxConcurrency` instelling is ingesteld op `0` , is er één netwerk verbinding met de partities van de container.

- **MaxBufferedItemCount**: hiermee wordt de latentie van de query ingewisseld voor het geheugengebruik aan de clientzijde. Als deze optie wordt weggelaten of ingesteld op -1, wordt het aantal items dat in de buffer opgeslagen tijdens parallelle query-uitvoering, beheerd door de SDK.

Dankzij de mogelijkheden van de Azure Cosmos DB om Kruis partitie query's te parallelliseren, wordt query latentie doorgaans goed geschaald als het systeem [fysieke partities](partitioning-overview.md#physical-partitions)toevoegt. De RU-kosten worden echter aanzienlijk verhoogd naarmate het totale aantal fysieke partities toeneemt.

Wanneer u een query voor meerdere partities uitvoert, maakt u in feite een afzonderlijke query per afzonderlijke fysieke partitie. Hoewel query's met query's in meerdere partities gebruikmaken van de index, indien beschikbaar, zijn ze nog steeds niet bijna zo efficiënt als in-partitie query's.

## <a name="useful-example"></a>Handig voor beeld

Hier volgt een uitleg van het beter inzicht in Kruis partitie query's:

Stel dat u een leverings stuur programma bent dat pakketten moet leveren aan verschillende Apartment-complexen. Elke Apartment bevat een lijst op de lokale locatie met alle eenheids nummers van de residente eenheden. We kunnen elk apartment-complex vergelijken met een fysieke partitie en elke lijst met de index van de fysieke partitie.

We kunnen in-Partition-en kruis partitie query's vergelijken met dit voor beeld:

### <a name="in-partition-query"></a>In-partitiequery

Als het leverings stuur programma de juiste Apartment-complex (fysieke partitie) kent, kunnen ze onmiddellijk naar het juiste gebouw worden gestuurd. Het stuur programma kan de lijst met de id's van de residente eenheden (de index) controleren en snel de juiste pakketten leveren. In dit geval wordt het stuur programma geen tijd verspild aan het bemoeilijken van een Apartment om te controleren of de ontvangers van het pakket daar wonen.

### <a name="cross-partition-query-fan-out"></a>Kruis partitie query (uitwaaieren)

Als het stuur programma voor de levering niet de juiste Apartment-waarde (fysieke partitie) kent, moet deze op elk apparaat met één appartement worden gestationd en de lijst met alle eenheids nummers van de Resident (de index) controleren. Zodra ze op elk appartementmodel zijn aangekomen, kunnen ze nog steeds de lijst met adressen van elke Resident gebruiken. Ze moeten echter alle items van de lijst van de appartementmodel controleren, ongeacht of de ontvangers van het pakket al dan niet live zijn. Zo werkt Kruis partitie query's. Hoewel ze de index kunnen gebruiken (niet op elke afzonderlijke deur hoeven te worden bedekken), moeten ze de index afzonderlijk controleren voor elke fysieke partitie.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Kruis partitie query (beperkt tot slechts een paar fysieke partities)

Als het stuur programma van de levering weet dat alle ontvangers van het pakket binnen een paar van de kamer complex zijn, hoeven ze niet op elke enkele laag te worden gestationeerd. Tijdens het overstappen op een paar opslag complexen is er nog steeds meer werk nodig dan het bezoek van een enkel gebouw. het leverings stuur programma bespaart echter aanzienlijke tijd en moeite. Als een query de partitie sleutel in het bijbehorende filter met het `IN` tref woord bevat, worden alleen de relevante indexen van de fysieke partitie voor gegevens gecontroleerd.

## <a name="avoiding-cross-partition-queries"></a>Query's voor meerdere partities voor komen

Voor de meeste containers is het onvermijdelijk dat er een aantal Kruis partitie query's worden uitgevoerd. Een aantal Kruis partitie query's is OK. Bijna alle query bewerkingen worden ondersteund voor verschillende partities (zowel logische-partitie sleutels als fysieke partities). Azure Cosmos DB heeft ook een groot aantal optimalisaties in de query-engine en de client-Sdk's om query's uit te voeren op de parallelliseren van de query.

Voor de meeste Lees-intensieve scenario's raden we u aan de meest voorkomende eigenschap in uw query filters te selecteren. U moet er ook voor zorgen dat de partitie sleutel voldoet aan de aanbevolen procedures voor het selecteren van de [partitie sleutel](partitioning-overview.md#choose-partitionkey).

Het voor komen van query's tussen partities is doorgaans alleen van belang voor grote containers. Elke keer dat u de index van een fysieke partitie voor resultaten controleert, wordt een minimum van ongeveer 2,5 RU in rekening gebracht, zelfs als er geen items in de fysieke partitie overeenkomen met het filter van de query. Als u slechts één (of slechts enkele) fysieke partities hebt, verbruiken query's voor meerdere partities niet aanzienlijk meer dan in-partitie query's.

Het aantal fysieke partities is gekoppeld aan de hoeveelheid ingerichte RU. Elke fysieke partitie staat Maxi maal 10.000 ingerichte RU toe en kan tot 50 GB aan gegevens opslaan. De fysieke partities voor u worden door Azure Cosmos DB automatisch beheerd. Het aantal fysieke partities in de container is afhankelijk van uw ingerichte door Voer en verbruikte opslag.

Vermijd Kruis partitie query's als uw workload voldoet aan de onderstaande criteria:
- U wilt meer dan 30.000 RU inrichten
- U bent van plan om meer dan 100 GB aan gegevens op te slaan

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over partitionering in Azure Cosmos DB:

- [Partitionering in Azure Cosmos DB](partitioning-overview.md)
- [Synthetic partition keys in Azure Cosmos DB](synthetic-partition-keys.md) (Synthetische partitiesleutels in Azure Cosmos DB)
