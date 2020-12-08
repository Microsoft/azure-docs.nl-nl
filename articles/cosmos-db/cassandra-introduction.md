---
title: Inleiding tot de Azure Cosmos DB Cassandra-API
description: Kom erachter hoe u Azure Cosmos DB kunt gebruiken voor een 'lift-and-shift' van bestaande toepassingen en om nieuwe toepassingen te maken met de Cassandra-stuurprogramma's en CQL
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 11/25/2020
ms.openlocfilehash: 2a778c2e9a44bda148fd40112776858943cffe4e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188612"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Inleiding tot de Azure Cosmos DB Cassandra-API
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB Cassandra-API kan worden gebruikt als gegevensopslag voor apps die zijn geschreven voor [Apache Cassandra](https://cassandra.apache.org). Dit betekent dat uw bestaande Cassandra-toepassing nu kan communiceren met de Azure Cosmos DB Cassandra-API, door gebruik te maken van bestaande [Apache-apparaatstuurprogramma’s](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) die compatibel zijn met CQLv4. In veel gevallen kunt u overschakelen van Apache Cassandra naar de Cassandra-API van Azure Cosmos DB door alleen een verbindingsreeks te wijzigen. 

De Cassandra-API stelt u in staat om gegevens te gebruiken die zijn opgeslagen in Azure Cosmos DB. Dit doet u via de Cassandra-querytaal (CQL), hulpprogramma's op basis van de Cassandra-querytaal (bijvoorbeeld cqlsh) en Cassandra-clientstuurprogramma's waarmee u al vertrouwd bent.

> [!NOTE]
> De [modus met serverloze capaciteit](serverless.md) is nu beschikbaar in de Cassandra-API van Azure Cosmos DB.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Wat is het voordeel van het gebruik van de Apache Cassandra-API voor Azure Cosmos DB?

**Geen operationeel management**: Azure Cosmos DB Cassandra-API is een volledig beheerde cloudservice, waardoor beheerders zich niet bezig hoeven te houden met het beheren en controleren van talloze instellingen in OS-, JVM- en yaml-bestanden en de bijbehorende wisselwerkingen. Azure Cosmos DB biedt controle van doorvoer, latentie, opslag, beschikbaarheid en waarschuwingen die kunnen worden geconfigureerd.

**Opensourcestandaard**: De Cassandra-API is een volledig beheerde service, maar ondersteunt een uitgebreide surface area van het systeemeigen [wire-protocol van Apache Cassandra](cassandra-support.md). Zo kunt u toepassingen ontwikkelen via een veelgebruikte en niet-cloudgebonden opensourcestandaard.

**Prestatiebeheer**: Azure Cosmos DB biedt gegarandeerde lage latentie van lees- en schrijfbewerkingen voor een percentiel van 99%, ondersteund met een SLA. Gebruikers hoeven zich geen zorgen te maken over operationele overhead voor hoge prestaties en lage latentie van lees- en schrijfbewerkingen. Dit betekent dat gebruikers zich niet bezig hoeven te houden met geplande compressie, het beheren van testblokken, het handmatig instellen van bloomfilters en replica’s. Azure Cosmos DB verwijdert de overhead om deze zaken te beheren, waardoor u zich beter kunt focussen op de logica van de toepassing.

**Mogelijkheid om bestaande code en hulpprogramma’s te gebruiken**: Azure Cosmos DB biedt compatibiliteit van wire-protocolniveau’s met bestaande Cassandra-SDK’s en hulpprogramma’s. Door deze compatibiliteit kunt u bestaande codebase gebruiken met de Azure Cosmos DB Cassandra-API, met slechts een klein aantal wijzigingen.

**Elasticiteit van doorvoer en opslag**: Azure Cosmos DB biedt doorvoer naar alle regio's en kan de ingerichte doorvoer schalen met Azure Portal, PowerShell of CLI-bewerkingen. U kunt naar behoefte [flexibel](manage-scale-cassandra.md) de schaal van de opslag en doorvoer voor de tabellen aanpassen met voorspelbare prestaties.

**Wereldwijde distributie en beschikbaarheid**: Azure Cosmos DB heeft de mogelijkheid om gegevens wereldwijd te distribueren over alle Azure-regio’s en de gegevens lokaal aanbieden terwijl lage latentie gegevenstoegang en hoge beschikbaarheid gegarandeerd blijft. Azure Cosmos DB biedt 99,99% hoge beschikbaarheid binnen een regio, en 99,999% beschikbaarheid voor lezen en schrijven in meerdere regio’s, zonder operationele overhead. Lees het artikel [Gegevens wereldwijd distribueren](distribute-data-globally.md) voor meer informatie. 

**Keuze in consistentie**: Azure Cosmos DB biedt de keuze uit vijf goed gedefinieerde consistentieniveaus voor een optimale balans tussen consistentie en prestaties. Deze consistentieniveaus zijn: sterk, gebonden-verouderd, sessie, consistent voorvoegsel en mogelijk. Op basis van deze goed gedefinieerde, praktische en intuïtieve consistentieniveaus kunnen ontwikkelaars een nauwkeurige balans vinden tussen de consistentie, beschikbaarheid en latentie. Lees het artikel [consistentieniveaus](consistency-levels.md) voor meer informatie. 

**Zakelijk niveau**: Azure Cosmos DB biedt [nalevingscertificeringen](https://www.microsoft.com/trustcenter) om ervoor te zorgen dat het platform veilig kan worden gebruikt. Azure Cosmos DB biedt ook versleuteling van niet-actieve en actieve gegevens, een IP-firewall en auditlogboeken voor beheeractiviteiten.

**Gebeurtenisbronnen**: De Cassandra-API biedt toegang tot een permanent wijzigingenlogboek, de [wijzigingenfeed](cassandra-change-feed.md), waarmee u gebeurtenisbronnen rechtstreeks uit de database kunt ophalen. Het enige equivalent in Apache Cassandra is change data capture (CDC). Dit is een mechanisme om specifieke tabellen te markeren voor archivering. Hiermee kunnen ook schrijfbewerkingen naar die tabellen worden geweigerd wanneer de ingestelde grootte op de schijf voor het CDC-logboek wordt bereikt. Deze mogelijkheden zijn overbodig in Cosmos DB omdat de relevante aspecten automatisch worden beheerd.

## <a name="next-steps"></a>Volgende stappen

* U kunt snel aan de slag met het bouwen van de volgende taalspecifieke toepassingen om Cassandra-API-gegevens te maken en beheren:
  - [Node.js-app](create-cassandra-nodejs.md)
  - [.NET app](create-cassandra-dotnet.md)
  - [Python-app](create-cassandra-python.md)

* Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java-toepassing.

* [Laad voorbeeldgegevens in de Cassandra-API-tabel](cassandra-api-load-data.md) met behulp van een Java-toepassing.

* [Zoek in gegevens in de Cassandra-API-account](cassandra-api-query-data.md) met behulp van een Java-toepassing.

* Lees het artikel [Cassandra-ondersteuning](cassandra-support.md) voor meer informatie over Apache Cassandra-functies ondersteund door Azure Cosmos DB Cassandra API.

* Lees de [veelgestelde vragen (FAQ)](cassandra-faq.md).
