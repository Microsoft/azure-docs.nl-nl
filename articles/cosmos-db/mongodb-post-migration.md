---
title: Optimalisatie stappen na de migratie met de API van Azure Cosmos DB voor MongoDB
description: Dit document bevat de optimalisatie technieken na migratie van MongoDB naar de APi van Azure Cosmos DB voor Mongo DB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 02/14/2021
ms.author: chrande
ms.openlocfilehash: ce0facaba43e9cc8e37b19fbd3dfc840067d278f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560392"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Optimalisatie stappen na de migratie wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Nadat u de gegevens die zijn opgeslagen in de MongoDB-database hebt gemigreerd naar de Azure Cosmos DB API voor MongoDB, kunt u verbinding maken met Azure Cosmos DB en de gegevens beheren. Deze handleiding bevat de stappen die u moet overwegen na de migratie. Zie de [MongoDb voor de MongoDb-zelf studie Migrate to Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md) voor de migratie stappen.

In deze handleiding leert u het volgende:

- [Verbinding maken met uw toepassing](#connect-your-application)
- [Het indexerings beleid optimaliseren](#optimize-the-indexing-policy)
- [Globale distributie configureren voor de API van Azure Cosmos DB voor MongoDB](#globally-distribute-your-data)
- [Consistentie niveau instellen](#set-consistency-level)

> [!NOTE]
> De enige verplichte stap na de migratie op het niveau van de toepassing wijzigt de connection string in uw toepassing zodat deze naar uw nieuwe Azure Cosmos DB account verwijst. Alle andere migratie stappen zijn aanbevolen optimalisaties.
>

## <a name="connect-your-application"></a>Verbinding maken met uw toepassing

1. Meld u in een nieuw venster aan bij de [Azure Portal](https://www.portal.azure.com/)
2. Open vanuit de [Azure Portal](https://www.portal.azure.com/)in het linkerdeel venster het menu **alle resources** en zoek het Azure Cosmos DB account waarnaar u uw gegevens hebt gemigreerd.
3. Open de Blade **verbindings reeks** . Het rechterdeelvenster bevat alle informatie die u nodig hebt om verbinding te maken met uw account.
4. Gebruik de verbindings gegevens in de configuratie van uw toepassing (of andere relevante locaties) om de API van de Azure Cosmos DB voor MongoDB-verbinding in uw app weer te geven.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="Scherm afbeelding toont de instellingen voor een verbindings reeks.":::

Ga naar de pagina [een MongoDb-toepassing verbinden met Azure Cosmos DB](connect-mongodb-account.md) voor meer informatie.

## <a name="optimize-the-indexing-policy"></a>Het indexerings beleid optimaliseren

Alle gegevens velden worden standaard automatisch geïndexeerd tijdens de migratie van gegevens die naar Azure Cosmos DB worden gemigreerd. In veel gevallen is dit standaard indexerings beleid acceptabel. Over het algemeen optimaliseert het verwijderen van indexen het schrijven van aanvragen en het standaard indexerings beleid (bijvoorbeeld automatische indexering) optimaliseert Lees aanvragen.

Zie voor meer informatie over indexeren [gegevens indexeren in de API van Azure Cosmos DB voor MongoDb](mongodb-indexing.md) en de [indexering in azure Cosmos DB](index-overview.md) -artikelen.

## <a name="globally-distribute-your-data"></a>Uw gegevens wereld wijd distribueren

Azure Cosmos DB is wereldwijd beschikbaar in alle [Azure-regio's](https://azure.microsoft.com/regions/#services). Nadat u het standaard consistentie niveau voor uw Azure Cosmos DB-account hebt geselecteerd, kunt u een of meer Azure-regio's koppelen (afhankelijk van uw globale distributie behoeften). Voor hoge Beschik baarheid en bedrijfs continuïteit wordt u aangeraden altijd in ten minste twee regio's uit te voeren. U kunt de tips voor het [optimaliseren van de kosten voor implementaties met meerdere regio's in azure Cosmos DB](optimize-cost-regions.md)bekijken.

Als u uw gegevens wereld wijd wilt distribueren, raadpleegt u [gegevens wereld wijd distribueren op de API van Azure Cosmos DB voor MongoDb](tutorial-global-distribution-mongodb.md).

## <a name="set-consistency-level"></a>Consistentie niveau instellen

Azure Cosmos DB biedt vijf goed gedefinieerde [consistentie niveaus](consistency-levels.md). Lees [consistentie niveaus en Azure Cosmos DB api's](./consistency-levels.md)voor meer informatie over de toewijzing tussen MongoDB en Azure Cosmos DB consistentie niveaus. Het standaard consistentie niveau is het consistentie niveau van de sessie. Het wijzigen van het consistentie niveau is optioneel en u kunt het optimaliseren voor uw app. Consistentie niveau wijzigen met Azure Portal:

1. Ga naar de **standaard consistentie** -Blade onder instellingen.
2. Uw [consistentie niveau](consistency-levels.md) selecteren

De meeste gebruikers behouden hun consistentie niveau bij de standaard instelling voor sessie consistentie. Er zijn echter [Beschik baarheid en prestatie afwegingen voor verschillende consistentie niveaus](./consistency-levels.md).

## <a name="next-steps"></a>Volgende stappen

* [Een MongoDB-toepassing verbinden met Azure Cosmos DB](connect-mongodb-account.md)
* [Verbinding maken met Azure Cosmos DB account met behulp van Studio 3T gebruiken](mongodb-mongochef.md)
* [Lees bewerkingen globaal distribueren met behulp van de API van Azure Cosmos DB voor MongoDB](mongodb-readpreference.md)
* [Gegevens automatisch laten verlopen met de Azure Cosmos DB-API voor MongoDB](mongodb-time-to-live.md)
* [Consistentie niveaus in Azure Cosmos DB](consistency-levels.md)
* [Indexering in Azure Cosmos DB](index-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)