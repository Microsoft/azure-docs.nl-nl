---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: dbb32aa0cb61024c3d59879775fe73801d2b9668
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214261"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTimeUtc": ""
            }
        }
    }
}  
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|batchCheckpointFrequency|1|Het aantal gebeurtenisbatches dat moet worden verwerkt voordat een controlepunt voor een EventHub-cursor wordt gemaakt.|
|eventProcessorOptions/maxBatchSize|10|Het maximale gebeurtenisaantal dat per ontvangstlus wordt ontvangen.|
|eventProcessorOptions/prefetchCount|300|Het standaardaantal voor vooraf ophalen dat wordt gebruikt door de onderliggende `EventProcessorHost`. De minimaal toegestane waarde is 10.|
|initialOffsetOptions/type|fromStart|De locatie in de gegevensstroom van de gebeurtenis waar de verwerking moet worden gestart als een controlepunt niet aanwezig is in de opslag. De beschikbare opties zijn `fromStart` , `fromEnd` en `fromEnqueuedTime`. `fromEnd` verwerkt nieuwe gebeurtenissen die in de wachtrij zijn geplaatst nadat de functie-app werd uitgevoerd. Dit is van toepassing op alle partities.  Raadpleeg de [documentatie over EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet) voor meer informatie.|
|initialOffsetOptions/enqueuedTimeUtc|N.v.t.| Hiermee wordt de wachtrijtijd aangegeven van de gebeurtenis in de gegevensstroom waar de verwerking moet beginnen. Wanneer `initialOffsetOptions/type` is geconfigureerd als `fromEnqueuedTime`, is deze instelling verplicht. Ondersteunt tijd in alle indelingen die worden ondersteund door [DateTime.Parse()](/dotnet/standard/base-types/parsing-datetime), zoals  `2020-10-26T20:31Z`. U kunt het best ook een tijdzone opgeven. Als er geen tijdzone is opgegeven, gebruikt Functions de lokale tijdzone van de computer waarop de functie-app wordt uitgevoerd. Als u Azure gebruikt, is dat UTC. Raadpleeg de [documentatie over EventProcessorOptions](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet) voor meer informatie.|
> [!NOTE]
> Zie [Naslaginformatie over host.json voor Azure Functions](../articles/azure-functions/functions-host-json.md) voor naslaginformatie over host.json in Azure Functions 2.x en hoger.

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|maxBatchSize|64|Het maximale gebeurtenisaantal dat per ontvangstlus wordt ontvangen.|
|prefetchCount|n.v.t.|Het standaardaantal voor vooraf ophalen dat wordt gebruikt door de onderliggende `EventProcessorHost`.| 
|batchCheckpointFrequency|1|Het aantal gebeurtenisbatches dat moet worden verwerkt voordat een controlepunt voor een EventHub-cursor wordt gemaakt.| 

> [!NOTE]
> Zie [Naslaginformatie over host.json voor Azure Functions 1.x](../articles/azure-functions/functions-host-json-v1.md) voor naslaginformatie over host.json voor Azure Functions 1.x.
