---
title: Eeuwige-integratie in Durable Functions-Azure
description: Meer informatie over het implementeren van eeuwige-integratie met behulp van de Durable Functions-extensie voor Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: azfuncdf
ms.openlocfilehash: 82108ae0b2cabaab6dfa47c8bb5e893a44df38af
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182057"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eeuwige-integraties in Durable Functions (Azure Functions)

*Eeuwige* -indelingen zijn Orchestrator-functies die nooit eindigen. Ze zijn handig wanneer u [Durable functions](durable-functions-overview.md) wilt gebruiken voor aggregators en elk scenario waarvoor een oneindige lus is vereist.

## <a name="orchestration-history"></a>Indelingsgeschiedenis

Zoals uitgelegd in het onderwerp [Orchestration-geschiedenis](durable-functions-orchestrations.md#orchestration-history) , houdt het duurzame taak raamwerk de geschiedenis bij van elke functie indeling. Deze geschiedenis neemt voortdurend toe zolang de Orchestrator-functie blijft werken om nieuwe werkzaamheden te plannen. Als de Orchestrator-functie een oneindige lus doorloopt en voortdurend schema's werken, kan deze geschiedenis kritiek toenemen en aanzienlijke prestatie problemen veroorzaken. Het *eeuwige-Orchestration* -concept is ontworpen om dit soort problemen te verhelpen voor toepassingen die oneindige lussen nodig hebben.

## <a name="resetting-and-restarting"></a>Opnieuw instellen en opnieuw starten

In plaats van een oneindige lus te gebruiken, wordt de status van Orchestrator-functies opnieuw ingesteld door de `ContinueAsNew` (.net), `continueAsNew` (Java script) of `continue_as_new` de methode (python) van de [Orchestration-trigger binding](durable-functions-bindings.md#orchestration-trigger)aan te roepen. Deze methode heeft één JSON-Serializable-para meter, die de nieuwe invoer wordt voor de volgende Orchestrator-functie generatie.

Wanneer `ContinueAsNew` wordt aangeroepen, in het exemplaar een bericht naar zichzelf voordat het wordt afgesloten. Het bericht start het exemplaar opnieuw met de nieuwe invoer waarde. Dezelfde exemplaar-ID wordt bewaard, maar de geschiedenis van de Orchestrator-functie wordt in feite afgekapt.

> [!NOTE]
> Het duurzame taak raamwerk houdt dezelfde exemplaar-ID bij, maar maakt intern een nieuwe *uitvoerings-id* voor de Orchestrator-functie die opnieuw wordt ingesteld door `ContinueAsNew` . Deze uitvoerings-ID wordt over het algemeen niet extern weer gegeven, maar het kan nuttig zijn om te weten wanneer de uitvoering van een fout in de Orchestration is opgetreden.

## <a name="periodic-work-example"></a>Voor beeld van periodieke werk

Een use-case voor eeuwige-indelingen is code die voor onbepaalde tijd periodiek moet worden uitgevoerd.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> Het vorige C#-voor beeld is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    yield context.df.continueAsNew(undefined);
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("DoCleanup")

    # sleep for one hour between cleanups
    next_cleanup = context.current_utc_datetime + timedelta(hours = 1)
    yield context.create_timer(next_cleanup)

    context.continue_as_new(None)

main = df.Orchestrator.create(orchestrator_function)
```

---

Het verschil tussen dit voor beeld en een door een timer geactiveerde functie is dat hier niet op basis van een schema wordt geactiveerd. Zo kan een CRON-schema waarmee een functie elk uur wordt uitgevoerd, worden uitgevoerd op 1:00, 2:00, 3:00 enz. Dit kan mogelijk worden uitgevoerd in overlappende problemen. Als het opschonen echter 30 minuten duurt, wordt het gepland op 1:00, 2:30, 4:00 enzovoort. er is geen kans op overlap ping.

## <a name="starting-an-eternal-orchestration"></a>Een eeuwige-indeling starten

Gebruik de `StartNewAsync` (.net), de ( `startNew` Java script)- `start_new` methode (python) om een eeuwige-indeling te starten, net als bij andere Orchestration-functies.  

> [!NOTE]
> Als u er zeker van wilt zijn dat er een singleton eeuwige-indeling wordt uitgevoerd, is het belang rijk dat u hetzelfde exemplaar behoudt `id` bij het starten van de indeling. Zie [instance Management](durable-functions-instance-management.md)(Engelstalig) voor meer informatie.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> De vorige code is voor Durable Functions 2. x. Voor Durable Functions 1. x, moet u `OrchestrationClient` kenmerk gebruiken in plaats van het `DurableClient` kenmerk, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```
# <a name="python"></a>[Python](#tab/python)

```python
async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = 'StaticId'

    await client.start_new('Periodic_Cleanup_Loop', instance_id, None)

    logging.info(f"Started orchestration with ID = '{instance_id}'.")
    return client.create_check_status_response(req, instance_id)

```

---

## <a name="exit-from-an-eternal-orchestration"></a>Afsluiten vanuit een eeuwige-indeling

Als een Orchestrator-functie uiteindelijk moet worden voltooid, hoeft u *niet* te bellen `ContinueAsNew` en kunt u de functie niet afsluiten.

Als een Orchestrator-functie zich in een oneindige lus bevindt en moet worden gestopt, gebruikt u de `TerminateAsync` methode (.net), `terminate` (Java script) of `terminate` (python) van de [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) om deze te stoppen. Zie [instance Management](durable-functions-instance-management.md)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van Singleton-indelingen](durable-functions-singletons.md)
