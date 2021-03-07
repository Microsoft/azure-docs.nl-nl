---
title: Bindingen voor Durable Functions-Azure
description: Triggers en bindingen gebruiken voor de uitbrei ding van de Durable Functions voor Azure Functions.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: fe64945aeb71011e769ba4016ae36cb557f41f33
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424254"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindingen voor Durable Functions (Azure Functions)

De uitbrei ding [Durable functions](durable-functions-overview.md) introduceert twee nieuwe trigger bindingen die de uitvoering van Orchestrator-en activiteit functies regelen. Er wordt ook een uitvoer binding geïntroduceerd die fungeert als een client voor de Durable Functions runtime.

## <a name="orchestration-trigger"></a>Orchestration-trigger

Met de Orchestration-trigger kunt u [duurzame Orchestrator-functies](durable-functions-types-features-overview.md#orchestrator-functions)ontwerpen. Deze trigger ondersteunt het starten van nieuwe Orchestrator-functie instanties en het hervatten van bestaande Orchestrator-functie instanties die ' wachten op ' een taak.

Wanneer u de Visual Studio-hulpprogram ma's voor Azure Functions gebruikt, wordt de Orchestration-trigger geconfigureerd met het kenmerk [OrchestrationTriggerAttribute](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.orchestrationtriggerattribute) .net.

Wanneer u Orchestrator-functies schrijft in script talen (bijvoorbeeld Java script of C#-Scripting), wordt de Orchestration-trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van de *function.jsin* het bestand:

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration` is de naam van de indeling die clients moeten gebruiken wanneer ze nieuwe exemplaren van deze Orchestrator-functie willen starten. Deze eigenschap is optioneel. Als u niets opgeeft, wordt de naam van de functie gebruikt.

Intern deze trigger roept een reeks wacht rijen aan in het standaard opslag account voor de functie-app. Deze wacht rijen zijn interne implementatie details van de uitbrei ding. Daarom worden ze niet expliciet geconfigureerd in de binding eigenschappen.

### <a name="trigger-behavior"></a>Triggergedrag

Hier volgen enkele opmerkingen over de Orchestration-trigger:

* **Eén threading** : er wordt één dispatcher-thread gebruikt voor alle Orchestrator-functies die worden uitgevoerd op één exemplaar van een host. Daarom is het belang rijk om ervoor te zorgen dat de functie code van Orchestrator efficiënt is en geen I/O uitvoert. Het is ook belang rijk om ervoor te zorgen dat deze thread geen async-werk doet, behalve wanneer u op Durable Functions-specifieke taak typen wacht.
* **Poison-Message Handling** -er is geen ondersteuning voor Poison-berichten in Orchestration-triggers.
* **Bericht zichtbaarheid** : indelings trigger berichten worden niet in de wachtrij geplaatst en blijven onzichtbaar voor een Configureer bare duur. De zicht baarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app wordt uitgevoerd en in orde is.
* **Retour waarden** : retour waarden worden GESERIALISEERD in JSON en persistent gemaakt in de Orchestration-geschiedenis tabel in azure Table Storage. Deze retour waarden kunnen worden opgevraagd door de Orchestrator-client binding, die later wordt beschreven.

> [!WARNING]
> Orchestrator-functies mogen nooit alle invoer-of uitvoer bindingen gebruiken, behalve de Orchestration-trigger binding. Als u dit doet, is het mogelijk om problemen met de extensie duurzame taak te veroorzaken, omdat deze bindingen de enkelvoudige threading en I/O-regels niet kunnen navolgen. Als u andere bindingen wilt gebruiken, voegt u deze toe aan een activiteit functie die wordt aangeroepen vanuit uw Orchestrator-functie.

> [!WARNING]
> Java script Orchestrator-functies mogen nooit worden gedeclareerd `async` .

### <a name="trigger-usage-net"></a>Gebruik van triggers (.NET)

De Orchestration-trigger binding ondersteunt zowel invoer als uitvoer. Hier volgen enkele dingen die u moet weten over de verwerking van de invoer en uitvoer:

* **invoer** -.net-Orchestration-functies ondersteunen alleen `DurableOrchestrationContext` als een parameter type. Het deserialiseren van invoer rechtstreeks in de functie handtekening wordt niet ondersteund. Code moet de `GetInput<T>` (.net) of `getInput` (Java script)-methode gebruiken om de functie-invoer van Orchestrator te verkrijgen. Deze invoer moet JSON-Serializable typen zijn.
* **outputs** : indelings triggers ondersteunen uitvoer waarden en invoer. De geretourneerde waarde van de functie wordt gebruikt om de uitvoer waarde toe te wijzen en moet JSON serialiseerbaar zijn. Als een .NET-functie retourneert `Task` of `void` , `null` wordt een waarde opgeslagen als uitvoer.

### <a name="trigger-sample"></a>Voor beeld trigger

In de volgende voorbeeld code ziet u wat de eenvoudigste functie ' Hallo wereld ' Orchestrator kan zien:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> De vorige code is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

#### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Het `context` object in Java script vertegenwoordigt niet de DurableOrchestrationContext, maar de [functie context als geheel](../functions-reference-node.md#context-object). U kunt toegang krijgen tot indelings methoden via de `context` eigenschap van het object `df` .

> [!NOTE]
> Java script-Orchestrator moet gebruiken `return` . De `durable-functions` tape wisselaar zorgt ervoor dat de `context.done` methode wordt aangeroepen.

De meeste functies van Orchestrator roepen activiteit functies aan, dus hier volgt een voor beeld van een Hallo wereld dat laat zien hoe u een activiteit functie aanroept:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> De vorige code is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

#### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Activiteit trigger

Met de trigger activiteit kunt u functies schrijven die worden aangeroepen door Orchestrator-functies, ook wel [activiteit functies](durable-functions-types-features-overview.md#activity-functions)genoemd.

Als u Visual Studio gebruikt, wordt de activiteit trigger geconfigureerd met behulp van het `ActivityTriggerAttribute` .net-kenmerk.

Als u VS code gebruikt of de Azure Portal voor ontwikkeling, wordt de activiteit trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van *function.jsop*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity` is de naam van de activiteit. Deze waarde is de naam die door Orchestrator-functies wordt gebruikt om deze activiteit functie aan te roepen. Deze eigenschap is optioneel. Als u niets opgeeft, wordt de naam van de functie gebruikt.

Intern deze trigger roept een wachtrij aan in het standaard opslag account voor de functie-app. Deze wachtrij is een interne implementatie details van de uitbrei ding, wat daarom niet expliciet is geconfigureerd in de binding eigenschappen.

### <a name="trigger-behavior"></a>Triggergedrag

Hier volgen enkele opmerkingen over de trigger voor activiteiten:

* **Threading** : in tegens telling tot de Orchestration-trigger gelden er geen beperkingen voor threading of I/O. Ze kunnen worden behandeld als normale functies.
* **Poison-verwerking van berichten** -er is geen Poison-bericht ondersteuning in activiteit triggers.
* **Bericht zichtbaarheid** : activiteit trigger berichten worden niet in de wachtrij geplaatst en worden gedurende een Configureer bare duur onzichtbaar bewaard. De zicht baarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app wordt uitgevoerd en in orde is.
* **Retour waarden** : retour waarden worden GESERIALISEERD in JSON en persistent gemaakt in de Orchestration-geschiedenis tabel in azure Table Storage.

> [!WARNING]
> De opslag back-end voor activiteit functies is een implementatie detail en gebruikers code mogen niet rechtstreeks met deze opslag entiteiten werken.

### <a name="trigger-usage-net"></a>Gebruik van triggers (.NET)

De activiteit trigger binding ondersteunt zowel invoer als uitvoer, net als de Orchestration-trigger. Hier volgen enkele dingen die u moet weten over de verwerking van de invoer en uitvoer:

* **invoer** -.net-activiteit functies die systeem eigen gebruik `DurableActivityContext` als parameter type gebruiken. U kunt ook een functie activity declareren met elk parameter type dat kan worden geserialiseerd. Wanneer u gebruikt `DurableActivityContext` , kunt u aanroepen `GetInput<T>` om de invoer van de activiteit functie op te halen en te deserialiseren.
* **uitvoer** -activiteit functies ondersteunen uitvoer waarden en invoer. De geretourneerde waarde van de functie wordt gebruikt om de uitvoer waarde toe te wijzen en moet JSON serialiseerbaar zijn. Als een .NET-functie retourneert `Task` of `void` , `null` wordt een waarde opgeslagen als uitvoer.
* **meta gegevens** -.net-activiteit functies kunnen worden gebonden aan een `string instanceId` para meter om de exemplaar-id van de bovenliggende indeling op te halen.

### <a name="trigger-sample"></a>Voor beeld trigger

De volgende voorbeeld code laat zien hoe een eenvoudige activiteit functie ' Hallo wereld ' eruit kan zien als:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> De vorige code is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableActivityContext` in plaats van gebruiken `IDurableActivityContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

Het standaard parameter type voor de .NET- `ActivityTriggerAttribute` binding is `IDurableActivityContext` . .NET-activiteit Triggers bieden echter ook ondersteuning voor het rechtstreeks binden aan JSON-serializeable-typen (waaronder primitieve typen), zodat dezelfde functie als volgt kan worden vereenvoudigd:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Java script-bindingen kunnen ook worden door gegeven als aanvullende para meters, zodat dezelfde functie als volgt kan worden vereenvoudigd:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Invoer-en uitvoer bindingen gebruiken

U kunt normale invoer-en uitvoer bindingen gebruiken naast de trigger voor het activeren van de activiteit. U kunt bijvoorbeeld de invoer naar uw activiteit binden en een bericht naar een EventHub verzenden met behulp van de EventHub-uitvoer binding:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Orchestration-client

Met de Orchestrator client binding kunt u functies schrijven die communiceren met Orchestrator-functies. Deze functies worden soms [client functies](durable-functions-types-features-overview.md#client-functions)genoemd. U kunt bijvoorbeeld op de volgende manieren handelen op indelings instanties:

* Start deze.
* Hun status opvragen.
* Deze beëindigen.
* Verzend gebeurtenissen naar hen terwijl ze worden uitgevoerd.
* Exemplaar geschiedenis opschonen.

Als u Visual Studio gebruikt, kunt u verbinding maken met de Orchestration-client met behulp van het `OrchestrationClientAttribute` .net-kenmerk voor Durable Functions 1,0. Vanaf de Durable Functions 2,0 kunt u verbinding maken met de Orchestration-client met behulp van het `DurableClientAttribute` .net-kenmerk.

Als u script talen (bijvoorbeeld *. CSX* -of *. js* -bestanden) gebruikt voor ontwikkeling, wordt de Orchestration-trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van *function.jsop*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub` -Wordt gebruikt in scenario's waarbij meerdere functie-apps hetzelfde opslag account delen, maar van elkaar moeten worden geïsoleerd. Als u niets opgeeft, wordt de standaard waarde van `host.json` gebruikt. Deze waarde moet overeenkomen met de waarde die wordt gebruikt door de doel Orchestrator-functies.
* `connectionName` -De naam van een app-instelling die een opslag account bevat connection string. Het opslag account dat door deze connection string wordt weer gegeven, moet hetzelfde zijn als de naam die wordt gebruikt door de doel Orchestrator-functies. Als dat niet is opgegeven, wordt het standaard-opslag account connection string voor de functie-app gebruikt.

> [!NOTE]
> In de meeste gevallen wordt u aangeraden deze eigenschappen over te slaan en te vertrouwen op het standaard gedrag.

### <a name="client-usage"></a>Client gebruik

In .NET functions maakt u doorgaans een binding aan `IDurableOrchestrationClient` . Dit biedt volledige toegang tot alle api's van de Orchestration-client die door Durable functions worden ondersteund. In de oudere Durable Functions 2. x-releases maakt u in plaats daarvan een binding met de `DurableOrchestrationClient` klasse. In Java script worden dezelfde Api's weer gegeven door het object dat wordt geretourneerd door `getClient` . Api's op het client object zijn onder andere:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

.NET-functies kunnen ook worden gekoppeld aan `IAsyncCollector<T>` waar `T` `StartOrchestrationArgs` of `JObject` .

Zie de API-documentatie voor meer informatie over deze bewerkingen `IDurableOrchestrationClient` .

### <a name="client-sample-visual-studio-development"></a>Client-voor beeld (Visual Studio-ontwikkeling)

Hier volgt een voor beeld van een door de wachtrij geactiveerde functie waarmee de indeling HelloWorld wordt gestart.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> De vorige C#-code is voor Durable Functions 2. x. Voor Durable Functions 1. x, moet u `OrchestrationClient` kenmerk gebruiken in plaats van het `DurableClient` kenmerk, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="client-sample-not-visual-studio"></a>Client-voor beeld (niet Visual Studio)

Als u Visual Studio niet gebruikt voor ontwikkeling, kunt u de volgendefunction.jsmaken *in* het bestand. In dit voor beeld ziet u hoe u een functie die door een wachtrij wordt geactiveerd, configureert die gebruikmaakt van de duurzame Orchestration-client binding:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> De vorige JSON is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `orchestrationClient` in plaats van het `durableClient` trigger type gebruiken. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

Hieronder vindt u taalspecifieke voor beelden die nieuwe Orchestrator-functie instanties starten.

#### <a name="c-script-sample"></a>C#-voorbeeld script

In het volgende voor beeld ziet u hoe u de duurzame Orchestration-client binding kunt gebruiken om een nieuw functie-exemplaar te starten vanuit een C#-functie die door een wachtrij wordt geactiveerd:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> De vorige code is voor Durable Functions 2. x. Voor Durable Functions 1. x, moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

#### <a name="javascript-sample"></a>Java script-voor beeld

In het volgende voor beeld ziet u hoe u de duurzame Orchestration-client binding kunt gebruiken om een nieuw functie-exemplaar te starten vanuit een Java script-functie:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Meer informatie over begin instanties vindt u in [exemplaar beheer](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Entiteits trigger

Met entiteit triggers kunt u [entiteits functies](durable-functions-entities.md)ontwerpen. Deze trigger ondersteunt het verwerken van gebeurtenissen voor een specifiek entiteits exemplaar.

Wanneer u de Visual Studio-hulpprogram ma's voor Azure Functions gebruikt, wordt de entiteits trigger geconfigureerd met het `EntityTriggerAttribute` .net-kenmerk.

> [!NOTE]
> Entiteit triggers zijn beschikbaar vanaf Durable Functions 2. x.

Intern deze trigger roept een reeks wacht rijen aan in het standaard opslag account voor de functie-app. Deze wacht rijen zijn interne implementatie details van de uitbrei ding. Daarom worden ze niet expliciet geconfigureerd in de binding eigenschappen.

### <a name="trigger-behavior"></a>Triggergedrag

Hier volgen enkele opmerkingen over de entiteits trigger:

* **Eén thread**: er wordt één dispatcher-thread gebruikt voor het verwerken van bewerkingen voor een bepaalde entiteit. Als meerdere berichten gelijktijdig naar één entiteit worden verzonden, worden de bewerkingen één voor een in de tijd verwerkt.
* **Poison-Message Handling** -er is geen ondersteuning voor Poison-berichten in entiteit triggers.
* **Bericht zichtbaarheid** : entiteits trigger berichten worden niet in de wachtrij geplaatst en worden gedurende een Configureer bare duur onzichtbaar bewaard. De zicht baarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app wordt uitgevoerd en in orde is.
* **Retour waarden** : entiteit functies bieden geen ondersteuning voor retour waarden. Er zijn specifieke Api's die kunnen worden gebruikt om de status op te slaan of waarden terug te geven naar Orchestrations.

Status wijzigingen die tijdens de uitvoering van een entiteit zijn aangebracht, worden automatisch behouden nadat de uitvoering is voltooid.

### <a name="trigger-usage-net"></a>Gebruik van triggers (.NET)

Elke entiteit functie heeft het parameter type `IDurableEntityContext` , dat de volgende leden heeft:

* **EntityName**: de naam van de entiteit die momenteel wordt uitgevoerd.
* **EntityKey**: de sleutel van de entiteit die momenteel wordt uitgevoerd.
* **EntityId**: de id van de entiteit die momenteel wordt uitgevoerd.
* **Operationname**: de naam van de huidige bewerking.
* **HasState**: of de entiteit bestaat, dat wil zeggen een bepaalde status heeft. 
* **GetState \<TState> ()**: Hiermee wordt de huidige status van de entiteit opgehaald. Als deze nog niet bestaat, wordt deze gemaakt en geïnitialiseerd naar `default<TState>` . De `TState` para meter moet een primitief-of JSON-serializeable-type zijn. 
* **GetState \<TState> (initfunction)**: Hiermee wordt de huidige status van de entiteit opgehaald. Als deze nog niet bestaat, wordt deze gemaakt door de gegeven para meter aan te roepen `initfunction` . De `TState` para meter moet een primitief-of JSON-serializeable-type zijn. 
* **SetState (ARG)**: Hiermee wordt de status van de entiteit gemaakt of bijgewerkt. De `arg` para meter moet een JSON-serializeable-object of primitieve zijn.
* **DeleteState ()**: de status van de entiteit wordt verwijderd. 
* **GetInput \<TInput> ()**: Hiermee wordt de invoer voor de huidige bewerking opgehaald. De `TInput` type parameter moet een primitief-of JSON-serializeable-type zijn.
* **Retour (ARG)**: retourneert een waarde voor de indeling die de bewerking wordt genoemd. De `arg` para meter moet een primitief-of JSON-serializeable-object zijn.
* **SignalEntity (EntityId, scheduledTimeUtc, Operation, invoer)**: er wordt een eenrichtings bericht naar een entiteit verzonden. De `operation` para meter moet een teken reeks zijn die niet gelijk is aan nul, het optionele `scheduledTimeUtc` moet een UTC-datum zijn waarop de bewerking kan worden aangeroepen en de `input` para meter moet een primitief of JSON-serializeable-object zijn.
* **CreateNewOrchestration (orchestratorFunctionName, invoer)**: start een nieuwe indeling. De `input` para meter moet een primitief-of JSON-serializeable-object zijn.

Het `IDurableEntityContext` object dat aan de functie entiteit is door gegeven, is toegankelijk via de `Entity.Current` eigenschap async-local. Deze methode is handig wanneer u het op klassen gebaseerde programmeer model gebruikt.

### <a name="trigger-sample-c-function-based-syntax"></a>Trigger voorbeeld (syntaxis op basis van C#-functie)

De volgende code is een voor beeld van een eenvoudige *teller* -entiteit die is geïmplementeerd als een duurzame functie. Deze functie definieert drie bewerkingen, `add` , `reset` , en `get` , die allemaal werken met een gehele status.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Zie [syntaxis op basis van functies](durable-functions-dotnet-entities.md#function-based-syntax)voor meer informatie over de syntaxis van de functie en hoe u deze kunt gebruiken.

### <a name="trigger-sample-c-class-based-syntax"></a>Trigger-voor beeld (syntaxis op basis van C#-klasse)

Het volgende voorbeeld is een equivalente implementatie van de `Counter`-entiteit met behulp van klassen en methoden.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

De status van deze entiteit is een object van het type `Counter`, dat een veld bevat waarin de huidige waarde van de teller wordt opgeslagen. Als u dit object in de opslag ruimte wilt behouden, wordt het geserialiseerd en gedeserialiseerd door de [JSON.net](https://www.newtonsoft.com/json) -bibliotheek. 

Zie [Entiteitsklassen definiëren](durable-functions-dotnet-entities.md#defining-entity-classes) voor meer informatie over de op klassen gebaseerde syntaxis en hoe u deze kunt gebruiken.

> [!NOTE]
> De methode van het functie-invoer punt met het `[FunctionName]` kenmerk *moet* worden gedeclareerd `static` bij het gebruik van entity klassen. Niet-statische toegangs punt methoden kunnen leiden tot meervoudige initialisatie van objecten en mogelijk andere niet-gedefinieerde gedragingen.

Entiteits klassen hebben speciale mechanismen voor interactie met bindingen en .NET-afhankelijkheids injectie. Zie [entiteits constructie](durable-functions-dotnet-entities.md#entity-construction)voor meer informatie.

### <a name="trigger-sample-javascript"></a>Voor beeld van trigger (Java script)

De volgende code is een voor beeld van een eenvoudige *teller* -entiteit die is geïmplementeerd als een duurzame functie die is geschreven in Java script. Deze functie definieert drie bewerkingen, `add` , `reset` , en `get` , die allemaal werken met een gehele status.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

> [!NOTE]
> Duurzame entiteiten zijn beschikbaar in JavaScript vanaf versie **1.3.0** van het npm-pakket `durable-functions`.

## <a name="entity-client"></a>Entiteit-client

Met de client binding van de entiteit kunt u de [entiteits functies](#entity-trigger)asynchroon activeren. Deze functies worden soms [client functies](durable-functions-types-features-overview.md#client-functions)genoemd.

Als u Visual Studio gebruikt, kunt u verbinding maken met de entiteit client met behulp van het `DurableClientAttribute` .net-kenmerk.

> [!NOTE]
> De `[DurableClientAttribute]` kan ook worden gebruikt om een binding te maken met de [Orchestration-client](#orchestration-client).

Als u script talen (bijvoorbeeld *. CSX* -of *. js* -bestanden) gebruikt voor ontwikkeling, wordt de entiteit trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van *function.jsop*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub` -Wordt gebruikt in scenario's waarbij meerdere functie-apps hetzelfde opslag account delen, maar van elkaar moeten worden geïsoleerd. Als u niets opgeeft, wordt de standaard waarde van `host.json` gebruikt. Deze waarde moet overeenkomen met de waarde die wordt gebruikt door de functies van de doel entiteit.
* `connectionName` -De naam van een app-instelling die een opslag account bevat connection string. Het opslag account dat door deze connection string wordt weer gegeven, moet hetzelfde zijn als de locatie die wordt gebruikt door de functies van de doel entiteit. Als dat niet is opgegeven, wordt het standaard-opslag account connection string voor de functie-app gebruikt.

> [!NOTE]
> In de meeste gevallen is het raadzaam om de optionele eigenschappen te weglaten en het standaard gedrag te vertrouwen.

### <a name="entity-client-usage"></a>Client gebruik van entiteit

In .NET functions maakt u doorgaans een binding aan `IDurableEntityClient` . Dit biedt volledige toegang tot alle client-api's die door duurzame entiteiten worden ondersteund. U kunt ook een binding maken met de `IDurableOrchestrationClient` Interface, waarmee u toegang hebt tot client-api's voor zowel entiteiten als Orchestration. Api's op het client object zijn onder andere:

* **ReadEntityStateAsync \<T>**: Hiermee wordt de status van een entiteit gelezen. Het retourneert een antwoord dat aangeeft of de doel entiteit bestaat en zo ja, wat de status is.
* **SignalEntityAsync**: Hiermee verzendt u een bericht in één richting naar een entiteit, waarna wordt gewacht tot deze in de wachtrij is geplaatst.
* **ListEntitiesAsync**: query's voor de status van meerdere entiteiten. Entiteiten kunnen worden opgevraagd met behulp van de *naam* en de tijd van de *laatste bewerking*.

Het is niet nodig om de doel entiteit te maken voordat u een signaal verzendt: de status van de entiteit kan worden gemaakt vanuit de entiteit functie die het signaal verwerkt.

> [!NOTE]
> Het is belang rijk om te begrijpen dat de ' signalen ' die van de client zijn verzonden, eenvoudigweg in de wachtrij worden geplaatst, zodat ze op een later tijdstip asynchroon kunnen worden verwerkt. In het bijzonder `SignalEntityAsync` retourneert de waarde meestal voordat de entiteit de bewerking start en is het niet mogelijk om de retour waarde terug te halen of uitzonde ringen te observeren. Als sterkere garanties vereist zijn (bijvoorbeeld voor werk stromen), moeten *Orchestrator-functies* worden gebruikt, die kunnen wachten tot de bewerking van de entiteit is voltooid, en kunnen retour waarden verwerken en uitzonde ringen observeren.

### <a name="example-client-signals-entity-directly---c"></a>Voor beeld: client geeft entiteit rechtstreeks-C #

Hier volgt een voor beeld van een door de wachtrij geactiveerde functie die een counter-entiteit aanroept.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="example-client-signals-entity-via-interface---c"></a>Voor beeld: client signaleert entiteit via interface-C #

Waar mogelijk wordt u aangeraden om [toegang te krijgen tot entiteiten via interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , omdat het meer type controle biedt. Stel bijvoorbeeld dat de `Counter` entiteit die eerder een `ICounter` interface heeft genoemd, als volgt heeft geïmplementeerd:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Client code kan vervolgens `SignalEntityAsync<ICounter>` worden gebruikt om een type veilige proxy te genereren:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

De `proxy` para meter is een dynamisch gegenereerd exemplaar van `ICounter` , waarmee de aanroep naar `Add` de equivalente (niet-getypte) aanroep wordt omgezet in `SignalEntityAsync` .

> [!NOTE]
> De `SignalEntityAsync` api's vertegenwoordigen eenrichtings bewerkingen. Als een entiteit interfaces retourneert `Task<T>` , is de waarde van de `T` para meter altijd Null of `default` .

Het is met name niet zinvol om de bewerking te Signa leren `Get` , omdat er geen waarde wordt geretourneerd. In plaats daarvan kunnen clients gebruiken `ReadStateAsync` om rechtstreeks toegang te krijgen tot de status van het item, of kan een Orchestrator-functie worden gestart die de bewerking aanroept `Get` .

### <a name="example-client-signals-entity---javascript"></a>Voor beeld: client signaleert entiteit-java script

Hier volgt een voor beeld van een door de wachtrij geactiveerde functie waarmee de entiteit Counter in Java script wordt gesignaleerd.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Duurzame entiteiten zijn beschikbaar in JavaScript vanaf versie **1.3.0** van het npm-pakket `durable-functions`.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.jsop instellingen

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ingebouwde HTTP API-referentie voor exemplaar beheer](durable-functions-http-api.md)
