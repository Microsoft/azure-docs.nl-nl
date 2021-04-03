---
title: Migreren van de bibliotheek voor bulk-uitvoering naar de bulk ondersteuning in Azure Cosmos DB .NET v3 SDK
description: Meer informatie over hoe u uw toepassing migreert van met behulp van de bulk-uitvoerder bibliotheek naar de bulk ondersteuning in Azure Cosmos DB SDK v3
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 24d6b475964e4bf7745495e9c41d0e89bb76f7e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341277"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>Migreren van de bibliotheek voor bulk-uitvoering naar de bulk ondersteuning in Azure Cosmos DB .NET v3 SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel worden de vereiste stappen beschreven voor het migreren van de code van een bestaande toepassing die gebruikmaakt van de [.net-bibliotheek voor bulksgewijs](bulk-executor-dot-net.md) uitvoeren naar de functie voor [bulk ondersteuning](tutorial-sql-api-dotnet-bulk-import.md) in de nieuwste versie van de .NET SDK.

## <a name="enable-bulk-support"></a>Bulk ondersteuning inschakelen

Schakel bulksgewijs ondersteuning in voor het `CosmosClient` exemplaar via de [AllowBulkExecution](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) -configuratie:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>Taken voor elke bewerking maken

Bulk ondersteuning in de .NET SDK werkt door gebruik te maken van de [parallelle tape wisselaar](/dotnet/standard/parallel-programming/task-parallel-library-tpl) en groeperings bewerkingen die gelijktijdig optreden. 

De SDK bevat geen enkele methode waarmee u een lijst met documenten of bewerkingen als invoer parameter kunt gebruiken, maar in plaats daarvan moet u een taak maken voor elke bewerking die u in bulk wilt uitvoeren en vervolgens gewoon wachten totdat deze is voltooid.

Als uw eerste invoer bijvoorbeeld een lijst met items is waarbij elk item het volgende schema heeft:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

Als u Bulk Import wilt uitvoeren (vergelijkbaar met het gebruik van BulkExecutor. BulkImportAsync), moet u gelijktijdige aanroepen naar hebben `CreateItemAsync` . Bijvoorbeeld:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

Als u bulk *Update* wilt uitvoeren (vergelijkbaar met het gebruik van [BulkExecutor. BulkUpdateAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)), moet u `ReplaceItemAsync` na het bijwerken van de waarde van het item gelijktijdige aanroepen naar de methode hebben. Bijvoorbeeld:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

En als u bulksgewijs *verwijderen* wilt uitvoeren (vergelijkbaar met het gebruik van [BulkExecutor. BulkDeleteAsync](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)), moet u gelijktijdig aanroepen naar `DeleteItemAsync` , met de- `id` en-partitie sleutel van elk item. Bijvoorbeeld:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>Resultaat status van de opname taak

In de vorige code voorbeelden hebben we een gelijktijdige lijst met taken gemaakt en de `CaptureOperationResponse` methode voor elk van deze taken genoemd. Deze methode is een uitbrei ding waarmee we een *soortgelijk antwoord schema* kunnen onderhouden als BulkExecutor, door eventuele fouten vast te leggen en het gebruik van de [aanvraag eenheden](request-units.md)bij te houden.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

Waar de `OperationResponse` is gedeclareerd als:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>Gelijktijdig bewerkingen uitvoeren

Als u het bereik van de volledige lijst met taken wilt bijhouden, gebruiken we deze helperklasse:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

De `ExecuteAsync` methode wacht totdat alle bewerkingen zijn voltooid en u kunt deze als volgt gebruiken:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>Statistieken vastleggen

De vorige code wacht totdat alle bewerkingen zijn voltooid en de vereiste statistieken worden berekend. Deze statistieken zijn vergelijkbaar met die van de [BulkImportResponse](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)van de bulk-uitvoerder bibliotheek.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

De `BulkOperationResponse` bevat:

1. De totale tijd die nodig is om de lijst met bewerkingen te verwerken via bulksgewijze ondersteuning.
1. Het aantal geslaagde bewerkingen.
1. Het totaal van verbruikte aanvraag eenheden.
1. Als er fouten zijn, wordt er een lijst met Tuples weer gegeven die de uitzonde ring en het bijbehorende item voor logboek registratie en identificatie doeleinden bevatten.

## <a name="retry-configuration"></a>Configuratie opnieuw proberen

De bibliotheek voor bulksgewijs uitvoerder bevat [richt lijnen](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) voor het instellen `MaxRetryWaitTimeInSeconds` van de RetryOptions en om het `MaxRetryAttemptsOnThrottledRequests` [](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) `0` beheer te delegeren aan de bibliotheek.

Voor bulk ondersteuning in de .NET SDK is er geen verborgen gedrag. U kunt de opties voor opnieuw proberen rechtstreeks configureren via [CosmosClientOptions. MaxRetryAttemptsOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) en [CosmosClientOptions. MaxRetryWaitTimeOnRateLimitedRequests](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests).

> [!NOTE]
> In gevallen waarin de ingerichte aanvraag eenheden veel lager zijn dan verwacht op basis van de hoeveelheid gegevens, kunt u overwegen deze in te stellen op hoge waarden. De bulk bewerking duurt langer, maar heeft een hogere kans om volledig te slagen als gevolg van de hogere nieuwe pogingen.

## <a name="performance-improvements"></a>Prestatieverbeteringen

Net als bij andere bewerkingen met de .NET SDK resulteert het gebruik van de stream-Api's in betere prestaties en vermijdt overbodige serialisatie. 

Het gebruik van stream-Api's is alleen mogelijk als de aard van de gegevens die u gebruikt, overeenkomt met die van een byte stroom (bijvoorbeeld bestands stromen). In dergelijke gevallen kunt u met behulp `CreateItemStreamAsync` van de-, `ReplaceItemStreamAsync` -of- `DeleteItemStreamAsync` methoden en werken met `ResponseMessage` (in plaats van `ItemResponse` ) de door Voer verhogen die kan worden bereikt.

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel over de [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) voor meer informatie over de versies van .NET SDK.
* De volledige [bron code](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) voor de migratie ophalen van github.
* [Bijkomende bulk voorbeelden op GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)