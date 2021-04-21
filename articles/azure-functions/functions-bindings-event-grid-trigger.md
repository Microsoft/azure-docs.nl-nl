---
title: Azure Event Grid trigger voor Azure Functions
description: Leer hoe u code kunt uitvoeren Event Grid gebeurtenissen in Azure Functions worden verzonden.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: devx-track-csharp, fasttrack-edit, devx-track-python
ms.openlocfilehash: 17968f2c137eef51eecdb6c7098c7056944dc970
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782185"
---
# <a name="azure-event-grid-trigger-for-azure-functions"></a>Azure Event Grid trigger voor Azure Functions

Gebruik de functietrigger om te reageren op een gebeurtenis die naar een Event Grid verzonden.

Zie het overzicht voor meer informatie over instellingen en [configuratiedetails.](./functions-bindings-event-grid.md)

## <a name="example"></a>Voorbeeld

# <a name="c"></a>[C#](#tab/csharp)

Zie Gebeurtenissen naar een HTTP-eindpunt ontvangen voor een voorbeeld van [een HTTP-trigger.](../event-grid/receive-events.md)

### <a name="c-2x-and-higher"></a>C# (2.x en hoger)

In het volgende voorbeeld ziet u [een C#-functie](functions-dotnet-class-library.md) die wordt binden aan `EventGridEvent` :

```cs
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTest")]
        public static void EventGridTest([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.Data.ToString());
        }
    }
}
```

Zie Pakketten, [Kenmerken,](#attributes-and-annotations)Configuratie [en](#configuration)Gebruik voor meer [informatie.](#usage)

### <a name="version-1x"></a>Versie 1.x

In het volgende voorbeeld ziet u een [C#-functie](functions-dotnet-class-library.md) functions 1.x die wordt binden aan `JObject` :

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using Microsoft.Extensions.Logging;

namespace Company.Function
{
    public static class EventGridTriggerCSharp
    {
        [FunctionName("EventGridTriggerCSharp")]
        public static void Run([EventGridTrigger]JObject eventGridEvent, ILogger log)
        {
            log.LogInformation(eventGridEvent.ToString(Formatting.Indented));
        }
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In het volgende voorbeeld ziet u een triggerbinding in *function.jsbestand en* een [C#-scriptfunctie](functions-reference-csharp.md) die gebruikmaakt van de binding.

Dit zijn de bindingsgegevens in de *function.jsbestand:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

### <a name="version-2x-and-higher"></a>Versie 2.x en hoger

Hier is een voorbeeld dat wordt binden aan `EventGridEvent` :

```csharp
#r "Microsoft.Azure.EventGrid"
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(EventGridEvent eventGridEvent, ILogger log)
{
    log.LogInformation(eventGridEvent.Data.ToString());
}
```

Zie Pakketten, [Kenmerken,](#attributes-and-annotations)Configuratie [en](#configuration)Gebruik voor meer [informatie.](#usage)

### <a name="version-1x"></a>Versie 1.x

Hier is de C#-scriptcode van Functions 1.x die wordt binden aan `JObject` :

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(JObject eventGridEvent, TraceWriter log)
{
    log.Info(eventGridEvent.ToString(Formatting.Indented));
}
```

# <a name="java"></a>[Java](#tab/java)

Deze sectie bevat de volgende voorbeelden:

* [Event Grid trigger, tekenreeksparameter](#event-grid-trigger-string-parameter)
* [Event Grid- en POJO-parameter](#event-grid-trigger-pojo-parameter)

De volgende voorbeelden tonen triggerbinding in [Java](functions-reference-java.md) die gebruikmaken van de binding en een gebeurtenis afdrukken, die de gebeurtenis eerst ontvangt als en `String` tweede als een POJO.

### <a name="event-grid-trigger-string-parameter"></a>Event Grid trigger, tekenreeksparameter

```java
  @FunctionName("eventGridMonitorString")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    String content, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: " + content);      
  }
```

### <a name="event-grid-trigger-pojo-parameter"></a>Event Grid trigger, POJO-parameter

In dit voorbeeld wordt de volgende POJO gebruikt, die de eigenschappen op het hoogste niveau van een Event Grid vertegenwoordigt:

```java
import java.util.Date;
import java.util.Map;

public class EventSchema {

  public String topic;
  public String subject;
  public String eventType;
  public Date eventTime;
  public String id;
  public String dataVersion;
  public String metadataVersion;
  public Map<String, Object> data;

}
```

Bij aankomst wordt de JSON-nettolading van de gebeurtenis gedeser serialiseerd in de ```EventSchema``` POJO voor gebruik door de functie . Met dit proces kan de functie op een objectgeoriënteerde manier toegang krijgen tot de eigenschappen van de gebeurtenis.

```java
  @FunctionName("eventGridMonitor")
  public void logEvent(
    @EventGridTrigger(
      name = "event"
    ) 
    EventSchema event, 
    final ExecutionContext context) {
      context.getLogger().info("Event content: ");
      context.getLogger().info("Subject: " + event.subject);
      context.getLogger().info("Time: " + event.eventTime); // automatically converted to Date by the runtime
      context.getLogger().info("Id: " + event.id);
      context.getLogger().info("Data: " + event.data);
  }
```

Gebruik in [de Java Functions-runtimebibliotheek](/java/api/overview/azure/functions/runtime)de `EventGridTrigger` aantekening voor parameters waarvan de waarde afkomstig zou zijn van EventGrid. Door parameters met deze aantekeningen wordt de functie uitgevoerd wanneer er een gebeurtenis optreedt.  Deze aantekening kan worden gebruikt met systeemeigen Java-typen, POJO's of nullbare waarden met `Optional<T>`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In het volgende voorbeeld ziet u een triggerbinding in *function.jsbestand en* een [JavaScript-functie](functions-reference-node.md) die gebruikmaakt van de binding.

Dit zijn de bindingsgegevens in het *function.jsbestand:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Dit is de JavaScript-code:

```javascript
module.exports = function (context, eventGridEvent) {
    context.log("JavaScript Event Grid function processed a request.");
    context.log("Subject: " + eventGridEvent.subject);
    context.log("Time: " + eventGridEvent.eventTime);
    context.log("Data: " + JSON.stringify(eventGridEvent.data));
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voorbeeld ziet u hoe u een triggerbinding Event Grid configureren in *function.jsbestand.*

```powershell
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "eventGridEvent",
      "direction": "in"
    }
  ]
}
```

De Event Grid gebeurtenis wordt beschikbaar gesteld voor de functie via een parameter met de naam `eventGridEvent` , zoals wordt weergegeven in het volgende PowerShell-voorbeeld.

```powershell
param($eventGridEvent, $TriggerMetadata)

# Make sure to pass hashtables to Out-String so they're logged correctly
$eventGridEvent | Out-String | Write-Host
```

# <a name="python"></a>[Python](#tab/python)

In het volgende voorbeeld ziet u een triggerbinding in *eenfunction.jsbestand* en een [Python-functie](functions-reference-python.md) die gebruikmaakt van de binding.

Dit zijn de bindingsgegevens in het *function.jsbestand:*

```json
{
  "bindings": [
    {
      "type": "eventGridTrigger",
      "name": "event",
      "direction": "in"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

Dit is de Python-code:

```python
import json
import logging

import azure.functions as func

def main(event: func.EventGridEvent):

    result = json.dumps({
        'id': event.id,
        'data': event.get_json(),
        'topic': event.topic,
        'subject': event.subject,
        'event_type': event.event_type,
    })

    logging.info('Python EventGrid trigger processed an event: %s', result)
```

---

## <a name="attributes-and-annotations"></a>Kenmerken en aantekeningen

# <a name="c"></a>[C#](#tab/csharp)

Gebruik [in C#-klassebibliotheken](functions-dotnet-class-library.md)het [kenmerk EventGridTrigger.](https://github.com/Azure/azure-functions-eventgrid-extension/blob/master/src/EventGridExtension/TriggerBinding/EventGridTriggerAttribute.cs)

Hier is een kenmerk `EventGridTrigger` in een methodehandtekening:

```csharp
[FunctionName("EventGridTest")]
public static void EventGridTest([EventGridTrigger] JObject eventGridEvent, ILogger log)
{
    ...
}
```

Zie C#-voorbeeld voor een volledig voorbeeld.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Kenmerken worden niet ondersteund door C# Script.

# <a name="java"></a>[Java](#tab/java)

Met [de EventGridTrigger-aantekening](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/EventGridTrigger.java) kunt u een Event Grid configureren door configuratiewaarden op te geven. Zie de [secties](#example) [voorbeeld en](#configuration) configuratie voor meer informatie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Kenmerken worden niet ondersteund door JavaScript.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kenmerken worden niet ondersteund door PowerShell.

# <a name="python"></a>[Python](#tab/python)

Kenmerken worden niet ondersteund door Python.

---

## <a name="configuration"></a>Configuratie

In de volgende tabel worden de bindingsconfiguratie-eigenschappen uitgelegd die u hebt ingesteld in *function.jsbestand.* Er zijn geen constructorparameters of -eigenschappen om in te stellen in het `EventGridTrigger` kenmerk .

|function.json-eigenschap |Beschrijving|
|---------|---------|
| **type** | Vereist: moet worden ingesteld op `eventGridTrigger` . |
| **direction** | Vereist: moet worden ingesteld op `in` . |
| **name** | Vereist: de naam van de variabele die wordt gebruikt in functiecode voor de parameter die de gebeurtenisgegevens ontvangt. |

## <a name="usage"></a>Gebruik

# <a name="c"></a>[C#](#tab/csharp)

In Azure Functions 1.x kunt u de volgende parametertypen gebruiken voor de Event Grid trigger:

* `JObject`
* `string`

In Azure Functions 2.x en hoger hebt u ook de optie om het volgende parametertype te gebruiken voor de Event Grid trigger:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definieert eigenschappen voor de velden die gemeenschappelijk zijn voor alle gebeurtenistypen.

> [!NOTE]
> Als u in Functions v1 probeert te verbinden met , wordt in de compiler een 'afgeschaft' bericht weergegeven en wordt u geadviseerd om in plaats daarvan `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` te `Microsoft.Azure.EventGrid.Models.EventGridEvent` gebruiken. Als u het nieuwere type wilt gebruiken, verwijst u naar het NuGet-pakket [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) en kwalificeren we de typenaam volledig door deze vooraf te laten `EventGridEvent` gaan door `Microsoft.Azure.EventGrid.Models` .

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

In Azure Functions 1.x kunt u de volgende parametertypen gebruiken voor de Event Grid trigger:

* `JObject`
* `string`

In Azure Functions 2.x en hoger hebt u ook de optie om het volgende parametertype te gebruiken voor de Event Grid trigger:

* `Microsoft.Azure.EventGrid.Models.EventGridEvent`- Definieert eigenschappen voor de velden die gemeenschappelijk zijn voor alle gebeurtenistypen.

> [!NOTE]
> Als u in Functions v1 probeert te verbinden met , wordt in de compiler een 'afgeschaft' bericht weergegeven en wordt u geadviseerd om in plaats daarvan `Microsoft.Azure.WebJobs.Extensions.EventGrid.EventGridEvent` te `Microsoft.Azure.EventGrid.Models.EventGridEvent` gebruiken. Als u het nieuwere type wilt gebruiken, verwijst u naar het NuGet-pakket [Microsoft.Azure.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.EventGrid) en kwalificeren we de typenaam volledig door deze vooraf te laten `EventGridEvent` gaan door `Microsoft.Azure.EventGrid.Models` . Zie Using NuGet packages (NuGet-pakketten gebruiken) voor meer informatie over het verwijzen naar [NuGet-pakketten](functions-reference-csharp.md#using-nuget-packages) in een C#-scriptfunctie

# <a name="java"></a>[Java](#tab/java)

Het Event Grid gebeurtenis-exemplaar is beschikbaar via de parameter die is gekoppeld aan het `EventGridTrigger` kenmerk , getypt als een `EventSchema` . Zie het [voorbeeld](#example) voor meer informatie.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Het Event Grid is beschikbaar via de parameter die is geconfigureerd in defunction.js *van de* eigenschap van het `name` bestand.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Het Event Grid is beschikbaar via de parameter die is geconfigureerd in defunction.js *van de* eigenschap van het `name` bestand.

# <a name="python"></a>[Python](#tab/python)

Het Event Grid is beschikbaar via de parameter die is geconfigureerd in defunction.js *van* de eigenschap van het `name` bestand, getypt als `func.EventGridEvent` .

---

## <a name="event-schema"></a>Gebeurtenisschema

Gegevens voor een Event Grid gebeurtenis worden ontvangen als een JSON-object in de body van een HTTP-aanvraag. De JSON lijkt op het volgende voorbeeld:

```json
[{
  "topic": "/subscriptions/{subscriptionid}/resourceGroups/eg0122/providers/Microsoft.Storage/storageAccounts/egblobstore",
  "subject": "/blobServices/default/containers/{containername}/blobs/blobname.jpg",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2018-01-23T17:02:19.6069787Z",
  "id": "{guid}",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "{guid}",
    "requestId": "{guid}",
    "eTag": "0x8D562831044DDD0",
    "contentType": "application/octet-stream",
    "contentLength": 2248,
    "blobType": "BlockBlob",
    "url": "https://egblobstore.blob.core.windows.net/{containername}/blobname.jpg",
    "sequencer": "000000000000272D000000000003D60F",
    "storageDiagnostics": {
      "batchId": "{guid}"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Het voorbeeld dat wordt weergegeven, is een matrix van één element. Event Grid verzendt altijd een matrix en kan meer dan één gebeurtenis in de matrix verzenden. De runtime roept uw functie eenmaal aan voor elk matrixelement.

De eigenschappen op het hoogste niveau in de JSON-gebeurtenisgegevens zijn hetzelfde voor alle gebeurtenistypen, terwijl de inhoud van de eigenschap `data` specifiek is voor elk gebeurtenistype. Het voorbeeld dat wordt weergegeven, is voor een blobopslaggebeurtenis.

Zie Gebeurteniseigenschappen [in](../event-grid/event-schema.md#event-properties) de documentatie Event Grid uitleg over de algemene en gebeurtenisspecifieke eigenschappen.

Het `EventGridEvent` type definieert alleen de eigenschappen op het hoogste niveau; `Data` de eigenschap is een `JObject` .

## <a name="create-a-subscription"></a>Een abonnement maken

Als u http Event Grid aanvragen wilt ontvangen, maakt u een Event Grid-abonnement dat de eindpunt-URL specificeert die de functie aanroept.

### <a name="azure-portal"></a>Azure Portal

Voor functies die u in de Azure Portal ontwikkelt met de  Event Grid-trigger, selecteert u Integratie, kiest u de **trigger Event Grid en** selecteert u **Event Grid maken.**

:::image type="content" source="media/functions-bindings-event-grid/portal-sub-create.png" alt-text="Een nieuw gebeurtenisabonnement verbinden om te activeren in de portal.":::

Wanneer u deze koppeling selecteert, wordt in de portal de **pagina Gebeurtenisabonnement** maken geopend met het huidige trigger-eindpunt dat al is gedefinieerd.

:::image type="content" source="media/functions-bindings-event-grid/endpoint-url.png" alt-text="Gebeurtenisabonnement maken met een functie-eindpunt dat al is gedefinieerd" :::

Zie Aangepaste gebeurtenis maken [-](../event-grid/custom-event-quickstart-portal.md) Azure Portal in de Event Grid documentatie voor meer informatie over het maken van abonnementen met behulp van de Azure Portal.

### <a name="azure-cli"></a>Azure CLI

Als u een abonnement wilt maken met [behulp van de Azure CLI,](/cli/azure/get-started-with-azure-cli)gebruikt u de opdracht az [eventgrid event-subscription create.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create)

Voor de opdracht is de eindpunt-URL vereist die de functie aanroept. In het volgende voorbeeld ziet u het versiespecifieke URL-patroon:

#### <a name="version-2x-and-higher-runtime"></a>Versie 2.x (en hoger) runtime

```http
https://{functionappname}.azurewebsites.net/runtime/webhooks/eventgrid?functionName={functionname}&code={systemkey}
```

#### <a name="version-1x-runtime"></a>Versie 1.x-runtime

```http
https://{functionappname}.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName={functionname}&code={systemkey}
```

De systeemsleutel is een autorisatiesleutel die moet worden opgenomen in de eindpunt-URL voor een Event Grid trigger. In de volgende sectie wordt uitgelegd hoe u de systeemsleutel op kunt halen.

Hier is een voorbeeld van een abonnement op een blob-opslagaccount (met een tijdelijke aanduiding voor de systeemsleutel):

#### <a name="version-2x-and-higher-runtime"></a>Versie 2.x (en hoger) runtime

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/runtime/webhooks/eventgrid?functionName=imageresizefunc&code=<key>
```

---

#### <a name="version-1x-runtime"></a>Versie 1.x van runtime

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup \
    --provider-namespace Microsoft.Storage --resource-type storageAccounts \
    --resource-name myblobstorage12345 --name myFuncSub \
    --included-event-types Microsoft.Storage.BlobCreated \
    --subject-begins-with /blobServices/default/containers/images/blobs/ \
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventgrid resource event-subscription create -g myResourceGroup ^
    --provider-namespace Microsoft.Storage --resource-type storageAccounts ^
    --resource-name myblobstorage12345 --name myFuncSub ^
    --included-event-types Microsoft.Storage.BlobCreated ^
    --subject-begins-with /blobServices/default/containers/images/blobs/ ^
    --endpoint https://mystoragetriggeredfunction.azurewebsites.net/admin/extensions/EventGridExtensionConfig?functionName=imageresizefunc&code=<key>
```

---

Zie voor meer informatie over het maken van een abonnement de [snelstart](../storage/blobs/storage-blob-event-quickstart.md#subscribe-to-your-storage-account) voor Blob Storage of de andere Event Grid quickstarts.

### <a name="get-the-system-key"></a>De systeemsleutel op halen

U kunt de systeemsleutel op halen met behulp van de volgende API (HTTP GET):

#### <a name="version-2x-and-higher-runtime"></a>Versie 2.x (en hoger) runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgrid_extension?code={masterkey}
```

#### <a name="version-1x-runtime"></a>Versie 1.x van runtime

```
http://{functionappname}.azurewebsites.net/admin/host/systemkeys/eventgridextensionconfig_extension?code={masterkey}
```

Dit is een beheer-API, dus hiervoor is de hoofdsleutel van uw [functie-app vereist.](functions-bindings-http-webhook-trigger.md#authorization-keys) Verwar de systeemsleutel (voor het aanroepen van een Event Grid-triggerfunctie) niet met de hoofdsleutel (voor het uitvoeren van beheertaken in de functie-app). Wanneer u zich abonneert op een Event Grid onderwerp, moet u de systeemsleutel gebruiken.

Hier is een voorbeeld van het antwoord dat de systeemsleutel levert:

```
{
  "name": "eventgridextensionconfig_extension",
  "value": "{the system key for the function}",
  "links": [
    {
      "rel": "self",
      "href": "{the URL for the function, without the system key}"
    }
  ]
}
```

U kunt de hoofdsleutel voor uw functie-app op het tabblad Instellingen van **functie-app** in de portal downloaden.

> [!IMPORTANT]
> De hoofdsleutel biedt beheerderstoegang tot uw functie-app. Deel deze sleutel niet met derden en distribueert deze niet in native clienttoepassingen.

Zie Autorisatiesleutels in [het naslagartikel](functions-bindings-http-webhook-trigger.md#authorization-keys) over HTTP-triggers voor meer informatie.

U kunt ook een HTTP PUT verzenden om de sleutelwaarde zelf op te geven.

## <a name="local-testing-with-viewer-web-app"></a>Lokaal testen met viewer-web-app

Als u een Event Grid wilt testen, moet u http Event Grid aanvragen van hun oorsprong in de cloud laten leveren aan uw lokale computer. Een manier om dat te doen, is door aanvragen online vast te leggen en ze handmatig opnieuw te indienen op uw lokale computer:

1. [Maak een viewer-web-app](#create-a-viewer-web-app) waarmee gebeurtenisberichten worden vast leggen.
1. [Maak een Event Grid waarmee](#create-an-event-grid-subscription) gebeurtenissen naar de viewer-app worden verzendt.
1. [Genereer een aanvraag en](#generate-a-request) kopieer de aanvraag van de viewer-app.
1. [Plaats de aanvraag handmatig op de](#manually-post-the-request) localhost-URL van uw Event Grid triggerfunctie.

Wanneer u klaar bent met testen, kunt u hetzelfde abonnement gebruiken voor productie door het eindpunt bij te werken. Gebruik de [Azure CLI-opdracht az eventgrid event-subscription update.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_update)

### <a name="create-a-viewer-web-app"></a>Een viewer-web-app maken

Om het vastleggen van gebeurtenisberichten te vereenvoudigen, kunt u een [vooraf gebouwde web-app implementeren](https://github.com/Azure-Samples/azure-event-grid-viewer) waarmee de gebeurtenisberichten worden weergegeven. De geïmplementeerde oplossing omvat een App Service-plan, een App Service-web-app en broncode van GitHub.

Selecteer **Implementeren in Azure** om de oplossing voor uw abonnement te implementeren. Geef in Azure Portal waarden op voor de parameters.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

De implementatie kan enkele minuten duren. Controleer of uw web-app wordt uitgevoerd nadat de implementatie is voltooid. Navigeer in een webbrowser naar: `https://<your-site-name>.azurewebsites.net`

De site wordt weergegeven, maar er zijn nog geen gebeurtenissen op gepubliceerd.

![Nieuwe site weergeven](media/functions-bindings-event-grid/view-site.png)

### <a name="create-an-event-grid-subscription"></a>Een Event Grid-abonnement maken

Maak een Event Grid van het type dat u wilt testen en geef deze de URL van uw web-app als eindpunt voor gebeurtenismeldingen. Het eindpunt voor uw web-app moet het achtervoegsel `/api/updates/` bevatten. De volledige URL is dus `https://<your-site-name>.azurewebsites.net/api/updates`

Zie Aangepaste gebeurtenis maken [-](../event-grid/custom-event-quickstart-portal.md) Azure Portal in de Event Grid documentatie voor meer informatie over het maken van abonnementen met behulp van de Azure Portal.

### <a name="generate-a-request"></a>Een aanvraag genereren

Activeer een gebeurtenis die HTTP-verkeer genereert naar het eindpunt van uw web-app.  Als u bijvoorbeeld een blob-opslagabonnement hebt gemaakt, uploadt of verwijdert u een blob. Wanneer een aanvraag wordt weer geven in uw web-app, kopieert u de aanvraag body.

De validatieaanvraag voor het abonnement wordt eerst ontvangen; negeer eventuele validatieaanvragen en kopieer de gebeurtenisaanvraag.

![Aanvraag body kopiëren uit web-app](media/functions-bindings-event-grid/view-results.png)

### <a name="manually-post-the-request"></a>De aanvraag handmatig posten

Voer uw Event Grid functie lokaal uit. De `Content-Type` `aeg-event-type` headers en moeten handmatig worden ingesteld, terwijl en alle andere waarden als standaard kunnen worden overgelaten.

Gebruik een hulpprogramma zoals [Postman](https://www.getpostman.com/) of [curl om](https://curl.haxx.se/docs/httpscripting.html) een HTTP POST-aanvraag te maken:

* Stel een `Content-Type: application/json` header in.
* Stel een `aeg-event-type: Notification` header in.
* Plak de RequestBin-gegevens in de aanvraagbody.
* Plaats op de URL van uw Event Grid triggerfunctie.
  * Gebruik voor 2.x en hoger het volgende patroon:

    ```
    http://localhost:7071/runtime/webhooks/eventgrid?functionName={FUNCTION_NAME}
    ```

  * Gebruik voor 1.x:

    ```
    http://localhost:7071/admin/extensions/EventGridExtensionConfig?functionName={FUNCTION_NAME}
    ```

De `functionName` parameter moet de naam zijn die is opgegeven in het kenmerk `FunctionName` .

In de volgende schermafbeeldingen worden de headers en aanvraagtekst in Postman weergeven:

![Headers in Postman](media/functions-bindings-event-grid/postman2.png)

![Aanvraag body in Postman](media/functions-bindings-event-grid/postman.png)

De Event Grid triggerfunctie wordt uitgevoerd en toont logboeken die vergelijkbaar zijn met het volgende voorbeeld:

![Voorbeeld van Event Grid triggerfunctielogboeken](media/functions-bindings-event-grid/eg-output.png)

## <a name="next-steps"></a>Volgende stappen

* [Een Event Grid verzenden](./functions-bindings-event-grid-output.md)
