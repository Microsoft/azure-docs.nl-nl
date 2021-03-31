---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673253"
---
In een Java-project worden de bindingen gedefinieerd als bindingsaantekeningen in de functiemethode. Het bestand *function.json* wordt vervolgens automatisch gegenereerd op basis van deze aantekeningen.

Blader naar de locatie van de functiecode onder _src/main/java_, open het projectbestand *Function.java* en voeg de volgende parameter aan de methodedefinitie `run` toe:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Parameter `msg` is van het type [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), dat een verzameling berichten voorstelt die naar een uitvoerbinding wordt geschreven wanneer de functie is voltooid. In dit geval is de uitvoer een opslagwachtrij met de naam `outqueue`. De verbindingsreeks voor het Storage-account wordt ingesteld door de methode `connection`. In plaats van de verbindingsreeks zelf, geeft u de toepassingsinstelling door die de verbindingsreeks voor het Storage-account bevat.

De methodedefinitie `run` moet er nu uitzien als in het volgende voorbeeld:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```