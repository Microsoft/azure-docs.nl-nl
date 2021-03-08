---
title: Azure Blob-opslag trigger voor Azure Functions
description: Meer informatie over het uitvoeren van een Azure function als Azure Blob Storage-gegevens wijzigingen.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 1d83a828829d27d85749b3fa7b283cad9683bffc
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102455909"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Azure Blob-opslag trigger voor Azure Functions

Met de trigger voor Blob-opslag wordt een functie gestart wanneer er een nieuwe of bijgewerkte BLOB wordt gedetecteerd. De inhoud van de BLOB wordt opgegeven als [invoer voor de functie](./functions-bindings-storage-blob-input.md).

Voor de Azure Blob Storage-trigger is een opslag account voor algemeen gebruik vereist. Opslag v2-accounts met [hiërarchische naam ruimten](../storage/blobs/data-lake-storage-namespace.md) worden ook ondersteund. Als u een alleen-BLOB-account wilt gebruiken of als uw toepassing gespecialiseerde behoeften heeft, bekijkt u de alternatieven voor het gebruik van deze trigger.

Zie het [overzicht](./functions-bindings-storage-blob.md)voor meer informatie over de installatie-en configuratie details.

## <a name="polling"></a>Navragen

Polling werkt als een hybride tussen het inspecteren van Logboeken en het uitvoeren van periodieke container scans. Blobs worden gescand in groepen van 10.000 tegelijk met een vervolg token dat tussen intervallen wordt gebruikt.

> [!WARNING]
> Daarnaast [worden opslag logboeken gemaakt op basis van ' Best effort '](/rest/api/storageservices/About-Storage-Analytics-Logging) . Er is geen garantie dat alle gebeurtenissen worden vastgelegd. Onder bepaalde omstandigheden kunnen Logboeken worden gemist.
> 
> Als u snellere of meer betrouw bare BLOB-verwerking nodig hebt, kunt u overwegen om een [wachtrij bericht](../storage/queues/storage-dotnet-how-to-use-queues.md) te maken wanneer u de BLOB maakt. Gebruik vervolgens een [wachtrij trigger](functions-bindings-storage-queue.md) in plaats van een BLOB-trigger om de BLOB te verwerken. Een andere optie is het gebruik van Event Grid. Raadpleeg de zelf studie voor het [automatiseren van het formaat van geüploade afbeeldingen met behulp van Event grid](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="alternatives"></a>Alternatieven

### <a name="event-grid-trigger"></a>Event Grid trigger

De [trigger](functions-bindings-event-grid.md) van de Event Grid heeft ook ingebouwde ondersteuning voor [BLOB-gebeurtenissen](../storage/blobs/storage-blob-event-overview.md). Gebruik Event Grid in plaats van de Blob Storage-trigger voor de volgende scenario's:

- **Alleen-Blob Storage-accounts**: [alleen-Blob Storage-accounts](../storage/common/storage-account-overview.md#types-of-storage-accounts) worden ondersteund voor BLOB-invoer-en uitvoer bindingen, maar niet voor BLOB-triggers.

- **Hoge** schaal: High Scale kan soepel worden gedefinieerd als containers met meer dan 100.000 blobs in deze of opslag accounts met meer dan 100 BLOB-updates per seconde.

- **Latentie minimaliseren**: als de functie-app zich in het verbruiks abonnement bevindt, kan er een vertraging van 10 minuten zijn bij het verwerken van nieuwe blobs als een functie-app niet actief is geweest. Om deze latentie te voor komen, kunt u overschakelen naar een App Service plan met Always ingeschakeld. U kunt ook een [Event grid trigger](functions-bindings-event-grid.md) gebruiken met uw Blob Storage-account. Zie de [Event grid zelf studie](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)voor een voor beeld.

Bekijk het [formaat van de afbeelding met Event grid](../event-grid/resize-images-on-storage-blob-upload-event.md) zelf studie van een event grid voor beeld.

### <a name="queue-storage-trigger"></a>Queue Storage-trigger

Een andere manier om blobs te verwerken is het schrijven van wachtrij berichten die overeenkomen met de blobs die worden gemaakt of gewijzigd en vervolgens een [wachtrij opslag trigger](./functions-bindings-storage-queue.md) gebruiken om te beginnen met de verwerking.

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

In het volgende voor beeld ziet u een [C#-functie](functions-dotnet-class-library.md) die een logboek schrijft wanneer een BLOB wordt toegevoegd of bijgewerkt in de `samples-workitems` container.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Met de teken reeks `{name}` in het pad van de BLOB-trigger `samples-workitems/{name}` maakt u een [bindings expressie](./functions-bindings-expressions-patterns.md) die u in functie code kunt gebruiken om toegang te krijgen tot de bestands naam van de activerings-blob. Zie voor meer informatie [BLOB name patronen](#blob-name-patterns) verderop in dit artikel.

`BlobTrigger`Zie [kenmerken en aantekeningen](#attributes-and-annotations)voor meer informatie over het kenmerk.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voor beeld ziet u een binding van een BLOB-trigger in een *function.jsvoor* een bestand en code die gebruikmaakt van de binding. De functie schrijft een logboek wanneer een BLOB wordt toegevoegd of bijgewerkt in de `samples-workitems` [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Hier vindt u de bindings gegevens in de *function.js* in het bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Met de teken reeks `{name}` in het pad van de BLOB-trigger `samples-workitems/{name}` maakt u een [bindings expressie](./functions-bindings-expressions-patterns.md) die u in functie code kunt gebruiken om toegang te krijgen tot de bestands naam van de activerings-blob. Zie voor meer informatie [BLOB name patronen](#blob-name-patterns) verderop in dit artikel.

Zie de sectie [configuratie](#configuration) bevat een uitleg van deze eigenschappen voor meer informatie over *function.jsvan* bestands eigenschappen.

Dit is de C#-script code die wordt gekoppeld aan een `Stream` :

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Dit is de C#-script code die wordt gekoppeld aan een `CloudBlockBlob` :

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="java"></a>[Java](#tab/java)

Deze functie schrijft een logboek wanneer een BLOB wordt toegevoegd of bijgewerkt in de `myblob` container.

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voor beeld ziet u een binding van een BLOB-trigger in een *function.jsin* bestand-en [Java script-code](functions-reference-node.md) die gebruikmaakt van de binding. De functie schrijft een logboek wanneer een BLOB wordt toegevoegd of bijgewerkt in de `samples-workitems` container.

Dit is de *function.jsvoor* het volgende bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Met de teken reeks `{name}` in het pad van de BLOB-trigger `samples-workitems/{name}` maakt u een [bindings expressie](./functions-bindings-expressions-patterns.md) die u in functie code kunt gebruiken om toegang te krijgen tot de bestands naam van de activerings-blob. Zie voor meer informatie [BLOB name patronen](#blob-name-patterns) verderop in dit artikel.

Zie de sectie [configuratie](#configuration) bevat een uitleg van deze eigenschappen voor meer informatie over *function.jsvan* bestands eigenschappen.

Dit is de JavaScript-code:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voor beeld ziet u hoe u een functie maakt die wordt uitgevoerd wanneer een bestand wordt toegevoegd aan de `source` Blob Storage-container.

Het functie configuratie bestand (_function.jsaan_) bevat een binding met `type` van `blobTrigger` en `direction` ingesteld op `in` .

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "MyStorageAccountConnectionString"
    }
  ]
}
```

Hier ziet u de gekoppelde code voor het _run.ps1_ -bestand.

```powershell
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voor beeld ziet u een binding van een BLOB-trigger in een *function.jsin* het bestand en de [python-code](functions-reference-python.md) die gebruikmaakt van de binding. De functie schrijft een logboek wanneer een BLOB wordt toegevoegd of bijgewerkt in de `samples-workitems` [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

Dit is de *function.jsvoor* het volgende bestand:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

Met de teken reeks `{name}` in het pad van de BLOB-trigger `samples-workitems/{name}` maakt u een [bindings expressie](./functions-bindings-expressions-patterns.md) die u in functie code kunt gebruiken om toegang te krijgen tot de bestands naam van de activerings-blob. Zie voor meer informatie [BLOB name patronen](#blob-name-patterns) verderop in dit artikel.

Zie de sectie [configuratie](#configuration) bevat een uitleg van deze eigenschappen voor meer informatie over *function.jsvan* bestands eigenschappen.

Dit is de Python-code:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik in [C#-klassebibliotheek](functions-dotnet-class-library.md)de volgende kenmerken voor het configureren van een BLOB-trigger:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  De constructor van het kenmerk heeft een padtekenreeks die aangeeft dat de container moet worden weer gegeven en optioneel een [BLOB-naam patroon](#blob-name-patterns). Hier volgt een voorbeeld:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  U kunt de `Connection` eigenschap instellen om het opslag account op te geven dat moet worden gebruikt, zoals wordt weer gegeven in het volgende voor beeld:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  Zie [trigger voorbeeld](#example)voor een volledig voor beeld.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Biedt een andere manier om het opslag account op te geven dat moet worden gebruikt. De constructor krijgt de naam van een app-instelling die een opslag connection string bevat. Het kenmerk kan worden toegepast op de para meter, methode of class-niveau. In het volgende voor beeld wordt het niveau klasse en de methode weer gegeven:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

Het opslag account dat moet worden gebruikt, wordt in de volgende volg orde bepaald:

* De `BlobTrigger` eigenschap van het kenmerk `Connection` .
* Het `StorageAccount` kenmerk dat wordt toegepast op dezelfde para meter als het `BlobTrigger` kenmerk.
* Het `StorageAccount` kenmerk dat wordt toegepast op de functie.
* Het `StorageAccount` kenmerk dat wordt toegepast op de klasse.
* Het standaard opslag account voor de functie-app (de app-instelling AzureWebJobsStorage).

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

Het `@BlobTrigger` kenmerk wordt gebruikt om u toegang te geven tot de BLOB waarmee de functie is geactiveerd. Raadpleeg het [voor beeld](#example) van de trigger voor meer informatie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door Power shell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

## <a name="configuration"></a>Configuratie

De volgende tabel bevat informatie over de bindingsconfiguratie-eigenschappen die u instelt in het bestand *function.json* en het kenmerk `BlobTrigger`.

|function.json-eigenschap | Kenmerkeigenschap |Beschrijving|
|---------|---------|----------------------|
|**type** | N.v.t. | Moet worden ingesteld op `blobTrigger`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal.|
|**direction** | N.v.t. | Moet worden ingesteld op `in`. Deze eigenschap wordt automatisch ingesteld wanneer u de trigger maakt in de Azure-portal. Uitzonde ringen worden vermeld in de sectie [gebruik](#usage) . |
|**name** | N.v.t. | De naam van de variabele die de BLOB in functie code vertegenwoordigt. |
|**path** | **BlobPath** |De [container](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) die moet worden bewaakt.  Dit kan een [patroon](#blob-name-patterns)voor een BLOB-naam zijn. |
|**connection** | **Verbinding** | De naam van een app-instelling die de opslag connection string bevat die moet worden gebruikt voor deze binding. Als de naam van de app-instelling begint met ' AzureWebJobs ', kunt u hier alleen de rest van de naam opgeven. Als u bijvoorbeeld instelt `connection` op ' mijn opslag ', zoekt de functie runtime naar een app-instelling met de naam ' AzureWebJobsMyStorage '. Als u `connection` leeg laat, gebruikt de functions runtime de standaard opslag Connection String in de app-instelling met de naam `AzureWebJobsStorage` .<br><br>Het connection string moet voor een opslag account voor algemeen gebruik zijn, geen [Blob Storage-account](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Als u [versie 5. x of hoger van de extensie](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)gebruikt in plaats van een Connection String, kunt u een verwijzing naar een configuratie sectie opgeven waarmee de verbinding wordt gedefinieerd. Zie [verbindingen](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="java"></a>[Java](#tab/java)

Het `@BlobTrigger` kenmerk wordt gebruikt om u toegang te geven tot de BLOB waarmee de functie is geactiveerd. Raadpleeg het [voor beeld](#example) van de trigger voor meer informatie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Toegang krijgen tot BLOB-gegevens die `context.bindings.<NAME>` `<NAME>` overeenkomen met de waarde die is gedefinieerd in *function.jsop*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Open de BLOB-gegevens via een para meter die overeenkomt met de naam die is opgegeven door de naam parameter van de binding in de _function.jsin_ het bestand.

# <a name="python"></a>[Python](#tab/python)

Toegang tot BLOB-gegevens via de para meter getypeerd als [InputStream](/python/api/azure-functions/azure.functions.inputstream). Raadpleeg het [voor beeld](#example) van de trigger voor meer informatie.

---

## <a name="blob-name-patterns"></a>Patronen voor BLOB-naam

U kunt een patroon van een BLOB-naam opgeven in de `path` eigenschap in *function.jsop* of in de `BlobTrigger` kenmerk-constructor. Het naam patroon kan een [filter of een bindings expressie](./functions-bindings-expressions-patterns.md)zijn. De volgende secties bevatten voor beelden.

> [!TIP]
> Een container naam kan geen resolver bevatten in het naam patroon.

### <a name="get-file-name-and-extension"></a>Bestands naam en-extensie ophalen

In het volgende voor beeld ziet u hoe u een afzonderlijke binding maakt met de naam en extensie van het BLOB-bestand:

```json
"path": "input/{blobname}.{blobextension}",
```

Als de BLOB de naam *original-Blob1.txt* heeft, zijn de waarden van de `blobname` `blobextension` variabelen en in functie code *oorspronkelijk-Blob1* en *txt*.

### <a name="filter-on-blob-name"></a>Filteren op blobnaam

Het volgende voor beeld wordt alleen geactiveerd op blobs in de `input` container die beginnen met de teken reeks ' origineel-':

```json
"path": "input/original-{name}",
```

Als de naam van de BLOB *original-Blob1.txt* is, is de waarde van de `name` variabele in functie code `Blob1.txt` .

### <a name="filter-on-file-type"></a>Filteren op bestands type

In het volgende voor beeld worden alleen de *. png* -bestanden geactiveerd:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Filteren op accolades in bestands namen

Als u wilt zoeken naar accolades in bestands namen, plaatst u de accolades met behulp van twee accolades. Het volgende voor beeld wordt gefilterd op blobs met accolades in de naam:

```json
"path": "images/{{20140101}}-{name}",
```

Als de BLOB de naam *{20140101}-soundfile.mp3* heeft, `name` wordt de waarde van de variabele in de functie code *soundfile.mp3*.

## <a name="metadata"></a>Metagegevens

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="java"></a>[Java](#tab/java)

Meta gegevens zijn niet beschikbaar in Java.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Meta gegevens zijn beschikbaar via de `$TriggerMetadata` para meter.

# <a name="python"></a>[Python](#tab/python)

Meta gegevens zijn niet beschikbaar in python.

---

## <a name="blob-receipts"></a>BLOB-ontvangst bewijzen

De Azure Functions runtime zorgt ervoor dat er geen blob-activering meer dan één keer wordt aangeroepen voor dezelfde nieuwe of bijgewerkte blob. Om te bepalen of een bepaalde BLOB-versie is verwerkt, worden de *BLOB-ontvangst bevestigingen* onderhouden.

Azure Functions worden BLOB-ontvangsten opgeslagen in een container met de naam *Azure-webjobs-hosts* in het Azure-opslag account voor uw functie-app (gedefinieerd door de app `AzureWebJobsStorage` -instelling). Een BLOB-ontvangst heeft de volgende informatie:

* De getriggerde functie ( `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>` bijvoorbeeld: `MyFunctionApp.Functions.CopyBlob` )
* De container naam
* Het type BLOB ( `BlockBlob` of `PageBlob` )
* De BLOB-naam
* De ETag (een BLOB-versie-id, bijvoorbeeld: `0x8D1DC6E70A277EF` )

Als u het opnieuw verwerken van een BLOB wilt afdwingen, verwijdert u de BLOB-ontvangst voor die blob van de container *Azure-webjobs-hosts* hand matig. Tijdens het opnieuw verwerken wordt mogelijk niet onmiddellijk uitgevoerd. Dit is gegarandeerd op een later tijdstip. Als u onmiddellijk opnieuw wilt verwerken, kunt u de *ScanInfo* -Blob in *Azure-webjobs-hosts/blobscaninfo* bijwerken. Alle blobs met een tijds tempel dat als laatste is gewijzigd nadat de `LatestScan` eigenschap opnieuw wordt gescand.

## <a name="poison-blobs"></a>Verontreinigde blobs

Wanneer een BLOB-activerings functie mislukt voor een bepaalde blob, worden Azure Functions de nieuwe pogingen standaard een totaal van vijf keer uitgevoerd.

Als alle 5 pogingen mislukken, Azure Functions een bericht aan een opslag wachtrij met de naam *webjobs-sjabloon blobtrigger-Poison* toevoegen. Het maximum aantal nieuwe pogingen kan worden geconfigureerd. Dezelfde MaxDequeueCount-instelling wordt gebruikt voor de verwerking van verontreinigde BLOB-verwerking en verontreinigde wachtrij berichten. Het wachtrij bericht voor verontreinigde blobs is een JSON-object dat de volgende eigenschappen bevat:

* FunctionId (in de indeling `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>` )
* BlobType ( `BlockBlob` of `PageBlob` )
* ContainerName
* BlobName
* ETag (een BLOB-versie-id, bijvoorbeeld: `0x8D1DC6E70A277EF` )

## <a name="concurrency-and-memory-usage"></a>Gelijktijdigheid en geheugen gebruik

De BLOB-trigger maakt intern gebruik van een wachtrij, waardoor het maximum aantal gelijktijdige functie aanroepen wordt bepaald door de [wachtrij configuratie in host.jsop](functions-host-json.md#queues). De standaard instellingen beperken de gelijktijdigheid tot 24 aanroepen. Deze limiet geldt afzonderlijk voor elke functie die gebruikmaakt van een BLOB-trigger.

> [!NOTE]
> Voor apps die gebruikmaken [van de 5.0.0 of hogere versie van de opslag uitbreiding](functions-bindings-storage-blob.md#storage-extension-5x-and-higher), is de configuratie van de wacht rijen in host.jsalleen van toepassing op de wachtrij Triggers. De gelijktijdige BLOB-trigger wordt in plaats daarvan bestuurd door de configuratie van de [blobs in host.jsop](functions-host-json.md#blobs).

[Het verbruiks plan](event-driven-scaling.md) beperkt een functie-app op één virtuele machine (VM) tot 1,5 GB aan geheugen. Het geheugen wordt gebruikt door elk gelijktijdig uitgevoerde functie-exemplaar en door de functions-runtime zelf. Als een door BLOB geactiveerde functie de volledige Blob in het geheugen laadt, is de maximale hoeveelheid geheugen die door de functie wordt gebruikt alleen voor blobs 24 * maximum grootte van BLOB. Een functie-app met drie door BLOB geactiveerde functies en de standaard instellingen hebben bijvoorbeeld een Maxi maal per VM gelijktijdigheid van 3 * 24 = 72 functie aanroepen.

Java script-en Java-functies laden de volledige Blob in het geheugen, en C#-functies doen dat als u verbinding `string` maakt met of `Byte[]` .

## <a name="hostjson-properties"></a>host.json-eigenschappen

De [host.jsin](functions-host-json.md#blobs) het bestand bevat instellingen die het gedrag van BLOB-trigger regelen. Zie de sectie [host.jsop instellingen](functions-bindings-storage-blob.md#hostjson-settings) voor meer informatie over de beschik bare instellingen.

## <a name="next-steps"></a>Volgende stappen

- [Blob Storage-gegevens lezen wanneer een functie wordt uitgevoerd](./functions-bindings-storage-blob-input.md)
- [Blob Storage-gegevens van een functie schrijven](./functions-bindings-storage-blob-output.md)
