---
title: Testen van Azure Durable Functions-eenheid
description: Meer informatie over het testen van de eenheids Durable Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: 89b6419e95b3971b0d272490e19354f300204e1e
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491041"
---
# <a name="durable-functions-unit-testing"></a>Testen van Durable Functions eenheid

Eenheids tests is een belang rijk onderdeel van moderne software ontwikkelings procedures. Eenheids tests verifiëren het gedrag van bedrijfs logica en beveiligen tegen het introduceren van onopgemerkte wijzigingen in de toekomst. Durable Functions kan gemakkelijk groeien in complexiteit zodat er door het introduceren van de eenheids tests geen wijzigingen kunnen worden opgesplitst. In de volgende secties wordt uitgelegd hoe u de drie functie typen-Orchestration-client, Orchestrator en activiteit functies test eenheid testen.

> [!NOTE]
> Dit artikel bevat richt lijnen voor het testen van eenheden voor Durable Functions-apps die zijn gericht op Durable Functions 2. x. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

## <a name="prerequisites"></a>Vereisten

Voor de voor beelden in dit artikel is kennis van de volgende concepten en frameworks vereist:

* Het testen van modules

* Durable Functions

* [xUnit](https://github.com/xunit/xunit) -test framework

* [MOQ](https://github.com/moq/moq4) : Framework model

## <a name="base-classes-for-mocking"></a>Basis klassen voor het aftrekken

De deprototypen wordt ondersteund via de volgende Interface:

* [IDurableOrchestrationClient](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationClient), [IDurableEntityClient](/dotnet/api/microsoft.azure.webjobs.IDurableEntityClient) en [IDurableClient](/dotnet/api/microsoft.azure.webjobs.IDurableClient)

* [IDurableOrchestrationContext](/dotnet/api/microsoft.azure.webjobs.IDurableOrchestrationContext)

* [IDurableActivityContext](/dotnet/api/microsoft.azure.webjobs.IDurableActivityContext)
  
* [IDurableEntityContext](/dotnet/api/microsoft.azure.webjobs.IDurableEntityContext)

Deze interfaces kunnen worden gebruikt met de verschillende triggers en bindingen die door Durable Functions worden ondersteund. Bij het uitvoeren van uw Azure Functions voert de functions-runtime uw functie code uit met een concrete implementatie van deze interfaces. Voor het testen van de eenheid kunt u een gesimuleerde versie van deze interfaces door geven om uw bedrijfs logica te testen.

## <a name="unit-testing-trigger-functions"></a>Activerings functies voor eenheids tests

In deze sectie valideert de eenheids test de logica van de volgende HTTP-activerings functie voor het starten van nieuwe integraties.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

De eenheids test taak wordt uitgevoerd om de waarde van de `Retry-After` header te controleren die is opgenomen in de nettolading van de reactie. De eenheids test maakt dus een aantal `IDurableClient` methoden voor een voorspelbaar gedrag te zien.

Eerst gebruiken we een Modeling Framework ([MOQ](https://github.com/moq/moq4) in dit geval) om het volgende te trekken `IDurableClient` :

```csharp
// Mock IDurableClient
var durableClientMock = new Mock<IDurableClient>();
```

> [!NOTE]
> U kunt interfaces uitdelen door de interface rechtstreeks te implementeren als een klasse, waardoor het proces op verschillende manieren wordt vereenvoudigd. Als er bijvoorbeeld een nieuwe methode wordt toegevoegd aan de interface voor kleine releases, is voor MOQ geen code wijzigingen vereist in tegens telling tot concrete implementaties.

Vervolgens `StartNewAsync` wordt de methode gebruikt om een bekende exemplaar-id te retour neren.

```csharp
// Mock StartNewAsync method
durableClientMock.
    Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
    ReturnsAsync(instanceId);
```

De volgende is gesimuleerd `CreateCheckStatusResponse` om altijd een leeg HTTP 200-antwoord te retour neren.

```csharp
// Mock CreateCheckStatusResponse method
durableClientMock
    // Notice that even though the HttpStart function does not call IDurableClient.CreateCheckStatusResponse() 
    // with the optional parameter returnInternalServerErrorOnFailure, moq requires the method to be set up
    // with each of the optional parameters provided. Simply use It.IsAny<> for each optional parameter
    .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId, returnInternalServerErrorOnFailure: It.IsAny<bool>())
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
    durableClientMock.Object,
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
var durableOrchestrationContextMock = new Mock<IDurableOrchestrationContext>();
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

En de eenheids tests controleren de indeling van de uitvoer. De eenheids tests kunnen de parameter typen rechtstreeks of model `IDurableActivityContext` klasse gebruiken:

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over xUnit](https://xunit.net/docs/getting-started/netcore/cmdline)
> 
> [Meer informatie over MOQ](https://github.com/Moq/moq4/wiki/Quickstart)
