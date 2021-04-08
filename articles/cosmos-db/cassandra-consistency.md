---
title: Consistentie niveaus Apache Cassandra en Azure Cosmos DB
description: Consistentie niveaus van Apache Cassandra en Azure Cosmos DB.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516817"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Consistentie niveaus voor Apache Cassandra en Azure Cosmos DB Cassandra-API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In tegens telling tot Azure Cosmos DB biedt Apache Cassandra geen systeem eigen nauw keurige consistentie garanties. In plaats daarvan biedt Apache Cassandra een schrijf consistentie niveau en een consistentie niveau voor lezen om de hoge Beschik baarheid, consistentie en latentie-afwegingen mogelijk te maken. Bij het gebruik van de Cassandra-API van Azure Cosmos DB:

* Het schrijf consistentie niveau van Apache Cassandra wordt toegewezen aan het standaard consistentie niveau dat is geconfigureerd voor uw Azure Cosmos-account. Consistentie voor een schrijf bewerking (LC) kan niet per aanvraag worden gewijzigd.

* Azure Cosmos DB wijst het Lees consistentie niveau dat is opgegeven door het Cassandra-client stuur programma dynamisch toe aan een van de Azure Cosmos DB consistentie niveaus die dynamisch zijn geconfigureerd op een lees aanvraag.

## <a name="multi-region-writes-vs-single-region-writes"></a>Schrijf bewerkingen met meerdere regio's versus schrijf bewerkingen in één regio

De Apache Cassandra-data base is standaard een systeem met meerdere masters en biedt geen out-of-Box optie voor schrijf bewerkingen met meerdere regio's voor lees bewerkingen. Azure Cosmos DB biedt echter een kant-en-klare mogelijkheid om één regio of [meerdere regio's](how-to-multi-master.md) te schrijven. Een van de voor delen van het kiezen van een configuratie met één regio voor meerdere regio's is het voor komen van conflict scenario's met meerdere regio's en de optie voor het onderhouden van sterke consistentie tussen meerdere regio's. 

Met schrijf bewerkingen in één regio kunt u sterke consistentie behouden, terwijl u toch een niveau van hoge Beschik baarheid in verschillende regio's met [automatische failover](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)onderhoudt. In deze configuratie kunt u nog steeds data localiteit exploiteren om de lees latentie te verminderen door te downgradeen op basis van de uiteindelijke consistentie. Naast deze mogelijkheden biedt het Azure Cosmos DB-platform ook de mogelijkheid [zone redundantie](high-availability.md#availability-zone-support) in te scha kelen wanneer u een regio selecteert. Zo kunt u, in tegens telling tot native Apache Cassandra Azure Cosmos DB, het theorema [Trade-out-spectrum](consistency-levels.md#rto) door lopen met meer granulariteit.

## <a name="mapping-consistency-levels"></a>Consistentieniveaus toewijzen

Het Azure Cosmos DB-platform biedt een set van vijf goed gedefinieerde consistentie-instellingen voor zakelijk gebruik met betrekking tot de replicatie en de afwegingen die zijn gedefinieerd door de [Cap theorema](https://en.wikipedia.org/wiki/CAP_theorem) -en [PACLC-theorema](https://en.wikipedia.org/wiki/PACELC_theorem). Omdat deze benadering aanzienlijk verschilt van Apache Cassandra, raden wij aan dat u tijd hebt om Azure Cosmos DB consistentie-instellingen in onze [documentatie](consistency-levels.md)te controleren en te begrijpen, of Bekijk deze korte [video](https://www.youtube.com/watch?v=t1--kZjrG-o) handleiding om inzicht te krijgen in consistentie-instellingen in het Azure Cosmos DB platform.

In de volgende tabel ziet u de mogelijke toewijzingen tussen Apache Cassandra-en Azure Cosmos DB-consistentie niveaus bij gebruik van Cassandra-API. Dit toont configuraties voor één regio, meerdere regio's met schrijf bewerkingen in één regio en schrijf bewerkingen met meerdere regio's.

> [!NOTE]
> Dit zijn niet exacte toewijzingen. We hebben in plaats daarvan de meest overeenkomende analoges voorzien van Apache Cassandra en disambiguated alle kwalitatieve verschillen in de meest rechtse kolom. Zoals hierboven vermeld, wordt u aangeraden de [consistentie-instellingen](consistency-levels.md)van Azure Cosmos DB te bekijken. 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Toewijzing van Cassandra-consistentie op account niveau" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Dynamische toewijzing van Cassandra-consistentie" lightbox="./media/cassandra-consistency/dynamic.png" :::

Als uw Azure Cosmos-account is geconfigureerd met een ander consistentie niveau dan de sterke consistentie, kunt u de kans ontdekken dat uw clients sterke en consistente Lees bewerkingen voor uw werk belastingen krijgen door te kijken naar de metrische gegevens van de *Probabilistically-gebonden veroudering* (PBS). Deze metriek wordt weer gegeven in de Azure Portal voor meer informatie, Zie [Probabilistically gebonden verouderde waarde (PBS) controleren](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic gebonden veroudering laten zien hoe uiteindelijk uw uiteindelijke consistentie is. Deze metrische gegevens bieden een inzicht in hoe vaak u een sterkere consistentie kunt krijgen dan het consistentie niveau dat u momenteel hebt geconfigureerd in uw Azure Cosmos-account. Met andere woorden, u kunt de waarschijnlijkheid (gemeten in milliseconden) voor het verkrijgen van sterk consistente Lees bewerkingen voor een combi natie van de regio's schrijven en lezen zien.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over globale distributie-en consistentie niveaus voor Azure Cosmos DB:

* [Overzicht van wereldwijde distributie](distribute-data-globally.md)
* [Overzicht van consistentie niveau](consistency-levels.md)
* [Hoge beschikbaarheid](high-availability.md)
