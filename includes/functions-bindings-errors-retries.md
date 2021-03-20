---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/01/2020
ms.author: glenga
ms.openlocfilehash: 2ccff72be66a88b9bf0a5e9eb9c29ade8397804b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96356190"
---
Fouten die zijn opgetreden in een Azure Functions kunnen afkomstig zijn van een van de volgende oorsprongen:

- Gebruik van ingebouwde Azure Functions [Triggers en bindingen](..\articles\azure-functions\functions-triggers-bindings.md)
- Aanroepen naar Api's van onderliggende Azure-Services
- Aanroepen naar REST-eind punten
- Aanroepen van client Bibliotheken, pakketten of Api's van derden

De volgende goede procedures voor het afhandelen van fouten zijn belang rijk om verlies van gegevens of gemiste berichten te voor komen. De aanbevolen procedures voor het afhandelen van fouten zijn onder andere de volgende acties:

- [Application Insights inschakelen](../articles/azure-functions/functions-monitoring.md)
- [Gestructureerde fout afhandeling gebruiken](#use-structured-error-handling)
- [Ontwerpen voor idempotentie](../articles/azure-functions/functions-idempotent.md)
- [Beleid voor opnieuw proberen implementeren](#retry-policies-preview) (indien van toepassing)

### <a name="use-structured-error-handling"></a>Gestructureerde fout afhandeling gebruiken

Vastleggen en vastleggen van fouten is van cruciaal belang voor het controleren van de status van uw toepassing. Het bovenste niveau van een functie code moet een try/catch-blok bevatten. In het blok catch kunt u fouten vastleggen en registreren.

## <a name="retry-policies-preview"></a>Beleid voor opnieuw proberen (voor beeld)

Een beleid voor opnieuw proberen kan worden gedefinieerd voor elke functie voor elk type trigger in uw functie-app.  Het beleid voor opnieuw proberen voert een functie opnieuw uit totdat de uitvoering is geslaagd of totdat het maximum aantal nieuwe pogingen is ontstaan.  Beleid voor opnieuw proberen kan worden gedefinieerd voor alle functies in een app of voor afzonderlijke functies.  Standaard worden berichten van een functie-app niet opnieuw geprobeerd (afgezien van de [specifieke triggers die een beleid voor opnieuw proberen hebben op de trigger bron](#using-retry-support-on-top-of-trigger-resilience)).  Een beleid voor opnieuw proberen wordt geëvalueerd wanneer een uitvoering resulteert in een niet-onderschepte uitzonde ring.  Als best practice moet u alle uitzonde ringen in uw code opvangen en eventuele fouten opnieuw genereren die moeten resulteren in een nieuwe poging.  Event Hubs en Azure Cosmos DB controle punten worden pas geschreven wanneer het beleid voor opnieuw proberen voor de uitvoering is voltooid, wat betekent dat de voortgang van de partitie wordt onderbroken totdat de huidige batch is voltooid.

### <a name="retry-policy-options"></a>Beleids opties voor opnieuw proberen

De volgende opties zijn beschikbaar voor het definiëren van een beleid voor opnieuw proberen.

Het maximale **aantal nieuwe pogingen** is het maximum aantal keren dat een uitvoering opnieuw wordt uitgevoerd voordat er een fout optreedt. Een waarde `-1` om te voor onbepaalde tijd opnieuw te proberen. Het huidige aantal nieuwe pogingen wordt opgeslagen in het geheugen van het exemplaar. Het is mogelijk dat een exemplaar een fout heeft tussen nieuwe pogingen.  Wanneer een exemplaar mislukt tijdens een beleid voor opnieuw proberen, gaat het aantal nieuwe pogingen verloren.  Als er sprake is van exemplaar fouten, kunnen triggers zoals Event Hubs, Azure Cosmos DB en Queue Storage de verwerking hervatten en de batch opnieuw uitvoeren op een nieuw exemplaar, waarbij het aantal nieuwe pogingen opnieuw wordt ingesteld op nul.  Andere triggers, zoals HTTP en timer, worden niet hervat op een nieuw exemplaar.  Dit betekent dat het maximale aantal nieuwe pogingen een beste poging is, en in sommige zeldzame gevallen kan een uitvoering meer dan het maximum doen, of voor triggers zoals HTTP en opnieuw proberen minder dan het maximum.

**Strategie voor opnieuw proberen** bepaalt hoe nieuwe pogingen werken.  Hieronder vindt u twee ondersteunde opties voor opnieuw proberen:

| Optie | Beschrijving|
|---|---|
|**`fixedDelay`**| Een opgegeven hoeveelheid tijd mag verstrijken tussen elke nieuwe poging,|
| **`exponentialBackoff`**| De eerste poging wacht op minimale vertraging. Bij nieuwe pogingen wordt tijd exponentieel toegevoegd aan de eerste duur van elke nieuwe poging, totdat de maximale vertraging wordt bereikt.  Exponentiële back-off voegt een kleine wille keurigheid toe aan vertragingen bij het aantal nieuwe pogingen in scenario's met hoge door voer.|

#### <a name="app-level-configuration"></a>Configuratie van het app-niveau

U kunt een beleid voor opnieuw proberen definiëren voor alle functies in een app [die het `host.json` bestand gebruikt](../articles/azure-functions/functions-host-json.md#retry). 

#### <a name="function-level-configuration"></a>Configuratie van functie niveau

U kunt een beleid voor opnieuw proberen definiëren voor een specifieke functie.  De functie-specifieke configuratie heeft voor rang op de configuratie op app-niveau.

#### <a name="fixed-delay-retry"></a>Nieuwe poging vaste vertraging

# <a name="c"></a>[C#](#tab/csharp)

Voor nieuwe pogingen is NuGet Package [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 vereist

```csharp
[FunctionName("EventHubTrigger")]
[FixedDelayRetry(5, "00:00:10")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:


```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "fixedDelay",
        "maxRetryCount": 4,
        "delayInterval": "00:00:10"
    }
}
```
---

#### <a name="exponential-backoff-retry"></a>Exponentiële uitstel opnieuw proberen

# <a name="c"></a>[C#](#tab/csharp)

Voor nieuwe pogingen is NuGet Package [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 vereist

```csharp
[FunctionName("EventHubTrigger")]
[ExponentialBackoffRetry(5, "00:00:04", "00:15:00")]
public static async Task Run([EventHubTrigger("myHub", Connection = "EventHubConnection")] EventData[] events, ILogger log)
{
// ...
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Dit is het beleid voor opnieuw proberen in het *function.js* bestand:

```json
{
    "disabled": false,
    "bindings": [
        {
            ....
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": 5,
        "minimumInterval": "00:00:10",
        "maximumInterval": "00:15:00"
    }
}
```
---

|function.json-eigenschap  |Kenmerk eigenschap | Beschrijving |
|---------|---------|---------| 
|Pele|n.v.t.|Vereist. De strategie voor opnieuw proberen die moet worden gebruikt. Geldige waarden zijn `fixedDelay` of `exponentialBackoff` .|
|maxRetryCount|n.v.t.|Vereist. Het maximum aantal nieuwe pogingen dat per functie-uitvoering is toegestaan. `-1` betekent dat u voor onbepaalde tijd opnieuw kunt proberen.|
|delayInterval|n.v.t.|De vertraging die wordt gebruikt tussen nieuwe pogingen wanneer de strategie wordt gebruikt `fixedDelay` .|
|minimumInterval|n.v.t.|De minimale vertraging voor nieuwe pogingen bij het gebruik van de `exponentialBackoff` strategie.|
|maximumInterval|n.v.t.|De maximale vertraging voor nieuwe pogingen bij het gebruik van de `exponentialBackoff` strategie.| 

### <a name="retry-limitations-during-preview"></a>De beperkingen voor opnieuw proberen tijdens de preview

- Voor .NET-projecten moet u mogelijk hand matig een versie van [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) >= 3.0.23 ophalen.
- In het verbruiks abonnement kan de app worden geschaald naar nul tijdens het opnieuw proberen van de eind berichten in een wachtrij.
- In het verbruiks abonnement kan de app worden geschaald tijdens het uitvoeren van nieuwe pogingen.  Kies voor de beste resultaten een interval voor opnieuw proberen <= 00:01:00 en <= 5 nieuwe pogingen.

## <a name="using-retry-support-on-top-of-trigger-resilience"></a>Ondersteuning voor nieuwe pogingen gebruiken boven op de trigger tolerantie

Het beleid voor het opnieuw proberen van de functie-app is onafhankelijk van eventuele nieuwe pogingen of toleranties die door de trigger worden geboden.  Het beleid voor opnieuw proberen wordt alleen op de laag van een trigger overschreven tot een nieuwe poging.  Als u bijvoorbeeld Azure Service Bus gebruikt, wordt voor de standaard wachtrij een aantal berichten geleverd van 10.  Het standaard aantal bezorgingen betekent dat na 10 pogingen om een wachtrij bericht te leveren, Service Bus onbestelbaar bericht wordt verzonden.  U kunt een beleid voor opnieuw proberen definiëren voor een functie met een Service Bus trigger, maar de nieuwe pogingen slaagt op de Service Bus bezorgings pogingen.  

Als u bijvoorbeeld de standaard Service Bus leverings aantallen van 10 hebt gebruikt en een beleid voor opnieuw proberen van de functie 5 hebt gedefinieerd.  Het bericht wordt eerst in de wachtrij geplaatst, waarbij het service bus-bezorgings account wordt verhoogd naar 1.  Als het uitvoeren van elke uitvoering is mislukt, wordt dat bericht na vijf pogingen om hetzelfde bericht te activeren, gemarkeerd als afgebroken.  Service Bus het bericht onmiddellijk opnieuw in de wachtrij plaatsen, wordt de functie geactiveerd en wordt het aantal levering verhoogd naar 2.  Ten slotte wordt na 50 tot een aantal pogingen (10 service bus-leveringen * vijf nieuwe pogingen per levering) het bericht afgebroken en wordt er een onbestelbare letter op service bus geactiveerd.

> [!WARNING]
> Het is niet raadzaam om het aantal bezorgingen voor een trigger als Service Bus wacht rijen in te stellen op 1, wat betekent dat het bericht onbestelbaar wordt direct na een nieuwe cyclus van een enkele functie.  Dit komt omdat triggers toleranties met nieuwe pogingen bieden, terwijl het beleid voor nieuwe pogingen het beste is en dit kan leiden tot minder dan het gewenste aantal nieuwe pogingen.

### <a name="triggers-with-additional-resiliency-or-retries"></a>Triggers met extra tolerantie of nieuwe pogingen

Met de volgende triggers worden nieuwe pogingen voor de trigger bron ondersteund:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (wachtrij/onderwerp)](../articles/azure-functions/functions-bindings-service-bus.md)

De meeste pogingen van triggers worden standaard Maxi maal vijf keer aangevraagd. Na de vijfde nieuwe poging wordt door de Azure Queue-opslag een bericht naar een [verontreinigde wachtrij](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#poison-messages)geschreven.  Met de standaard Service Bus wachtrij en onderwerpbeleid wordt een bericht naar een [wachtrij met onbestelbare](../articles/service-bus-messaging/service-bus-dead-letter-queues.md) berichten geschreven na 10 pogingen.
