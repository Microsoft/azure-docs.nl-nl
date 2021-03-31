---
title: Singleton voor Durable Functions-Azure
description: Het gebruik van Singleton in de Durable Functions extensie voor Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: azfuncdf
ms.openlocfilehash: 50ed473d61dff19f41f77a79513c0ddab521e56f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325739"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton-Orchestrator in Durable Functions (Azure Functions)

Voor achtergrond taken moet u er vaak voor zorgen dat slechts één exemplaar van een bepaalde Orchestrator tegelijk wordt uitgevoerd. U kunt dit soort Singleton gedrag in [Durable functions](durable-functions-overview.md) controleren door een specifieke exemplaar-id toe te wijzen aan een Orchestrator tijdens het maken.

## <a name="singleton-example"></a>Voor beeld van Singleton

In het volgende voor beeld ziet u een HTTP-trigger-functie waarmee een singleton achtergrond taak indeling wordt gemaakt. De code zorgt ervoor dat er slechts één exemplaar bestaat voor een opgegeven exemplaar-ID.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Completed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Failed 
    || existingInstance.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return new HttpResponseMessage(HttpStatusCode.Conflict)
        {
            Content = new StringContent($"An instance with ID '{instanceId}' already exists."),
        };
    }
}
```

> [!NOTE]
> De vorige C#-code is voor Durable Functions 2. x. Voor Durable Functions 1. x, moet u `OrchestrationClient` kenmerk gebruiken in plaats van het `DurableClient` kenmerk, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists or an existing one stopped running(completed/failed/terminated).
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance 
        || existingInstance.runtimeStatus == "Completed" 
        || existingInstance.runtimeStatus == "Failed" 
        || existingInstance.runtimeStatus == "Terminated") {
        // An instance with the specified ID doesn't exist or an existing one stopped running, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists or an existing one still running, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

# <a name="python"></a>[Python](#tab/python)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**__init__. py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance != None or existing_instance.runtime_status in ["Completed", "Failed", "Terminated"]:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${instance_id}' already exists"
        }

```

---

Exemplaar-Id's zijn standaard wille keurig gegenereerde GUID'S. In het vorige voor beeld wordt de exemplaar-ID echter door gegeven in route gegevens van de URL. De code aanroepen `GetStatusAsync` (C#), `getStatus` (Java script) of `get_status` (python) om te controleren of een exemplaar met de opgegeven id al wordt uitgevoerd. Als een dergelijk exemplaar niet wordt uitgevoerd, wordt er een nieuw exemplaar gemaakt met die ID.

> [!NOTE]
> Dit voor beeld bevat een mogelijke race voorwaarde. Als twee instanties van **HttpStartSingle** gelijktijdig worden uitgevoerd, wordt door beide functie aanroepen geslaagd, maar er wordt slechts één Orchestration-exemplaar gestart. Afhankelijk van uw vereisten kan dit ongewenste neven effecten hebben. Daarom is het belang rijk om ervoor te zorgen dat twee aanvragen deze trigger gelijktijdig kunnen uitvoeren.

De implementatie details van de Orchestrator-functie zijn niet werkelijk van belang. Dit kan een normale Orchestrator-functie zijn die wordt gestart en voltooid, of kan een van de functies zijn die permanent worden uitgevoerd (dat wil zeggen, een [eeuwige](durable-functions-eternal-orchestrations.md)-indeling). Het belang rijk punt is dat er maar één instantie tegelijk wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de systeem eigen HTTP-functies van Orchestrations](durable-functions-http-features.md)
