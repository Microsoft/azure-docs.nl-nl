---
title: Testen van Azure Durable Functions-eenheid
description: Meer informatie over het testen van de eenheids Durable Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 7786a0a2e2d31086e1938b70e63fe2374e16fe7f
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601353"
---
# <a name="durable-functions-unit-testing"></a>Testen van Durable Functions eenheid

Eenheids tests is een belang rijk onderdeel van moderne software ontwikkelings procedures. Eenheids tests verifiëren het gedrag van bedrijfs logica en beveiligen tegen het introduceren van onopgemerkte wijzigingen in de toekomst. Durable Functions kan gemakkelijk groeien in complexiteit zodat er door het introduceren van de eenheids tests geen wijzigingen kunnen worden opgesplitst. In de volgende secties wordt uitgelegd hoe u de drie functie typen-Orchestration-client, Orchestrator en activiteit functies test eenheid testen.

> [!NOTE]
> Dit artikel bevat richt lijnen voor het testen van eenheden voor Durable Functions-apps die zijn gericht op Durable Functions 1. x. Het is nog niet bijgewerkt naar het account voor wijzigingen die zijn aangebracht in Durable Functions 2. x. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

## <a name="prerequisites"></a>Vereisten

Voor de voor beelden in dit artikel is kennis van de volgende concepten en frameworks vereist:

* Het testen van modules

* Durable Functions

* [xUnit](https://github.com/xunit/xunit) -test framework

* [MOQ](https://github.com/moq/moq4) : Framework model

## <a name="base-classes-for-mocking"></a>Basis klassen voor het aftrekken

De deprototypen wordt ondersteund via drie abstracte klassen in Durable Functions 1. x:

* `DurableOrchestrationClientBase`

* `DurableOrchestrationContextBase`

* `DurableActivityContextBase`

Deze klassen zijn basis klassen voor `DurableOrchestrationClient` , `DurableOrchestrationContext` en `DurableActivityContext` die de Orchestration-client, Orchestrator en activiteiten methoden definiëren. Met de-modellen wordt het verwachte gedrag voor basis klassen methoden ingesteld, zodat de eenheids test de bedrijfs logica kan controleren. Er is een werk stroom met twee stappen voor het testen van de bedrijfs logica in de Orchestration-client en Orchestrator:

1. Gebruik de basis klassen in plaats van de concrete implementatie bij het definiëren van de hand tekeningen van de Orchestration-client en Orchestrator-functie.
2. In de eenheids tests wordt het gedrag van de basis klassen gesimuleerd en wordt de bedrijfs logica gecontroleerd.

Meer informatie vindt u in de volgende alinea's voor het testen van functies die gebruikmaken van de Orchestration-client binding en de Orchestrator-trigger binding.

## <a name="unit-testing-trigger-functions"></a>Activerings functies voor eenheids tests

In deze sectie valideert de eenheids test de logica van de volgende HTTP-activerings functie voor het starten van nieuwe integraties.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

De eenheids test taak wordt uitgevoerd om de waarde van de `Retry-After` header te controleren die is opgenomen in de nettolading van de reactie. De eenheids test maakt dus een aantal `DurableOrchestrationClientBase` methoden voor een voorspelbaar gedrag te zien.

Eerst is een model van de basis klasse vereist `DurableOrchestrationClientBase` . De Modeler kan een nieuwe klasse zijn die wordt geïmplementeerd `DurableOrchestrationClientBase` . Het gebruik van een model raamwerk zoals [MOQ](https://github.com/moq/moq4) vereenvoudigt echter het proces:

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

Vervolgens `StartNewAsync` wordt de methode gebruikt om een bekende exemplaar-id te retour neren.

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

De volgende is gesimuleerd `CreateCheckStatusResponse` om altijd een leeg HTTP 200-antwoord te retour neren.

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
            Headers =
            {
                RetryAfter = new RetryConditionHeaderValue(TimeSpan.FromSeconds(10))
            }
        });
```

`ILogger` wordt ook gemodeleerd:

```csharp
    // Mock ILogger
    var loggerMock = new Mock<ILogger>();
```  

De `Run` methode wordt nu aangeroepen vanuit de eenheids test:

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object,
        functionName,
        loggerMock.Object);
 ```

 De laatste stap bestaat uit het vergelijken van de uitvoer met de verwachte waarde:

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

Na het combi neren van alle stappen, heeft de eenheids test de volgende code:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>Orchestrator-functies voor het testen van eenheden

Orchestrator-functies zijn nog interessanter voor het testen van de eenheid omdat ze meestal veel meer bedrijfs logica hebben.

In dit gedeelte wordt de uitvoer van de Orchestrator-functie door de eenheids tests gevalideerd `E1_HelloSequence` :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

De code voor de eenheids test wordt gestart met het maken van een model:

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

Vervolgens worden de aanroepen van de activiteit methode gesimuleerd:

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

De volgende methode wordt aangeroepen door de eenheids test `HelloSequence.Run` :

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

En ten slotte wordt de uitvoer gevalideerd:

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

Na het combi neren van alle stappen, heeft de eenheids test de volgende code:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>Activiteiten functies voor eenheids tests

Activiteit functies kunnen als eenheid worden getest op dezelfde manier als niet-duurzame functies.

In deze sectie wordt met de eenheids test het gedrag van de `E1_SayHello` activiteit functie gevalideerd:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

En de eenheids tests controleren de indeling van de uitvoer. De eenheids tests kunnen de parameter typen rechtstreeks of model `DurableActivityContextBase` klasse gebruiken:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Meer informatie over MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
