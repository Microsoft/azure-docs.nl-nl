---
title: Werken met de ondersteuning voor wijzigings invoer in Azure Cosmos DB
description: Gebruik Azure Cosmos DB Change feed-ondersteuning voor het bijhouden van wijzigingen in documenten, verwerking op basis van gebeurtenissen, zoals triggers, en houd caches en analyse systemen up-to-date
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18, "seo-nov-2020"
ms.openlocfilehash: c6856a0cb70123f1a3570b611c81660a592fdc1b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98027760"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Wijzigingenfeed in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]

Change feed in Azure Cosmos DB is een permanente record met wijzigingen in een container in de volg orde waarin deze worden uitgevoerd. Ondersteuning voor de wijzigingenfeed in Azure Cosmos DB gebeurt door te luisteren naar een Azure Cosmos DB-container voor wijzigingen. Als output verschijnt er vervolgens een gesorteerde lijst met gewijzigde documenten op volgorde van wijziging. De persistente wijzigingen kunnen asynchroon en incrementeel worden verwerkt en de uitvoer kan worden verdeeld over een of meer consumenten voor parallelle verwerking.

Meer informatie over [ontwerp patronen voor veranderingen in feeds](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>Ondersteunde Api's en client-Sdk's

Deze functie wordt momenteel ondersteund door de volgende Azure Cosmos DB-Api's en client-Sdk's.

| **Client Stuur Programma's** | **SQL-API** | **API van Azure Cosmos DB voor Cassandra** | **Azure Cosmos DB-API voor MongoDB** | **Gremlin-API**|**Table-API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ja | Ja | Ja | Ja | Nee |
|Java|Ja|Ja|Ja|Ja|Nee|
|Python|Ja|Ja|Ja|Ja|Nee|
|Node/JS|Ja|Ja|Ja|Ja|Nee|

## <a name="change-feed-and-different-operations"></a>Feed en verschillende bewerkingen wijzigen

U ziet nu alle invoegingen en updates in de feed voor wijzigingen. U kunt de wijzigings feed niet filteren op een specifiek type bewerking. Een mogelijke alternatief is het toevoegen van een ' zachte markering ' voor het item voor updates en filteren op basis van die bij het verwerken van items in de wijzigings feed.

Wijzigingen in de feed worden niet in het logboek geregistreerd. Net als in het vorige voor beeld kunt u een zachte markering toevoegen voor de items die worden verwijderd. U kunt bijvoorbeeld een kenmerk toevoegen aan het item met de naam ' verwijderd ' en instellen op ' True ' en een TTL instellen voor het item, zodat het automatisch kan worden verwijderd. U kunt de wijzigings feed voor historische items lezen (de meest recente wijziging die overeenkomt met het item, het bevat geen tussenliggende wijzigingen), bijvoorbeeld items die vijf jaar geleden zijn toegevoegd. U kunt de wijzigings feed tot nu toe lezen als de oorsprong van de container, maar als een item wordt verwijderd, wordt het verwijderd uit de wijzigings feed.

### <a name="sort-order-of-items-in-change-feed"></a>Sorteer volgorde van items in een wijzigings feed

De wijzigings items worden in de volg orde van de wijzigings tijd van kracht. Deze sorteer volgorde wordt gegarandeerd per logische partitie sleutel.

### <a name="consistency-level"></a>Consistentieniveau

Bij het gebruik van de wijzigings feed in een mogelijk consistentie niveau, kunnen er dubbele gebeurtenissen optreden in de volgende Lees bewerkingen voor de invoer van wijzigingen (de laatste gebeurtenis van één Lees bewerking wordt weer gegeven als de eerste van de volgende).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed wijzigen in azure Cosmos-accounts met meerdere regio's

Als er in een Azure Cosmos-account met meerdere regio's een failover wordt uitgevoerd, werkt de wijziging in de bewerking voor hand matige failover en wordt deze aaneengesloten.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed en Time to Live wijzigen (TTL)

Als een TTL-eigenschap (Time to Live) is ingesteld voor een item op-1, blijft de wijzigings feed altijd behouden. Als de gegevens niet worden verwijderd, blijft deze in de wijzigings feed.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Feed en _etag, _lsn of _ts wijzigen

De indeling van de _etag is intern en u moet er geen afhankelijkheid van maken, omdat deze op elk gewenst moment kan worden gewijzigd. _ts is een wijziging of een tijds tempel voor het maken. U kunt _ts gebruiken voor chronologische vergelijking. _lsn is een batch-ID die alleen is toegevoegd voor wijzigings invoer. het vertegenwoordigt de trans actie-ID. Veel items hebben mogelijk hetzelfde _lsn. ETag op FeedResponse wijkt af van de _etag die u op het item ziet. _etag is een interne id die wordt gebruikt voor gelijktijdigheids beheer. De eigenschap _etag vertelt de versie van het item, terwijl de ETag-eigenschap wordt gebruikt voor het sequentiëren van de feed.

## <a name="working-with-change-feed"></a>Werken met wijzigings feed

U kunt met behulp van de volgende opties met een wijzigings feed werken:

* [Change feed gebruiken met Azure Functions](change-feed-functions.md)
* [Change feed gebruiken met Change feed-processor](change-feed-processor.md) 

De wijzigings feed is beschikbaar voor elke logische partitie sleutel in de container en kan worden gedistribueerd voor een of meer consumenten voor parallelle verwerking, zoals wordt weer gegeven in de onderstaande afbeelding.

:::image type="content" source="./media/change-feed/changefeedvisual.png" alt-text="Gedistribueerde verwerking van Azure Cosmos DB Change feed" border="false":::

## <a name="features-of-change-feed"></a>Functies van wijzigings feed

* De wijzigings feed is standaard ingeschakeld voor alle Azure Cosmos-accounts.

* U kunt uw [ingerichte door Voer](request-units.md) gebruiken voor het lezen van de wijzigings feed, net als bij andere Azure Cosmos DB bewerkingen, in een van de regio's die zijn gekoppeld aan uw Azure Cosmos-data base.

* De wijzigings feed bevat invoeg-en update-bewerkingen die zijn aangebracht aan items in de container. U kunt verwijderingen vastleggen door een vlag "zacht verwijderen" in te stellen in uw items (bijvoorbeeld documenten) in plaats van verwijderen. U kunt ook een eindige verval periode instellen voor uw items met de [TTL-mogelijkheid](time-to-live.md). Bijvoorbeeld 24 uur en gebruik de waarde van die eigenschap om verwijderingen vast te leggen. Met deze oplossing moet u de wijzigingen verwerken binnen een korter tijds interval dan de TTL-verval periode.

* Elke wijziging in een item wordt slechts één keer weer gegeven in de wijzigings feed en de clients moeten de logica voor het controle punt beheren. Als u de complexiteit van het beheer van controle punten wilt voor komen, biedt de Change feed-processor automatische controle punten en ' ten minste eenmaal ' semantiek. Zie [Change feed gebruiken met Change feed processor](change-feed-processor.md).

* Alleen de meest recente wijziging voor een bepaald item is opgenomen in het wijzigings logboek. Tussenliggende wijzigingen zijn mogelijk niet beschikbaar.

* De wijzigings feed wordt gesorteerd op volg orde van wijziging binnen elke logische partitie sleutel waarde. Er is geen gegarandeerde volg orde in de partitie sleutel waarden.

* Wijzigingen kunnen worden gesynchroniseerd vanuit elk tijdstip, dat wil zeggen dat er geen vaste gegevens Bewaar periode is waarvoor wijzigingen beschikbaar zijn.

* Wijzigingen zijn parallel beschikbaar voor alle logische partitie sleutels van een Azure Cosmos-container. Met deze mogelijkheid kunnen wijzigingen van grote containers parallel worden verwerkt door meerdere gebruikers.

* Toepassingen kunnen meerdere wijzigings feeds op dezelfde container tegelijk aanvragen. ChangeFeedOptions. StartTime kan worden gebruikt om een eerste begin punt op te geven. Bijvoorbeeld om het vervolg token te vinden dat overeenkomt met een bepaalde klok tijd. De ContinuationToken, indien opgegeven, heeft voor rang op de waarden StartTime en StartFromBeginning. De precisie van ChangeFeedOptions. StartTime is ~ 5 seconden.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed wijzigen in Api's voor Cassandra en MongoDB

De functionaliteit voor het wijzigen van de feed wordt geoppereerd als een wijzigings stroom in de MongoDB-API en query met een predikaat in Cassandra-API. Zie voor meer informatie over de implementatie Details voor MongoDB API de [wijzigings stromen in de Azure Cosmos DB-API voor MongoDb](mongodb-change-streams.md).

Systeem eigen Apache Cassandra biedt change data capture (CDC), een mechanisme voor het markeren van specifieke tabellen voor archivering en het afwijzen van schrijf bewerkingen naar die tabellen wanneer een Configureer bare grootte-op-schijf voor het CDC-logboek wordt bereikt. De functie voor het wijzigen van de feed in Azure Cosmos DB-API voor Cassandra verbetert de mogelijkheid om query's uit te voeren op de wijzigingen met een predikaat via CQL. Zie [Change feed in de Azure Cosmos DB-API voor Cassandra voor](cassandra-change-feed.md)meer informatie over de implementatie details.

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over wijzigings invoer in de volgende artikelen:

* [Opties voor het lezen van een wijzigings feed](read-change-feed.md)
* [Change feed gebruiken met Azure Functions](change-feed-functions.md)
* [Een Change feed-processor gebruiken](change-feed-processor.md)
