---
title: Diagnostische gegevens in Durable Functions-Azure
description: Meer informatie over het vaststellen van problemen met de Durable Functions extensie voor Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: 62cc5e1762a2a54b26cbebae5aa7cfbf64204ba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584615"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostische gegevens in Durable Functions in Azure

Er zijn verschillende opties voor het vaststellen van problemen met [Durable functions](durable-functions-overview.md). Sommige van deze opties zijn hetzelfde voor gewone functies en sommige zijn uniek voor Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) is de aanbevolen methode voor het uitvoeren van diagnostische gegevens en bewaking in azure functions. Hetzelfde geldt voor Durable Functions. Zie [Azure functions bewaken](../functions-monitoring.md)voor een overzicht van het gebruik van Application Insights in uw functie-app.

De Azure Functions duurzame extensie levert ook *gebeurtenissen* op waarmee u de end-to-end-uitvoering van een indeling kunt volgen. Deze tracerings gebeurtenissen kunnen worden gevonden en opgevraagd met behulp van het hulp programma [Application Insights Analytics](../../azure-monitor/logs/log-query-overview.md) in de Azure Portal.

### <a name="tracking-data"></a>Gegevens bijhouden

Elke levens cyclus gebeurtenis van een indelings instantie zorgt ervoor dat een tracerings gebeurtenis wordt geschreven naar de verzameling **traces** in Application Insights. Deze gebeurtenis bevat een **customDimensions** -nettolading met verschillende velden.  Veld namen zijn allemaal voor `prop__` .

* **hubName**: de naam van de taak hub waarin uw Orchestrations worden uitgevoerd.
* **AppName**: de naam van de functie-app. Dit veld is handig wanneer u meerdere functie-apps hebt die hetzelfde Application Insights exemplaar delen.
* naam **sleuf**: de [implementatie site](../functions-deployment-slots.md) waarin de huidige functie-app wordt uitgevoerd. Dit veld is handig wanneer u implementatie sleuven gebruikt voor het versie nummer van uw Orchestrations.
* Rolnaam: de naam van **de functie Orchestrator** of de activiteit.
* **functionType**: het type functie, zoals **Orchestrator** of **Activity**.
* **instanceId**: de unieke id van het Orchestration-exemplaar.
* **status**: de uitvoerings status van de levens cyclus van het exemplaar. Geldige waarden zijn:
  * **Gepland**: de functie is gepland om te worden uitgevoerd, maar is nog niet gestart.
  * **Gestart**: de functie is gestart, maar is nog niet gewacht of voltooid.
  * In **afwachting** van: de Orchestrator heeft een aantal werk gepland en wacht tot deze is voltooid.
  * **Luis teren**: de Orchestrator luistert naar een externe gebeurtenis melding.
  * **Voltooid**: de functie is voltooid.
  * **Mislukt**: de functie is mislukt met een fout.
* **reden**: aanvullende gegevens die zijn gekoppeld aan de tracerings gebeurtenis. Als een instantie bijvoorbeeld wacht op een externe gebeurtenis melding, wordt in dit veld de naam van de gebeurtenis aangegeven waarin wordt gewacht. Als een functie is mislukt, bevat dit veld de fout Details.
* **isReplay**: een Booleaanse waarde waarmee wordt aangegeven of de tracerings gebeurtenis is ingesteld op opnieuw gespelde uitvoering.
* **extensionVersion**: de versie van de duurzame taak uitbreiding. De versie gegevens zijn met name belang rijk bij het rapporteren van mogelijke fouten in de uitbrei ding. Langlopende instanties rapporteren mogelijk meerdere versies als er een update wordt uitgevoerd terwijl deze actief is.
* **sequenceNumber**: Volg nummer voor uitvoering voor een gebeurtenis. In combi natie met de tijds tempel kunnen de gebeurtenissen worden gesorteerd op uitvoerings tijd. *Houd er rekening mee dat dit aantal wordt ingesteld op nul als de host opnieuw wordt opgestart terwijl het exemplaar wordt uitgevoerd. het is dus belang rijk om altijd eerst op tijds tempel te sorteren en vervolgens op sequenceNumber.*

De uitgebreide tracerings gegevens die naar Application Insights worden verzonden, kunnen worden geconfigureerd in de `logger` sectie (functions 1. x) of `logging` (functions 2,0) van het `host.json` bestand.

#### <a name="functions-10"></a>Functies 1,0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Functies 2,0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Standaard worden alle gebeurtenissen voor het bijhouden van niet-opnieuw afspelen verzonden. Het gegevens volume kan worden verkleind door in `Host.Triggers.DurableTask` te stellen `"Warning"` of in het `"Error"` geval dat het volgen van gebeurtenissen alleen wordt verzonden voor uitzonderlijke situaties.

Als u de uitgebreide Orchestration replay-gebeurtenissen wilt laten verzenden, `LogReplayEvents` kunt u deze instellen `true` in het `host.json` bestand onder `durableTask` zoals hieronder wordt weer gegeven:

#### <a name="functions-10"></a>Functies 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Functies 2,0

```json
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Application Insights telemetrie wordt standaard door de Azure Functions-runtime bemonsterd om te voor komen dat gegevens te vaak worden verzonden. Dit kan ervoor zorgen dat de tracerings informatie verloren gaat wanneer er veel levenscyclus gebeurtenissen in korte tijd optreden. In het [artikel over Azure functions bewaking](../configure-monitoring.md#configure-sampling) wordt uitgelegd hoe u dit gedrag kunt configureren.

### <a name="single-instance-query"></a>Query met één exemplaar

Met de volgende query worden historische tracking gegevens weer gegeven voor één exemplaar van de functie Orchestration van de [Hello-reeks](durable-functions-sequence.md) . Het is geschreven met behulp van de [Kusto-query taal](/azure/data-explorer/kusto/query/). Hiermee wordt de uitvoering van herhalingen gefilterd, zodat alleen het *logische* uitvoerings traject wordt weer gegeven. Gebeurtenissen kunnen worden gerangschikt door te sorteren op `timestamp` en `sequenceNumber` zoals wordt weer gegeven in de onderstaande query:

```kusto
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Het resultaat is een lijst met tracerings gebeurtenissen waarmee het pad van de uitvoering van de indeling wordt weer gegeven, inclusief de activiteit functies die worden besteld door de uitvoerings tijd in oplopende volg orde.

![Application Insights geordende query voor één exemplaar](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Query voor exemplaar samenvatting

Met de volgende query wordt de status weer gegeven van alle indelings instanties die zijn uitgevoerd in een opgegeven tijds bereik.

```kusto
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Het resultaat is een lijst met exemplaar-Id's en de huidige runtime status.

![Application Insights query met één exemplaar](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="durable-task-framework-logging"></a>Duurzame logboek registratie van taak raamwerk

De duurzame extensie logboeken zijn handig voor het bepalen van het gedrag van uw Orchestration Logic. Deze logboeken bevatten echter niet altijd voldoende informatie om problemen met de prestaties van het Framework-niveau en de betrouw baarheid op te sporen. Vanaf **v 2.3.0** van de duurzame uitbrei ding zijn de logboeken die worden gegenereerd door het onderliggende duurzame taak raamwerk (DTFx) ook beschikbaar voor de verzameling.

Bij het bekijken van de logboeken die worden gegenereerd door de DTFx, is het belang rijk te weten dat de DTFx-engine bestaat uit twee onderdelen: de kern Dispatch Engine ( `DurableTask.Core` ) en een van de vele ondersteunde opslag providers (Durable functions `DurableTask.AzureStorage` standaard gebruikt).

* **DurableTask. core**: bevat informatie over de uitvoering van Orchestration en het plannen op laag niveau.
* **DurableTask. opslag**: bevat informatie met betrekking tot interacties met Azure Storage artefacten, waaronder de interne wacht rijen, blobs en opslag tabellen die worden gebruikt voor het opslaan en ophalen van de interne Orchestrator-status.

U kunt deze logboeken inschakelen door de `logging/logLevel` sectie van dehost.jsvan uw functie-app **bij** te werken in het bestand. In het volgende voor beeld ziet u hoe u waarschuwings-en fout logboeken inschakelt van zowel `DurableTask.Core` en `DurableTask.AzureStorage` :

```json
{
  "version": "2.0",
  "logging": {
    "logLevel": {
      "DurableTask.AzureStorage": "Warning",
      "DurableTask.Core": "Warning"
    }
  }
}
```

Als u Application Insights hebt ingeschakeld, worden deze logboeken automatisch toegevoegd aan de `trace` verzameling. U kunt deze op dezelfde manier doorzoeken als andere `trace` Logboeken met behulp van Kusto-query's.

> [!NOTE]
> Voor productie toepassingen is het raadzaam om `DurableTask.Core` het filter in te scha kelen en `DurableTask.AzureStorage` Logboeken te gebruiken `"Warning"` . Hogere uitgebreide filters, zoals `"Information"` is nuttig voor het oplossen van problemen met prestaties. Deze logboek gebeurtenissen zijn echter zeer groot en kunnen de kosten voor de Application Insights gegevens opslag aanzienlijk verhogen.

De volgende Kusto-query laat zien hoe u een query kunt uitvoeren voor DTFx-Logboeken. Het belangrijkste deel van de query is `where customerDimensions.Category startswith "DurableTask"` omdat de resultaten worden gefilterd op Logboeken in de `DurableTask.Core` en- `DurableTask.AzureStorage` Categorieën.

```kusto
traces
| where customDimensions.Category startswith "DurableTask"
| project
    timestamp,
    severityLevel,
    Category = customDimensions.Category,
    EventId = customDimensions.EventId,
    message,
    customDimensions
| order by timestamp asc 
```
Het resultaat is een set logboeken die zijn geschreven door de duurzame logboek providers van het taak raamwerk.

![DTFx-query resultaten Application Insights](./media/durable-functions-diagnostics/app-insights-dtfx.png)

Voor meer informatie over welke logboek gebeurtenissen beschikbaar zijn, raadpleegt u de [documentatie over de gestructureerde logboek registratie van het duurzame taak raamwerk op github](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Core/Logging#durabletaskcore-logging).

## <a name="app-logging"></a>App-logboek registratie

Het is belang rijk om ervoor te zorgen dat het Orchestrator-gedrag voor het opnieuw afspelen van een logboeken rechtstreeks vanuit een Orchestrator-functie wordt geschreven. Denk bijvoorbeeld aan de volgende Orchestrator-functie:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)
```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    logging.info("Calling F1.")
    yield context.call_activity("F1")
    logging.info("Calling F2.")
    yield context.call_activity("F2")
    logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

De resulterende logboek gegevens zien er ongeveer uit zoals in de volgende voorbeeld uitvoer:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Houd er rekening mee dat de logboeken van de claim F1, F2 en F3 worden aangeroepen. deze functies *worden alleen de* eerste keer dat ze worden aangetroffen. Volgende aanroepen die tijdens het herhalen plaatsvinden, worden overgeslagen en de uitvoer wordt opnieuw afgespeeld naar de Orchestrator-logica.

Als u alleen logboeken met niet-replay-uitvoeringen wilt schrijven, kunt u een voorwaardelijke expressie schrijven om alleen te registreren als de vlag ' wordt opnieuw afspelen ' is `false` . Bekijk het bovenstaande voor beeld, maar deze keer met replay controles.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

Vanaf Durable Functions 2,0 kunnen .NET Orchestrator-functies ook een optie maken `ILogger` waarmee logboek overzichten tijdens het opnieuw afspelen automatisch worden gefilterd. Deze automatische filter bewerking wordt uitgevoerd met behulp van de API [IDurableOrchestrationContext. CreateReplaySafeLogger (ILogger)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durablecontextextensions.createreplaysafelogger) .

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

> [!NOTE]
> De vorige C#-voor beelden zijn voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    if not context.is_replaying:
        logging.info("Calling F1.")
    yield context.call_activity("F1")
    if not context.is_replaying:
        logging.info("Calling F2.")
    yield context.call_activity("F2")
    if not context.is_replaying:
        logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Met de eerder genoemde wijzigingen ziet de logboek uitvoer er als volgt uit:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Aangepaste status

Met de aangepaste indelings status kunt u een aangepaste status waarde instellen voor uw Orchestrator-functie. Deze aangepaste status is vervolgens zichtbaar voor externe clients via de [HTTP-status query-API](durable-functions-http-api.md#get-instance-status) of via taalspecifieke API-aanroepen. De aangepaste indelings status maakt uitgebreide bewaking mogelijk voor Orchestrator-functies. De functie code van Orchestrator kan bijvoorbeeld de API ' aangepaste status instellen ' aanroepen om de voortgang voor een langlopende bewerking bij te werken. Een-client, zoals een webpagina of een ander extern systeem, kan vervolgens periodiek query's uitvoeren op de HTTP-status query Api's voor informatie over rijkere voortgang. Hieronder vindt u voorbeeld code voor het instellen van een aangepaste status waarde in een Orchestrator-functie:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> Het vorige C#-voor beeld is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    # update the status of the orchestration with some arbitrary data
    custom_status = {'completionPercentage': 90.0, 'status': 'Updating database records'}
    context.set_custom_status(custom_status)
    # ...do more work...

    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Terwijl de Orchestration wordt uitgevoerd, kunnen externe clients deze aangepaste status ophalen:

```http
GET /runtime/webhooks/durabletask/instances/instance123?code=XYZ

```

Clients ontvangen het volgende antwoord:

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> De nettolading van de aangepaste status is beperkt tot 16 KB aan UTF-16-JSON-tekst omdat deze moet kunnen passen in een Azure Table Storage-kolom. U kunt externe opslag gebruiken als u een grotere Payload nodig hebt.

## <a name="debugging"></a>Foutopsporing

Azure Functions ondersteunt functie code voor fout opsporing rechtstreeks en dezelfde ondersteuning gaat naar Durable Functions, ongeacht of deze wordt uitgevoerd in azure of lokaal. Er zijn echter enkele gedragingen waarmee u rekening moet houden bij fout opsporing:

* **Replay**: Orchestrator functions wordt regel matig [herhaald](durable-functions-orchestrations.md#reliability) wanneer nieuwe invoer wordt ontvangen. Dit gedrag houdt in dat één *logische* uitvoering van een Orchestrator-functie kan leiden tot hetzelfde onderbrekings punt meerdere keren, vooral als het vroegtijdig is ingesteld in de functie code.
* **Await**: wanneer een `await` wordt aangetroffen in een Orchestrator-functie, wordt het besturings element teruggebracht naar de verzender van het duurzame taak raamwerk. Als dit de eerste keer is dat een bepaald item is `await` aangetroffen, wordt de bijbehorende taak *nooit* hervat. Omdat de taak nooit wordt hervat, is *het niet* mogelijk om door te lopen (F10 in Visual Studio). Alleen uitvoeren werkt wanneer een taak wordt herhaald.
* **Time-outs van berichten**: Durable functions intern gebruik van wachtrij berichten om de uitvoering van orchestrator-, activity-en Entity-functies te verzorgen. In een omgeving met meerdere VM'S kan de fout opsporing voor langere Peri Oden ertoe leiden dat een andere virtuele machine het bericht ophaalt, wat resulteert in dubbele uitvoering. Dit gedrag bestaat ook voor normale functies van de wachtrij-trigger, maar is belang rijk om in deze context te wijzen, omdat de wacht rijen een implementatie detail zijn.
* **Stoppen en starten**: berichten in duurzame functies blijven behouden tussen sessies voor fout opsporing. Als u de fout opsporing stopt en het lokale hostproces beëindigt terwijl een duurzame functie wordt uitgevoerd, wordt deze functie mogelijk automatisch opnieuw uitgevoerd in een toekomstige foutopsporingssessie. Dit gedrag kan verwarrend zijn wanneer niet wordt verwacht. Het wissen van alle berichten uit de [interne opslag wachtrijen](durable-functions-perf-and-scale.md#internal-queue-triggers) tussen sessies voor fout opsporing is een techniek om dit gedrag te voor komen.

> [!TIP]
> Als u onderbrekings punten instelt in Orchestrator-functies en u alleen een niet-replay-uitvoering wilt onderbreken, kunt u een voorwaardelijk onderbrekings punt instellen dat alleen wordt onderbroken als de waarde ' wordt opnieuw afspelen ' is `false` .

## <a name="storage"></a>Storage

Durable Functions-status wordt standaard opgeslagen in Azure Storage. Dit betekent dat u de status van uw Orchestrations kunt inspecteren met behulp van hulpprogram ma's zoals [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

![Scherm afbeelding Azure Storage Explorer](./media/durable-functions-diagnostics/storage-explorer.png)

Dit is handig voor het opsporen van fouten, omdat u precies ziet in welke staat een indeling kan zijn. Berichten in de wacht rijen kunnen ook worden onderzocht om te ontdekken wat er in behandeling is (of in sommige gevallen vastgelopen).

> [!WARNING]
> Hoewel het handig is om uitvoerings geschiedenis te zien in tabel opslag, moet u geen afhankelijkheid van deze tabel maken. De tabel kan veranderen naarmate de Durable Functions-uitbreiding zich ontwikkelt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over bewaking in Azure Functions](../functions-monitoring.md)
