---
title: Veelvoorkomende fouten in de API van Azure Cosmos DB voor Mongo DB oplossen
description: Dit document bevat informatie over de manieren om veelvoorkomende problemen op te lossen die zijn opgetreden in de API van Azure Cosmos DB voor MongoDB.
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: chrande
ms.openlocfilehash: 9d76c3d9943300f88a146e82b862624d491cf546
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017811"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Veelvoorkomende problemen met de API van Azure Cosmos DB voor MongoDB oplossen
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In het volgende artikel worden veelvoorkomende fouten en oplossingen voor data bases beschreven met behulp van de Azure Cosmos DB-API voor MongoDB.

>[!Note]
> Azure Cosmos DB host de MongoDB-engine niet. Het biedt een implementatie van de MongoDB [wire-protocol versie 3,6](mongodb-feature-support-36.md) en de verouderde ondersteuning voor [wire-protocol versie 3,2](mongodb-feature-support.md). sommige van deze fouten zijn daarom alleen te vinden in de API van Azure Cosmos DB voor MongoDb. 

## <a name="common-errors-and-solutions"></a>Veelvoorkomende fouten en oplossingen

| Fout               | Code  | Description  | Oplossing  |
|---------------------|-------|--------------|-----------|
| ExceededTimeLimit   | 50 | De aanvraag heeft de time-out van 60 seconden uitvoeringstijd overschreden. | Er kunnen veel oorzaken voor deze fout zijn. Een van de oorzaken is wanneer het aantal toegewezen aanvraageenheden van de huidige capaciteit niet voldoende is om de aanvraag af te ronden. Dit kan worden opgelost door het aantal aanvraageenheden van die verzameling of database te vergroten. In andere gevallen kan deze fout worden omzeild door een grote aanvraag in kleinere items te splitsen. |
| TooManyRequests     | 16500 | Het totale aantal verbruikte aanvraageenheden is groter dan de ingerichte aanvraageenheidsnelheid voor de verzameling en is beperkt. | Overweeg de aan een container of een set containers toegewezen doorvoer te schalen vanuit de Azure-portal, of probeer de bewerking opnieuw uit te voeren. |
| ExceededMemoryLimit | 16501 | Als multi tenant service heeft de bewerking de geheugen toewijzing van de client overschreden. | Verklein het bereik van de bewerking via meer beperkende query criteria of neem contact op met de ondersteuning van de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Voorbeeld: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Het indexpad dat overeenkomt met het opgegeven ORDER-BY-item wordt uitgesloten / De ORDER-BY-query heeft geen overeenkomstige samengestelde index waaruit het kan worden geleverd. | 2 | De query vraagt om een sortering op een veld dat niet is geïndexeerd. | Maak een overeenkomende index (of samengestelde index) voor de sorteer query die wordt geprobeerd. |
| Problemen met wire-versies van MongoDB | - | De oudere versies van MongoDB-Stuur Programma's kunnen de naam van het Azure Cosmos-account in de verbindings reeksen niet detecteren. | Voeg *AppName = @**AccountName** @* toe aan het einde van de API van uw Cosmos DB voor MongoDb Connection String, waarbij ***AccountName*** de naam van uw Cosmos DB-account is. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [gebruik van Studio 3T](mongodb-mongochef.md) met de API voor MongoDB van Azure Cosmos DB.
- Meer informatie over het [gebruik van Robo 3T](mongodb-robomongo.md) met de API voor MongoDB van Azure Cosmos DB.
- Verken [voorbeelden](mongodb-samples.md) van MongoDB met de API van Azure Cosmos DB voor MongoDB.

