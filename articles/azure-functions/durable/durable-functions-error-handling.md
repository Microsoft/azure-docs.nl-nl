---
title: Fouten afhandelen in Durable Functions-Azure
description: Meer informatie over het afhandelen van fouten in de Durable Functions extensie voor Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 023f9dfcc421935c3f7515e847108925d5e5521e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673644"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Het afhandelen van fouten in Durable Functions (Azure Functions)

Duurzame functie-indelingen worden in code geïmplementeerd en kunnen de ingebouwde functies voor fout afhandeling van de programmeer taal gebruiken. Er zijn echt geen nieuwe concepten die u nodig hebt om fout afhandeling en compensatie toe te voegen aan uw integraties. Er zijn echter enkele gedragingen waarmee u rekening moet houden.

## <a name="errors-in-activity-functions"></a>Fouten in de activiteit functies

Uitzonde ringen die worden gegenereerd in een activiteit functie, worden teruggestuurd naar de Orchestrator-functie en gegenereerd als een `FunctionFailedException` . U kunt fout afhandeling en compensatie code schrijven die aan uw behoeften voldoen in de Orchestrator-functie.

Denk bijvoorbeeld aan de volgende Orchestrator-functie waarmee u fondsen van het ene naar het andere account overbrengt:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> De vorige C#-voor beelden zijn voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
```

---

Als de eerste aanroep van de functie **CreditAccount** mislukt, compenseert de Orchestrator-functie door de tegoeden terug te sturen naar het bron account.

## <a name="automatic-retry-on-failure"></a>Automatische nieuwe poging bij fout

Wanneer u activiteit functies of suborchestration-functies aanroept, kunt u een beleid voor automatische nieuwe pogingen opgeven. In het volgende voor beeld wordt geprobeerd een functie Maxi maal drie keer aan te roepen en wordt vijf seconden gewacht tussen elke nieuwe poging:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> De vorige C#-voor beelden zijn voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

De functie aanroep van de activiteit in het vorige voor beeld neemt een para meter op voor het configureren van een beleid voor automatische nieuwe pogingen. Er zijn verschillende opties voor het aanpassen van het beleid voor automatische opnieuw proberen:

* **Maximum aantal pogingen**: het maximum aantal nieuwe pogingen.
* **Interval voor eerste poging**: de hoeveelheid tijd die moet worden gewacht voordat de eerste nieuwe poging wordt gedaan.
* **Uitstel coëfficiënt**: de coëfficiënt die wordt gebruikt voor het bepalen van de mate van toename van uitstel. Standaardwaarde is 1.
* Maximum **interval voor nieuwe pogingen**: de maximale tijds duur tussen nieuwe pogingen.
* **Time-out voor opnieuw proberen**: de maximale hoeveelheid tijd die nodig is voor het uitvoeren van nieuwe pogingen. Het standaard gedrag is om voor onbepaalde tijd opnieuw te proberen.
* **Ingang**: een door de gebruiker gedefinieerde retour aanroep kan worden opgegeven om te bepalen of een functie opnieuw moet worden uitgevoerd. 

> [!NOTE]
> Door de gebruiker gedefinieerde retour aanroepen worden momenteel niet ondersteund door Durable Functions in Java script ( `context.df.RetryOptions` ).


## <a name="function-timeouts"></a>Time-outs van functies

Het kan zijn dat u een functie aanroep binnen een Orchestrator-functie wilt verlaten als het te lang duurt om te volt ooien. De juiste manier om dit te doen is door een [duurzame timer](durable-functions-timers.md) te maken met `context.CreateTimer` (.net), `context.df.createTimer` (Java script) of `context.create_timer` (python) in combi natie met `Task.WhenAny` (.net), `context.df.Task.any` (Java script) of `context.task_any` (python), zoals in het volgende voor beeld:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> De vorige C#-voor beelden zijn voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!NOTE]
> Met dit mechanisme wordt de uitvoering van de uitgevoerde activiteiten functie niet daad werkelijk afgesloten. In plaats daarvan kan de Orchestrator-functie het resultaat negeren en verplaatsen. Zie de [time](durable-functions-timers.md#usage-for-timeout) -outdocumentatie voor meer informatie.

## <a name="unhandled-exceptions"></a>Onverwerkte uitzonderingen

Als een Orchestrator-functie mislukt met een niet-verwerkte uitzonde ring, worden de details van de uitzonde ring vastgelegd en wordt het exemplaar voltooid met een `Failed` status.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over eeuwige-Orchestrations](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Meer informatie over het vaststellen van problemen](durable-functions-diagnostics.md)
