---
title: Azure Cosmos DB trigger voor functies 2. x en hoger
description: Meer informatie over het gebruik van de Azure Cosmos DB trigger in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: a2f57fd6a369fba4a78799f768eb3fd2f3d27050
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98071473"
---
# <a name="azure-cosmos-db-trigger-for-azure-functions-2x-and-higher"></a>Azure Cosmos DB trigger voor Azure Functions 2. x en hoger

De Azure Cosmos DB trigger gebruikt de [Azure Cosmos DB wijzigings feed](../cosmos-db/change-feed.md) om te Luis teren naar invoeg toepassingen en updates op meerdere partities. De wijzigings feed publiceert invoegingen en updates, en kan niet worden verwijderd.

Zie het [overzicht](./functions-bindings-cosmosdb-v2.md)voor meer informatie over de installatie-en configuratie details.

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die wordt aangeroepen wanneer er invoeg acties of updates in de opgegeven Data Base en verzameling zijn.

```cs
using Microsoft.Azure.Documents;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Collections.Generic;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class CosmosTrigger
    {
        [FunctionName("CosmosTrigger")]
        public static void Run([CosmosDBTrigger(
            databaseName: "ToDoItems",
            collectionName: "Items",
            ConnectionStringSetting = "CosmosDBConnection",
            LeaseCollectionName = "leases",
            CreateLeaseCollectionIfNotExists = true)]IReadOnlyList<Document> documents,
            ILogger log)
        {
            if (documents != null && documents.Count > 0)
            {
                log.LogInformation($"Documents modified: {documents.Count}");
                log.LogInformation($"First document Id: {documents[0].Id}");
            }
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een Cosmos DB trigger binding in een *function.jsin* bestand en een [C#-script functie](functions-reference-csharp.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden toegevoegd of gewijzigd.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Dit is de C# Script-code:

```cs
    #r "Microsoft.Azure.DocumentDB.Core"

    using System;
    using Microsoft.Azure.Documents;
    using System.Collections.Generic;
    using Microsoft.Extensions.Logging;

    public static void Run(IReadOnlyList<Document> documents, ILogger log)
    {
      log.LogInformation("Documents modified " + documents.Count);
      log.LogInformation("First document Id " + documents[0].Id);
    }
```

# <a name="java"></a>[Java](#tab/java)

Deze functie wordt aangeroepen wanneer er invoeg-of update bewerkingen zijn in de opgegeven Data Base en verzameling.

```java
    @FunctionName("cosmosDBMonitor")
    public void cosmosDbProcessor(
        @CosmosDBTrigger(name = "items",
            databaseName = "ToDoList",
            collectionName = "Items",
            leaseCollectionName = "leases",
            createLeaseCollectionIfNotExists = true,
            connectionStringSetting = "AzureCosmosDBConnection") String[] items,
            final ExecutionContext context ) {
                context.getLogger().info(items.length + "item(s) is/are changed.");
            }
```


Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBTrigger` annotatie voor para meters waarvan de waarde afkomstig is van Cosmos db.  Deze aantekening kan worden gebruikt met systeemeigen Java-typen, POJO's of nullbare waarden met `Optional<T>`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een Cosmos DB trigger binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden toegevoegd of gewijzigd.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "type": "cosmosDBTrigger",
    "name": "documents",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Dit is de JavaScript-code:

```javascript
    module.exports = function (context, documents) {
      context.log('First document Id modified : ', documents[0].id);

      context.done();
    }
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voor beeld ziet u hoe u een functie kunt uitvoeren als gegevens wijzigingen in Cosmos DB.

```json
{
  "type": "cosmosDBTrigger",
  "name": "Documents",
  "direction": "in",
  "leaseCollectionName": "leases",
  "connectionStringSetting": "MyStorageConnectionAppSetting",
  "databaseName": "Tasks",
  "collectionName": "Items",
  "createLeaseCollectionIfNotExists": true
}
```

In het _run.ps1_ -bestand hebt u toegang tot het document waarmee de functie wordt geactiveerd via de `$Documents` para meter.

```powershell
param($Documents, $TriggerMetadata) 

Write-Host "First document Id modified : $($Documents[0].id)" 
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een Cosmos DB trigger binding in een *function.jsin* bestand en een [python-functie](functions-reference-python.md) die gebruikmaakt van de binding. De functie schrijft logboek berichten wanneer Cosmos DB records worden gewijzigd.

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "name": "documents",
    "type": "cosmosDBTrigger",
    "direction": "in",
    "leaseCollectionName": "leases",
    "connectionStringSetting": "<connection-app-setting>",
    "databaseName": "Tasks",
    "collectionName": "Items",
    "createLeaseCollectionIfNotExists": true
}
```

Dit is de Python-code:

```python
    import logging
    import azure.functions as func


    def main(documents: func.DocumentList) -> str:
        if documents:
            logging.info('First document Id modified: %s', documents[0]['id'])
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het kenmerk [CosmosDBTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/Trigger/CosmosDBTriggerAttribute.cs) .

De constructor van het kenmerk neemt de naam van de data base en de verzameling. Zie [trigger-configuratie](#configuration)voor meer informatie over deze instellingen en andere eigenschappen die u kunt configureren. Hier volgt een `CosmosDBTrigger` voor beeld van een kenmerk in een methode handtekening:

```csharp
    [FunctionName("DocumentUpdates")]
    public static void Run([CosmosDBTrigger("database", "collection", ConnectionStringSetting = "myCosmosDB")]
        IReadOnlyList<Document> documents,
        ILogger log)
    {
        ...
    }
```

Zie [trigger](#example)voor een volledig voor beeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@CosmosDBInput` annotatie voor para meters die gegevens uit Cosmos DB lezen.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `CosmosDBTrigger`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `cosmosDBTrigger`. |
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Deze para meter wordt automatisch ingesteld wanneer u de trigger maakt in de Azure Portal. |
|**name** | N.v.t. | De naam van de variabele die wordt gebruikt in de functie code die de lijst met documenten met wijzigingen vertegenwoordigt. |
|**connectionStringSetting**|**ConnectionStringSetting** | De naam van een app-instelling die de connection string bevat die wordt gebruikt om verbinding te maken met het Azure Cosmos DB account dat wordt bewaakt. |
|**databaseName**|**DatabaseName**  | De naam van de Azure Cosmos DB-Data Base met de verzameling die wordt bewaakt. |
|**collectionName** |**NaamVerzameling** | De naam van de verzameling die wordt bewaakt. |
|**leaseConnectionStringSetting** | **LeaseConnectionStringSetting** | Beschrijving De naam van een app-instelling die de connection string bevat naar het Azure Cosmos DB-account waarin de lease verzameling zich bevindt. Als deze niet wordt ingesteld, `connectionStringSetting` wordt de waarde gebruikt. Deze para meter wordt automatisch ingesteld wanneer de binding wordt gemaakt in de portal. De connection string voor de leases-verzameling moet schrijf machtigingen hebben.|
|**leaseDatabaseName** |**LeaseDatabaseName** | Beschrijving De naam van de data base die de verzameling bevat die wordt gebruikt voor het opslaan van leases. Als deze niet wordt ingesteld, wordt de waarde van de `databaseName` instelling gebruikt. Deze para meter wordt automatisch ingesteld wanneer de binding wordt gemaakt in de portal. |
|**leaseCollectionName** | **LeaseCollectionName** | Beschrijving De naam van de verzameling die wordt gebruikt voor het opslaan van leases. Als deze niet wordt ingesteld, `leases` wordt de waarde gebruikt. |
|**createLeaseCollectionIfNotExists** | **CreateLeaseCollectionIfNotExists** | Beschrijving Als deze eigenschap `true` is ingesteld op, wordt de leases-verzameling automatisch gemaakt als deze nog niet bestaat. De standaardwaarde is `false`. |
|**leasesCollectionThroughput**| **LeasesCollectionThroughput**| Beschrijving Hiermee wordt het aantal aanvraag eenheden gedefinieerd dat moet worden toegewezen wanneer de leases-verzameling wordt gemaakt. Deze instelling wordt alleen gebruikt wanneer `createLeaseCollectionIfNotExists` is ingesteld op `true` . Deze para meter wordt automatisch ingesteld wanneer de binding wordt gemaakt met behulp van de portal.
|**leaseCollectionPrefix**| **LeaseCollectionPrefix**| Beschrijving Als deze optie is ingesteld, wordt de waarde als voor voegsel toegevoegd aan de leases die zijn gemaakt in de lease verzameling voor deze functie. Met een voor voegsel kunnen twee afzonderlijke Azure Functions dezelfde lease verzameling delen door gebruik te maken van verschillende voor voegsels.
|**feedPollDelay**| **FeedPollDelay**| Beschrijving De tijd (in milliseconden) voor de vertraging tussen het pollen van een partitie voor nieuwe wijzigingen in de feed, nadat alle huidige wijzigingen zijn vertraagd. De standaard waarde is 5.000 milliseconden of 5 seconden.
|**leaseAcquireInterval**| **LeaseAcquireInterval**| Beschrijving Als deze instelling is ingesteld, wordt in milliseconden het interval voor het starten van een taak gedefinieerd om te berekenen of de partities gelijkmatig worden verdeeld over bekende exemplaren van hosts. De standaard waarde is 13000 (13 seconden).
|**leaseExpirationInterval**| **LeaseExpirationInterval**| Beschrijving Wanneer dit is ingesteld, wordt in milliseconden het interval voor het nemen van de lease op een lease die een partitie vertegenwoordigt. Als de lease niet binnen dit interval wordt vernieuwd, wordt het verlopen en het eigendom van de partitie verplaatst naar een ander exemplaar. De standaard waarde is 60000 (60 seconden).
|**leaseRenewInterval**| **LeaseRenewInterval**| Beschrijving Wanneer dit is ingesteld, wordt in milliseconden het Vernieuwings interval voor alle leases gedefinieerd voor partities die momenteel door een instantie worden beheerd. De standaard waarde is 17000 (17 seconden).
|**checkpointFrequency**| **CheckpointFrequency**| Beschrijving Als deze instelling is ingesteld, wordt in milliseconden het interval tussen de controle punten van de lease gedefinieerd. De standaard waarde is altijd na elke functie aanroep.
|**maxItemsPerInvocation**| **MaxItemsPerInvocation**| Beschrijving Als deze eigenschap is ingesteld, wordt het maximum aantal items ingesteld dat per functie aanroep wordt ontvangen. Als bewerkingen in de bewaakte verzameling worden uitgevoerd via opgeslagen procedures, blijft het [transactie bereik](../cosmos-db/stored-procedures-triggers-udfs.md#transactions) behouden bij het lezen van items van de wijzigings feed. Als gevolg hiervan kan het aantal ontvangen items hoger zijn dan de opgegeven waarde, zodat de items die door dezelfde trans actie worden gewijzigd, worden geretourneerd als onderdeel van één atomische batch.
|**startFromBeginning**| **StartFromBeginning**| Beschrijving Met deze optie geeft u aan dat de trigger wijzigingen moet lezen aan het begin van de wijzigings geschiedenis van de verzameling, in plaats van vanaf de huidige tijd. Lezen vanaf het begin werkt alleen de eerste keer dat de trigger wordt gestart, zoals bij volgende uitvoeringen, de controle punten al zijn opgeslagen. Als u deze optie instelt op `true` wanneer er al leases zijn gemaakt, heeft dit geen effect. |
|**preferredLocations**| **PreferredLocations**| Beschrijving Hiermee worden voorkeurs locaties (regio's) gedefinieerd voor accounts met geo-gerepliceerde data bases in de Azure Cosmos DB-service. De waarden moeten worden gescheiden door komma's. Bijvoorbeeld ' vs-Oost, VS Zuid-Centraal, Europa-noord '. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

Voor de trigger is een tweede verzameling vereist die wordt gebruikt voor het opslaan van _leases_ via de partities. Zowel de verzameling die wordt bewaakt als de verzameling die de leases bevat, moet beschikbaar zijn voor de werking van de trigger.

>[!IMPORTANT]
> Als er meerdere functies zijn geconfigureerd voor het gebruik van een Cosmos DB trigger voor dezelfde verzameling, moeten voor elk van de functies een toegewezen lease verzameling worden gebruikt, of moet voor elke functie een andere worden opgegeven `LeaseCollectionPrefix` . Anders wordt slechts een van de functies geactiveerd. Zie de [sectie configuratie](#configuration)voor meer informatie over het voor voegsel.

De trigger geeft niet aan of een document is bijgewerkt of ingevoegd, maar het document zelf wordt geleverd. Als u updates wilt afhandelen en anders wilt invoegen, kunt u dit doen door tijds tempel velden te implementeren voor invoegen of bijwerken.

## <a name="next-steps"></a>Volgende stappen

- [Een Azure Cosmos DB document lezen (invoer binding)](./functions-bindings-cosmosdb-v2-input.md)
- [Wijzigingen opslaan in een Azure Cosmos DB-document (uitvoer binding)](./functions-bindings-cosmosdb-v2-output.md)
