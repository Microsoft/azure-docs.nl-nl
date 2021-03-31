---
title: Aangepaste indelings status in Durable Functions-Azure
description: Meer informatie over het configureren en gebruiken van een aangepaste indelings status voor Durable Functions.
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: azfuncdf
ms.openlocfilehash: 4a95e7c74fac7043d0adb5f31d2bdcdd73b9577a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97766325"
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Aangepaste indelings status in Durable Functions (Azure Functions)

Met de aangepaste indelings status kunt u een aangepaste status waarde instellen voor uw Orchestrator-functie. Deze status wordt gegeven via de [http GetStatus-API](durable-functions-http-api.md#get-instance-status) of de [ `GetStatusAsync` API](durable-functions-instance-management.md#query-instances) op de Orchestration-client.

## <a name="sample-use-cases"></a>Voorbeelden van toepassingsscenario’s

> [!NOTE]
> De volgende voor beelden laten zien hoe u een aangepaste status functie kunt gebruiken in C#, java script en python. De C#-voor beelden zijn geschreven voor Durable Functions 2. x en zijn niet compatibel met Durable Functions 1. x. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="visualize-progress"></a>Voortgang visualiseren

Clients kunnen het eind punt van de status controleren en een voortgangs GEBRUIKERSINTERFACE weer geven die de huidige uitvoerings fase visualiseert. In het volgende voor beeld ziet u de voortgang van het delen:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    context.SetCustomStatus("Tokyo");
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    context.SetCustomStatus("Seattle");
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
    context.SetCustomStatus("London");

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E1_HelloSequence` Orchestrator-functie:

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const outputs = [];

    outputs.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    context.df.setCustomStatus("Tokyo");
    outputs.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    context.df.setCustomStatus("Seattle");
    outputs.push(yield context.df.callActivity("E1_SayHello", "London"));
    context.df.setCustomStatus("London");

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
});
```

`E1_SayHello` functie activity:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```
# <a name="python"></a>[Python](#tab/python)

### <a name="e1_hellosequence-orchestrator-function"></a>`E1_HelloSequence` Orchestrator-functie
```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    
    output1 = yield context.call_activity('E1_SayHello', 'Tokyo')
    context.set_custom_status('Tokyo')
    output2 = yield context.call_activity('E1_SayHello', 'Seattle')
    context.set_custom_status('Seattle')
    output3 = yield context.call_activity('E1_SayHello', 'London')
    context.set_custom_status('London')
    
    return [output1, output2, output3]

main = df.Orchestrator.create(orchestrator_function)
```

### <a name="e1_sayhello-activity-function"></a>`E1_SayHello` Functie activity
```python
def main(name: str) -> str:
    return f"Hello {name}!"

```

---

En de client ontvangt de uitvoer van de indeling alleen wanneer het `CustomStatus` veld is ingesteld op Amsterdam:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, (string)eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
        await Task.Delay(200);
        durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
        Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    let durableOrchestrationStatus = await client.getStatus(instanceId);
    while (durableOrchestrationStatus.customStatus.toString() !== "London") {
        await new Promise((resolve) => setTimeout(resolve, 200));
        durableOrchestrationStatus = await client.getStatus(instanceId);
    }

    const httpResponseMessage = {
        status: 200,
        body: JSON.stringify(durableOrchestrationStatus),
    };

    return httpResponseMessage;
};
```

> [!NOTE]
> In Java script `customStatus` wordt het veld ingesteld wanneer de volgende `yield` of `return` actie wordt gepland.

# <a name="python"></a>[Python](#tab/python)
```python
import json
import logging
import azure.functions as func
import azure.durable_functions as df
from time import sleep

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)    
    instance_id = await client.start_new(req.params.functionName, None, None)

    logging.info(f"Started orchestration with ID = '{instance_id}'.")

    durable_orchestration_status = await client.get_status(instance_id)
    while durable_orchestration_status.custom_status != 'London':
        sleep(0.2)
        durable_orchestration_status = await client.get_status(instance_id)

    return func.HttpResponse(body='Success', status_code=200, mimetype='application/json')
```

> [!NOTE]
> In python wordt het `custom_status` veld ingesteld wanneer de volgende `yield` of `return` actie wordt gepland.

---

### <a name="output-customization"></a>Aanpassing van de uitvoer

Een ander interessant scenario is het segmenteren van gebruikers door een aangepaste uitvoer te retour neren op basis van unieke kenmerken of interacties. Met behulp van de aangepaste indelings status blijft de client-side-code algemeen. Alle belang rijke wijzigingen worden aangebracht aan de server zijde, zoals wordt weer gegeven in het volgende voor beeld:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] IDurableOrchestrationContext context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="cityrecommender-orchestrator"></a>`CityRecommender` Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userChoice = context.df.getInput();

    switch (userChoice) {
        case 1:
            context.df.setCustomStatus({
                recommendedCities: [ "Tokyo", "Seattle" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
        case 2:
            context.df.setCustomStatus({
                recommendedCities: [ "Seattle", "London" ],
                recommendedSeasons: [ "Summer" ],
            });
            break;
        case 3:
            context.df.setCustomStatus({
                recommendedCity: [ "Tokyo", "London" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
    }

    // Wait for user selection and refine the recommendation
});
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="cityrecommender-orchestrator"></a>`CityRecommender` Orchestrator

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    userChoice = int(context.get_input())

    if userChoice == 1:
        context.set_custom_status({
            'recommendedCities' : ['Tokyo', 'Seattle'], 
            'recommendedSeasons' : ['Spring', 'Summer']
        }))
    if userChoice == 2:
        context.set_custom_status({
            'recommendedCities' : ['Seattle', 'London']
            'recommendedSeasons' : ['Summer']
        }))
    if userChoice == 3:
        context.set_custom_status({
            'recommendedCities' : ['Tokyo', 'London'], 
            'recommendedSeasons' : ['Spring', 'Summer']
        }))



    # Wait for user selection and refine the recommendation

main = df.Orchestrator.create(orchestrator_function)
```
---

### <a name="instruction-specification"></a>Instructie specificatie

De Orchestrator kan unieke instructies bieden aan de clients via de aangepaste status. De aangepaste status instructies worden toegewezen aan de stappen in de indelings code:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] IDurableOrchestrationContext context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userId = context.df.getInput();

    const discount = yield context.df.callActivity("CalculateDiscount", userId);

    context.df.setCustomStatus({
        discount,
        discountTimeout = 60,
        bookingUrl = "https://www.myawesomebookingweb.com",
    });

    const isBookingConfirmed = yield context.df.waitForExternalEvent("bookingConfirmed");

    context.df.setCustomStatus(isBookingConfirmed
        ? { message: "Thank you for confirming your booking." }
        : { message: "The booking was not confirmed on time. Please try again." }
    );

    return isBookingConfirmed;
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    userId = int(context.get_input())

    discount = yield context.call_activity('CalculateDiscount', userId)

    status = { 'discount' : discount,
        'discountTimeout' : 60,
        'bookingUrl' : "https://www.myawesomebookingweb.com",
    }
    context.set_custom_status(status)

    is_booking_confirmed = yield context.wait_for_external_event('BookingConfirmed')
    context.set_custom_status({'message': 'Thank you for confirming your booking.'} if is_booking_confirmed 
        else {'message': 'The booking was not confirmed on time. Please try again.'})
    return is_booking_confirmed

main = df.Orchestrator.create(orchestrator_function)
```

---

## <a name="sample"></a>Voorbeeld

In het volgende voor beeld wordt de aangepaste status eerst ingesteld.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { nextActions: [ "A", "B", "C" ], foo: 2, };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    custom_status = {'nextActions': ['A','B','C'], 'foo':2}
    context.set_custom_status(custom_status)

    # ...do more work...

main = df.Orchestrator.create(orchestrator_function)
```
---

Terwijl de Orchestration wordt uitgevoerd, kunnen externe clients deze aangepaste status ophalen:

```http
GET /runtime/webhooks/durabletask/instances/instance123
```

Clients ontvangen het volgende antwoord:

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2019-10-06T18:30:24Z",
  "lastUpdatedTime": "2019-10-06T19:40:30Z"
}
```

> [!WARNING]
> De nettolading van de aangepaste status is beperkt tot 16 KB aan UTF-16-JSON-tekst omdat deze moet kunnen passen in een Azure Table Storage-kolom. U wordt aangeraden externe opslag te gebruiken als u een grotere Payload nodig hebt.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over duurzame timers](durable-functions-timers.md)
