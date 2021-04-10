---
title: Veelvoorkomende fouten in de API van Azure Cosmos DB voor Mongo DB oplossen
description: Dit document bevat informatie over de manieren om veelvoorkomende problemen op te lossen die zijn opgetreden in de API van Azure Cosmos DB voor MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: de39aee73a6f4b422af4524d3302f8858f8b060b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101692229"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Veelvoorkomende problemen met de API van Azure Cosmos DB voor MongoDB oplossen
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In het volgende artikel worden veelvoorkomende fouten en oplossingen voor implementaties beschreven met behulp van de Azure Cosmos DB-API voor MongoDB.

>[!Note]
> Azure Cosmos DB host de MongoDB-engine niet. Het biedt een implementatie van de MongoDB [wire-protocol versie 4,0](mongodb-feature-support-40.md), [3,6](mongodb-feature-support-36.md)en de verouderde ondersteuning voor [wire-protocol versie 3,2](mongodb-feature-support.md). Daarom zijn sommige van deze fouten alleen te vinden in de API van Azure Cosmos DB voor MongoDB.

## <a name="common-errors-and-solutions"></a>Veelvoorkomende fouten en oplossingen

| Code       | Fout                | Beschrijving  | Oplossing  |
|------------|----------------------|--------------|-----------|
| 2 | BadValue | Een veelvoorkomende oorzaak is dat een indexpad dat overeenkomt met het opgegeven ORDER-BY-item wordt uitgesloten of dat de ORDER-BY-query geen overeenkomstige samengestelde index heeft waaruit het kan worden geleverd. De query vraagt om een sortering op een veld dat niet is geïndexeerd. | Maak een overeenkomende index (of samengestelde index) voor de sorteerquery die moet worden uitgevoerd. |
| 2 | Transactie is niet actief | De transactie met meerdere documenten heeft de vaste tijdslimiet van 5 seconden overschreden. | Probeer de transactie met meerdere documenten opnieuw of beperk het bereik van bewerkingen binnen de transactie met meerdere documenten om deze binnen de tijdslimiet van vijf seconden te voltooien. |
| 13 | Niet geautoriseerd | De aanvraag heeft geen machtigingen waarmee dit kan worden voltooid. | Zorg ervoor dat u de juiste sleutels gebruikt.  |
| 26 | NamespaceNotFound | De database of verzameling waarnaar in de query wordt verwezen, kan niet worden gevonden. | Zorg ervoor dat de naam van uw database/verzameling precies overeenkomt met de naam in uw query.|
| 50 | ExceededTimeLimit | De aanvraag heeft de time-out van 60 seconden uitvoeringstijd overschreden. |  Deze fout kan meerdere oorzaken hebben. Een van de oorzaken is wanneer het huidige aantal toegewezen aanvraageenheden van de capaciteit niet voldoende is om de aanvraag af te ronden. Dit kan worden opgelost door het aantal aanvraageenheden van die verzameling of database te vergroten. In andere gevallen kan deze fout worden omzeild door een grote aanvraag op te delen in kleinere. Als u een schrijfbewerking opnieuw probeert uit te voeren die deze fout heeft ontvangen, kan dit leiden tot een dubbele schrijfbewerking. <br><br>Als u grote hoeveelheden gegevens wilt verwijderen zonder dat dit van invloed is op RU's: <br>- Overweeg het gebruik van TTL (op basis van tijdstempel): [Gegevens automatisch laten verlopen met de Azure Cosmos DB-API voor MongoDB](mongodb-time-to-live.md) <br>- Gebruik de cursor-/batchgrootte om de verwijdering uit te voeren. U kunt één document tegelijk ophalen en dit document verwijderen via een lus. Zo kunt u langzaam gegevens verwijderen zonder dat dit van invloed is op de productietoepassing.|
| 61 | ShardKeyNotFound | Het document in uw aanvraag bevat de shardsleutel van de verzameling niet (Azure Cosmos DB-partitiesleutel). | Zorg ervoor dat de shardsleutel van de verzameling in de aanvraag wordt gebruikt.|
| 66 | ImmutableField | Met de aanvraag wordt geprobeerd een onveranderbaar veld te wijzigen | de velden _id zijn onveranderbaar. Zorg ervoor dat het veld of het shardsleutelveld niet wordt bijwerkt. |
| 67 | CannotCreateIndex | De aanvraag voor het maken van een index kan niet worden voltooid. | Er kunnen maximaal 500 enkelvoudige veldindexen in een container worden gemaakt. Er kunnen maximaal acht velden worden opgenomen in een samengestelde index (samengestelde indexen worden ondersteund in versie 3.6+). |
| 112 | WriteConflict | De transactie met meerdere documenten is mislukt vanwege een conflicterende transactie met meerdere documenten | Probeer de transactie met meerdere documenten opnieuw totdat deze lukt. |
| 115 | CommandNotSupported | De aanvraag wordt niet ondersteund. | Meer informatie vindt u in de fout. Als deze functie belang rijk is voor uw implementaties, maakt u een ondersteunings ticket in het [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) en wordt het Azure Cosmos DB-team teruggeleid naar u. |
| 11000 | DuplicateKey | De shardsleutel (Azure Cosmos DB-partitiesleutel) van het document dat u invoegt, bevindt zich al in de verzameling of er wordt een unieke indexveldbeperking geschonden. | Gebruik de functie update() om een bestaand document bij te werken. Als de unieke indexveldbeperking is geschonden, moet u het document invoegen of bijwerken met een veldwaarde die nog niet in de shard/partitie aanwezig is. Een andere optie is door gebruik te maken van een veld met een combinatie van de id en shardsleutelvelden. |
| 16500 | TooManyRequests  | Het totale aantal verbruikte aanvraageenheden is groter dan de ingerichte aanvraageenheidsnelheid voor de verzameling en is beperkt. | Overweeg de aan een container of een set containers toegewezen doorvoer te schalen vanuit de Azure-portal, of probeer de bewerking opnieuw uit te voeren. Als u SSR (nieuwe poging aan serverzijde) inschakelt, gaat Azure Cosmos DB automatisch opnieuw proberen de aanvragen uit te voeren die vanwege deze fout zijn mislukt. |
| 16501 | ExceededMemoryLimit | Voor een service met meerdere tenants betekent dit dat de bewerking de geheugentoewijzing van de client heeft overschreden. Dit is alleen van toepassing op de Azure Cosmos DB-API voor MongoDB versie 3.2. | Verklein het bereik van de bewerking via meer beperkende querycriteria of neem contact op met de ondersteuning via [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Voorbeeld: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | De naam van de pijplijnfase wordt niet herkend. | De naam van de fase in de aanvraag van uw aggregatiepijplijn wordt niet herkend. | Zorg ervoor dat alle namen van aggregatiepijplijnen geldig zijn in uw aanvraag. |
| - | Problemen met wire-versies van MongoDB | De oudere versies van MongoDB-stuurprogramma's kunnen de naam van het Azure Cosmos-account niet detecteren in de verbindingsreeksen. | Voeg `appName=@accountName@` aan het einde van uw connection string toe, waarbij `accountName` de naam van uw Azure Cosmos DB account is. |
| - | Problemen met het MongoDB-clientnetwerk (zoals socket- of endOfStream-uitzonderingen)| De netwerkaanvraag is mislukt. Dit wordt vaak veroorzaakt door een inactieve TCP-verbinding die de MongoDB-client wil gebruiken. MongoDB-stuurprogramma's gebruiken vaak groepsgewijze verbindingen, wat ertoe leidt dat er een willekeurige verbinding uit de groep voor een aanvraag wordt gekozen. Op het Azure Cosmos DB-eind hebben inactieve verbindingen meestal na vier minuten last van een time-out. | U kunt deze mislukte aanvragen in uw toepassingscode opnieuw proberen, uw MongoDB-clientinstellingen (stuurprogramma) wijzigen om inactieve TCP-verbindingen open te breken vóór de time-out van vier minuten of de `keepalive`-instellingen voor het besturingsprogramma configureren om de TCP-verbindingen in een actieve status te houden.<br><br>U kunt de verbindingsreeks wijzigen en `maxConnectionIdleTime` instellen op 1-2 minuten om berichten over de verbinding te voorkomen.<br>- Mongo-stuurprogramma: configureer `maxIdleTimeMS=120000` <br>- Node.JS: configureer `socketTimeoutMS=120000`, `autoReconnect` = true, `keepAlive` = true, `keepAliveInitialDelay` = 3 minuten
| - | De Mongo-shell werkt niet in Azure Portal | Wanneer een gebruiker een Mongo-shell wil openen, gebeurt er niets en blijft het tabblad leeg.  | Controleer de firewall. De firewall wordt niet ondersteund met de Mongo-shell in Azure Portal. <br>- Installeer Mongo-shell op de lokale computer binnen de firewallregels <br>- gebruik verouderde Mongo-shell
| - | Kan geen verbinding maken met verbindingsreeks  | De verbindingsreeks is gewijzigd bij het upgraden van 3.2 naar 3.6 | Houd er rekening mee dat wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB-accounts, de 3.6-versie van accounts het eindpunt in de indeling `*.mongo.cosmos.azure.com` en de 3.2-versie van accounts het eindpunt in de indeling `*.documents.azure.com` heeft.  

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
