---
title: Voorbereidende stappen voor het migreren van gegevens naar de API van Azure Cosmos DB voor MongoDB
description: Dit document bevat een overzicht van de vereisten voor een gegevens migratie van MongoDB naar Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anfeldma
ms.openlocfilehash: cdc5dc9cee3520d9a3f22ff710dfa193e6ef4fed
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553286"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Stappen voorafgaand aan de migratie voor gegevens migraties van MongoDB naar de API van Azure Cosmos DB voor MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!IMPORTANT]  
> Deze MongoDB-hand leiding voor vóór de migratie is de eerste in een reeks bij het migreren van MongoDB naar Azure Cosmos DB Mongo-API op schaal. Klanten die licenties hebben en MongoDB op zelf beheerde infra structuur implementeren, kunnen de kosten van hun gegevens beperken en beheren door te migreren naar een beheerde Cloud service, zoals Azure Cosmos DB met prijzen voor betalen per gebruik en een elastische schaal baarheid. Het doel van deze serie is de klant te begeleiden door het migratie proces:
>
> 1. [Voorafgaand](mongodb-pre-migration.md) aan de migratie: Inventariseer de bestaande MongoDb data onroerend goed, plan migratie en kies de juiste migratie hulpprogramma's.
> 2. Uitvoering: migreren van MongoDB naar Azure Cosmos DB met behulp van de meegeleverde [zelf studies]().
> 3. [Na de migratie](mongodb-post-migration.md) : bestaande toepassingen bijwerken en optimaliseren om uit te voeren op uw nieuwe Azure Cosmos DB Data-erfgoed.
>

Een vast pre-migratie plan kan een grote invloed hebben op de tijd lijn en het slagen van de migratie van uw team. Een goede analogeie voor vóór de migratie begint met het maken van vereisten. u kunt beginnen met het definiëren van behoeften, vervolgens een overzicht geven van de taken die zijn betrokken en de grootste taken die als eerste moeten worden behandeld. Dit helpt om uw project planning voorspelbaar te maken, maar uiteraard kunnen er onverwachte vereisten ontstaan en de project planning bemoeilijken. Als u terugkeert naar migratie: door een uitgebreid uitvoerings plan te bouwen in de fase voorafgaand aan de migratie, minimaliseert u de kans dat u onverwachte migratie taken in de loop van het proces zult ontdekken, bespaart u tijd tijdens de migratie en helpt u ervoor te zorgen dat er doelen worden gehaald.

Voordat u uw gegevens migreert van MongoDB (on-premises of in de Cloud) naar de API van Azure Cosmos DB voor MongoDB, moet u het volgende doen:

1. [Lees de belangrijkste aandachtspunten over het gebruik van de API van Azure Cosmos DB voor MongoDB](#considerations)
2. [Kies een optie voor het migreren van uw gegevens](#options)
3. [Een schatting maken van de door Voer die nodig is voor uw workloads](#estimate-throughput)
4. [Kies een optimale partitie sleutel voor uw gegevens](#partitioning)
5. [Meer informatie over het indexerings beleid dat u op uw gegevens kunt instellen](#indexing)

Als u de bovenstaande vereisten voor de migratie al hebt voltooid, kunt u MongoDB- [gegevens migreren naar de API van Azure Cosmos DB voor MongoDb met behulp van de Azure database Migration service](../dms/tutorial-mongodb-cosmos-db.md). Als u nog geen account hebt gemaakt, kunt u door een van de [Quick](create-mongodb-dotnet.md) starts bladeren die de stappen voor het maken van een account weer geven.

## <a name="considerations-when-using-azure-cosmos-dbs-api-for-mongodb"></a><a id="considerations"></a>Overwegingen bij het gebruik van de API van Azure Cosmos DB voor MongoDB

Hieronder vindt u specifieke kenmerken over de API van Azure Cosmos DB voor MongoDB:

- **Capaciteits model**: de capaciteit van de data base op Azure Cosmos DB is gebaseerd op een model op basis van door voer. Dit model is gebaseerd op [aanvraag eenheden per seconde](request-units.md). Dit is een eenheid die het aantal database bewerkingen vertegenwoordigt dat per seconde kan worden uitgevoerd voor een verzameling. Deze capaciteit kan worden toegewezen op [Data Base-of verzamelings niveau](set-throughput.md), en kan worden ingericht voor een toewijzings model, of met de [ingerichte door Voer voor automatisch schalen](provision-throughput-autoscale.md).

- **Aanvraag eenheden**: elke database bewerking heeft een bijbehorende aanvraag eenheden (RUs) in azure Cosmos db. Wanneer dit wordt uitgevoerd, wordt dit afgetrokken van het niveau van de beschik bare aanvraag eenheden van een opgegeven seconde. Als voor een aanvraag meer RUs is vereist dan de momenteel toegewezen RU/s, zijn er twee opties om het probleem op te lossen: Verhoog de hoeveelheid RUs of wacht tot de volgende seconde wordt gestart en voer de bewerking vervolgens opnieuw uit.

- **Elastische capaciteit**: de capaciteit van een bepaalde verzameling of Data Base kan op elk gewenst moment worden gewijzigd. Hierdoor kan de data base flexibel worden aangepast aan de doorvoer vereisten van uw workload.

- **Automatische sharding**: Azure Cosmos DB biedt een automatisch partitioneren-systeem dat alleen een Shard (of een partitie sleutel) nodig heeft. Het [mechanisme voor automatische partitionering](partitioning-overview.md) wordt gedeeld met alle Azure Cosmos DB api's en maakt naadloze gegevens en schaalbaar door horizontale distributie mogelijk.

## <a name="migration-options-for-azure-cosmos-dbs-api-for-mongodb"></a><a id="options"></a>Migratie opties voor de API van Azure Cosmos DB voor MongoDB

De [Azure database Migration service voor Azure Cosmos DB-API voor MongoDb](../dms/tutorial-mongodb-cosmos-db.md) biedt een mechanisme dat de gegevens migratie vereenvoudigt door een volledig beheerd hosting platform, migratie controle opties en automatische beperkings verwerking te bieden. De volledige lijst met opties is als volgt:

|**Type migratie**|**Oplossing**|**Overwegingen**|
|---------|---------|---------|
|Online|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Maakt gebruik van de bibliotheek van de Azure Cosmos DB bulk-uitvoerder <br/>&bull; Geschikt voor grote gegevens sets en zorgt voor het repliceren van Live wijzigingen <br/>&bull; Werkt alleen met andere MongoDB-bronnen|
|Offline|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; Maakt gebruik van de bibliotheek van de Azure Cosmos DB bulk-uitvoerder <br/>&bull; Geschikt voor grote gegevens sets en zorgt voor het repliceren van Live wijzigingen <br/>&bull; Werkt alleen met andere MongoDB-bronnen|
|Offline|[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)|&bull; Eenvoudig in te stellen en ondersteunt meerdere bronnen <br/>&bull; Maakt gebruik van de bibliotheek van de Azure Cosmos DB bulk-uitvoerder <br/>&bull; Geschikt voor grote gegevens sets <br/>&bull; Geen controle punten houdt in dat een probleem tijdens de migratie het opnieuw opstarten van het hele migratie proces vereist.<br/>&bull; Geen wachtrij met onbestelbare berichten zou betekenen dat een paar foutieve bestanden het gehele migratie proces zouden kunnen stoppen <br/>&bull; Aangepaste code nodig om de Lees doorvoer voor bepaalde gegevens bronnen te verg Roten|
|Offline|[Bestaande Mongo-Hulpprogram Ma's (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; Eenvoudig in te stellen en te integreren <br/>&bull; Aangepaste verwerking vereist voor beperking|

## <a name="estimate-the-throughput-need-for-your-workloads"></a><a id="estimate-throughput"></a> De door Voer voor uw workloads schatten

In Azure Cosmos DB wordt de door Voer vooraf ingericht en in aanvraag eenheden (RU) per seconde gemeten. In tegens telling tot Vm's of on-premises servers is RUs op elk gewenst moment eenvoudig omhoog en omlaag schalen. U kunt het aantal ingerichte RUs direct wijzigen. Zie [aanvraag eenheden in azure Cosmos DB](request-units.md)voor meer informatie.

U kunt de [Azure Cosmos DB capaciteit Calculator](https://cosmos.azure.com/capacitycalculator/) gebruiken om de hoeveelheid aanvraag eenheden te bepalen op basis van de configuratie van uw database account, de hoeveelheid gegevens, de document grootte en de vereiste Lees-en schrijf bewerkingen per seconde.

Hieronder vindt u belang rijke factoren die van invloed zijn op het aantal vereiste RUs:
- **Document grootte**: als de grootte van een item/document toeneemt, wordt het aantal dat is gebruikt voor het lezen of schrijven van het item/document ook verhoogd.

- **Aantal document eigenschappen**: het aantal verbruikte RUs voor het maken of bijwerken van een document is gerelateerd aan het aantal, de complexiteit en de lengte van de eigenschappen. U kunt het verbruik van aanvraag eenheden voor schrijf bewerkingen verlagen door [het aantal geïndexeerde eigenschappen te beperken](mongodb-indexing.md).

- **Query patronen**: de complexiteit van een query is van invloed op het aantal aanvraag eenheden dat door de query wordt verbruikt. 

De beste manier om de kosten van query's te begrijpen is het gebruik van voorbeeld gegevens in Azure Cosmos DB [en om voorbeeld query's uit de MongoDb-shell uit te voeren](connect-mongodb-account.md) met behulp van de `getLastRequestStastistics` opdracht om de aanvraag kosten op te halen, waardoor het aantal verbruikt RUs wordt uitgevoerd:

`db.runCommand({getLastRequestStatistics: 1})`

Met deze opdracht wordt een JSON-document uitgevoerd dat lijkt op het volgende:

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

U kunt ook [de diagnostische instellingen](cosmosdb-monitor-resource-logs.md) gebruiken om inzicht te krijgen in de frequentie en patronen van de query's die worden uitgevoerd op Azure Cosmos db. De resultaten van de diagnostische logboeken kunnen worden verzonden naar een opslag account, een EventHub-exemplaar of [Azure-log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).  

## <a name="choose-your-partition-key"></a><a id="partitioning"></a>Kies uw partitie sleutel
Partitioneren, ook wel bekend als sharding, is een belang rijk aandachtspunt voordat u gegevens migreert. Azure Cosmos DB maakt gebruik van volledig beheerde partities om de capaciteit in een Data Base te verg Roten om te voldoen aan de vereisten voor opslag en door voer. Voor deze functie is geen hosting-of configuratie van routerings servers nodig.   

Op dezelfde manier wordt met de partitionering automatisch capaciteit toegevoegd en worden de gegevens dienovereenkomstig opnieuw gebalanceerd. Zie het [artikel een partitie sleutel kiezen](partitioning-overview.md#choose-partitionkey)voor meer informatie en aanbevelingen over het kiezen van de juiste partitie sleutel voor uw gegevens. 

## <a name="index-your-data"></a><a id="indexing"></a>Uw gegevens indexeren

Met de API van Azure Cosmos DB voor MongoDB-server versies 3,6 en hoger wordt het `_id` veld alleen automatisch geïndexeerd. Dit veld kan niet worden verwijderd. De unieke waarde van het veld wordt automatisch afgedwongen `_id` per Shard-sleutel. Als u extra velden wilt indexeren, past u de [MongoDb-index beheer opdrachten](mongodb-indexing.md)toe. Dit standaard indexeringsbeleid wijkt af van de Azure Cosmos DB SQL-API, waarmee standaard alle velden worden geïndexeerd.

De indexerings mogelijkheden van Azure Cosmos DB zijn onder andere het toevoegen van samengestelde indexen, unieke indexen en TTL-indexen (time-to-Live). De index beheer-interface wordt toegewezen aan de `createIndex()` opdracht. Meer informatie vindt u [in indexering in azure Cosmos DB-API voor MongoDb](mongodb-indexing.md)-artikel.

[Azure database Migration service](../dms/tutorial-mongodb-cosmos-db.md) wordt automatisch MongoDb-verzamelingen met unieke indexen gemigreerd. De unieke indexen moeten echter worden gemaakt vóór de migratie. Azure Cosmos DB biedt geen ondersteuning voor het maken van unieke indexen wanneer er al gegevens in uw verzamelingen aanwezig zijn. Zie [Unieke sleutels in Azure Cosmos DB](unique-keys.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Migreer uw MongoDB-gegevens naar Cosmos DB met behulp van de Database Migration Service.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Door Voer in te richten op Azure Cosmos-containers en-data bases](set-throughput.md)
* [Partitionering in Azure Cosmos DB](partitioning-overview.md)
* [Globale distributie in Azure Cosmos DB](distribute-data-globally.md)
* [Indexering in Azure Cosmos DB](index-overview.md)
* [Aanvraageenheden in Azure Cosmos DB](request-units.md)
