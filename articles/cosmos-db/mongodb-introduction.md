---
title: Inleiding tot de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over hoe u Azure Cosmos DB kunt gebruiken voor het opslaan van enorme hoeveelheden gegevens met de API van Azure Cosmos DB voor MongoDB en er query's op kunt uitvoeren.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 11/25/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ab335c8cc682f6b33dcb30509d682186a178c676
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861069"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB](introduction.md) is de wereldwijd gedistribueerde databaseservice met meerdere modellen van Microsoft voor essentiële toepassingen. Azure Cosmos DB biedt [gebruiksklare wereldwijde distributie](distribute-data-globally.md), [elastisch schalen van doorvoer en opslag](partitioning-overview.md), latentie van slechts enkele milliseconden op het 99e percentiel, en een gegarandeerd hoge beschikbaarheid, ondersteund met [toonaangevende SLA’s](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Met Azure Cosmos DB worden [gegevens automatisch geïndexeerd](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), zonder dat u te maken krijgt met schema- en indexbeheer. Azure Cosmos DB beschikt over meerdere modellen en ondersteunt modellen voor document-, sleutelwaarde-, grafiek- en kolomgegevens. Met de Azure Cosmos DB-service worden wire-protocollen geïmplementeerd voor veelgebruikte NoSQL-API's, zoals Cassandra, MongoDB, Gremlin en Azure Table Storage. Hiermee kunt u uw vertrouwde NoSQL-clientstuurprogramma's en hulpprogramma's gebruiken om te werken met uw Cosmos-database.

> [!NOTE]
> De [modus met serverloze capaciteit](serverless.md) is nu beschikbaar in de API van Azure Cosmos DB voor MongoDB.

## <a name="wire-protocol-compatibility"></a>Compatibiliteit met wire-protocollen

Azure Cosmos DB implementeert het wire-protocol voor MongoDB. Deze implementatie maakt transparante compatibiliteit mogelijk met de systeemeigen SDK's, stuurprogramma's en hulpprogramma's van de MongoDB-client. Azure Cosmos DB fungeert niet als host voor de MongoDB-database-engine. De details van de functies die worden ondersteund door MongoDB vindt u hier: 
- [Azure Cosmos DB's API for Mongo DB-engine versie 3.6](mongodb-feature-support-36.md)
- [Azure Cosmos DB's API for Mongo DB-engine versie 3.2](mongodb-feature-support.md)

Nieuwe accounts die met de API van Azure Cosmos DB voor MongoDB zijn gemaakt, zijn standaard compatibel met versie 3.6 van het wire-protocol van MongoDB. Elk clientstuurprogramma van MongoDB dat geschikt is voor deze protocolversie, kan een systeemeigen verbinding maken met Cosmos DB.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Azure Cosmos DB-API voor MongoDB" border="false":::

## <a name="key-benefits"></a>Belangrijkste voordelen

De belangrijkste voordelen van Cosmos DB als volledig beheerde, wereldwijd gedistribueerde Database as a Service worden [hier](introduction.md) beschreven. Door een systeemeigen implementatie van wire-protocollen van populaire NoSQL-API's uit te voeren, biedt Cosmos DB bovendien de volgende voordelen:

* U kunt uw toepassing eenvoudig migreren naar Cosmos DB met behoud van belangrijke onderdelen van uw toepassingslogica.
* Behoud de overdraagbaarheid van uw toepassing zonder dat u de vrijheid verliest om van cloudleverancier te veranderen.
* Voor de meestgebruikte NoSQL-API's krijgt u toonaangevende SLA's met een solide financiële basis, mogelijk gemaakt door Cosmos DB.
* U kunt de ingerichte doorvoer en opslag voor uw Cosmos-databases elastisch schalen op basis van uw behoeften, en u betaalt alleen voor de doorvoer en opslag die u nodig hebt. Dit leidt tot aanzienlijke kostenbesparingen.
* Kant-en-klare wereldwijde distributie met replicatie voor schrijfbewerkingen in meerdere regio's.

## <a name="cosmos-dbs-api-for-mongodb"></a>API van Cosmos DB voor MongoDB

Volg de stappen in de sneltstartstudies om een Azure Cosmos-account te maken en uw bestaande MongoDB-toepassing te migreren om Azure Cosmos DB te gebruiken, of bouw een nieuwe:

* [Een bestaande MongoDB Node.js-web-app migreren](create-mongodb-nodejs.md).
* [Met de API van Azure Cosmos DB voor MongoDB en de .NET-SDK een web-app maken](create-mongodb-dotnet.md).
* [Met de API van Azure Cosmos DB voor MongoDB en de Java-SDK een console-app maken](create-mongodb-java.md).

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanwijzingen om aan de slag te gaan:

* Volg de zelfstudie [Een MongoDB-toepassing verbinden met Azure Cosmos DB](connect-mongodb-account.md) voor informatie over het ophalen van verbindingsreeksinformatie voor uw account.
* Volg de zelfstudie [Studio 3T gebruiken met Azure Cosmos DB](mongodb-mongochef.md) om te leren hoe u een verbinding maakt tussen een Cosmos-database en de MongoDB-app in Studio 3T.
* Volg de zelfstudie [MongoDB-gegevens importeren in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) om uw gegevens te importeren in een Cosmos-database.
* Verbinding maken met een Cosmos-account met behulp van [Robo 3T](mongodb-robomongo.md).
* Meer informatie over het [configureren van leesvoorkeuren voor wereldwijd gedistribueerde apps](../cosmos-db/tutorial-global-distribution-mongodb.md).
* Vind de oplossingen voor veelvoorkomende fouten in onze [Gids voor probleemoplossing](mongodb-troubleshoot.md)


<sup>Opmerking: In dit artikel wordt een functie van Azure Cosmos DB beschreven waarmee compatibiliteit met wire-protocollen met MongoDB-databases kan worden geboden. MongoDB-databases worden niet door Microsoft uitgevoerd om deze service te kunnen leveren. Azure Cosmos DB is niet verbonden aan MongoDB, Inc.</sup>