---
title: Invoer binding voor Azure Blob Storage voor Azure Functions
description: Leer hoe u Azure Blob Storage-invoer bindings gegevens kunt leveren aan een Azure-functie.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: cd69e89954fab2256ffc7c23e22d3b8d44ab2a11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455870"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Invoer binding voor Azure Blob Storage voor Azure Functions

Met de invoer binding kunt u Blob Storage-gegevens als invoer naar een Azure-functie lezen.

Zie het [overzicht](./functions-bindings-storage-blob.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

Het volgende voor beeld is een [C#-functie](functions-dotnet-class-library.md) die gebruikmaakt van een wachtrij trigger en een invoer-BLOB-binding. Het wachtrij bericht bevat de naam van de BLOB en de functie registreert de grootte van de blob.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u de BLOB-invoer-en uitvoer bindingen in een *function.jsvoor* de code van het bestand en [C#-script (. CSX)](functions-reference-csharp.md) die gebruikmaakt van de bindingen. De functie maakt een kopie van een tekst-blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

In de *function.jsop* bestand wordt de `queueTrigger` meta gegevens eigenschap gebruikt om de naam van de BLOB op te geven in de `path` Eigenschappen:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de C# Script-code:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

Deze sectie bevat de volgende voor beelden:

* [HTTP-trigger, naam van de BLOB opzoeken in de query reeks](#http-trigger-look-up-blob-name-from-query-string)
* [Wachtrij trigger, naam van de BLOB ontvangen van wachtrij bericht](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-trigger, naam van de BLOB opzoeken in de query reeks

 In het volgende voor beeld ziet u een Java-functie die gebruikmaakt `HttpTrigger` van de aantekening om een para meter te ontvangen met de naam van een bestand in een BLOB storage-container. De `BlobInput` aantekening leest vervolgens het bestand en geeft de inhoud door aan de functie als een `byte[]` .

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Wachtrij trigger, naam van de BLOB ontvangen van wachtrij bericht

 In het volgende voor beeld ziet u een Java-functie die gebruikmaakt `QueueTrigger` van de aantekening om een bericht te ontvangen met daarin de naam van een bestand in een BLOB storage-container. De `BlobInput` aantekening leest vervolgens het bestand en geeft de inhoud door aan de functie als een `byte[]` .

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

Gebruik in de [runtime-bibliotheek van Java functions](/java/api/overview/azure/functions/runtime)de `@BlobInput` annotatie voor para meters waarvan de waarde afkomstig is uit een blob.  Deze aantekening kan worden gebruikt met systeemeigen Java-typen, POJO's of nullbare waarden met `Optional<T>`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u BLOB-invoer-en uitvoer bindingen in eenfunction.jsin bestands-en [Java script-code](functions-reference-node.md) die gebruikmaakt *van* de bindingen. De functie maakt een kopie van een blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

In de *function.jsop* bestand wordt de `queueTrigger` meta gegevens eigenschap gebruikt om de naam van de BLOB op te geven in de `path` Eigenschappen:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

Dit is de JavaScript-code:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voor beeld ziet u een BLOB-invoer binding die is gedefinieerd in de _function.jsop_ bestand, waardoor de binnenkomende BLOB-gegevens beschikbaar zijn voor de [Power shell](functions-reference-powershell.md) -functie.

Hier is de JSON-configuratie:

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Dit is de functie code:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

In het volgende voor beeld ziet u de BLOB-invoer-en uitvoer bindingen in eenfunction.jsin het bestand en de [python-code](functions-reference-python.md) die gebruikmaken *van* de bindingen. De functie maakt een kopie van een blob. De functie wordt geactiveerd door een wachtrij bericht dat de naam bevat van de blob die moet worden gekopieerd. De nieuwe BLOB heet *{originalblobname}-Copy*.

In de *function.jsop* bestand wordt de `queueTrigger` meta gegevens eigenschap gebruikt om de naam van de BLOB op te geven in de `path` Eigenschappen:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

In de [configuratie](#configuration) sectie worden deze eigenschappen uitgelegd.

De `dataType` eigenschap bepaalt welke binding wordt gebruikt. De volgende waarden zijn beschikbaar voor de ondersteuning van verschillende bindings strategieën:

| Bindings waarde | Standaard | Beschrijving | Voorbeeld |
| --- | --- | --- | --- |
| `undefined` | J | Maakt gebruik van uitgebreide bindingen | `def main(input: func.InputStream)` |
| `string` | N | Maakt gebruik van algemene binding en cast het invoer type als een `string` | `def main(input: str)` |
| `binary` | N | Maakt gebruik van generieke binding en cast de invoer BLOB als `bytes` python-object | `def main(input: bytes)` |

Dit is de Python-code:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

De constructor van het kenmerk gebruikt het pad naar de BLOB en een `FileAccess` para meter die aangeeft dat er een lees-of schrijf bewerking wordt uitgevoerd, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

U kunt het- `StorageAccount` kenmerk gebruiken om het opslag account op te geven op klasse, methode of parameter niveau. Zie [trigger-Attributes en annotaties](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)(Engelstalig) voor meer informatie.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

`@BlobInput`Met het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, stelt u `dataType` in op `binary` . Raadpleeg het [invoer voorbeeld](#example) voor meer informatie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `Blob`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `blob`. |
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Uitzonde ringen worden vermeld in de sectie [gebruik](#usage) . |
|**name** | N.v.t. | De naam van de variabele die de BLOB in functie code vertegenwoordigt.|
|**path** |**BlobPath** | Het pad naar de blob. |
|**connection** |**Verbinding**| De naam van een app-instelling die de [opslag Connection String](../storage/common/storage-configure-connection-string.md) bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met de naam `AzureWebJobsStorage` .<br><br>Het connection string moet voor een opslag account voor algemeen gebruik zijn, niet een [opslag account met alleen BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Als u [versie 5. x of hoger van de extensie](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)gebruikt in plaats van een Connection String, kunt u een verwijzing naar een configuratie sectie opgeven waarmee de verbinding wordt gedefinieerd. Zie [verbindingen](./functions-reference.md#connections).|
|**dataType**| n.v.t. | Voor dynamisch getypeerde talen geeft u het onderliggende gegevens type op. Mogelijke waarden zijn `string` , `binary` , of `stream` . Raadpleeg de [concepten voor triggers en bindingen](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions)voor meer details. |
|n.v.t. | **Toegang** | Hiermee wordt aangegeven of u wilt lezen of schrijven. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobInput`Met het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, stelt u `dataType` in op `binary` . Raadpleeg het [invoer voorbeeld](#example) voor meer informatie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Toegang krijgen tot BLOB-gegevens die `context.bindings.<NAME>` `<NAME>` overeenkomen met de waarde die is gedefinieerd in *function.jsop*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Open de BLOB-gegevens via een para meter die overeenkomt met de naam die is opgegeven door de naam parameter van de binding in de _function.jsin_ het bestand.

# <a name="python"></a>[Python](#tab/python)

Toegang tot BLOB-gegevens via de para meter getypeerd als [InputStream](/python/api/azure-functions/azure.functions.inputstream). Raadpleeg het [invoer voorbeeld](#example) voor meer informatie.

---

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer Blob Storage-gegevens worden gewijzigd](./functions-bindings-storage-blob-trigger.md)
- [Blob Storage-gegevens van een functie schrijven](./functions-bindings-storage-blob-output.md)
