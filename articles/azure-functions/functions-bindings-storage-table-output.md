---
title: Uitvoer bindingen voor Azure Table Storage voor Azure Functions
description: Meer informatie over het gebruik van Azure Table Storage-uitvoer bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f793f96f55a258b2d7cb11f214984416557618df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102452999"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Uitvoer bindingen voor Azure Table Storage voor Azure Functions

Gebruik een Azure Table Storage-uitvoer binding om entiteiten te schrijven naar een tabel in een Azure Storage-account.

> [!NOTE]
> Deze uitvoer binding biedt geen ondersteuning voor het bijwerken van bestaande entiteiten. Gebruik de `TableOperation.Replace` bewerking [van de Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) om een bestaande entiteit bij te werken.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die gebruikmaakt van een http-trigger om één tabelrij te schrijven.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een tabel-uitvoer binding in eenfunction.jsin bestands-en [C#-script](functions-reference-csharp.md) code die gebruikmaakt *van* de binding. Met de functie worden meerdere tabel entiteiten geschreven.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C# Script-code:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="java"></a>[Java](#tab/java)

In het volgende voor beeld ziet u een Java-functie die gebruikmaakt van een HTTP-trigger om één tabelrij te schrijven.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

In het volgende voor beeld ziet u een Java-functie die gebruikmaakt van een HTTP-trigger om meerdere tabel rijen te schrijven.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een tabel-uitvoer binding in een *function.jsin* een bestand en een [Java script-functie](functions-reference-node.md) die gebruikmaakt van de binding. Met de functie worden meerdere tabel entiteiten geschreven.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voor beeld ziet u hoe u meerdere entiteiten naar een tabel schrijft vanuit een functie.

Bindings configuratie in _function.jsop_:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Power shell-code in _run.ps1_:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u hoe u de Table Storage-uitvoer binding kunt gebruiken. De `table` binding wordt geconfigureerd in de *function.jsop* door waarden toe te wijzen aan `name` , `tableName` , en `partitionKey` `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

Met de volgende functie wordt een unieke UUI voor de `rowKey` waarde gegenereerd en wordt het bericht in tabel opslag bewaard.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

De constructor van het kenmerk heeft de tabel naam. Het kenmerk kan worden gebruikt voor een `out` para meter of op de retour waarde van de functie, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Zie het [C#-voor beeld](#example)voor een volledig voor beeld.

U kunt het- `StorageAccount` kenmerk gebruiken om het opslag account op te geven op klasse, methode of parameter niveau. Zie [invoer kenmerken](./functions-bindings-storage-table-input.md#attributes-and-annotations)voor meer informatie.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) -aantekening voor para meters voor het schrijven van waarden naar tabel opslag.

Zie het voor [beeld voor meer informatie](#example).

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
|**direction** | N.v.t. | Moet worden ingesteld op `out`. Deze eigenschap wordt automatisch ingesteld wanneer u de binding maakt in de Azure Portal. |
|**name** | N.v.t. | De naam van de variabele die wordt gebruikt in de functie code die de tabel of entiteit vertegenwoordigt. Instellen op `$return` om te verwijzen naar de functie retour waarde.| 
|**tableName** |**TableName** | De naam van de tabel.| 
|**partitionKey** |**PartitionKey** | De partitie sleutel van de tabel entiteit die moet worden geschreven. Zie de [sectie gebruik](#usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**rowKey** |**RowKey** | De rij van de tabel entiteit die moet worden geschreven. Zie de [sectie gebruik](#usage) voor richt lijnen voor het gebruik van deze eigenschap.| 
|**connection** |**Verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' mijn opslag '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met de naam `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

Toegang tot de entiteit uitvoer tabel met behulp van een methode parameter `ICollector<T> paramName` of `IAsyncCollector<T> paramName` waar `T` de en-eigenschappen zijn opgenomen `PartitionKey` `RowKey` . Deze eigenschappen gaan vaak vergezeld van implementatie `ITableEntity` of overname `TableEntity` .

U kunt ook een `CloudTable` methode parameter gebruiken om naar de tabel te schrijven met behulp van de Azure Storage SDK. Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Toegang tot de entiteit uitvoer tabel met behulp van een methode parameter `ICollector<T> paramName` of `IAsyncCollector<T> paramName` waar `T` de en-eigenschappen zijn opgenomen `PartitionKey` `RowKey` . Deze eigenschappen gaan vaak vergezeld van implementatie `ITableEntity` of overname `TableEntity` . De `paramName` waarde wordt opgegeven in de `name` eigenschap van *function.jsop*.

U kunt ook een `CloudTable` methode parameter gebruiken om naar de tabel te schrijven met behulp van de Azure Storage SDK. Als u probeert verbinding te maken met `CloudTable` een fout bericht, moet u ervoor zorgen dat u een verwijzing naar [de juiste versie van de Storage SDK](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)hebt.

# <a name="java"></a>[Java](#tab/java)

Er zijn twee opties voor het uitvoeren van een rij in een tabel opslag van een functie met behulp van de [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput) -aantekening:

- **Retour waarde**: door de aantekening toe te passen op de functie zelf, wordt de geretourneerde waarde van de functie persistent gemaakt als een rij in tabel opslag.

- Verplicht **: als** u de bericht waarde expliciet wilt instellen, past u de aantekening toe op een specifieke para meter van het type [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , waarbij `T` de `PartitionKey` Eigenschappen en zijn opgenomen `RowKey` . Deze eigenschappen gaan vaak vergezeld van implementatie `ITableEntity` of overname `TableEntity` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Open de uitvoergebeurtenis met behulp van `context.bindings.<name>` waarbij `<name>` de waarde is die is opgegeven voor de eigenschap `name` van *function.json*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Als u wilt schrijven naar tabel gegevens, gebruikt u de `Push-OutputBinding` cmdlet, stelt u de `-Name TableBinding` para meter en de `-Value` para meter gelijk in op de rijgegevens. Zie het [Power shell-voor beeld](#example) voor meer informatie.

# <a name="python"></a>[Python](#tab/python)

Er zijn twee opties voor het uitvoeren van een tabelrij bericht in tabel opslag van een functie:

- **Retourwaarde**: stel de eigenschap `name` in *function. json* in op `$return`. Met deze configuratie wordt de retour waarde van de functie persistent gemaakt als tabelrij.

- **Imperatief**: geef een waarde door aan de methode [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) voor de parameter die is gedeclareerd als een type [Out](/python/api/azure-functions/azure.functions.out). De waarde die aan `set` is doorgegeven, wordt persistent gemaakt als een Event Hub-bericht.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding | Referentie |
|---|---|
| Tabel | [Fout codes voor tabellen](/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tabel, wachtrij | [Opslag fout codes](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabel, wachtrij | [Problemen oplossen](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure functions-triggers en-bindingen](functions-triggers-bindings.md)
