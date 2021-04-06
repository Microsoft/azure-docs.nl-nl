---
title: Invoer bindingen voor Azure Table Storage voor Azure Functions
description: Meer informatie over het gebruik van invoer bindingen in azure Table Storage in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4fc2426189384856d2d2e95887cdabd2f9e9ebea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98033775"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Invoer bindingen voor Azure Table Storage voor Azure Functions

Gebruik de Azure Table Storage-invoer binding om een tabel in een Azure Storage-account te lezen.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Eén entiteit

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee één tabelrij wordt gelezen. Voor elk bericht dat naar de wachtrij wordt verzonden, wordt de functie geactiveerd.

De waarde {Queue trigger} van de rij geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="cloudtable"></a>CloudTable

`CloudTable` wordt alleen ondersteund in de [functies v2 en en hogere Runtimes](functions-versions.md).

Gebruik een `CloudTable` methode parameter om de tabel te lezen met behulp van de Azure Storage SDK. Hier volgt een voor beeld van een functie die een Azure Functions-logboek tabel opvraagt:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

Zie [aan de slag met Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md)voor meer informatie over het gebruik van CloudTable.

Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)hebt.

### <a name="iqueryable"></a>IQueryable

`IQueryable` wordt alleen ondersteund in de [functions v1-runtime](functions-versions.md).

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) waarmee meerdere tabel rijen worden gelezen waarvan de `MyPoco` klasse is afgeleid `TableEntity` .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

### <a name="one-entity"></a>Eén entiteit

In het volgende voor beeld wordt een tabel-invoer binding weer gegeven in een *function.jsvoor* de bestands-en [C#-script](functions-reference-csharp.md) code die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij trigger om één tabelrij te lezen. 

Het *function.js* bestand bevat een `partitionKey` en een `rowKey` . De `rowKey` waarde {Queue trigger} geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C# Script-code:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` wordt niet ondersteund in de functions-runtime voor [versie 2. x en hoger)](functions-versions.md). U kunt ook een `CloudTable` methode parameter gebruiken om de tabel te lezen met behulp van de Azure Storage SDK. Hier volgt een voor beeld van een functie die een Azure Functions-logboek tabel opvraagt:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

Zie [aan de slag met Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md)voor meer informatie over het gebruik van CloudTable.

Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)hebt.

### <a name="iqueryable"></a>IQueryable

In het volgende voor beeld wordt een tabel-invoer binding weer gegeven in een *function.jsvoor* de bestands-en [C#-script](functions-reference-csharp.md) code die gebruikmaakt van de binding. De functie leest entiteiten voor een partitie sleutel die is opgegeven in een wachtrij bericht.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Met de C#-script code wordt een verwijzing toegevoegd aan de Azure Storage SDK, zodat het entiteits type kan worden afgeleid van `TableEntity` :

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een HTTP-geactiveerde functie die een lijst retourneert met objecten van de persoon die zich in een opgegeven partitie in tabel opslag bevinden. In het voor beeld wordt de partitie sleutel geëxtraheerd uit de http-route en de tabel naam en de verbinding zijn afkomstig uit de functie-instellingen. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

De TableInput-aantekening kan ook de bindingen ophalen uit de JSON-hoofd tekst van de aanvraag, zoals in het volgende voor beeld wordt getoond.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

In het volgende voor beeld wordt het filter gebruikt om een query uit te zoeken voor personen met een specifieke naam in een Azure-tabel en wordt het aantal mogelijke overeenkomsten beperkt tot 10 resultaten.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld wordt een tabel-invoer binding weer gegeven in een *function.jsvoor* de bestands-en [Java script-code](functions-reference-node.md) die gebruikmaakt van de binding. De functie maakt gebruik van een wachtrij trigger om één tabelrij te lezen. 

Het *function.js* bestand bevat een `partitionKey` en een `rowKey` . De `rowKey` waarde {Queue trigger} geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

De volgende functie maakt gebruik van een wachtrij trigger om één tabelrij als invoer naar een functie te lezen.

In dit voor beeld geeft de binding configuratie een expliciete waarde voor de tabel `partitionKey` en maakt gebruik van een expressie om door te geven aan de `rowKey` . De `rowKey` expressie, `{queueTrigger}` geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

Bindings configuratie in _function.jsop_:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "MyQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "PersonEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Power shell-code in _run.ps1_:

```powershell
param($MyQueueItem, $PersonEntity, $TriggerMetadata)
Write-Host "PowerShell queue trigger function processed work item: $MyQueueItem"
Write-Host "Person entity name: $($PersonEntity.Name)"
```

# <a name="python"></a>[Python](#tab/python)

De volgende functie maakt gebruik van een wachtrij trigger om één tabelrij als invoer naar een functie te lezen.

In dit voor beeld geeft de binding configuratie een expliciete waarde voor de tabel `partitionKey` en maakt gebruik van een expressie om door te geven aan de `rowKey` . De `rowKey` expressie `{id}` geeft aan dat de rij-sleutel afkomstig is uit de wachtrij bericht teken reeks.

Bindings configuratie in het _function.js_ bestand:

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

Python-code in het bestand *\_ \_ init \_ \_ . py* :

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

 Gebruik in [C# class libraries](functions-dotnet-class-library.md)de volgende kenmerken om een tabel-invoer binding te configureren:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  De constructor van het kenmerk heeft de tabel naam, de partitie sleutel en de rij-sleutel. Het kenmerk kan worden gebruikt voor een `out` para meter of op de retour waarde van de functie, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Zie het C#-voor beeld voor een volledig voor beeld.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het opslag account op te geven dat moet worden gebruikt. De constructor krijgt de naam van een app-instelling die een opslag connection string bevat. Het kenmerk kan worden toegepast op de para meter, methode of class-niveau. In het volgende voor beeld wordt het niveau klasse en de methode weer gegeven:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

Het opslag account dat moet worden gebruikt, wordt in de volgende volg orde bepaald:

* De `Table` eigenschap van het kenmerk `Connection` .
* Het `StorageAccount` kenmerk dat wordt toegepast op dezelfde para meter als het `Table` kenmerk.
* Het `StorageAccount` kenmerk dat wordt toegepast op de functie.
* Het `StorageAccount` kenmerk dat wordt toegepast op de klasse.
* Het standaard opslag account voor de functie-app (de app-instelling AzureWebJobsStorage).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@TableInput` aantekening voor para meters waarvan de waarde afkomstig is uit de tabel opslag.  Deze aantekening kan worden gebruikt met systeemeigen Java-typen, POJO's of nullbare waarden met `Optional<T>`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `Table`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `table`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**name** | N.v.t. | De naam van de variabele die de tabel of entiteit in functie code vertegenwoordigt. | 
|**tableName** | **TableName** | De naam van de tabel.| 
|**partitionKey** | **PartitionKey** |Optioneel. De partitie sleutel van de tabel entiteit die moet worden gelezen. Zie de sectie [gebruik](#usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | Optioneel. De rij van de tabel entiteit die moet worden gelezen. Zie de sectie [gebruik](#usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**Houd** |**Houd** | Optioneel. Het maximum aantal entiteiten dat in Java script kan worden gelezen. Zie de sectie [gebruik](#usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**Filterwebonderdelen** |**Filter** | Optioneel. Een OData-filter expressie voor tabel invoer in Java script. Zie de sectie [gebruik](#usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**connection** |**Verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. De instelling kan de naam zijn van een vooringestelde app-instelling voor ' AzureWebJobs ' of connection string naam. Als de naam van de instelling bijvoorbeeld ' AzureWebJobsMyStorage ' is, kunt u hier ' mijn opslag ' opgeven. De functions-runtime zoekt automatisch naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met de naam `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

* **Eén rij in**

  Instellen `partitionKey` en `rowKey` . Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `T <paramName>` . In C#-script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *function.json*. `T` is doorgaans een type dat van wordt geïmplementeerd `ITableEntity` of afgeleid `TableEntity` . De `filter` `take` Eigenschappen en worden niet gebruikt in dit scenario.

* **Een of meer rijen lezen**

  Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `IQueryable<T> <paramName>` . In C#-script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *function.json*. `T` moet een type zijn dat van wordt geïmplementeerd `ITableEntity` of afgeleid `TableEntity` . U kunt `IQueryable` methoden gebruiken om alle benodigde filters uit te voeren. De `partitionKey` `rowKey` Eigenschappen,, `filter` en `take` worden niet gebruikt in dit scenario.  

  > [!NOTE]
  > `IQueryable` wordt niet ondersteund in de [runtime van functions v2](functions-versions.md). U kunt ook de [methode para meter CloudTable param gebruiken](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) om de tabel te lezen met behulp van de SDK van Azure Storage. Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

* **Eén rij in**

  Instellen `partitionKey` en `rowKey` . Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `T <paramName>` . In C#-script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *function.json*. `T` is doorgaans een type dat van wordt geïmplementeerd `ITableEntity` of afgeleid `TableEntity` . De `filter` `take` Eigenschappen en worden niet gebruikt in dit scenario.

* **Een of meer rijen lezen**

  Toegang krijgen tot de tabel gegevens met behulp van een methode parameter `IQueryable<T> <paramName>` . In C#-script is `paramName` de waarde die is opgegeven in de eigenschap `name` van *function.json*. `T` moet een type zijn dat van wordt geïmplementeerd `ITableEntity` of afgeleid `TableEntity` . U kunt `IQueryable` methoden gebruiken om alle benodigde filters uit te voeren. De `partitionKey` `rowKey` Eigenschappen,, `filter` en `take` worden niet gebruikt in dit scenario.  

  > [!NOTE]
  > `IQueryable` wordt niet ondersteund in de [runtime van functions v2](functions-versions.md). U kunt ook de [methode para meter CloudTable param gebruiken](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) om de tabel te lezen met behulp van de SDK van Azure Storage. Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="java"></a>[Java](#tab/java)

Met het kenmerk [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) krijgt u toegang tot de tabelrij waarin de functie is geactiveerd.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Stel de `filter` Eigenschappen en in `take` . Niet instellen `partitionKey` of `rowKey` . Open de entiteit (of entiteiten) van de invoer tabel met behulp van `context.bindings.<BINDING_NAME>` . De gedeserialiseerd objecten hebben `RowKey` en `PartitionKey` Eigenschappen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Gegevens worden door gegeven aan de invoer parameter zoals opgegeven door de `name` sleutel in de *function.jsvoor* het bestand. Opgeven `partitionKey` en `rowKey` Hiermee kunt u filteren op specifieke records. Zie het [Power shell-voor beeld](#example) voor meer informatie.

# <a name="python"></a>[Python](#tab/python)

Tabel gegevens worden door gegeven aan de functie als een JSON-teken reeks. Deserialiseren van het bericht door aan te roepen `json.loads` zoals wordt weer gegeven in het invoer [voorbeeld](#example).

---

## <a name="next-steps"></a>Volgende stappen

* [Table Storage-gegevens schrijven vanuit een functie](./functions-bindings-storage-table-output.md)
