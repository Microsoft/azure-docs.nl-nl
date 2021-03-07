---
title: Paginering in Azure Cosmos DB
description: Meer informatie over wissel concepten en vervolg tokens
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.openlocfilehash: 5faff410fa18c5161d93f739f77eeb9c85d581a8
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430952"
---
# <a name="pagination-in-azure-cosmos-db"></a>Paginering in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB kunnen query's meerdere pagina's met resultaten bevatten. In dit document worden de criteria uitgelegd die worden gebruikt door de query-engine van Azure Cosmos DB om te bepalen of query resultaten moeten worden gesplitst in meerdere pagina's. U kunt eventueel vervolg tokens gebruiken voor het beheren van query resultaten die meerdere pagina's omvatten.

## <a name="understanding-query-executions"></a>Informatie over het uitvoeren van query's

Soms worden query resultaten verdeeld over meerdere pagina's. De resultaten van elke pagina worden gegenereerd door een afzonderlijke query-uitvoering. Wanneer query resultaten niet in één uitvoering kunnen worden geretourneerd, worden de resultaten door Azure Cosmos DB automatisch in meerdere pagina's gesplitst.

U kunt het maximum aantal items opgeven dat door een query wordt geretourneerd door de in te stellen `MaxItemCount` . De `MaxItemCount` is opgegeven per aanvraag en geeft aan dat de query-engine dat aantal items of minder heeft geretourneerd. U kunt instellen `MaxItemCount` op `-1` Als u geen limiet wilt plaatsen voor het aantal resultaten per query uitvoering.

Daarnaast zijn er andere redenen waarom de query-engine mogelijk query resultaten in meerdere pagina's moet splitsen. Deze omvatten:

- De container is beperkt en er is geen beschik bare RUs beschikbaar om meer query resultaten te retour neren
- Het antwoord van de query uitvoering is te groot
- De tijd voor het uitvoeren van de query is te lang
- Het is efficiënter voor de query-engine om resultaten te retour neren in extra uitvoeringen

Het aantal geretourneerde items per query-uitvoering is altijd kleiner dan of gelijk aan `MaxItemCount` . Het is echter mogelijk dat andere criteria een beperkt aantal resultaten van de query kunnen retour neren. Als u dezelfde query meerdere keren uitvoert, is het aantal pagina's mogelijk niet constant. Als een query bijvoorbeeld wordt beperkt, kunnen er minder beschik bare resultaten per pagina zijn, wat betekent dat de query extra pagina's bevat. In sommige gevallen is het ook mogelijk dat uw query een lege pagina met resultaten retourneert.

## <a name="handling-multiple-pages-of-results"></a>Meerdere pagina's met resultaten verwerken

Om ervoor te zorgen dat de query resultaten nauw keurig worden uitgevoerd, moet u alle pagina's door lopen. U moet door gaan met het uitvoeren van query's totdat er geen extra pagina's zijn.

Hier volgen enkele voor beelden van het verwerken van resultaten van query's met meerdere pagina's:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L280)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/documentcrud/sync/DocumentCRUDQuickstart.java#L162-L176)
- [Node.js SDK](https://github.com/Azure/azure-sdk-for-js/blob/83fcc44a23ad771128d6e0f49043656b3d1df990/sdk/cosmosdb/cosmos/samples/IndexManagement.ts#L128-L140)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/examples.py#L89)

## <a name="continuation-tokens"></a>Vervolg tokens

In de .NET SDK en Java SDK kunt u optioneel vervolg tokens gebruiken als blad wijzer voor de voortgang van uw query. Azure Cosmos DB uitvoeringen van query's zijn stateless aan de server zijde en kunnen op elk gewenst moment worden hervat met behulp van het vervolg token. Vervolg tokens worden niet ondersteund in de Node.js SDK. Voor de python-SDK wordt de ondersteuning voor enkelvoudige partitie query's ondersteund en moet de PK worden opgegeven in het object Options, omdat het niet voldoende is om deze in de query zelf te laten staan.

Hier volgen enkele voor beelden van het gebruik van vervolg tokens:

- [.NET SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/samples/code-samples/Queries/Program.cs#L699-L734)
- [Java SDK](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/main/src/main/java/com/azure/cosmos/examples/queries/sync/QueriesQuickstart.java#L216)
- [Python SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/test/test_query.py#L533)

Als de query een vervolg token retourneert, zijn er aanvullende query resultaten.

In Azure Cosmos DB REST API kunt u vervolg tokens met de `x-ms-continuation` header beheren. Net als bij het uitvoeren van query's met de .NET-of Java-SDK, `x-ms-continuation` betekent dit dat de query aanvullende resultaten oplevert.

Zolang u dezelfde SDK-versie gebruikt, verlopen vervolg tokens nooit. U kunt eventueel [de grootte van een vervolg token beperken](/dotnet/api/microsoft.azure.documents.client.feedoptions.responsecontinuationtokenlimitinkb#Microsoft_Azure_Documents_Client_FeedOptions_ResponseContinuationTokenLimitInKb). Ongeacht de hoeveelheid gegevens of het aantal fysieke partities in uw container, retour neren query's één vervolg token.

U kunt geen vervolg tokens gebruiken voor query's met [Group by](sql-query-group-by.md) of [DISTINCT](sql-query-keywords.md#distinct) , omdat voor deze query's een aanzienlijke hoeveelheid status zou moeten worden opgeslagen. Voor query's met `DISTINCT` kunt u vervolg tokens gebruiken als u `ORDER BY` aan de query toevoegt.

Hier volgt een voor beeld van een query `DISTINCT` waarmee een vervolg token kan worden gebruikt:

```sql
SELECT DISTINCT VALUE c.name
FROM c
ORDER BY c.name
```

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [ORDER BY-component](sql-query-order-by.md)
