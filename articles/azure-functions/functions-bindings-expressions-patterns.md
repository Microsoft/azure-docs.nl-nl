---
title: Azure Functions bindingen expressies en patronen
description: Meer informatie over het maken van verschillende Azure Functions bindings expressies op basis van algemene patronen.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 7245b0c0fb1e96959ef5dca4992cf52a38accb58
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920287"
---
# <a name="azure-functions-binding-expression-patterns"></a>Expressie patronen voor de binding Azure Functions

Een van de krach tigste functies van [Triggers en bindingen](./functions-triggers-bindings.md) is *binding expressies*. In de *function.jsop* bestand en in functie parameters en code kunt u expressies gebruiken die worden omgezet in waarden van verschillende bronnen.

De meeste expressies worden geïdentificeerd door ze tussen accolades te plaatsen. Bijvoorbeeld, in een functie voor wachtrij activering, wordt `{queueTrigger}` omgezet in de tekst van de wachtrij berichten. Als de `path` eigenschap voor een BLOB-uitvoer binding is `container/{queueTrigger}` en de functie wordt geactiveerd door een wachtrij bericht `HelloWorld` , wordt een BLOB gemaakt met de naam `HelloWorld` .

Typen bindingexpressies

* [App-instellingen](#binding-expressions---app-settings)
* [Trigger-bestandsnaam](#trigger-file-name)
* [Trigger-metagegevens](#trigger-metadata)
* [JSON-nettoladingen](#json-payloads)
* [Nieuwe GUID](#create-guids)
* [Huidige datum en tijd](#current-time)

## <a name="binding-expressions---app-settings"></a>Bindings expressies-app-instellingen

Als best practice, moeten geheimen en verbindings reeksen worden beheerd met behulp van app-instellingen in plaats van configuratie bestanden. Zo beperkt u de toegang tot deze geheimen en is het veilig om bestanden zoals *function.jsop* te slaan in opslag plaatsen voor open bare-broncode beheer.

App-instellingen zijn ook handig wanneer u de configuratie wilt wijzigen op basis van de omgeving. In een test omgeving kunt u bijvoorbeeld een andere wachtrij of BLOB storage-container bewaken.

App-instelling bindings expressies worden anders geïdentificeerd dan andere bindings expressies: ze worden ingepakt in procent tekens in plaats van accolades. Als bijvoorbeeld het pad voor de BLOB-uitvoer binding `%Environment%/newblob.txt` en de waarde van de `Environment` app-instelling is `Development` , wordt er een BLOB in de `Development` container gemaakt.

Wanneer een functie lokaal wordt uitgevoerd, zijn de waarden van de app-instellingen afkomstig van de *local.settings.jsin* het bestand.

> [!NOTE]
> De `connection` eigenschap van triggers en bindingen is een speciaal geval en automatisch waarden worden omgezet als app-instellingen, zonder procent tekens. 

Het volgende voor beeld is een Azure Queue Storage-trigger die gebruikmaakt van een app-instelling `%input_queue_name%` voor het definiëren van de wachtrij waarmee wordt geactiveerd.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input_queue_name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

U kunt dezelfde benadering gebruiken in klassen bibliotheken:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input_queue_name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Trigger-bestandsnaam

De `path` voor een BLOB-trigger kan een patroon zijn waarmee u naar de naam van de trigger-Blob in andere bindingen en functie code kunt verwijzen. Het patroon kan ook filter criteria bevatten waarmee wordt aangegeven welke Blobs een functie aanroep kunnen activeren.

In het volgende voor beeld van een BLOB trigger is het `path` patroon bijvoorbeeld `sample-images/{filename}` , waarmee een bindings expressie wordt gemaakt met de naam `filename` :

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

De expressie `filename` kan vervolgens worden gebruikt in een uitvoer binding om de naam op te geven van de blob die wordt gemaakt:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Functie code heeft toegang tot deze waarde door gebruik te maken van de `filename` naam van een para meter:

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

Dezelfde mogelijkheid voor het gebruik van bindings expressies en patronen is van toepassing op kenmerken in klassen bibliotheken. In het volgende voor beeld zijn de para meters van de kenmerk-constructor dezelfde `path` waarden als de voor gaande *function.jsop* voor beelden: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

U kunt ook expressies maken voor delen van de bestands naam. In het volgende voor beeld wordt de functie alleen geactiveerd voor bestands namen die overeenkomen met een patroon: `anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

Zie voor meer informatie over het gebruik van expressies en patronen in de teken reeks BLOB de [verwijzing naar de opslag-BLOB-binding](functions-bindings-storage-blob.md).

## <a name="trigger-metadata"></a>Trigger-metagegevens

Naast de gegevens lading die wordt verstrekt door een trigger (zoals de inhoud van het wachtrij bericht dat een functie heeft geactiveerd), bieden veel triggers aanvullende waarden voor meta gegevens. Deze waarden kunnen worden gebruikt als invoer parameters in C# en F # of op Eigenschappen van het `context.bindings` object in Java script. 

Een Azure Queue-opslag trigger ondersteunt bijvoorbeeld de volgende eigenschappen:

* Queue trigger: de inhoud van het bericht wordt geactiveerd als een geldige teken reeks
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Deze meta gegevens waarden zijn toegankelijk in *function.jsvoor* bestands eigenschappen. Stel bijvoorbeeld dat u een wachtrij trigger gebruikt en dat het wachtrij bericht de naam bevat van een blob die u wilt lezen. In het *function.js* bestand kunt u de `queueTrigger` eigenschap meta gegevens gebruiken in de `path` eigenschap blob, zoals wordt weer gegeven in het volgende voor beeld:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
}
```

Details van eigenschappen van meta gegevens voor elke trigger worden beschreven in het bijbehorende referentie artikel. Zie meta gegevens van de [wachtrij activeren](functions-bindings-storage-queue-trigger.md#message-metadata)voor een voor beeld. Documentatie is ook beschikbaar op het tabblad **integreren** van de portal, in de sectie **documentatie** onder het gedeelte bindings configuratie.  

## <a name="json-payloads"></a>JSON-nettoladingen

Wanneer een trigger Payload JSON is, kunt u naar de eigenschappen in de configuratie verwijzen voor andere bindingen in dezelfde functie en in functie code.

In het volgende voor beeld ziet u de *function.jsop* bestand voor een webhook-functie die een blobnaam in JSON ontvangt: `{"BlobName":"HelloWorld.txt"}` . Een BLOB-invoer binding leest de BLOB en de HTTP-uitvoer binding retourneert de blob-inhoud in het HTTP-antwoord. U ziet dat de BLOB-invoer binding de BLOB-naam krijgt door rechtstreeks naar de eigenschap te verwijzen `BlobName` ( `"path": "strings/{BlobName}"` )

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Als u wilt werken in C# en F #, hebt u een klasse nodig waarmee de velden worden gedefinieerd die moeten worden gedeserialiseerd, zoals in het volgende voor beeld:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

In Java script wordt JSON-deserialisatie automatisch uitgevoerd.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Punt notatie

Als sommige eigenschappen in uw JSON-Payload objecten met eigenschappen zijn, kunt u deze rechtstreeks naar de volgende elementen verwijzen met behulp van de punt notatie. Stel bijvoorbeeld dat uw JSON er als volgt uitziet:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

U kunt rechtstreeks naar `FileName` als verwijzen `BlobName.FileName` . De `path` eigenschap in het vorige voor beeld ziet er als volgt uit:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

In C# hebt u twee klassen nodig:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>GUID'S maken

Met de `{rand-guid}` bindings expressie maakt u een GUID. Met het volgende BLOB-pad in een `function.json` bestand maakt u een blob met een naam als *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Huidige tijd

De bindings expressie wordt `DateTime` omgezet in `DateTime.UtcNow` . Met het volgende BLOB-pad in een `function.json` bestand maakt u een blob met een naam als *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Binding tijdens runtime

In C# en andere .NET-talen kunt u een dwingend bindings patroon gebruiken, in plaats van de declaratieve bindingen in *function.js* en-kenmerken. Dwingende binding is handig wanneer bindings parameters tijdens runtime moeten worden berekend in plaats van ontwerp tijd. Zie de naslag informatie voor [c#-ontwikkel aars](functions-dotnet-class-library.md#binding-at-runtime) of de [Naslag Gids voor ontwikkel aars van c#-scripts](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [De functie retour waarde van Azure gebruiken](./functions-bindings-return-value.md)
