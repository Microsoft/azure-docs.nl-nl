---
title: HTTP-Api's in Durable Functions-Azure Functions
description: Meer informatie over het implementeren van HTTP-Api's in de Durable Functions-extensie voor Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 4e4081ecca4714c713d105d363a83a4f96a0d3fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84697840"
---
# <a name="http-api-reference"></a>HTTP API-verwijzing

De uitbrei ding Durable Functions maakt een reeks ingebouwde HTTP-Api's die kunnen worden gebruikt voor het uitvoeren van beheer taken voor [Orchestrations](durable-functions-types-features-overview.md#orchestrator-functions), [entiteiten](durable-functions-types-features-overview.md#entity-functions)en [taak hubs](durable-functions-task-hubs.md). Deze HTTP-Api's zijn uitbreid bare webhooks die worden geautoriseerd door de Azure Functions-host, maar rechtstreeks worden verwerkt door de uitbrei ding Durable Functions.

Voor alle HTTP-Api's die door de extensie worden geïmplementeerd, zijn de volgende para meters vereist. Het gegevens type van alle para meters is `string` .

| Parameter        | Parameter type  | Beschrijving |
|------------------|-----------------|-------------|
| **`taskHub`**    | Queryreeksen    | De naam van de [taak hub](durable-functions-task-hubs.md). Indien niet opgegeven, wordt ervan uitgegaan dat de naam van de taak-hub van de huidige functie-app wordt gebruikt. |
| **`connection`** | Queryreeksen    | De **naam** van de Connection String voor het opslag account. Als deze niet wordt opgegeven, wordt aangenomen dat de standaard connection string voor de functie-app wordt gebruikt. |
| **`systemKey`**  | Queryreeksen    | De autorisatie sleutel die is vereist om de API aan te roepen. |

`systemKey` is een autorisatie sleutel die automatisch wordt gegenereerd door de Azure Functions-host. Het geeft specifiek toegang tot de duurzame Api's voor taak uitbreidingen en kan op dezelfde manier worden beheerd als [andere autorisatie sleutels](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). U kunt Url's genereren die de juiste waarden bevatten, `taskHub` `connection` en `systemKey` query teken reeksen gebruiken met behulp van [Orchestrator client binding](durable-functions-bindings.md#orchestration-client) api's, zoals de `CreateCheckStatusResponse` `CreateHttpManagementPayload` api's in .net of de api's `createCheckStatusResponse` en `createHttpManagementPayload` in Java script.

In de volgende secties worden de specifieke HTTP-Api's beschreven die worden ondersteund door de uitbrei ding en worden voor beelden gegeven van hoe ze kunnen worden gebruikt.

## <a name="start-orchestration"></a>Indeling starten

Het uitvoeren van een nieuw exemplaar van de opgegeven Orchestrator-functie wordt gestart.

### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld              | Parametertype  | Beschrijving |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | De naam van de Orchestrator-functie die moet worden gestart. |
| **`instanceId`**   | URL             | Optionele parameter. De ID van het Orchestration-exemplaar. Als deze niet wordt opgegeven, begint de Orchestrator-functie met een wille keurige exemplaar-ID. |
| **`{content}`**    | Inhoud van aanvraag | Optioneel. De functie-invoer voor de JSON-indeling van Orchestrator. |

### <a name="response"></a>Antwoord

Er kunnen verschillende mogelijke status code waarden worden geretourneerd.

* **HTTP 202 (geaccepteerd)**: de opgegeven Orchestrator-functie is gepland om te worden uitgevoerd. De `Location` antwoord header bevat een URL voor het navragen van de Orchestration-status.
* **HTTP 400 (ongeldige aanvraag)**: de opgegeven Orchestrator-functie bestaat niet, de opgegeven exemplaar-id is ongeldig of de aanvraag inhoud is geen geldige JSON.

Hier volgt een voorbeeld aanvraag waarmee een Orchestrator- `RestartVMs` functie wordt gestart en die JSON-object Payload bevat:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

De nettolading van de reactie voor de **HTTP 202-** cases is een JSON-object met de volgende velden:

| Veld                       | Beschrijving                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |De ID van het Orchestration-exemplaar. |
| **`statusQueryGetUri`**     |De status-URL van het Orchestrator-exemplaar. |
| **`sendEventPostUri`**      |De URL van de functie ' raise ' van het Orchestrator-exemplaar. |
| **`terminatePostUri`**      |De ' Terminate ' URL van het Orchestrator-exemplaar. |
| **`purgeHistoryDeleteUri`** |De URL voor het verwijderen van de geschiedenis van het Orchestrator-exemplaar. |
| **`rewindPostUri`**         |evaluatie De ' rewinde ' URL van het Orchestrator-exemplaar. |

Het gegevens type van alle velden is `string` .

Hier volgt een voor beeld van een nettolading voor een Orchestrator-exemplaar met `abc123` als id (opgemaakt voor de Lees baarheid):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Het HTTP-antwoord is bedoeld om compatibel te zijn met het *polling Consumer-patroon*. Het bevat ook de volgende belang rijke reactie headers:

* **Locatie**: de URL van het eind punt van de status. Deze URL bevat dezelfde waarde als het `statusQueryGetUri` veld.
* **Opnieuw proberen na**: het aantal seconden dat moet worden gewacht tussen polling bewerkingen. De standaardwaarde is `10`.

Zie de documentatie over [http async-tracering](durable-functions-http-features.md#async-operation-tracking) voor meer informatie over het asynchrone HTTP polling-patroon.

## <a name="get-instance-status"></a>Status van exemplaar ophalen

Hiermee wordt de status van een opgegeven Orchestrator-exemplaar opgehaald.

### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld                   | Parametertype  | Beschrijving |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | De ID van het Orchestration-exemplaar. |
| **`showInput`**         | Queryreeksen    | Optionele parameter. Als deze optie is ingesteld op `false` , wordt de functie-invoer niet opgenomen in de nettolading van het antwoord.|
| **`showHistory`**       | Queryreeksen    | Optionele parameter. Als deze is ingesteld op `true` , wordt de geschiedenis van de Orchestration-uitvoering opgenomen in de nettolading van de reactie.|
| **`showHistoryOutput`** | Queryreeksen    | Optionele parameter. Als deze optie is ingesteld op `true` , worden de functie-uitvoer opgenomen in de geschiedenis van de Orchestration-uitvoering.|
| **`createdTimeFrom`**   | Queryreeksen    | Optionele parameter. Hiermee wordt de lijst met geretourneerde exemplaren die zijn gemaakt op of na de opgegeven ISO8601-tijds tempel gefilterd.|
| **`createdTimeTo`**     | Queryreeksen    | Optionele parameter. Hiermee wordt de lijst met geretourneerde exemplaren die zijn gemaakt op of vóór de opgegeven ISO8601-tijds tempel gefilterd.|
| **`runtimeStatus`**     | Queryreeksen    | Optionele parameter. Hiermee wordt indien opgegeven de lijst met geretourneerde instanties gefilterd op basis van de runtime status. Zie het artikel [querying instances](durable-functions-instance-management.md) voor een lijst met mogelijke runtime status waarden. |

### <a name="response"></a>Antwoord

Er kunnen verschillende mogelijke status code waarden worden geretourneerd.

* **HTTP 200 (OK)**: het opgegeven exemplaar bevindt zich in de status voltooid.
* **HTTP 202 (geaccepteerd)**: het opgegeven exemplaar wordt uitgevoerd.
* **HTTP 400 (ongeldige aanvraag)**: het opgegeven exemplaar is mislukt of is beëindigd.
* **HTTP 404 (niet gevonden)**: het opgegeven exemplaar bestaat niet of is niet gestart.
* **HTTP 500 (interne server fout)**: het opgegeven exemplaar is mislukt vanwege een onverwerkte uitzonde ring.

De nettolading van de reactie voor de **http 200-** en **http 202** -CASEs is een JSON-object met de volgende velden:

| Veld                 | Gegevenstype | Beschrijving |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | tekenreeks    | De runtime status van het exemplaar. Waarden zijn onder andere *actief*, *in behandeling*, *mislukt*, *geannuleerd*, *beëindigd*, *voltooid*. |
| **`input`**           | JSON      | De JSON-gegevens die worden gebruikt voor het initialiseren van het exemplaar. Dit veld is `null` als de `showInput` query teken reeks parameter is ingesteld op `false` .|
| **`customStatus`**    | JSON      | De JSON-gegevens die worden gebruikt voor de aangepaste indelings status. Dit veld is `null` niet ingesteld. |
| **`output`**          | JSON      | De JSON-uitvoer van het exemplaar. Dit veld is `null` een niet-voltooide status van het exemplaar. |
| **`createdTime`**     | tekenreeks    | Het tijdstip waarop het exemplaar is gemaakt. Maakt gebruik van ISO 8601 Extended-notatie. |
| **`lastUpdatedTime`** | tekenreeks    | Het tijdstip waarop het exemplaar het laatst is bewaard. Maakt gebruik van ISO 8601 Extended-notatie. |
| **`historyEvents`**   | JSON      | Een JSON-matrix met de geschiedenis van de Orchestration-uitvoering. Dit veld is `null` tenzij de `showHistory` query teken reeks parameter is ingesteld op `true` . |

Hier volgt een voor beeld van een nettolading met de geschiedenis van de Orchestration-uitvoering en uitvoer van de activiteit (opgemaakt voor de Lees baarheid):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

Het **HTTP 202-** antwoord bevat ook een **locatie** antwoord header die verwijst naar dezelfde URL als het `statusQueryGetUri` eerder genoemde veld.

## <a name="get-all-instances-status"></a>Status van alle instanties ophalen

U kunt ook een query uitvoeren op de status van alle instanties door de `instanceId` uit de aanvraag ' exemplaar status ophalen ' te verwijderen. In dit geval zijn de basis parameters hetzelfde als de Get-exemplaar status. Query reeks parameters voor filteren worden ook ondersteund.

Een ding die u moet onthouden is dat `connection` en is `code` optioneel. Als u anonieme verificatie hebt voor de functie, `code` is dit niet vereist.
Als u geen andere opslag connection string wilt gebruiken dan gedefinieerd in de app-instelling voor AzureWebJobsStorage, kunt u de teken reeks parameter voor de verbindings query veilig negeren.

### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld                   | Parametertype  | Beschrijving |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | De ID van het Orchestration-exemplaar. |
| **`showInput`**         | Queryreeksen    | Optionele parameter. Als deze optie is ingesteld op `false` , wordt de functie-invoer niet opgenomen in de nettolading van het antwoord.|
| **`showHistory`**       | Queryreeksen    | Optionele parameter. Als deze is ingesteld op `true` , wordt de geschiedenis van de Orchestration-uitvoering opgenomen in de nettolading van de reactie.|
| **`showHistoryOutput`** | Queryreeksen    | Optionele parameter. Als deze optie is ingesteld op `true` , worden de functie-uitvoer opgenomen in de geschiedenis van de Orchestration-uitvoering.|
| **`createdTimeFrom`**   | Queryreeksen    | Optionele parameter. Hiermee wordt de lijst met geretourneerde exemplaren die zijn gemaakt op of na de opgegeven ISO8601-tijds tempel gefilterd.|
| **`createdTimeTo`**     | Queryreeksen    | Optionele parameter. Hiermee wordt de lijst met geretourneerde exemplaren die zijn gemaakt op of vóór de opgegeven ISO8601-tijds tempel gefilterd.|
| **`runtimeStatus`**     | Queryreeksen    | Optionele parameter. Hiermee wordt indien opgegeven de lijst met geretourneerde instanties gefilterd op basis van de runtime status. Zie het artikel [querying instances](durable-functions-instance-management.md) voor een lijst met mogelijke runtime status waarden. |
| **`top`**               | Queryreeksen    | Optionele parameter. Hiermee wordt het aantal exemplaren dat door de query wordt geretourneerd, beperkt. |

### <a name="response"></a>Antwoord

Hier volgt een voor beeld van reactie payloads, inclusief de indelings status (opgemaakt voor de Lees baarheid):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Deze bewerking kan erg duur zijn in Azure Storage I/O als er veel rijen in de tabel instanties zijn. Meer informatie over de exemplaar tabel vindt u in de documentatie over [prestaties en schaal baarheid in Durable functions (Azure functions)](durable-functions-perf-and-scale.md#instances-table) .
>

Als er meer resultaten bestaan, wordt in de reactie header een vervolg token geretourneerd.  De naam van de koptekst is `x-ms-continuation-token` .

Als u de vervolg token waarde in de volgende aanvraag header instelt, kunt u de volgende pagina met resultaten ophalen. Dit is ook de naam van de aanvraag header `x-ms-continuation-token` .

## <a name="purge-single-instance-history"></a>Geschiedenis van één exemplaar opschonen

Hiermee verwijdert u de geschiedenis en gerelateerde artefacten voor een opgegeven Orchestration-exemplaar.

### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld             | Parametertype  | Beschrijving |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van het Orchestration-exemplaar. |

### <a name="response"></a>Antwoord

De volgende HTTP-status code waarden kunnen worden geretourneerd.

* **HTTP 200 (OK)**: de exemplaar geschiedenis is verwijderd.
* **HTTP 404 (niet gevonden)**: het opgegeven exemplaar bestaat niet.

De nettolading van de reactie voor de **HTTP 200-** case is een JSON-object met het volgende veld:

| Veld                  | Gegevenstype | Beschrijving |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | geheel getal   | Het aantal verwijderde exemplaren. Voor het geval van één exemplaar moet deze waarde altijd zijn `1` . |

Hier volgt een voor beeld van een Payload-reactie (opgemaakt voor de Lees baarheid):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Meerdere exemplaar geschiedenissen verwijderen

U kunt ook de geschiedenis en gerelateerde artefacten voor meerdere exemplaren binnen een taak hub verwijderen door de `{instanceId}` uit de aanvraag verwijderen van één exemplaar van de geschiedenis te verwijderen. Als u de instantie geschiedenis selectief wilt opschonen, gebruikt u dezelfde filters die in de aanvraag ' alle instanties ophalen ' worden beschreven.

### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld                 | Parametertype  | Beschrijving |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Queryreeksen    | Hiermee wordt de lijst met verwijderde exemplaren gefilterd die zijn gemaakt op of na de opgegeven ISO8601-tijds tempel.|
| **`createdTimeTo`**   | Queryreeksen    | Optionele parameter. Hiermee wordt de lijst met verwijderde exemplaren die zijn gemaakt op of vóór de opgegeven ISO8601-tijds tempel gefilterd.|
| **`runtimeStatus`**   | Queryreeksen    | Optionele parameter. Hiermee wordt indien opgegeven de lijst met verwijderde exemplaren gefilterd op basis van de runtime status. Zie het artikel [querying instances](durable-functions-instance-management.md) voor een lijst met mogelijke runtime status waarden. |

> [!NOTE]
> Deze bewerking kan erg kostbaar zijn in termen van Azure Storage I/O als er veel rijen in de tabellen instanties en/of geschiedenis zijn. Meer informatie over deze tabellen vindt u in de documentatie over [prestaties en schaal baarheid in Durable functions (Azure functions)](durable-functions-perf-and-scale.md#instances-table) .

### <a name="response"></a>Antwoord

De volgende HTTP-status code waarden kunnen worden geretourneerd.

* **HTTP 200 (OK)**: de exemplaar geschiedenis is verwijderd.
* **HTTP 404 (niet gevonden)**: er zijn geen instanties gevonden die overeenkomen met de filter expressie.

De nettolading van de reactie voor de **HTTP 200-** case is een JSON-object met het volgende veld:

| Veld                   | Gegevenstype | Beschrijving |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | geheel getal   | Het aantal verwijderde exemplaren. |

Hier volgt een voor beeld van een Payload-reactie (opgemaakt voor de Lees baarheid):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Gebeurtenis verhogen

Hiermee verzendt u een bericht over gebeurtenis meldingen naar een actief Orchestration-exemplaar.

### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld             | Parametertype  | Beschrijving |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van het Orchestration-exemplaar. |
| **`eventName`**   | URL             | De naam van de gebeurtenis waarop het doel exemplaar van de Orchestrator-instantie wacht. |
| **`{content}`**   | Inhoud van aanvraag | De nettolading van de JSON-indeling. |

### <a name="response"></a>Antwoord

Er kunnen verschillende mogelijke status code waarden worden geretourneerd.

* **HTTP 202 (geaccepteerd)**: de verhoogde gebeurtenis is geaccepteerd voor verwerking.
* **HTTP 400 (ongeldige aanvraag)**: de inhoud van de aanvraag is niet van het type `application/json` of is geen geldige JSON.
* **HTTP 404 (niet gevonden)**: het opgegeven exemplaar is niet gevonden.
* **HTTP 410 (verdwenen)**: het opgegeven exemplaar is voltooid of mislukt en kan geen geactiveerde gebeurtenissen verwerken.

Hier volgt een voorbeeld aanvraag waarmee de JSON-teken reeks wordt verzonden `"incr"` naar een instantie die wacht op een gebeurtenis **bewerking** met de naam:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

De antwoorden voor deze API bevatten geen inhoud.

## <a name="terminate-instance"></a>Exemplaar beëindigen

Hiermee beëindigt u een actief Orchestration-exemplaar.

### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meter.

| Veld             | Parameter type  | Beschrijving |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van het Orchestration-exemplaar. |
| **`reason`**      | Queryreeksen    | Optioneel. De reden voor het beëindigen van het Orchestrator-exemplaar. |

### <a name="response"></a>Antwoord

Er kunnen verschillende mogelijke status code waarden worden geretourneerd.

* **HTTP 202 (geaccepteerd)**: de aanvraag voor beëindigen is geaccepteerd voor verwerking.
* **HTTP 404 (niet gevonden)**: het opgegeven exemplaar is niet gevonden.
* **HTTP 410 (verdwenen)**: het opgegeven exemplaar is voltooid of mislukt.

Hier volgt een voorbeeld aanvraag waarmee een actief exemplaar wordt beëindigd en een reden voor het uitvoeren van **fouten** wordt opgegeven:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

De antwoorden voor deze API bevatten geen inhoud.

## <a name="rewind-instance-preview"></a>Exemplaar terugspoelen (preview-versie)

Hiermee herstelt u een mislukte Orchestrator-instantie in een actieve status door de meest recente mislukte bewerkingen te herhalen.

### <a name="request"></a>Aanvraag

Voor versie 1. x van de functions-runtime wordt de aanvraag als volgt opgemaakt (er worden meerdere lijnen weer gegeven voor duidelijkheid):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

In versie 2. x van de functions runtime heeft de URL-indeling dezelfde para meters, maar met een iets ander voor voegsel:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meter.

| Veld             | Parameter type  | Beschrijving |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | De ID van het Orchestration-exemplaar. |
| **`reason`**      | Queryreeksen    | Optioneel. De reden voor het terugspoelen van het Orchestration-exemplaar. |

### <a name="response"></a>Antwoord

Er kunnen verschillende mogelijke status code waarden worden geretourneerd.

* **HTTP 202 (geaccepteerd)**: de aanvraag voor terugspoelen is geaccepteerd voor verwerking.
* **HTTP 404 (niet gevonden)**: het opgegeven exemplaar is niet gevonden.
* **HTTP 410 (verdwenen)**: het opgegeven exemplaar is voltooid of is beëindigd.

Hier volgt een voorbeeld aanvraag voor het terugspoelen van een mislukt exemplaar en de reden van een **vast**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

De antwoorden voor deze API bevatten geen inhoud.

## <a name="signal-entity"></a>Signaal entiteit

Verzendt een eenrichtings bewerkings bericht naar een [duurzame entiteit](durable-functions-types-features-overview.md#entity-functions). Als de entiteit niet bestaat, wordt deze automatisch gemaakt.

> [!NOTE]
> Duurzame entiteiten zijn beschikbaar vanaf Durable Functions 2,0.

### <a name="request"></a>Aanvraag

De HTTP-aanvraag wordt als volgt ingedeeld (er worden meerdere regels weer gegeven voor de duidelijkheid):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld             | Parametertype  | Beschrijving |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | De naam (type) van de entiteit. |
| **`entityKey`**   | URL             | De sleutel (unieke ID) van de entiteit. |
| **`op`**          | Queryreeksen    | Optioneel. De naam van de door de gebruiker gedefinieerde bewerking die moet worden aangeroepen. |
| **`{content}`**   | Inhoud van aanvraag | De nettolading van de JSON-indeling. |

Hier volgt een voorbeeld aanvraag waarmee een door de gebruiker gedefinieerde "add"-bericht naar een entiteit met de naam wordt verzonden `Counter` `steps` . De inhoud van het bericht is de waarde `5` . Als de entiteit nog niet bestaat, wordt deze gemaakt door deze aanvraag:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Standaard met op [klassen gebaseerde entiteiten in .net](durable-functions-dotnet-entities.md#defining-entity-classes), waarbij de `op` waarde van `delete` wordt verwijderd, wordt de status van een entiteit. Als de entiteit een bewerkings naam definieert `delete` , wordt deze door de gebruiker gedefinieerde bewerking echter wel aangeroepen.

### <a name="response"></a>Antwoord

Deze bewerking heeft verschillende mogelijke reacties:

* **HTTP 202 (geaccepteerd)**: de signaal bewerking is geaccepteerd voor asynchrone verwerking.
* **HTTP 400 (ongeldige aanvraag)**: de inhoud van de aanvraag is niet van het type `application/json` , is geen geldige JSON of heeft een ongeldige `entityKey` waarde.
* **HTTP 404 (niet gevonden)**: de opgegeven `entityName` is niet gevonden.

Een geslaagde HTTP-aanvraag bevat geen inhoud in het antwoord. Een mislukte HTTP-aanvraag kan fout informatie in JSON-indeling bevatten in de antwoord inhoud.

## <a name="get-entity"></a>Entiteit ophalen

Hiermee wordt de status van de opgegeven entiteit opgehaald.

### <a name="request"></a>Aanvraag

De HTTP-aanvraag wordt als volgt ingedeeld (er worden meerdere regels weer gegeven voor de duidelijkheid):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Antwoord

Deze bewerking heeft twee mogelijke antwoorden:

* **HTTP 200 (OK)**: de opgegeven entiteit bestaat.
* **HTTP 404 (niet gevonden)**: de opgegeven entiteit is niet gevonden.

Een geslaagd antwoord bevat de JSON-serialisatie status van de entiteit als de inhoud.

### <a name="example"></a>Voorbeeld
In het volgende voor beeld van een HTTP-aanvraag wordt de status van een bestaande entiteit met de `Counter` naam `steps` :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Als de `Counter` entiteit simpelweg een aantal stappen bevat die zijn opgeslagen in een `currentValue` veld, kan de antwoord inhoud er als volgt uitzien (opgemaakt voor de Lees baarheid):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Entiteiten weergeven

U kunt een query voor meerdere entiteiten uitvoeren op de naam van de entiteit of op de datum van de laatste bewerking.

### <a name="request"></a>Aanvraag

De HTTP-aanvraag wordt als volgt ingedeeld (er worden meerdere regels weer gegeven voor de duidelijkheid):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Aanvraag parameters voor deze API zijn de standaardset die eerder is vermeld, evenals de volgende unieke para meters:

| Veld                       | Parametertype  | Beschrijving |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | Optioneel. Als deze is opgegeven, wordt de lijst met geretourneerde entiteiten gefilterd op basis van de naam van de entiteit (niet hoofdletter gevoelig). |
| **`fetchState`**            | Queryreeksen    | Optionele parameter. Als deze is ingesteld op `true` , wordt de status van de entiteit opgenomen in de nettolading van de reactie. |
| **`lastOperationTimeFrom`** | Queryreeksen    | Optionele parameter. Hiermee wordt de lijst met geretourneerde entiteiten die bewerkingen hebben verwerkt na de opgegeven ISO8601-tijds tempel gefilterd. |
| **`lastOperationTimeTo`**   | Queryreeksen    | Optionele parameter. Hiermee wordt indien opgegeven de lijst met geretourneerde entiteiten gefilterd die bewerkingen hebben verwerkt vóór de opgegeven ISO8601-tijds tempel. |
| **`top`**                   | Queryreeksen    | Optionele parameter. Hiermee wordt het aantal entiteiten beperkt dat door de query wordt geretourneerd. |


### <a name="response"></a>Antwoord

Een geslaagd HTTP 200-antwoord bevat een JSON-geserialiseerde matrix van entiteiten en optioneel de status van elke entiteit.

Standaard retourneert de bewerking de eerste 100 entiteiten die overeenkomen met de query criteria. De aanroeper kan een parameter waarde voor de query reeks opgeven `top` om een ander maximum aantal resultaten te retour neren. Als er meer resultaten zijn dan wordt geretourneerd, wordt er ook een vervolg token geretourneerd in de reactie header. De naam van de koptekst is `x-ms-continuation-token` .

Als u de vervolg token waarde in de volgende aanvraag header instelt, kunt u de volgende pagina met resultaten ophalen. Dit is ook de naam van de aanvraag header `x-ms-continuation-token` .

### <a name="example---list-all-entities"></a>Voor beeld: alle entiteiten weer geven

In het volgende voor beeld van een HTTP-aanvraag worden alle entiteiten in de taak hub vermeld:

```http
GET /runtime/webhooks/durabletask/entities
```

De JSON van het antwoord kan er als volgt uitzien (opgemaakt voor de Lees baarheid):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Voor beeld: de lijst met entiteiten filteren

In het volgende voor beeld van een HTTP-aanvraag worden alleen de eerste twee entiteiten van het type `counter` en de status ervan opgehaald:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

De JSON van het antwoord kan er als volgt uitzien (opgemaakt voor de Lees baarheid):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het gebruik van Application Insights om uw duurzame functies te bewaken](durable-functions-diagnostics.md)