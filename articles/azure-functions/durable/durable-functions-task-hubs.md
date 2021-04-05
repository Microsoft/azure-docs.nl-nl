---
title: Taak hubs in Durable Functions-Azure
description: Meer informatie over wat een task hub is in de Durable Functions-extensie voor Azure Functions. Meer informatie over het configureren van taak hubs.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 26234039c77601bc1d29beeebd3fcb8461d6d6c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009514"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Taak hubs in Durable Functions (Azure Functions)

Een *Task hub* in [Durable functions](durable-functions-overview.md) is een logische container voor Azure storage resources die worden gebruikt voor Orchestrations. Orchestrator-en activiteit functies kunnen alleen communiceren wanneer ze tot dezelfde taak hub behoren.

Als meerdere functie-apps een opslag account delen, *moet* elke functie-app worden geconfigureerd met de naam van een afzonderlijke taak hub. Een opslag account kan meerdere taak hubs bevatten. In het volgende diagram ziet u één taak-hub per functie-app in gedeelde en toegewezen opslag accounts.

![Diagram met gedeelde en speciale opslag accounts.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage resources

Een taak-hub bestaat uit de volgende opslag resources:

* Een of meer controle wachtrijen.
* Eén wachtrij voor werk items.
* Eén geschiedenis tabel.
* Tabel met één exemplaar.
* Eén opslag container met een of meer lease-blobs.
* Een opslag container met grote bericht payloads, indien van toepassing.

Al deze resources worden automatisch gemaakt in de standaard Azure Storage-account wanneer orchestrator-, entity-of activity-functies worden uitgevoerd of zijn gepland om te worden uitgevoerd. In het artikel [prestaties en schalen](durable-functions-perf-and-scale.md) wordt uitgelegd hoe deze bronnen worden gebruikt.

## <a name="task-hub-names"></a>Namen van taak hubs

Taak hubs worden geïdentificeerd aan de hand van een naam die voldoet aan deze regels:

* Bevat alleen alfanumerieke tekens
* Begint met een letter
* Heeft een lengte van Mini maal drie tekens en een maximale lengte van 45 tekens

De naam van de taak-hub wordt gedeclareerd in de *host.jsvoor* het bestand, zoals wordt weer gegeven in het volgende voor beeld:

### <a name="hostjson-functions-20"></a>host.js(functies 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host.js(functions 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Taak hubs kunnen ook worden geconfigureerd met behulp van app-instellingen, zoals wordt weer gegeven in het volgende `host.json` voorbeeld bestand:

### <a name="hostjson-functions-10"></a>host.js(functies 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.js(functies 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

De naam van de taak-hub wordt ingesteld op de waarde van de `MyTaskHub` app-instelling. In het volgende `local.settings.json` voor beeld ziet u hoe u de `MyTaskHub` instelling definieert als `samplehubname` :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

De volgende code laat zien hoe u een functie schrijft die gebruikmaakt van de [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) om te werken met een task hub die is geconfigureerd als een app-instelling:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    object eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Het vorige C#-voor beeld is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

De eigenschap task hub in het `function.json` bestand is ingesteld via de app-instelling:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

# <a name="python"></a>[Python](#tab/python)

De eigenschap task hub in het `function.json` bestand is ingesteld via de app-instelling:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Namen van taakhubs moeten beginnen met een letter en mogen alleen letters en cijfers bevatten. Als u niets opgeeft, wordt de naam van een standaard taak-hub gebruikt, zoals wordt weer gegeven in de volgende tabel:

| Duurzame extensie versie | Naam van de standaard taak-hub |
| - | - |
| 2.x | Bij implementatie in azure wordt de naam van de taak-hub afgeleid van de naam van de _functie-app_. Bij uitvoering buiten Azure is de standaard naam van de taak hub `TestHubName` . |
| 1.x | De standaard naam van de taak hub voor alle omgevingen is `DurableFunctionsHub` . |

Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen extensies.

> [!NOTE]
> De naam is wat een taak hub onderscheidt van een andere als er meerdere taak hubs in een gedeeld opslag account zijn. Als u meerdere functie-apps hebt die een gedeeld opslag account delen, moet u expliciet verschillende namen configureren voor elke taak hub in de *host.jsop* bestanden. Anders kunnen de meerdere functie-apps met elkaar concurreren voor berichten. Dit kan leiden tot ongedefinieerd gedrag, met inbegrip van Orchestrations die onverwacht ' vastgelopen ' in de `Pending` of- `Running` status.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het afhandelen van Orchestrator versie beheer](durable-functions-versioning.md)
