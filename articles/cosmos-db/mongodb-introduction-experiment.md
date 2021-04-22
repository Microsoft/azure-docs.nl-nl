---
title: Inleiding tot Azure Cosmos DB API voor MongoDB
description: Meer informatie over hoe u Azure Cosmos DB kunt gebruiken voor het opslaan van enorme hoeveelheden gegevens met de API van Azure Cosmos DB voor MongoDB en er query's op kunt uitvoeren.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 04/20/2021
author: gahl-levy
ms.author: gahllevy
robots: noindex
ms.openlocfilehash: 04763aaf91b077223b8c2c5b4f56dc7289a9dbb4
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107893051"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Met Azure Cosmos DB API voor MongoDB kunt u deze eenvoudig Cosmos DB alsof het een MongoDB-database is. U kunt gebruikmaken van uw MongoDB-ervaring en uw favoriete MongoDB-stuurprogramma's, SDK's en hulpprogramma's blijven gebruiken door uw toepassing te laten wijzen naar de API voor de mongoDB-account-connection string.

De API voor MongoDB biedt de volgende extra voordelen van het bouwen op [Azure Cosmos DB:](introduction.md)

* **Onmiddellijke schaalbaarheid:** door de functie [Automatisch schalen](provision-throughput-autoscale.md) in teschakelen, kan uw database omhoog/omlaag worden geschaald zonder opwarmperiode. 
* **Volledig beheerde sharding:** het beheren van de database-infrastructuur is moeilijk en tijdrovend. De API voor MongoDB beheert de infrastructuur voor u, waaronder sharding. U hoeft zich dus geen zorgen te maken over het beheer en u te richten op het bouwen van toepassingen voor uw gebruikers.
* **Maximaal vijf negens** beschikbaarheid: een beschikbaarheid van [99,999%](high-availability.md) kan eenvoudig worden geconfigureerd om ervoor te zorgen dat uw gegevens altijd voor u beschikbaar zijn.  
* **Kostenefficiënt,** onbeperkte schaalbaarheid: Shard-verzamelingen kunnen op een kostenefficiënte manier worden geschaald tot elke grootte, in stappen van slechts 1/100e van een virtueleM vanwege schaalvoordelen en resourcebeheer.
* **Upgrades duren seconden:** alle API-versies zijn opgenomen in één codebasis, waardoor versiewijzigingen net zo eenvoudig zijn als het spiegelen van een [switch,](mongodb-version-upgrade.md)zonder downtime.
* **Synapse-koppelingsanalyse:** analyseer uw realtime gegevens met behulp van de volledig geïsoleerde [Azure Synapse analytische](synapse-link.md) opslag voor snelle en goedkope analysequery's. Een eenvoudig selectievakje zorgt ervoor dat uw gegevens beschikbaar zijn in Synapse zonder ETL (extract-transform-load).

> [!NOTE]
> [U kunt Azure Cosmos DB API voor MongoDB gratis gebruiken met de gratis laag!](how-pricing-works.md). Met Azure Cosmos DB gratis laag krijgt u gratis de eerste 400 RU/s en 5 GB aan opslagruimte in uw account, toegepast op accountniveau.


## <a name="how-the-api-works"></a>Hoe de API werkt

Azure Cosmos DB API voor MongoDB implementeert het wire-protocol voor MongoDB. Deze implementatie maakt transparante compatibiliteit mogelijk met de systeemeigen SDK's, stuurprogramma's en hulpprogramma's van de MongoDB-client. Azure Cosmos DB fungeert niet als host voor de MongoDB-database-engine. Elk MongoDB-clientst stuurprogramma dat compatibel is met de API-versie die u gebruikt, moet verbinding kunnen maken, zonder speciale configuratie.

Compatibiliteit met MongoDB-functies:

Azure Cosmos DB API voor MongoDB is compatibel met de volgende MongoDB-serverversies:
- [Versie 4.0](mongodb-feature-support-40.md)
- [Versie 3.6](mongodb-feature-support-36.md)
- [Versie 3.2](mongodb-feature-support.md)

Alle API's voor MongoDB-versies worden uitgevoerd op dezelfde codebasis, waardoor upgrades een eenvoudige taak zijn die binnen enkele seconden zonder downtime kan worden uitgevoerd. Azure Cosmos DB slechts een paar functievlaggen om van de ene versie naar de andere te gaan.  De functievlaggen bieden ook ondersteuning voor oudere API-versies, zoals 3.2 en 3.6. U kunt de serverversie kiezen die het beste bij u past.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="Azure Cosmos DB-API voor MongoDB" border="false":::

## <a name="what-you-need-to-know-to-get-started"></a>Wat u moet weten om aan de slag te gaan

* U wordt niet gefactureerd voor virtuele machines in een cluster. [Prijzen](how-pricing-works.md) zijn gebaseerd op doorvoer in aanvraageenheden (AANVRAAGeenheden) die per database of per verzameling zijn geconfigureerd. De eerste 400 RUs per seconde zijn gratis met [de gratis laag](how-pricing-works.md).

* Er zijn drie manieren om een API Azure Cosmos DB MongoDB te implementeren:
     * [Inrichten van doorvoer:](set-throughput.md)stel een RU/sec-nummer in en wijzig dit handmatig. Dit model past het beste bij consistente workloads.
     * [Automatisch schalen:](provision-throughput-autoscale.md)stel een bovengrens in voor de doorvoer die u nodig hebt. Doorvoer wordt onmiddellijk geschaald om aan uw behoeften te voldoen. Dit model past het beste bij workloads die regelmatig veranderen en optimaliseert hun kosten.
     * [Serverloos](serverless.md) (preview): u betaalt alleen voor de doorvoer die u gebruikt, punt. Dit model past het beste bij dev/test-workloads. 

* Shard-clusterprestaties zijn afhankelijk van de shardsleutel die u kiest bij het maken van een verzameling. Kies zorgvuldig een shardsleutel om ervoor te zorgen dat uw gegevens gelijkmatig worden verdeeld over shards.

## <a name="quickstart"></a>Snelstart

* [Een bestaande MongoDB Node.js-web-app migreren](create-mongodb-nodejs.md).
* [Met de API van Azure Cosmos DB voor MongoDB en de .NET-SDK een web-app maken](create-mongodb-dotnet.md).
* [Met de API van Azure Cosmos DB voor MongoDB en de Java-SDK een console-app maken](create-mongodb-java.md).

## <a name="next-steps"></a>Volgende stappen

* Volg de zelfstudie [Een MongoDB-toepassing verbinden met Azure Cosmos DB](connect-mongodb-account.md) voor informatie over het ophalen van verbindingsreeksinformatie voor uw account.
* Volg de zelfstudie [Studio 3T gebruiken met Azure Cosmos DB](mongodb-mongochef.md) om te leren hoe u een verbinding maakt tussen een Cosmos-database en de MongoDB-app in Studio 3T.
* Volg de zelfstudie [MongoDB-gegevens importeren in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) om uw gegevens te importeren in een Cosmos-database.
* Verbinding maken met een Cosmos-account met behulp van [Robo 3T](mongodb-robomongo.md).
* Meer informatie over het [configureren van leesvoorkeuren voor wereldwijd gedistribueerde apps](../cosmos-db/tutorial-global-distribution-mongodb.md).
* Vind de oplossingen voor veelvoorkomende fouten in onze [Gids voor probleemoplossing](mongodb-troubleshoot.md)
