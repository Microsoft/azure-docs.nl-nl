---
title: Azure Blob Storage-uitvoer binding voor Azure Functions
description: Leer hoe u Azure Blob Storage-uitvoer bindings gegevens kunt leveren aan een Azure-functie.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: eaa8a4c600864f636d49813d415621d46130fff7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381649"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Azure Blob Storage-uitvoer binding voor Azure Functions

Met de uitvoer binding kunt u Blob Storage-gegevens wijzigen en verwijderen in een Azure-functie.

Zie het [overzicht](./functions-bindings-storage-blob.md)voor meer informatie over de installatie-en configuratie details.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

Het volgende voor beeld is een [C#-functie](functions-dotnet-class-library.md) die gebruikmaakt van een BLOB-trigger en twee uitvoer BLOB-bindingen. De functie wordt geactiveerd door het maken van een afbeeldings-Blob in de container voor *beeld-installatie kopieën* . Er worden kleine en middel grote kopieën van de afbeeldings-BLOB gemaakt.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

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

* [HTTP-trigger, met behulp van OutputBinding](#http-trigger-using-outputbinding-java)
* [Wachtrij trigger, met behulp van functie retour waarde](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-trigger, met behulp van OutputBinding (Java)

 In het volgende voor beeld ziet u een Java-functie die gebruikmaakt `HttpTrigger` van de aantekening om een para meter te ontvangen met de naam van een bestand in een BLOB storage-container. De `BlobInput` aantekening leest vervolgens het bestand en geeft de inhoud door aan de functie als een `byte[]` . De `BlobOutput` annotatie koppelt aan `OutputBinding outputItem` , die vervolgens door de functie wordt gebruikt om de inhoud van de invoer-BLOB naar de geconfigureerde opslag container te schrijven.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Wachtrij trigger, met behulp van de functie retour waarde (Java)

 In het volgende voor beeld ziet u een Java-functie die gebruikmaakt `QueueTrigger` van de aantekening om een bericht te ontvangen met daarin de naam van een bestand in een BLOB storage-container. De `BlobInput` aantekening leest vervolgens het bestand en geeft de inhoud door aan de functie als een `byte[]` . De `BlobOutput` aantekening koppelt aan de functie retour waarde, die vervolgens door de runtime wordt gebruikt om de inhoud van de invoer-BLOB naar de geconfigureerde opslag container te schrijven.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 Gebruik in de [runtime-bibliotheek van Java-functies](/java/api/overview/azure/functions/runtime)de `@BlobOutput` annotatie voor functie parameters waarvan de waarde zou worden geschreven naar een object in Blob Storage.  Het parameter type moet zijn `OutputBinding<T>` , waarbij T een systeem eigen Java-type is of een POJO.

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

In het volgende voor beeld ziet u hoe u een kopie van een binnenkomende BLOB maakt als uitvoer van een [Power shell-functie](functions-reference-powershell.md).

In het configuratie bestand van de functie (*function.jsop*), `trigger` wordt de eigenschap meta gegevens gebruikt om de naam van de uitvoer-Blob op te geven in de `path` Eigenschappen.

> [!NOTE]
> Zorg ervoor dat de invoer-en uitvoer paden verschillen om te voor komen dat er oneindige lussen zijn.

```json
{
  "bindings": [
    {
      "name": "myInputBlob",
      "path": "data/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in",
      "type": "blobTrigger"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "data/copy/{trigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Dit is de Power shell-code:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $myInputBlob, $TriggerMetadata)
Write-Host "PowerShell Blob trigger function Processed blob Name: $($TriggerMetadata.Name)"
Push-OutputBinding -Name myOutputBlob -Value $myInputBlob
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
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
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

Dit is de Python-code:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C# class libraries](functions-dotnet-class-library.md)het [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

De constructor van het kenmerk gebruikt het pad naar de BLOB en een `FileAccess` para meter die aangeeft dat er een lees-of schrijf bewerking wordt uitgevoerd, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput`Met het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, stelt u `dataType` in op `binary` . Raadpleeg het [uitvoer voorbeeld](#example) voor meer informatie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

Zie [uitvoer voorbeeld](#example)voor een volledig voor beeld.

U kunt het- `StorageAccount` kenmerk gebruiken om het opslag account op te geven op klasse, methode of parameter niveau. Zie [trigger-Attributes](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)(Engelstalig) voor meer informatie.

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `Blob`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `blob`. |
|**direction** | N.v.t. | Moet worden ingesteld op `out` voor een uitvoer binding. Uitzonde ringen worden vermeld in de sectie [gebruik](#usage) . |
|**name** | N.v.t. | De naam van de variabele die de BLOB in functie code vertegenwoordigt.  Instellen op `$return` om te verwijzen naar de functie retour waarde.|
|**path** |**BlobPath** | Het pad naar de BLOB-container. |
|**connection** |**Verbinding**| De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met de naam `AzureWebJobsStorage` .<br><br>Het connection string moet voor een opslag account voor algemeen gebruik zijn, niet een [opslag account met alleen BLOB](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Als u [versie 5. x of hoger van de extensie](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)gebruikt in plaats van een Connection String, kunt u een verwijzing naar een configuratie sectie opgeven waarmee de verbinding wordt gedefinieerd. Zie [verbindingen](./functions-reference.md#connections).|
|n.v.t. | **Toegang** | Hiermee wordt aangegeven of u wilt lezen of schrijven. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="java"></a>[Java](#tab/java)

`@BlobOutput`Met het kenmerk krijgt u toegang tot de BLOB waarmee de functie is geactiveerd. Als u een byte matrix met het-kenmerk gebruikt, stelt u `dataType` in op `binary` . Raadpleeg het [uitvoer voorbeeld](#example) voor meer informatie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Open de BLOB-gegevens met `context.bindings.<BINDING_NAME>` , waarbij de naam van de binding is gedefinieerd in de _function.jsin_ het bestand.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Open de BLOB-gegevens via een para meter die overeenkomt met de naam die is opgegeven door de naam parameter van de binding in de _function.jsin_ het bestand.

# <a name="python"></a>[Python](#tab/python)

U kunt functie parameters declareren als de volgende typen om naar Blob Storage te schrijven:

* Teken reeksen als `func.Out(str)`
* Stromen als `func.Out(func.InputStream)`

Raadpleeg het [uitvoer voorbeeld](#example) voor meer informatie.

---

## <a name="exceptions-and-return-codes"></a>Uitzonderingen en retourcodes

| Binding |  Referentie |
|---|---|
| Blob | [BLOB-fout codes](/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tabel, wachtrij |  [Opslag fout codes](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tabel, wachtrij |  [Problemen oplossen](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer Blob Storage-gegevens worden gewijzigd](./functions-bindings-storage-blob-trigger.md)
- [Blob Storage-gegevens lezen wanneer een functie wordt uitgevoerd](./functions-bindings-storage-blob-input.md)
