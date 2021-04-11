---
title: host.jsbij verwijzing voor Azure Functions 1. x
description: Referentie documentatie voor de Azure Functions host.jsin het bestand met de V1-runtime.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 48dba50b384731befdc7fba7c418e542994cedd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102608951"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>host.jsbij verwijzing voor Azure Functions 1. x

> [!div class="op_single_selector" title1="Selecteer de versie van de Azure Functions runtime die u gebruikt: "]
> * [Versie 1:](functions-host-json-v1.md)
> * [Versie 2](functions-host-json.md)

De *host.jsvan* het meta gegevensbestand bevat globale configuratie opties die van invloed zijn op alle functies voor een functie-app. In dit artikel vindt u de instellingen die beschikbaar zijn voor de V1-runtime. Het JSON-schema bevindt zich op http://json.schemastore.org/host .

> [!NOTE]
> Dit artikel is voor Azure Functions 1. x.  Zie voor een referentie van host.jsin in functies 2. x en hoger [host.jsbij verwijzing voor Azure functions 2. x](functions-host-json.md).

Andere opties voor de configuratie van de functie-app worden beheerd in de [app-instellingen](functions-app-settings.md).

Sommige host.jsop instellingen worden alleen gebruikt wanneer lokaal wordt uitgevoerd in de [local.settings.jsvoor](functions-run-local.md#local-settings-file) het bestand.

## <a name="sample-hostjson-file"></a>Voorbeeld host.jsvoor het bestand

Voor de volgende voorbeeld *host.jsop* bestanden zijn alle mogelijke opties opgegeven.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

In de volgende secties van dit artikel wordt elke eigenschap op het hoogste niveau uitgelegd. Alle zijn optioneel, tenzij anders aangegeven.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Configuratie-instellingen voor de [Azure Cosmos DB trigger en bindingen](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|GatewayMode|Gateway|De verbindings modus die wordt gebruikt door de functie bij het maken van verbinding met de Azure Cosmos DB-service. Opties zijn `Direct` en `Gateway`|
|Protocol|Https|Het verbindings protocol dat door de functie wordt gebruikt bij het verbinden met de Azure Cosmos DB-service.  Lees [hier voor een uitleg van beide modi](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n.v.t.|Het lease voorvoegsel dat moet worden gebruikt voor alle functies in een app.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Configuratie-instellingen voor [Event hub-triggers en-bindingen](functions-bindings-event-hubs.md#functions-1x).

## <a name="functions"></a>vervullen

Een lijst met functies die de taak host uitvoert. Een lege matrix houdt in dat alle functies worden uitgevoerd. Alleen bedoeld voor gebruik bij [lokaal uitvoeren](functions-run-local.md). In functie-apps in azure moet u in plaats daarvan de stappen volgen in [het uitschakelen van functies in azure functions](disable-function.md) om specifieke functies uit te scha kelen in plaats van deze instelling te gebruiken.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Hiermee wordt de duur van de time-out voor alle functies aangegeven. In een serverloze verbruiks abonnement is het geldige bereik van 1 seconde tot 10 minuten en de standaard waarde is 5 minuten. In een App Service plan is er geen algemene limiet en de standaard waarde is _Null_, wat geen time-out aangeeft.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Configuratie-instellingen voor de [host Health Monitor](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|enabled|true|Hiermee wordt aangegeven of de functie is ingeschakeld. | 
|healthCheckInterval|10 seconden|Het tijds interval tussen de periodieke status controles voor de achtergrond. | 
|healthCheckWindow|2 minuten|Een schuif tijd venster dat wordt gebruikt in combi natie met de `healthCheckThreshold` instelling.| 
|healthCheckThreshold|6|Maximum aantal keer dat de status controle kan mislukken voordat een host recyclen wordt gestart.| 
|counterThreshold|0,80|De drempel waarde waarbij een prestatie meter item wordt beschouwd als een slechte status.| 

## <a name="http"></a>http

Configuratie-instellingen voor [http-triggers en-bindingen](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|dynamicThrottlesEnabled|onjuist|Als deze instelling is ingeschakeld, wordt door de aanvraag verwerkings pijplijn regel matig de systeem prestatie meter items gecontroleerd, zoals verbindingen/threads/processen/geheugen/CPU/etc. als een van deze prestatie meter items een ingebouwde hoge drempel waarde (80%) heeft, worden aanvragen geweigerd met een 429 ' te druk ' te allen tijde, totdat de teller (s) op normale niveaus terugkeren.|
|maxConcurrentRequests|niet-gebonden ( `-1` )|Het maximum aantal HTTP-functies dat parallel wordt uitgevoerd. Op die manier kunt u gelijktijdigheid beheren, waardoor het resource gebruik kan worden beheerd. Stel dat u een HTTP-functie hebt die gebruikmaakt van veel systeem bronnen (geheugen/CPU/sockets), zodat er problemen ontstaan wanneer gelijktijdigheid te hoog is. Het is ook mogelijk dat u een functie hebt waarmee uitgaande aanvragen voor een service van derden worden uitgevoerd. deze aanroepen moeten een beperkt aantal zijn. In dergelijke gevallen kan het Toep assen van een beperking hier helpen.|
|maxOutstandingRequests|niet-gebonden ( `-1` )|Het maximum aantal openstaande aanvragen dat op een bepaald moment wordt bewaard. Deze limiet omvat aanvragen die in de wachtrij zijn geplaatst, maar die nog niet zijn gestart, evenals de uitvoeringen die worden uitgevoerd. Alle binnenkomende aanvragen die deze limiet overschrijden, worden geweigerd met een antwoord van 429 ' bezet '. Hiermee kunnen aanroepers op tijd gebaseerde strategieën voor nieuwe pogingen gebruiken en kunt u ook de maximum latentie van aanvragen beheren. Hiermee beheert u alleen de wachtrij die zich in het uitvoerings traject van de Script Host voordoet. Andere wacht rijen, zoals de ASP.NET-aanvraag wachtrij, blijven van kracht en worden niet beïnvloed door deze instelling.|
|routePrefix|api|Het route voorvoegsel dat van toepassing is op alle routes. Gebruik een lege teken reeks om het standaard voorvoegsel te verwijderen. |

## <a name="id"></a>id

De unieke ID voor een beveiligingshost. Kan een kleine letter-GUID zijn waarbij streepjes worden verwijderd. Vereist wanneer lokaal wordt uitgevoerd. Bij het uitvoeren in azure wordt u aangeraden geen ID-waarde in te stellen. Een ID wordt automatisch gegenereerd in azure wanneer `id` wordt wegge laten. 

Als u een opslag account deelt in meerdere functie-apps, moet u ervoor zorgen dat elke functie-app een andere heeft `id` . U kunt de eigenschap weglaten `id` of de functie-app hand matig instellen `id` op een andere waarde. De timer trigger gebruikt een opslag vergrendeling om ervoor te zorgen dat er slechts één timer exemplaar is wanneer een functie-app wordt geschaald naar meerdere exemplaren. Als twee functie-apps hetzelfde hebben `id` en elk een timer trigger gebruikt, wordt er slechts één timer uitgevoerd.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

Hiermee wordt gefilterd op Logboeken die zijn geschreven door een [ILogger](functions-dotnet-class-library.md#ilogger) -object of [context. log](functions-reference-node.md#contextlog-method).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|categoryFilter|n.v.t.|Hiermee wordt gefilterd op categorie opgegeven| 
|defaultLevel|Informatie|Voor alle categorieën die niet in de `categoryLevels` matrix zijn opgegeven, verzendt u logboeken op dit niveau en hierboven naar Application Insights.| 
|categoryLevels|n.v.t.|Een matrix met categorieën die het minimale logboek niveau opgeven dat moet worden verzonden naar Application Insights voor elke categorie. De categorie die hier is opgegeven, bepaalt alle categorieën die beginnen met dezelfde waarde, en de langere waarden hebben prioriteit. In het voor gaande voor beeld *host.jsop* het bestand alle categorieën die beginnen met ' host. aggregator ' aanmelden op `Information` niveau. Alle andere categorieën die beginnen met ' host ', zoals ' Host.Executor ', log op `Error` niveau.| 

## <a name="queues"></a>Bestel

Configuratie-instellingen voor [opslag wachtrij-Triggers en-bindingen](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|maxPollingInterval|60000|Het maximum interval in milliseconden tussen de wachtrij polls.| 
|visibilityTimeout|0|Het tijds interval tussen nieuwe pogingen wanneer het verwerken van een bericht mislukt.| 
|batchSize|16|Het aantal wachtrij berichten dat door de functions-runtime gelijktijdig wordt opgehaald en processen parallel. Wanneer het nummer dat wordt verwerkt, naar de wordt verwerk `newBatchThreshold` , wordt er door de runtime een andere batch opgehaald en worden deze berichten verwerkt. Het maximum aantal gelijktijdige berichten dat per functie wordt verwerkt, is `batchSize` plus `newBatchThreshold` . Deze limiet geldt afzonderlijk voor elke door de wachtrij geactiveerde functie. <br><br>Als u een parallelle uitvoering wilt voor komen voor berichten die worden ontvangen op één wachtrij, kunt u instellen `batchSize` op 1. Met deze instelling elimineert u echter alleen gelijktijdigheid, zolang uw functie-app wordt uitgevoerd op één virtuele machine (VM). Als de functie-app wordt geschaald naar meerdere Vm's, kan elke virtuele machine één exemplaar van elke door de wachtrij geactiveerde functie uitvoeren.<br><br>De maximum waarde `batchSize` is 32. | 
|maxDequeueCount|5|Het aantal keren dat een bericht moet worden verwerkt voordat het naar de verontreinigde wachtrij wordt verplaatst.| 
|newBatchThreshold|batchSize/2|Telkens wanneer het aantal berichten dat gelijktijdig wordt verwerkt, naar dit nummer wordt opgehaald, haalt de runtime een andere batch op.| 

## <a name="sendgrid"></a>SendGrid

Configuratie-instelling voor de [SendGrind-uitvoer binding](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
}    
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|from|n.v.t.|Het e-mail adres van de afzender over alle functies.| 

## <a name="servicebus"></a>serviceBus

Configuratie-instelling voor [Service Bus triggers en bindingen](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|maxConcurrentCalls|16|Het maximum aantal gelijktijdige aanroepen naar de retour aanroep dat de bericht pomp moet initiëren. De functie runtime verwerkt standaard meerdere berichten tegelijk. Als u wilt dat de runtime slechts één wachtrij of onderwerp bericht per keer verwerkt, stelt `maxConcurrentCalls` u in op 1. | 
|prefetchCount|n.v.t.|De standaard PrefetchCount die wordt gebruikt door de onderliggende MessageReceiver.| 
|autoRenewTimeout|00:05:00|De maximale duur waarbinnen de bericht vergrendeling automatisch wordt vernieuwd.|
|Automatisch aanvullen|true|Indien true, wordt de bericht verwerking door de trigger automatisch voltooid wanneer de bewerking is uitgevoerd. Als false is ingesteld, is het de verantwoordelijkheid van de functie om het bericht te volt ooien voordat het wordt geretourneerd.|

## <a name="singleton"></a>Singleton

Configuratie-instellingen voor het gedrag van Singleton-vergren deling. Zie [github-probleem over Singleton-ondersteuning](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)voor meer informatie.

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|lockPeriod|00:00:15|De periode waarin vergrendelingen op functie niveau worden uitgevoerd. De vergren delingen automatisch verlengen.| 
|listenerLockPeriod|00:01:00|De periode waarin de luister vergrendelingen worden uitgevoerd.| 
|listenerLockRecoveryPollingInterval|00:01:00|Het tijds interval dat wordt gebruikt voor het herstel van de listener-vergren deling als tijdens het opstarten geen listener-vergrendeling kan worden verkregen.| 
|lockAcquisitionTimeout|00:01:00|De maximale hoeveelheid tijd die de runtime probeert een vergren deling te verkrijgen.| 
|lockAcquisitionPollingInterval|n.v.t.|Het interval tussen overname pogingen voor vergren delen.| 

## <a name="tracing"></a>tracering

*Versie 1.x*

Configuratie-instellingen voor logboeken die u maakt met behulp van een- `TraceWriter` object. Zie [C#-logboek registratie] voor meer informatie.

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|consoleLevel|Info|Het tracerings niveau voor console logboek registratie. Opties zijn: `off` , `error` , `warning` , `info` en `verbose` .|
|fileLoggingMode|debugOnly|Het tracerings niveau voor logboek registratie van bestanden. Opties zijn `never` , `always` , `debugOnly` .| 

## <a name="watchdirectories"></a>watchDirectories

Een set [gedeelde code mappen](functions-reference-csharp.md#watched-directories) die moeten worden gecontroleerd op wijzigingen.  Zorgt ervoor dat wanneer de code in deze directory's wordt gewijzigd, de wijzigingen worden opgehaald door uw functies.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het bijwerken van de host.jsvoor het bestand](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Algemene instellingen in omgevings variabelen weer geven](functions-app-settings.md)
