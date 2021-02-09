---
title: Gegevens partitioneren in Azure Cosmos DB Gremlin-API
description: Meer informatie over hoe u een gepartitioneerde grafiek in Azure Cosmos DB kunt gebruiken. In dit artikel worden ook de vereisten en aanbevolen procedures voor een gepartitioneerde grafiek beschreven.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4d76bdcb385ed2fe4b8a697f24187b8e3d5addbc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988752"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Gepartitioneerde graaf gebruiken in Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Een van de belangrijkste functies van de Gremlin-API in Azure Cosmos DB is de mogelijkheid om grootschalige grafieken te verwerken door middel van Horizon taal schalen. De containers kunnen onafhankelijk van de voor waarden van opslag en door voer worden geschaald. U kunt containers maken in Azure Cosmos DB die automatisch kunnen worden geschaald om grafiek gegevens op te slaan. De gegevens worden automatisch gebalanceerd op basis van de opgegeven **partitie sleutel**.

Partitioneren wordt intern uitgevoerd als de container verwacht meer dan 20 GB op te slaan of als u meer dan 10.000 aanvraag eenheden per seconde (RUs) wilt toewijzen. Gegevens worden automatisch gepartitioneerd op basis van de partitie sleutel die u opgeeft. De partitie sleutel is vereist als u grafiek containers maakt op basis van de Azure Portal of de 3. x of hogere versies van Gremlin-Stuur Programma's. De partitie sleutel is niet vereist als u 2. x of een lagere versie van Gremlin-Stuur Programma's gebruikt.

Dezelfde algemene principes van het [Azure Cosmos DB partitioneren-mechanisme](partitioning-overview.md) zijn van toepassing met een aantal grafische specifieke optimalisaties die hieronder worden beschreven.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Graph-partitionering." border="false":::

## <a name="graph-partitioning-mechanism"></a>Mechanisme voor het partitioneren van grafieken

De volgende richt lijnen beschrijven hoe de strategie voor partitioneren in Azure Cosmos DB werkt:

- **Beide hoek punten en randen worden opgeslagen als JSON-documenten**.

- Voor **hoek punten is een partitie sleutel vereist**. Met deze sleutel wordt bepaald in welke partitie het hoek punt wordt opgeslagen met een hash-algoritme. De naam van de partitie sleutel eigenschap wordt gedefinieerd bij het maken van een nieuwe container en heeft de volgende indeling: `/partitioning-key-name` .

- **Randen worden opgeslagen met hun bron hoekpunt**. Met andere woorden, voor elk hoek punt is de partitie sleutel gedefinieerd waar ze samen met de uitgaande randen worden opgeslagen. Deze optimalisatie wordt uitgevoerd om cross-Partition query's te voor komen bij het gebruik `out()` van de kardinaliteit in Graph-query's.

- **Randen bevatten verwijzingen naar de hoek punten waarnaar ze verwijzen**. Alle randen worden opgeslagen met de partitie sleutels en de Id's van de hoek punten waarnaar ze verwijzen. Met deze berekening worden alle query's in de `out()` richting altijd een gepartitioneerde, niet-verwerkte query en niet een blinde Kruis partitie query.

- **Graph-query's moeten een partitie sleutel opgeven**. Als u optimaal wilt profiteren van de horizontale partitionering in Azure Cosmos DB, moet de partitie sleutel worden opgegeven wanneer één hoek punt wordt geselecteerd, wanneer dit mogelijk is. Hieronder vindt u query's waarmee u een of meer hoek punten in een gepartitioneerde grafiek selecteert:

    - `/id` en `/label` worden niet ondersteund als partitie sleutels voor een container in de Gremlin-API.


    - Selecteer een hoek punt op ID en **gebruik vervolgens de `.has()` stap om de eigenschap van de partitie sleutel op te geven**:

        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - Een hoek punt selecteren door **een tuple op te geven, inclusief de partitie sleutel waarde en de id**:

        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - Het opgeven **van een matrix met Tuples van partitie sleutel waarden en-id's**:

        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - Een set hoek punten selecteren met hun Id's en **een lijst met partitie sleutel waarden opgeven**:

        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Het gebruik van de **partitie strategie** aan het begin van een query en het opgeven van een partitie voor het bereik van de rest van de Gremlin-query:

        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Aanbevolen procedures voor het gebruik van een gepartitioneerde grafiek

Gebruik de volgende richt lijnen om te zorgen voor prestaties en schaal baarheid bij het gebruik van gepartitioneerde grafieken met onbeperkte containers:

- **Geef altijd de partitie sleutel waarde op bij het uitvoeren van een query op een hoek punt**. Het verkrijgen van hoekpunt van een bekende partitie is een manier om prestaties te behalen. Alle volgende bewerkings belendings zijn altijd binnen het bereik van een partitie, aangezien randen de verwijzings-ID en partitie sleutel naar hun doel hoekpunten bevatten.

- **Gebruik de uitgaande richting bij het uitvoeren van een query op randen wanneer dat mogelijk is**. Zoals hierboven vermeld, worden randen opgeslagen met hun bron hoekpunten in de uitgaande richting. De kans om query's op meerdere partities te maken, wordt dus geminimaliseerd wanneer de gegevens en query's zijn ontworpen met dit patroon. De query is in tegens telling tot `in()` altijd een dure ventilator-out-query.

- **Kies een partitie sleutel waarmee gegevens gelijkmatig over de partities kunnen worden verdeeld**. Deze beslissing is sterk afhankelijk van het gegevens model van de oplossing. Lees meer over het maken van een juiste partitie sleutel in [partitioneren en schalen in azure Cosmos DB](partitioning-overview.md).

- **Optimaliseer query's voor het verkrijgen van gegevens binnen de grenzen van een partitie**. Een optimale partitie strategie wordt uitgelijnd op de query patronen. Query's die gegevens ophalen uit één partitie, bieden de best mogelijke prestaties.

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met het lezen van de volgende artikelen:

* Meer informatie over [partitioneren en schalen in azure Cosmos DB](partitioning-overview.md).
* Meer informatie over de [Gremlin-ondersteuning in de Gremlin-API](gremlin-support.md).
* Meer informatie over [Introduction to GREMLIN API](graph-introduction.md).
