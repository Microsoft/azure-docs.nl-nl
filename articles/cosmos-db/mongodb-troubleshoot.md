---
title: Veelvoorkomende fouten in de API van Azure Cosmos DB voor Mongo DB oplossen
description: Dit document bevat informatie over de manieren om veelvoorkomende problemen op te lossen die zijn opgetreden in de API van Azure Cosmos DB voor MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 13ac90ae70262f2f6781f5f40ec67da4bf74ab68
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661270"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Veelvoorkomende problemen met de API van Azure Cosmos DB voor MongoDB oplossen
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In het volgende artikel worden veelvoorkomende fouten en oplossingen voor implementaties beschreven met behulp van de Azure Cosmos DB-API voor MongoDB.

>[!Note]
> Azure Cosmos DB host de MongoDB-engine niet. Het biedt een implementatie van de MongoDB [wire-protocol versie 4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)en de verouderde ondersteuning voor [wire-protocol versie 3,2](mongodb-feature-support.md). Daarom zijn sommige van deze fouten alleen te vinden in de API van Azure Cosmos DB voor MongoDB.

## <a name="common-errors-and-solutions"></a>Veelvoorkomende fouten en oplossingen

| Code       | Fout                | Beschrijving  | Oplossing  |
|------------|----------------------|--------------|-----------|
| 2 | Het pad van de index dat overeenkomt met het opgegeven order-by-item wordt uitgesloten of de order by-query heeft geen overeenkomende samengestelde index waaruit het kan worden geleverd. | De query vraagt om een sortering op een veld dat niet is geïndexeerd. | Maak een overeenkomende index (of samengestelde index) voor de sorteerquery die moet worden uitgevoerd. |
| 13 | Niet geautoriseerd | De aanvraag heeft geen machtigingen waarmee dit kan worden voltooid. | Zorg ervoor dat u de juiste machtigingen voor uw data base en verzameling hebt ingesteld.  |
| 16 | InvalidLength | De opgegeven aanvraag heeft een ongeldige lengte. | Als u de functie uitleggen () gebruikt, moet u ervoor zorgen dat u slechts één bewerking opgeeft. |
| 26 | NamespaceNotFound | De database of verzameling waarnaar in de query wordt verwezen, kan niet worden gevonden. | Zorg ervoor dat de naam van uw database/verzameling precies overeenkomt met de naam in uw query.|
| 50 | ExceededTimeLimit | De aanvraag heeft de time-out van 60 seconden uitvoeringstijd overschreden. |  Deze fout kan meerdere oorzaken hebben. Een van de oorzaken is wanneer het huidige aantal toegewezen aanvraageenheden van de capaciteit niet voldoende is om de aanvraag af te ronden. Dit kan worden opgelost door het aantal aanvraageenheden van die verzameling of database te vergroten. In andere gevallen kan deze fout worden omzeild door een grote aanvraag op te delen in kleinere. Als u een schrijfbewerking opnieuw probeert uit te voeren die deze fout heeft ontvangen, kan dit leiden tot een dubbele schrijfbewerking. <br><br>Als u grote hoeveelheden gegevens wilt verwijderen zonder dat dit van invloed is op RU's: <br>-Overweeg het gebruik van TTL (op basis van tijds tempel): [gegevens laten verlopen met de API van Azure Cosmos DB voor MongoDb](./mongodb-time-to-live.md) <br>-Gebruik de cursor/Batch grootte om het verwijderen uit te voeren. U kunt één document tegelijk ophalen en dit document verwijderen via een lus. Zo kunt u langzaam gegevens verwijderen zonder dat dit van invloed is op de productietoepassing.|
| 61 | ShardKeyNotFound | Het document in uw aanvraag bevat de shardsleutel van de verzameling niet (Azure Cosmos DB-partitiesleutel). | Zorg ervoor dat de shardsleutel van de verzameling in de aanvraag wordt gebruikt.|
| 66 | ImmutableField | Met de aanvraag wordt geprobeerd een onveranderbaar veld te wijzigen | de id-velden zijn onveranderbaar. Zorg ervoor dat uw aanvraag dat veld niet probeert bij te werken. |
| 67 | CannotCreateIndex | De aanvraag voor het maken van een index kan niet worden voltooid. | Er kunnen maximaal 500 enkelvoudige veldindexen in een container worden gemaakt. Er kunnen maximaal acht velden worden opgenomen in een samengestelde index (samengestelde indexen worden ondersteund in versie 3.6+). |
| 115 | CommandNotSupported | De aanvraag wordt niet ondersteund. | Meer informatie vindt u in de fout. Als deze functionaliteit belang rijk is voor uw implementaties, laat u ons weten door een ondersteunings ticket te maken in het [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | De shardsleutel (Azure Cosmos DB-partitiesleutel) van het document dat u invoegt, bevindt zich al in de verzameling of er wordt een unieke indexveldbeperking geschonden. | Gebruik de functie update() om een bestaand document bij te werken. Als de unieke indexveldbeperking is geschonden, moet u het document invoegen of bijwerken met een veldwaarde die nog niet in de shard/partitie aanwezig is. |
| 16500 | TooManyRequests  | Het totale aantal verbruikte aanvraageenheden is groter dan de ingerichte aanvraageenheidsnelheid voor de verzameling en is beperkt. | Overweeg de aan een container of een set containers toegewezen doorvoer te schalen vanuit de Azure-portal, of probeer de bewerking opnieuw uit te voeren. Als u SSR (nieuwe poging voor server zijde) [inschakelt](prevent-rate-limiting-errors.md) , probeert Azure Cosmos DB automatisch opnieuw de aanvragen uit te voeren die vanwege deze fout zijn mislukt. |
| 16501 | ExceededMemoryLimit | Voor een service met meerdere tenants betekent dit dat de bewerking de geheugentoewijzing van de client heeft overschreden. | Verklein het bereik van de bewerking via meer beperkende querycriteria of neem contact op met de ondersteuning via [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Voorbeeld: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | De naam van de pijplijnfase wordt niet herkend. | De naam van de fase in de aanvraag van uw aggregatiepijplijn wordt niet herkend. | Zorg ervoor dat alle namen van aggregatiepijplijnen geldig zijn in uw aanvraag. |
| - | Problemen met wire-versies van MongoDB | De oudere versies van MongoDB-stuurprogramma's kunnen de naam van het Azure Cosmos-account niet detecteren in de verbindingsreeksen. | Voeg *AppName = @**AccountName** @* toe aan het einde van de API van uw Cosmos DB voor MongoDb Connection String, waarbij ***AccountName*** de naam van uw Cosmos DB-account is. |
| - | Problemen met het MongoDB-clientnetwerk (zoals socket- of endOfStream-uitzonderingen)| De netwerkaanvraag is mislukt. Dit wordt vaak veroorzaakt door een inactieve TCP-verbinding die de MongoDB-client wil gebruiken. MongoDB-stuurprogramma's gebruiken vaak groepsgewijze verbindingen, wat ertoe leidt dat er een willekeurige verbinding uit de groep voor een aanvraag wordt gekozen. Op het Azure Cosmos DB-eind hebben inactieve verbindingen meestal na vier minuten last van een time-out. | U kunt deze mislukte aanvragen in uw toepassings code opnieuw proberen, uw MongoDB-client instellingen (stuur programma) wijzigen in teardown inactieve TCP-verbindingen vóór de time-out van vier minuten of uw keepalive-instellingen voor het besturings systeem configureren om de TCP-verbindingen met een actieve status te onderhouden.<br><br>Om berichten over de verbinding te voorkomen, kunt u de verbindingsreeks wijzigen en maxConnectionIdleTime instellen op 1-2 minuten.<br>-Mongo-stuur programma: *maxIdleTimeMS configureren = 120000* <br>-Node.JS: Configure *socketTimeoutMS = 120000*, *autoReconnect* = True, *keepAlive* = True, *keepAliveInitialDelay* = 3 minuten

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.