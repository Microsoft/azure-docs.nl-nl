---
title: Duurzame entiteiten - Azure Functions
description: Meer informatie over duurzame entiteiten en hoe u deze kunt gebruiken in de Durable Functions-extensie voor Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: c898444659c2ce071163e9ab774a4534f8c51a9c
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632047"
---
# <a name="entity-functions"></a>Entiteitsfuncties

Met entiteitsfuncties worden bewerkingen gedefinieerd voor het lezen en bijwerken van kleine stukjes status, ook wel *duurzame entiteiten* genoemd. Entiteitsfuncties zijn, net zoals orchestratorfuncties, functies met een speciaal type trigger: de *entiteitstrigger*. In tegenstelling tot orchestratorfuncties, wordt met entiteitsfuncties de status van een entiteit expliciet beheerd, in plaats van de status impliciet te bepalen via de controlestroom.
Entiteiten bieden een manier om toepassingen uit te schalen door het werk over een groot aantal entiteiten te verdelen, elk met een status van bescheiden grootte.

> [!NOTE]
> Entiteits functies en gerelateerde functionaliteit zijn alleen beschikbaar in [Durable Functions 2,0](durable-functions-versions.md#migrate-from-1x-to-2x) en hoger. Ze worden momenteel ondersteund in .NET, java script en python.

## <a name="general-concepts"></a>Algemene concepten

Entiteiten gedragen zich een beetje als kleine services die communiceren via berichten. Elke entiteit heeft een unieke id en een interne status (indien aanwezig). Net zoals services of objecten voeren entiteiten bewerkingen uit wanneer daarom wordt gevraagd. Wanneer een bewerking wordt uitgevoerd, kan de interne status van de entiteit worden bijgewerkt. Er kunnen ook externe services worden aanroepen en er kan worden gewacht op een reactie. Entiteiten communiceren met andere entiteiten, orchestrations en clients door gebruik te maken van berichten die impliciet worden verzonden via betrouwbare wachtrijen. 

Om conflicten te voorkomen, worden alle bewerkingen op een entiteit serieel (dat wil zeggen, een voor een) uitgevoerd.

> [!NOTE]
> Wanneer een entiteit wordt aangeroepen, wordt de payload verwerkt om te worden voltooid en wordt vervolgens een nieuwe uitvoering gepland om te activeren zodra toekomstige invoer arriveren. Als gevolg hiervan kunnen de uitvoerings logboeken van de entiteit een extra uitvoering tonen na elke entiteit aanroep; Dit wordt verwacht.

### <a name="entity-id"></a>Entiteits-id
Entiteiten worden geopend via een unieke id, de *entiteits-id*. Een entiteits-id bestaat uit een paar tekenreeksen waarmee een exemplaar van een entiteit uniek wordt geïdentificeerd. Het bestaat uit:

* Een **entiteitsnaam**, een naam die het soort entiteit aanduidt. Een voorbeeld hiervan is 'Counter' (teller). Deze naam moet overeenkomen met de naam van de entiteitsfunctie waarmee de entiteit wordt geïmplementeerd. De naam is niet hoofdlettergevoelig.
* Een **entiteitssleutel**, een tekenreeks waarmee de entiteit uniek wordt geïdentificeerd onder alle andere entiteiten met dezelfde naam. Een voorbeeld hiervan is een GUID.

Zo kan de entiteit `Counter` worden gebruikt om de score in een online spel bij te houden. Elk exemplaar van het spel heeft een unieke entiteits-id, zoals `@Counter@Game1` en `@Counter@Game2`. Voor alle bewerkingen die gericht zijn op een bepaalde entiteit, moet een entiteits-id als parameter worden opgegeven.

### <a name="entity-operations"></a>Entiteitsbewerkingen ###

Als u een bewerking voor een entiteit wilt aanroepen, geeft u het volgende op:

* **Entiteits-id** van de doelentiteit.
* **Bewerkingsnaam**, een tekenreeks die aangeeft welke bewerking moet worden uitgevoerd. De entiteit `Counter` kan bijvoorbeeld de bewerkingen `add`, `get` en `reset` ondersteunen.
* **Invoer voor bewerking**, een optionele invoerparameter voor de bewerking. De bewerking 'add' (toevoegen) kan bijvoorbeeld een geheel getal als invoer hebben.
* **Geplande tijd**, een optionele parameter voor het opgeven van de uitvoeringstijd van de bewerking. Een bewerking kan bijvoorbeeld betrouwbaar worden gepland om over enkele dagen te worden uitgevoerd.

Bewerkingen kunnen een waarde of een fout als resultaat retourneren, zoals een JavaScript-fout of een .NET-uitzondering. Een resultaat of fout kan worden waargenomen door orchestrations waardoor de bewerking is aangeroepen.

Een entiteitsbewerking kan ook de status van de entiteit maken, lezen, bijwerken en verwijderen. De status van de entiteit wordt altijd persistent bewaard in de opslag.

## <a name="define-entities"></a>Entiteiten definiëren

Momenteel zijn er twee afzonderlijke API's voor het definiëren van entiteiten:

**Op functies gebaseerde syntaxis**, waarbij entiteiten worden weergegeven als functies en bewerkingen expliciet worden verzonden door de toepassing. Deze syntaxis is handig voor entiteiten met een eenvoudige status, weinig bewerkingen of een dynamische set bewerkingen, zoals in toepassingsframeworks. Deze syntaxis kan lastig te onderhouden zijn, omdat typefouten niet worden gedetecteerd tijdens het compileren.

**Op klassen gebaseerde syntaxis (alleen .NET)** , waarbij entiteiten en bewerkingen worden vertegenwoordigd door klassen en methoden. Deze syntaxis produceert eenvoudiger leesbare code en maakt het mogelijk om bewerkingen op een typebeveiligde manier aan te roepen. De op klassen gebaseerde syntaxis is een dunne laag boven op de op functies gebaseerde syntaxis, zodat beide varianten door elkaar kunnen worden gebruikt in dezelfde toepassing.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Voorbeeld: Op functies gebaseerde syntaxis - C#

De volgende code is een voorbeeld van een eenvoudige `Counter`-entiteit die als een duurzame functie is geïmplementeerd. Deze functie definieert drie bewerkingen, `add`, `reset` en `get`, die zijn gericht op de status van een geheel getal.

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Zie [Op functies gebaseerde syntaxis](durable-functions-dotnet-entities.md#function-based-syntax) voor meer informatie over op functies gebaseerde syntaxis en hoe u deze kunt gebruiken.

### <a name="example-class-based-syntax---c"></a>Voorbeeld: Op klassen gebaseerde syntaxis - C#

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

De status van deze entiteit is een object van het type `Counter`, dat een veld bevat waarin de huidige waarde van de teller wordt opgeslagen. Als u dit object in de opslag wilt behouden, wordt het geserialiseerd en gedeserialiseerd door de [Json.NET](https://www.newtonsoft.com/json)-bibliotheek. 

Zie [Entiteitsklassen definiëren](durable-functions-dotnet-entities.md#defining-entity-classes) voor meer informatie over de op klassen gebaseerde syntaxis en hoe u deze kunt gebruiken.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Voorbeeld: JavaScript-entiteit

Duurzame entiteiten zijn beschikbaar in JavaScript vanaf versie **1.3.0** van het npm-pakket `durable-functions`. De volgende code is de `Counter`-entiteit die als een duurzame functie is geïmplementeerd en is geschreven in JavaScript.

**Counter/function.json**
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

**Counter/index.js**
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
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>Voor beeld: python-entiteit

De volgende code is de `Counter` entiteit die is geïmplementeerd als een duurzame functie die in python is geschreven.

**Counter/function.json**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Counter/__init__. py**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)


main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>Toegang tot entiteiten

U kunt toegang krijgen tot entiteiten via eenrichtings- of tweerichtingscommunicatie. De volgende terminologie maakt onderscheid tussen de volgende twee vormen van communicatie: 

* Bij het **aanroepen** van een entiteit wordt tweerichtingscommunicatie (round-trip) gebruikt. U verzendt een bewerkingsbericht naar de entiteit en wacht vervolgens op het antwoordbericht voordat u doorgaat. In het antwoordbericht kan een waarde of een fout als resultaat worden geretourneerd, zoals een JavaScript-fout of een .NET-uitzondering. Het resultaat of de fout wordt vervolgens waargenomen door de oproepende functie.
* Bij het **signaleren** van een entiteit wordt eenrichtingscommunicatie (fire and forget) gebruikt. U verzendt een bewerkingsbericht, maar u hoeft niet te wachten op een antwoord. Het bericht wordt gegarandeerd uiteindelijk bezorgd, maar de afzender weet niet wanneer en er wordt geen waarde of fout als resultaat geretourneerd.

Entiteiten zijn toegankelijk via clientfuncties, orchestratorfuncties of entiteitsfuncties. Niet alle vormen van communicatie worden ondersteund in elke context:

* Vanuit clients kunt u een signaal naar entiteiten verzenden en de status van de entiteit lezen.
* Vanuit orchestrations kunt u een signaal naar entiteiten verzenden en entiteiten aanroepen.
* Vanuit entiteiten kunt u een signaal naar entiteiten verzenden.

In de volgende voorbeelden ziet u de verschillende manieren waarop u toegang tot entiteiten kunt krijgen.

### <a name="example-client-signals-an-entity"></a>Voorbeeld: Client signaleert een entiteit

Als u toegang wilt tot entiteiten vanuit een gewone Azure-functie, ook wel een clientfunctie genoemd, gebruikt u de [clientbinding voor de entiteit](durable-functions-bindings.md#entity-client). In het volgende voorbeeld ziet u een door de wachtrij geactiveerde functie waarmee een signaal naar een entiteit wordt verzonden met behulp van deze binding.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> In de volgende voorbeelden wordt de syntaxis met losse typen voor toegang tot entiteiten weergegeven. Over het algemeen raden we u aan om toegang tot entiteiten [via interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) te gebruiken, omdat het meer mogelijkheden voor typecontrole biedt.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

De term *signaleren* staat voor het asynchroon aanroepen van de entiteits-API met eenrichtingscommunicatie. Bij een clientfunctie is het niet mogelijk om te weten wanneer de bewerking door de entiteit is verwerkt. Er kunnen ook geen resultaatwaarden of uitzonderingen worden waargenomen. 

### <a name="example-client-reads-an-entity-state"></a>Voorbeeld: Client leest de status van een entiteit

Met een clientfunctie kan ook de status van een entiteit worden opgevraagd, zoals u kunt zien in het volgende voorbeeld:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python biedt momenteel geen ondersteuning voor het lezen van entiteits statussen van een client. Gebruik `callEntity` in plaats daarvan een Orchestrator.

---

Entiteitsstatusquery's worden verzonden naar het Durable-opslagarchief en retourneren de laatste persistente status van de entiteit. Deze status is altijd een 'doorgevoerde' status. Dat wil zeggen dat er nooit een tijdelijke tussenliggende status wordt aangenomen tijdens het uitvoeren van een bewerking. Het is echter mogelijk dat deze status is verlopen ten opzichte van de status in het geheugen van de entiteit. Alleen orchestrations kunnen de status in het geheugen van een entiteit lezen, zoals wordt beschreven in de volgende sectie.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Voorbeeld: Orchestration signaleert een entiteit en roept een entiteit aan

Orchestratorfuncties hebben toegang tot entiteiten met behulp van API's voor de [orchestration-triggerbinding](durable-functions-bindings.md#orchestration-trigger). De volgende voorbeeldcode toont een orchestratorfunctie waarmee een `Counter`-entiteit wordt aangeroepen en gesignaleerd.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript biedt momenteel geen ondersteuning voor het signaleren van een entiteit vanuit een orchestrator. Gebruik in plaats daarvan `callEntity`.

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

Alleen met orchestrations kan een entiteit worden aangeroepen en een reactie worden verkregen, wat een geretourneerde waarde of een uitzondering kan zijn. Clientfuncties die gebruikmaken van de [clientbinding](durable-functions-bindings.md#entity-client) kunnen alleen entiteiten signaleren.

> [!NOTE]
> Het aanroepen van een entiteit vanuit een orchestratorfunctie is vergelijkbaar met het aanroepen van een [activiteitsfunctie](durable-functions-types-features-overview.md#activity-functions) vanuit een orchestratorfunctie. Het belangrijkste verschil is dat entiteitsfuncties duurzame objecten zijn met een adres, wat de entiteits-id is. Entiteitsfuncties bieden ondersteuning voor het opgeven van een bewerkingsnaam. Activiteitsfuncties zijn daarentegen staatloos en kennen niet het concept van bewerkingen.

### <a name="example-entity-signals-an-entity"></a>Voorbeeld: Entiteit signaleert een entiteit

Een entiteitsfunctie kan signalen verzenden naar andere entiteiten, of zelfs naar zichzelf, terwijl er een bewerking wordt uitgevoerd.
Zo kunnen we het voorbeeld van de vorige `Counter`-entiteit aanpassen zodat er een 'mijlpaal bereikt'-signaal wordt verzonden naar een bepaalde controle-entiteit wanneer de teller de waarde 100 bereikt.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Python biedt nog geen ondersteuning voor entiteit-naar-entiteit signalen. Gebruik in plaats daarvan een Orchestrator voor de Signa lering van entiteiten.

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Entiteitscoördinatie (alleen .NET)

Het kan voorkomen dat u bewerkingen tussen meerdere entiteiten moet coördineren. In een toepassing voor bankieren kunt u bijvoorbeeld entiteiten voor afzonderlijke bankrekeningen hebben. Wanneer u geld van de ene naar de andere rekening overmaakt, moet u controleren of het saldo op de bronrekening hoog genoeg is. U moet er ook voor zorgen dat wijzigingen in de bron- en doelrekening op consistente wijze worden verwerkt.

### <a name="example-transfer-funds-c"></a>Voorbeeld: Geld overboeken (C#)

Met de volgende voorbeeldcode wordt geld tussen twee rekeningentiteiten overgeboekt met behulp van een orchestratorfunctie. Voor het coördineren van entiteitswijzigingen moet u de methode `LockAsync` gebruiken om een _kritieke sectie_ in de orchestration te maken.

> [!NOTE]
> Voor het gemak wordt in dit voorbeeld de eerder gedefinieerde `Counter`-entiteit opnieuw gebruikt. In een echte toepassing is het beter om een gedetailleerde entiteit zoals `BankAccount` (Bankrekening) te definiëren.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

In .NET retourneert `LockAsync` `IDisposable`, waarmee de kritieke sectie wordt beëindigd wanneer deze wordt verwijderd. Dit `IDisposable`-resultaat kan worden gebruikt in combinatie met een `using`-blok om een syntactische weergave van de kritieke sectie te krijgen.

In het vorige voorbeeld is met een orchestratorfunctie geld overgemaakt van een bronentiteit naar een doelentiteit. De entiteiten van de bron- en doelrekening zijn vergrendeld door de methode `LockAsync`. Deze vergrendeling heeft ervoor gezorgd dat een andere client de status van de rekeningen niet kan opvragen of wijzigen, totdat de orchestrationlogica de kritieke sectie aan het einde van de `using`-instructie heeft verlaten. Hiermee wordt voorkomen dat er te veel geld van de bronrekening wordt afgeschreven.

> [!NOTE] 
> Wanneer een orchestration wordt beëindigd, normaal of met een fout, worden alle actieve kritieke secties impliciet beëindigd en worden alle vergrendelingen vrijgegeven.

### <a name="critical-section-behavior"></a>Gedrag van kritieke secties

Met de methode `LockAsync` wordt een kritieke sectie in een orchestration gemaakt. Met deze kritieke secties kunt u voorkomen dat andere orchestrations overlappende wijzigingen aanbrengen in een bepaalde set entiteiten. Intern verzendt de `LockAsync`-API vergrendelingsbewerkingen naar de entiteiten en wordt een antwoordbericht geretourneerd als de vergrendeling op elk van deze entiteiten is verkregen. Vergrendelen en ontgrendelen zijn ingebouwde bewerkingen die door alle entiteiten worden ondersteund.

Er zijn geen bewerkingen van andere clients toegestaan op een entiteit die de status 'vergrendeld' heeft. Dit gedrag zorgt ervoor dat elke entiteit slechts kan worden vergrendeld door één orchestrationexemplaar tegelijk. Als een oproepende functie probeert een bewerking aan te roepen voor een entiteit terwijl deze is vergrendeld door een orchestration, wordt die bewerking geplaatst in een wachtrij voor bewerkingen die in behandeling zijn. Wachtende bewerkingen worden pas verwerkt als de vergrendeling is vrijgegeven.

> [!NOTE] 
> Dit gedrag wijkt enigszins af van synchronisatieprimitieven die worden gebruikt in de meeste programmeertalen, zoals de `lock`-instructie in C#. In C# moet bijvoorbeeld de `lock`-instructie door alle threads worden gebruikt om de juiste synchronisatie tussen meerdere threads te garanderen. Voor entiteiten hoeven echter niet alle oproepende functies expliciet een entiteit te vergrendelen. Als een oproepende functie een entiteit vergrendelt, worden alle andere bewerkingen op die entiteit geblokkeerd en achter die vergrendeling in de wachtrij geplaatst.

Vergrendelingen op entiteiten zijn duurzaam zodat ze behouden blijven, zelfs als het uitvoerende proces wordt gerecycled. Vergrendelingen worden intern persistent gemaakt als onderdeel van de duurzame status van een entiteit.

In tegenstelling tot transacties worden in kritieke secties de wijzigingen niet automatisch teruggedraaid als er fouten optreden. In plaats daarvan moet elke foutafhandeling, zoals terugdraaien of opnieuw proberen, expliciet worden gecodeerd, bijvoorbeeld door fouten of uitzonderingen te ondervangen. Dit is een opzettelijke ontwerpkeuze. Het automatisch terugdraaien van de effecten van een orchestration is in het algemeen moeilijk of onmogelijk, omdat met orchestrations mogelijk activiteiten en aanroepen naar externe services worden uitgevoerd die niet kunnen worden teruggedraaid. Ook terugdraaipogingen kunnen mislukken en verdere foutafhandeling vereisen.

### <a name="critical-section-rules"></a>Regels voor kritieke secties

In tegenstelling tot vergrendelingsprimitieven op laag niveau in de meeste programmeertalen, raken kritieke secties *gegarandeerd niet in een impasse*. Om impasses te voorkomen, dwingen we de volgende beperkingen af: 

* Kritieke secties kunnen niet worden genest.
* In kritieke secties kunnen geen suborchestrations worden gemaakt.
* Met kritieke secties kunnen alleen entiteiten worden aangeroepen die zijn vergrendeld.
* Met kritieke secties kan niet dezelfde entiteit worden aangeroepen met meerdere parallelle aanroepen.
* Met kritieke secties kunnen alleen entiteiten worden gesignaleerd die niet zijn vergrendeld.

Eventuele schendingen van deze regels veroorzaken een runtime-fout, zoals `LockingRulesViolationException` in .NET, die een bericht bevat waarin wordt uitgelegd welke regel is verbroken.

## <a name="comparison-with-virtual-actors"></a>Vergelijking met virtuele actors

Veel functies van duurzame entiteiten zijn geïnspireerd op het [actor-model](https://en.wikipedia.org/wiki/Actor_model). Als u al bekend bent met actors, herkent u waarschijnlijk veel van de concepten die in dit artikel worden beschreven. Duurzame entiteiten zijn met name vergelijkbaar met [virtuele actors](https://research.microsoft.com/projects/orleans/), of korrels, zoals gepopulariseerd door het [Orleans-project](http://dotnet.github.io/orleans/). Bijvoorbeeld:

* Duurzame entiteiten zijn adresseerbaar via een entiteits-id.
* Bewerkingen van duurzame entiteiten worden serieel, één voor één, uitgevoerd om racevoorwaarden te voorkomen.
* Duurzame entiteiten worden impliciet gemaakt wanneer ze worden aangeroepen of gesignaleerd.
* Wanneer er geen bewerkingen worden uitgevoerd, worden duurzame entiteiten op de achtergrond uit het geheugen verwijderd.

Er vallen echter enkele belangrijke verschillen op te merken:

* Bij duurzame entiteiten gaat duurzaamheid boven latentie. Ze zijn dus mogelijk niet geschikt voor toepassingen met strikte latentievereisten.
* Duurzame entiteiten hebben geen ingebouwde time-outs voor berichten. In Orleans treedt er altijd een time-out op voor berichten na een configureerbare tijd. De standaardwaarde is 30 seconden.
* Berichten die tussen entiteiten worden verzonden, worden betrouwbaar en op volgorde bezorgd. In Orleans wordt een betrouwbare of bestelde levering ondersteund voor inhoud die via stromen wordt verzonden, maar niet gegarandeerd voor alle berichten tussen korrels.
* Aanvraag-antwoordpatronen in entiteiten zijn beperkt tot orchestrations. Vanuit entiteiten is alleen eenrichtingscommunicatie (ook wel signalering genoemd) toegestaan, zoals in het oorspronkelijke actor-model en niet zoals bij korrels in Orleans. 
* Duurzame entiteiten kunnen niet in een impasse raken. In Orleans kunnen impasses optreden die pas worden opgelost na een time-out van berichten.
* Duurzame entiteiten kunnen worden gebruikt in combinatie met duurzame orchestrations en bieden ondersteuning voor gedistribueerde vergrendelingsmechanismen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lees de handleiding voor ontwikkelaars voor duurzame entiteiten in .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Meer informatie over taakhubs](durable-functions-task-hubs.md)
