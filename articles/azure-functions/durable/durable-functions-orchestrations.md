---
title: Duurzame indelingen - Azure Functions
description: Inleiding tot de indelingsfunctie voor Azure Durable Functions.
author: cgillum
ms.topic: overview
ms.date: 09/08/2019
ms.author: azfuncdf
ms.openlocfilehash: ba314963058389e171601407ff00411049eecd45
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845417"
---
# <a name="durable-orchestrations"></a>Duurzame indelingen

Durable Functions is een uitbreiding op [Azure Functions](../functions-overview.md). U kunt een *orchestratorfunctie* gebruiken om de uitvoering van andere duurzame functies in een functie-app in te delen. Orchestratorfuncties hebben de volgende kenmerken:

* Met orchestratorfuncties worden functiewerkstromen gedefinieerd met behulp van procedurele code. Er zijn geen declaratieve schema's of ontwerpers nodig.
* Orchestratorfuncties kunnen op synchrone en asynchrone wijze andere duurzame functies aanroepen. Uitvoer van aangeroepen functies kan betrouwbaar worden opgeslagen in lokale variabelen.
* Orchestratorfuncties zijn duurzaam en betrouwbaar. Tijdens de uitvoering worden automatisch controlepunten gemaakt wanneer de functie wacht of onderbroken wordt. Er gaat nooit lokale status verloren wanneer het proces recyclet of de VM opnieuw wordt opgestart.
* Orchestratorfuncties kunnen langdurig worden uitgevoerd. De totale levensduur van een *indelingsinstantie* kan seconden, dagen, maanden of oneindig zijn.

In dit artikel vindt u een overzicht van orchestratorfuncties en hoe ze kunnen bijdragen tot het oplossen van verschillende problemen bij het ontwikkelen van apps. Als u nog niet bekend bent met de typen functies die beschikbaar zijn in een Durable Functions-app, leest u eerst het artikel [Durable Function-typen](durable-functions-types-features-overview.md).

## <a name="orchestration-identity"></a>Identiteit van indelingen

Elk *instantie* van een indeling heeft een *instantie-id*. Standaard is elke instantie-id een automatisch gegenereerde, unieke id. Instantie-id's kunnen echter ook een door de gebruiker gegenereerde tekenreekswaarde zijn. Elke instantie-id van de indeling moet uniek zijn binnen een [taakhub](durable-functions-task-hubs.md).

Hier volgen enkele regels voor instantie-id's:

* Instantie-id's bestaan uit 1 tot 256 tekens.
* Instantie-id's mogen niet beginnen met `@`.
* Instantie-id's mogen de tekens `/`, `\`, `#` of `?` niet bevatten.
* Instantie-id's mogen geen besturingstekens bevatten.

> [!NOTE]
> Het wordt in het algemeen aangeraden om waar mogelijk automatisch gegenereerde instantie-id's te gebruiken. Door de gebruiker gegenereerde instantie-id's zijn bedoeld voor scenario's waarbij een een-op-een-toewijzing bestaat tussen een indelingsinstantie en een bepaalde externe toepassingsspecifieke entiteit, zoals een aankooporder of een document.

De instantie-id van een indeling is een vereiste parameter voor de meeste [beheerbewerkingen voor instanties](durable-functions-instance-management.md). Ze zijn ook belangrijk voor diagnostische gegevens, zoals het [zoeken in traceringsgegevens van indelingen](durable-functions-diagnostics.md#application-insights) in Application Insights voor probleemoplossing of analyses. Daarom is het raadzaam om gegenereerde instantie-id's op te slaan op een externe locatie (bijvoorbeeld een database of een toepassingslogboek), waar u ze later eenvoudig kunt naslaan.

## <a name="reliability"></a>Betrouwbaarheid

Orchestratorfuncties behouden op betrouwbare wijze de status van de uitvoering door gebruik te maken van een ontwerppatroon in de vorm van [gebeurtenisbronnen](/azure/architecture/patterns/event-sourcing). In plaats van de huidige status van een indeling rechtstreeks op te slaan, gebruikt het duurzame taakframework een archief waaraan alleen iets kan worden toegevoegd (alleen-toevoegenarchief) om de volledige reeks acties vast te leggen die door de functie-indeling wordt uitgevoerd. Een alleen-toevoegenarchief heeft veel voordelen ten opzichte van het 'dumpen' van de volledige status tijdens runtime. Voordelen zijn onder andere betere prestaties, schaalbaarheid en reactiesnelheid. U krijgt ook consistentie van gebeurtenissen voor transactionele gegevens en volledige audittrails en een volledige geschiedenis. Betrouwbare compenserende acties worden door de audittrails ondersteund.

Durable Functions maakt op transparante wijze gebruik van gebeurtenisbronnen. Op de achtergrond geeft de operator `await` (C#) of `yield` (JavaScript/Python) in een orchestratorfunctie de controle over de orchestrator-thread terug aan de dispatcher van het duurzame taakframework. De dispatcher voert vervolgens nieuwe acties door die de orchestratorfunctie voor opslag heeft gepland (zoals het aanroepen van een of meer onderliggende functies of het plannen van een duurzame timer). De transparante doorvoeractie wordt toegevoegd aan de uitvoeringsgeschiedenis van de indelingsinstantie. De geschiedenis wordt opgeslagen in een opslagtabel. Met de doorvoeractie worden vervolgens berichten toegevoegd aan een wachtrij om het feitelijke werk te plannen. Op dit moment kan de orchestratorfunctie uit het geheugen worden verwijderd.

Wanneer een indelingsfunctie meer werk heeft gekregen (bijvoorbeeld wanneer er een antwoordbericht wordt ontvangen of er een duurzame timer verloopt), wordt de volledige functie door de orchestrator geactiveerd en vanaf het begin opnieuw uitgevoerd om de lokale status opnieuw op te bouwen. Als de code tijdens het opnieuw afspelen een functie wil aanroepen (of andere asynchrone werkzaamheden wil uitvoeren), wordt de uitvoeringsgeschiedenis van de huidige indeling door het duurzame taakframework geraadpleegd. Als wordt vastgesteld dat de [activiteitsfunctie](durable-functions-types-features-overview.md#activity-functions) al is uitgevoerd en een resultaat heeft opgeleverd, wordt het resultaat van die functie opnieuw afgespeeld en blijft de orchestratorcode actief. Het opnieuw afspelen gaat door totdat de functiecode wordt voltooid of totdat er nieuw asynchroon werk is gepland.

> [!NOTE]
> De code van de orchestratorfunctie moet *deterministisch* zijn opdat het nieuwe afspeelpatroon correct en betrouwbaar kan functioneren. Zie het onderwerp [Codebeperkingen voor orchestratorfuncties](durable-functions-code-constraints.md) voor meer informatie.

> [!NOTE]
> Als een orchestratorfunctie logboekberichten verzendt, kan het gedrag van het opnieuw afspelen ertoe leiden dat er dubbele logboekberichten worden verzonden. Zie het onderwerp [Logboekregistratie](durable-functions-diagnostics.md#app-logging) voor meer informatie over waarom dit gedrag optreedt en hoe u dit kunt omzeilen.

## <a name="orchestration-history"></a>Indelingsgeschiedenis

Het gedrag van gebeurtenisbronnen van het duurzame taakframework is nauw gekoppeld aan de orchestratorfunctiecode die u schrijft. Stel dat u een orchestratorfunctie voor het koppelen van activiteiten hebt, zoals de volgende orchestratorfunctie:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const output = [];
    output.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    output.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    output.push(yield context.df.callActivity("E1_SayHello", "London"));

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return output;
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    result1 = yield context.call_activity('SayHello', "Tokyo")
    result2 = yield context.call_activity('SayHello', "Seattle")
    result3 = yield context.call_activity('SayHello', "London")
    return [result1, result2, result3]

main = df.Orchestrator.create(orchestrator_function)
```
---

Bij elke `await`- of `yield`-instructie (respectievelijk in C# of JavaScript/Python) worden door het duurzame taakframework controlepunten vastgelegd voor de uitvoeringsstatus van de functie in een duurzame opslagback-end (doorgaans Azure Table Storage). Deze status wordt de *indelingsgeschiedenis* genoemd.

### <a name="history-table"></a>Geschiedenistabel

Normaal gesproken wordt door het duurzame taakframework op elk controlepunt het volgende uitgevoerd:

1. De uitvoeringsgeschiedenis wordt opgeslagen in Azure Storage-tabellen.
2. Berichten voor functies die de orchestrator wil aanroepen, worden in de wachtrij geplaatst.
3. Berichten voor de orchestrator zelf &mdash; bijvoorbeeld duurzame timerberichten, worden in de wachtrij geplaatst.

Zodra het controlepunt is voltooid, kan de orchestratorfunctie uit het geheugen worden verwijderd totdat er meer werk te doen.

> [!NOTE]
> Azure Storage biedt geen transactionele garanties voor het opslaan van gegevens in tabelopslag en wachtrijen. Voor het afhandelen van fouten gebruikt de Durable Functions-opslagprovider patronen van het type *gebeurtenisconsistentie*. Deze patronen zorgen ervoor dat er geen gegevens verloren gaan als er midden in een controlepunt een storing optreedt in.

Na voltooiing ziet de geschiedenis van de eerder weergegeven functie er ongeveer uit zoals in de volgende tabel in Azure Table Storage (in de afbeelding voor de duidelijkheid afgekort):

| PartitionKey (InstanceId)                     | EventType             | Tijdstempel               | Invoer | Naam             | Resultaat                                                    | Status |
|----------------------------------|-----------------------|----------|--------------------------|-------|------------------|-----------------------------------------------------------|
| eaee885b | ExecutionStarted      | 2017-05-05T18:45:28.852Z | null  | E1_HelloSequence |                                                           |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:32.362Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:32.670Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:32.670Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.201Z |       |                  | """Hello Tokyo!"""                                        |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.232Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.435Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.435Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.763Z |       |                  | """Hello Seattle!"""                                      |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskScheduled         | 2017-05-05T18:45:34.857Z |       | E1_SayHello      |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:34.857Z |       |                  |                                                           |                     |
| eaee885b | TaskCompleted         | 2017-05-05T18:45:34.919Z |       |                  | """Hello London!"""                                       |                     |
| eaee885b | OrchestratorStarted   | 2017-05-05T18:45:35.032Z |       |                  |                                                           |                     |
| eaee885b | OrchestratorCompleted | 2017-05-05T18:45:35.044Z |       |                  |                                                           |                     |
| eaee885b | ExecutionCompleted    | 2017-05-05T18:45:35.044Z |       |                  | "[""Hello Tokyo!"",""Hello Seattle!"",""Hello London!""]" | Voltooid           |

Enkele opmerkingen over de kolomwaarden:

* **PartitionKey**: Bevat de instantie-id van de indeling.
* **EventType**: Vertegenwoordigt het type gebeurtenis. Dit kan een van de volgende typen zijn:
  * **OrchestrationStarted**: De orchestratorfunctie wordt hervat vanuit een wachttoestand of wordt voor de eerste keer uitgevoerd. Kolom `Timestamp` wordt gebruikt om de deterministische waarde voor de API's `CurrentUtcDateTime` (.NET), `currentUtcDateTime` (JavaScript) en `current_utc_datetime` (Python) in te vullen.
  * **ExecutionStarted**: De orchestratorfunctie is voor de eerste keer gestart. Deze gebeurtenis bevat ook de functie-invoer in kolom `Input`.
  * **TaskScheduled**: Er is een activiteitsfunctie gepland. De naam van de activiteitsfunctie wordt vastgelegd in kolom `Name`.
  * **TaskCompleted**: Een voltooide activiteitsfunctie. Het resultaat van de functie bevindt zich in kolom `Result`.
  * **TimerCreated**: Er is een duurzame timer gemaakt. Kolom `FireAt` bevat de geplande UTC-tijd waarop de timer verloopt.
  * **TimerFired**: Een duurzame timer is geactiveerd.
  * **EventRaised**: Er is een externe gebeurtenis verzonden naar de indelingsinstantie. In kolom `Name` wordt de naam van de gebeurtenis vastgelegd en in kolom `Input` wordt de payload van de gebeurtenis vastgelegd.
  * **OrchestratorCompleted**: De verwachte orchestratorfunctie.
  * **ContinueAsNew**: De orchestratorfunctie is voltooid en opnieuw opgestart met een nieuwe status. Kolom `Result` bevat de waarde, die wordt gebruikt als invoer in de opnieuw gestarte instantie.
  * **ExecutionCompleted**: De orchestratorfunctie is voltooid (of mislukt). De uitvoer van de functie of de foutdetails worden opgeslagen in kolom `Result`.
* **Timestamp**: Het UTC-tijdstempel van de geschiedenisgebeurtenis.
* **Naam**: De naam van de functie die is aangeroepen.
* **Input**: De invoer in JSON-indeling van de functie.
* **Result**: De uitvoer van de functie; dat wil zeggen de retourwaarde.

> [!WARNING]
> Hoewel deze tabel als een hulpprogramma voor foutopsporing kan worden gebruikt, moet u er niet afhankelijk van worden. De tabel kan veranderen naarmate de Durable Functions-uitbreiding zich ontwikkelt.

Telkens wanneer de functie wordt hervat vanuit een `await` (C#) of `yield` (JavaScript/Python), voert het duurzame taakframework de orchestratorfunctie opnieuw uit. Bij elke herhaling wordt de uitvoeringsgeschiedenis geraadpleegd om te bepalen of de huidige asynchrone bewerking is uitgevoerd.  Als de bewerking is uitgevoerd, speelt het framework de uitvoer van die bewerking direct opnieuw af en gaat over naar de volgende `await` (C#) of `yield` (JavaScript/Python). Dit proces gaat door totdat de volledige geschiedenis opnieuw is afgespeeld. Zodra de huidige geschiedenis opnieuw is afgespeeld, zijn de lokale variabelen hersteld naar de vorige waarden.

## <a name="features-and-patterns"></a>Functies en patronen

In de volgende secties worden de functies en patronen van orchestratorfuncties beschreven.

### <a name="sub-orchestrations"></a>Onderliggende indelingen

Orchestratorfuncties kunnen activiteitsfuncties aanroepen, maar ook andere orchestratorfuncties. U kunt bijvoorbeeld een grotere indeling op basis van een bibliotheek van orchestratorfuncties bouwen. U kunt ook meerdere instanties van een orchestratorfunctie parallel uitvoeren.

Zie het artikel [Subindelingen](durable-functions-sub-orchestrations.md) voor meer informatie en voorbeelden.

### <a name="durable-timers"></a>Duurzame timers

Indelingen kunnen *duurzame timers* plannen voor het implementeren van vertragingen of het instellen van time-outverwerking voor asynchrone acties. Gebruik duurzame timers in orchestratorfuncties in plaats van `Thread.Sleep` en `Task.Delay` (C#), `setTimeout()` en `setInterval()` (JavaScript) of `time.sleep()` (Python).

Zie het artikel [Duurzame timers](durable-functions-timers.md) voor meer informatie en voorbeelden.

### <a name="external-events"></a>Externe gebeurtenissen

Orchestratorfuncties kunnen wachten op externe gebeurtenissen om een indelingsinstantie bij te werken. Deze Durable Functions-functie is vaak handig voor het verwerken van een menselijke interactie of andere externe callbacks.

Zie het artikel [Externe gebeurtenissen](durable-functions-external-events.md) voor meer informatie en voorbeelden.

### <a name="error-handling"></a>Foutafhandeling

Orchestratorfuncties kunnen de functies voor foutafhandeling van de programmeertaal gebruiken. Bestaande patronen als `try`/`catch` worden ondersteund in indelingscode.

Orchestratorfuncties kunnen ook beleid voor opnieuw proberen toevoegen aan de activiteits- of suborchestratorfuncties die ze aanroepen. Als een activiteits- of suborchestratorfunctie mislukt met een uitzondering, kan het opgegeven beleid voor opnieuw proberen automatisch worden vertraagd en de uitvoering een bepaald aantal keren opnieuw worden uitgevoerd.

> [!NOTE]
> Als er sprake is van een onverwerkte uitzondering in een orchestratorfunctie, wordt de orchestratorinstantie voltooid met status `Failed`. Een orchestratorinstantie kan niet opnieuw worden uitgevoerd als deze is mislukt.

Zie het artikel [Foutafhandeling](durable-functions-error-handling.md) voor meer informatie en voorbeelden.

### <a name="critical-sections-durable-functions-2x-currently-net-only"></a>Kritieke secties (Durable Functions 2.x, momenteel alleen .NET)

Indelingsinstanties bestaan uit één thread. U hoeft zich dus geen zorgen te maken over racevoorwaarden *binnen* een indeling. Racevoorwaarden zijn echter mogelijk wanneer indelingen met externe systemen communiceren. Voor het beperken van racevoorwaarden bij interactie met externe systemen kunnen orchestratorfuncties *kritieke secties* definiëren met behulp van een `LockAsync`-methode in .NET.

De volgende voorbeeldcode toont een orchestratorfunctie die een kritieke sectie definieert. De kritieke sectie wordt geactiveerd met behulp van de `LockAsync`-methode. Voor deze methode moeten een of meer verwijzingen naar een [duurzame entiteit](durable-functions-entities.md) worden doorgegeven, wat de vergrendelingsstatus duurzaam beheert. De code in de kritieke sectie kan per keer slechts door één instantie van deze indeling worden uitgevoerd.

```csharp
[FunctionName("Synchronize")]
public static async Task Synchronize(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var lockId = new EntityId("LockEntity", "MyLockIdentifier");
    using (await context.LockAsync(lockId))
    {
        // critical section - only one orchestration can enter at a time
    }
}
```

Een of meer duurzame vergrendelingen worden door `LockAsync` verkregen en geretourneerd als een `IDisposable`, wat de kritieke sectie beëindigd (indien verwijderd). Dit `IDisposable`-resultaat kan worden gebruikt in combinatie met een `using`-blok om een syntactische weergave van de kritieke sectie te krijgen. Wanneer een orchestratorfunctie naar een kritieke sectie overgaat, kan slechts één instantie dat codeblok uitvoeren. Alle andere instanties die naar de kritieke sectie willen overgaan, worden geblokkeerd totdat de vorige instantie de kritieke sectie verlaat.

De kritieke sectie is ook handig voor het coördineren van wijzigingen aan duurzame entiteiten. Zie het onderwerp [Duurzame entiteiten - Entiteitscoördinatie](durable-functions-entities.md#entity-coordination) voor meer informatie over kritieke secties.

> [!NOTE]
> Kritieke secties zijn beschikbaar in Durable Functions 2.0 en hoger. Op dit moment wordt deze functie alleen geïmplementeerd door .NET-indelingen.

### <a name="calling-http-endpoints-durable-functions-2x"></a>HTTP-eindpunten aanroepen (Durable Functions 2.x)

Orchestratorfuncties zijn niet toegestaan voor I/O, zoals beschreven in [Codebeperkingen voor orchestratorfuncties](durable-functions-code-constraints.md). Voor deze beperking kan als tijdelijke oplossing de code worden verpakt die I/O in een activiteitsfunctie moet uitvoeren. Indelingen die communiceren met externe systemen gebruiken vaak activiteitsfuncties om HTTP-aanroepen te maken en het resultaat naar de indeling te retourneren.

# <a name="c"></a>[C#](#tab/csharp)

Om dit algemene patroon te vereenvoudigen, kunnen orchestratorfuncties de `CallHttpAsync`-methode gebruiken om rechtstreeks HTTP-API's aan te roepen.

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const url = context.df.getInput();
    var res = yield context.df.callHttp("GET", url);
    if (res.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    url = context.get_input()
    res = yield context.call_http('GET', url)
    if res.status_code >= 400:
        # handing of error code goes here
```
---

Naast het ondersteunen van elementaire voor aanvraag-/antwoordpatronen ondersteunt de methode automatische verwerking van veelvoorkomende asynchrone HTTP 202-navraagpatronen en ondersteunt het tevens verificatie met externe services met behulp van [beheerde identiteiten](../../active-directory/managed-identities-azure-resources/overview.md).

Zie het artikel [HTTP-kenmerken](durable-functions-http-features.md) voor meer informatie en voor meer gedetailleerde voor beelden.

> [!NOTE]
> Het rechtstreeks aanroepen van HTTP-eindpunten vanuit orchestratorfuncties is mogelijk in Durable Functions 2.0 en hoger.

### <a name="passing-multiple-parameters"></a>Meerdere parameters doorgeven

Het is niet mogelijk om rechtstreeks meerdere parameters aan een activiteitsfunctie door te geven. Het wordt aangeraden een matrix met objecten of samengestelde objecten door te geven.

# <a name="c"></a>[C#](#tab/csharp)

In .NET kunt u ook [ValueTuple](/dotnet/csharp/tuples)-objecten gebruiken. In het volgende voorbeeld worden nieuwe functies van [ValueTuple](/dotnet/csharp/tuples) toegevoegd met [C# 7](/dotnet/csharp/whats-new/csharp-7#tuples):

```csharp
[FunctionName("GetCourseRecommendations")]
public static async Task<object> RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string major = "ComputerScience";
    int universityYear = context.GetInput<int>();

    object courseRecommendations = await context.CallActivityAsync<object>(
        "CourseRecommendations",
        (major, universityYear));
    return courseRecommendations;
}

[FunctionName("CourseRecommendations")]
public static async Task<object> Mapper([ActivityTrigger] IDurableActivityContext inputs)
{
    // parse input for student's major and year in university
    (string Major, int UniversityYear) studentInfo = inputs.GetInput<(string, int)>();

    // retrieve and return course recommendations by major and university year
    return new
    {
        major = studentInfo.Major,
        universityYear = studentInfo.UniversityYear,
        recommendedCourses = new []
        {
            "Introduction to .NET Programming",
            "Introduction to Linux",
            "Becoming an Entrepreneur"
        }
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="orchestrator"></a>Orchestrator

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const location = {
        city: "Seattle",
        state: "WA"
    };
    const weather = yield context.df.callActivity("GetWeather", location);

    // ...
};
```

#### <a name="getweather-activity"></a>`GetWeather`-activiteit

```javascript
module.exports = async function (context, location) {
    const {city, state} = location; // destructure properties into variables

    // ...
};
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="orchestrator"></a>Orchestrator

```python
from collections import namedtuple
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    Location = namedtuple('Location', ['city', 'state'])
    location = Location(city='Seattle', state= 'WA')

    weather = yield context.call_activity("GetWeather", location)

    # ...

```
#### <a name="getweather-activity"></a>`GetWeather`-activiteit

```python
from collections import namedtuple

Location = namedtuple('Location', ['city', 'state'])

def main(location: Location) -> str:
    city, state = location
    return f"Hello {city}, {state}!"
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Codebeperkingen voor orchestrator](durable-functions-code-constraints.md)
