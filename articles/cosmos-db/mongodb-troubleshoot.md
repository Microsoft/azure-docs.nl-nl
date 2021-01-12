---
title: Veelvoorkomende fouten in de API van Azure Cosmos DB voor Mongo DB oplossen
description: Dit document bevat informatie over de manieren om veelvoorkomende problemen op te lossen die zijn opgetreden in de API van Azure Cosmos DB voor MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 06a06d275ba6f5ded475ffd693ee61e7a72b9516
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127699"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Veelvoorkomende problemen met de API van Azure Cosmos DB voor MongoDB oplossen
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In het volgende artikel worden veelvoorkomende fouten en oplossingen voor implementaties beschreven met behulp van de Azure Cosmos DB-API voor MongoDB.

>[!Note]
> Azure Cosmos DB host de MongoDB-engine niet. Het biedt een implementatie van het MongoDB wire-protocol. Daarom zijn sommige van deze fouten alleen te vinden in de API van Azure Cosmos DB voor MongoDB. 

## <a name="common-errors-and-solutions"></a>Veelvoorkomende fouten en oplossingen

| Code       | Fout                | Beschrijving  | Oplossing  |
|------------|----------------------|--------------|-----------|
| 2 | Het pad van de index dat overeenkomt met het opgegeven order-by-item wordt uitgesloten of de order by-query heeft geen overeenkomende samengestelde index waaruit het kan worden geleverd. | De query vraagt om een sortering op een veld dat niet is geïndexeerd. | Maak een overeenkomende index (of samengestelde index) voor de sorteer query die wordt geprobeerd. |
| 13 | Niet geautoriseerd | De aanvraag heeft geen machtigingen om te volt ooien. | Zorg ervoor dat u de juiste machtigingen voor uw data base en verzameling hebt ingesteld.  |
| 16 | InvalidLength | De opgegeven aanvraag heeft een ongeldige lengte. | Als u de functie uitleggen () gebruikt, moet u ervoor zorgen dat u slechts één bewerking opgeeft. |
| 26 | NamespaceNotFound | De data base of verzameling waarnaar wordt verwezen in de query, kan niet worden gevonden. | Zorg ervoor dat de naam van uw data base/verzameling precies overeenkomt met de naam in uw query.|
| 50 | ExceededTimeLimit | De aanvraag heeft de time-out van 60 seconden uitvoeringstijd overschreden. |  Er kunnen veel oorzaken voor deze fout zijn. Een van de oorzaken is wanneer de momenteel toegewezen capaciteit van aanvraag eenheden onvoldoende is om de aanvraag te volt ooien. Dit kan worden opgelost door het aantal aanvraageenheden van die verzameling of database te vergroten. In andere gevallen kan deze fout worden omzeild door een grote aanvraag in kleinere items te splitsen. Als u een schrijf bewerking opnieuw probeert uit te voeren die deze fout heeft ontvangen, kan dit leiden tot dubbele schrijf bewerkingen.|
| 61 | ShardKeyNotFound | Het document in uw aanvraag bevat niet de Shard-sleutel (Azure Cosmos DB-partitie sleutel) van de verzameling. | Zorg ervoor dat de Shard-sleutel van de verzameling in de aanvraag wordt gebruikt.|
| 66 | ImmutableField | Er wordt geprobeerd een onveranderbaar veld te wijzigen met de aanvraag | de id-velden zijn onveranderbaar. Zorg ervoor dat uw aanvraag dat veld niet probeert bij te werken. |
| 67 | CannotCreateIndex | Kan de aanvraag voor het maken van een index niet volt ooien. | Maxi maal 500 enkelvoudige veld indexen kunnen in een container worden gemaakt. Er kunnen Maxi maal acht velden worden opgenomen in een samengestelde index (samengestelde indexen worden ondersteund in versie 3.6 +). |
| 115 | CommandNotSupported | De gevraagde aanvraag wordt niet ondersteund. | Meer informatie vindt u in de fout. Als deze functionaliteit belang rijk is voor uw implementaties, laat u ons weten door een ondersteunings ticket te maken in het [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). |
| 11000 | DuplicateKey | De Shard-sleutel (Azure Cosmos DB partitie sleutel) van het document dat u invoegt, bevindt zich al in de verzameling of een unieke index veld beperking is geschonden. | Gebruik de functie update () om een bestaand document bij te werken. Als de beperking voor het unieke index veld is geschonden, moet u het document invoegen of bijwerken met een veld waarde die nog niet in de Shard/partitie bestaat. |
| 16500 | TooManyRequests  | Het totale aantal verbruikte aanvraageenheden is groter dan de ingerichte aanvraageenheidsnelheid voor de verzameling en is beperkt. | Overweeg de aan een container of een set containers toegewezen doorvoer te schalen vanuit de Azure-portal, of probeer de bewerking opnieuw uit te voeren. Als u SSR (nieuwe poging voor server zijde) inschakelt, probeert Azure Cosmos DB automatisch opnieuw de aanvragen uit te voeren die vanwege deze fout zijn mislukt. |
| 16501 | ExceededMemoryLimit | Als multi tenant service heeft de bewerking de geheugen toewijzing van de client overschreden. | Verklein het bereik van de bewerking via meer beperkende query criteria of neem contact op met de ondersteuning van de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Voorbeeld: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| 40324 | De naam van de pijplijn fase wordt niet herkend. | De naam van het stadium in uw aggregatie pijplijn aanvraag is niet herkend. | Zorg ervoor dat alle namen van de aggregatie pijplijnen geldig zijn in uw aanvraag. |
| - | Problemen met wire-versies van MongoDB | De oudere versies van MongoDB-Stuur Programma's kunnen de naam van het Azure Cosmos-account in de verbindings reeksen niet detecteren. | Voeg *AppName = @**AccountName** @* toe aan het einde van de API van uw Cosmos DB voor MongoDb Connection String, waarbij ***AccountName*** de naam van uw Cosmos DB-account is. |
| - | MongoDB-client netwerk problemen (zoals socket-of endOfStream-uitzonde ringen)| De netwerk aanvraag is mislukt. Dit wordt vaak veroorzaakt door een inactieve TCP-verbinding die de MongoDB-client probeert te gebruiken. MongoDB-Stuur Programma's gebruiken vaak groepsgewijze verbindingen, wat resulteert in een wille keurige verbinding die wordt gekozen uit de groep voor een aanvraag. Inactieve verbindingen hebben meestal een time-out op het Azure Cosmos DB dat na vier minuten eindigt. | U kunt deze mislukte aanvragen in uw toepassings code opnieuw proberen, uw MongoDB-client instellingen (stuur programma) wijzigen in teardown inactieve TCP-verbindingen vóór de time-out van vier minuten of uw keepalive-instellingen voor het besturings systeem configureren om de TCP-verbindingen met een actieve status te onderhouden. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.
