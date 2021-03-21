---
title: bestand opnemen
description: bestand opnemen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 54ce9438f768e347e306432a1874ab1816a1ae95
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719740"
---
Configuratie-instellingen voor [Durable Functions](../articles/azure-functions/durable/durable-functions-overview.md).

> [!NOTE]
> Alle belangrijke versies van Durable Functions worden ondersteund in alle versies van de Azure Functions-runtime. Het schema van de configuratie host.json is echter iets anders afhankelijk van de versie van de Azure Functions-runtime en de versie van de Durable Functions-extensie die u gebruikt. De volgende voorbeelden zijn voor gebruik met Azure Functions 2.0 en 3.0. Als u gebruikmaakt van Azure Functions 1.0, zijn de beschikbare functies in beide voorbeelden dezelfde, maar moet het gedeelte 'durableTask' van host.json in de hoofdmap van de configuratie host.json worden geplaatst, in plaats van in een veld onder 'extensions'.

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2.x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "useLegacyPartitionManagement": true,
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useAppLease": true,
    "useGracefulShutdown": false
  }
 }
}

```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub",
      "controlQueueBatchSize": 32,
      "partitionCount": 4,
      "controlQueueVisibilityTimeout": "00:05:00",
      "workItemQueueVisibilityTimeout": "00:05:00",
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10,
      "maxQueuePollingInterval": "00:00:30",
      "azureStorageConnectionStringName": "AzureWebJobsStorage",
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "traceInputsAndOutputs": false,
      "logReplayEvents": false,
      "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
      "eventGridKeySettingName":  "EventGridKey",
      "eventGridPublishRetryCount": 3,
      "eventGridPublishRetryInterval": "00:00:30",
      "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
    }
  }
}
```

Namen van taakhubs moeten beginnen met een letter en mogen alleen letters en cijfers bevatten. Als u dit niet opgeeft, wordt de standaard naam van de taakhub voor een functie-app **DurableFunctionsHub**. Zie voor meer informatie [Taakhubs](../articles/azure-functions/durable/durable-functions-task-hubs.md).

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|----------|
|hubName|DurableFunctionsHub|Er kunnen alternatieve namen voor [taakhubs](../articles/azure-functions/durable/durable-functions-task-hubs.md) worden gebruikt om meerdere Durable Functions-toepassingen van elkaar te isoleren, zelfs als ze dezelfde back-end voor opslag gebruiken.|
|controlQueueBatchSize|32|Het aantal berichten dat tegelijkertijd moet worden opgehaald uit de controlewachtrij.|
|controlQueueBufferThreshold| **Verbruiks abonnement**: 32 <br> **Speciaal/Premium-abonnement**: 256 |Het aantal controlewachtrijberichten dat in het geheugen tegelijk kan worden gebufferd, waarna de dispatcher wacht totdat extra berichten uit de wachtrij worden verwijderd.|
|partitionCount |4|Het aantal partities voor de controlewachtrij. Kan een positief geheel getal tussen 1 en 16 zijn.|
|controlQueueVisibilityTimeout |5 minuten|De time-out voor zichtbaarheid van berichten in de controlewachtrij die uit de wachtrij zijn verwijderd.|
|workItemQueueVisibilityTimeout |5 minuten|De time-out voor zichtbaarheid van berichten in de werkitemwachtrij die uit de wachtrij zijn verwijderd.|
|maxConcurrentActivityFunctions | **Verbruiks abonnement**: 10 <br> **Speciaal/Premium-abonnement**: 10x het aantal processors op de huidige computer|Het maximum aantal activiteitenfuncties dat gelijktijdig kan worden verwerkt op één exemplaar van een host.|
|maxConcurrentOrchestratorFunctions | **Verbruiks abonnement**: 5 <br> **Speciaal/Premium-abonnement**: 10x het aantal processors op de huidige computer |Het maximum aantal orchestratorfuncties dat gelijktijdig kan worden verwerkt op één exemplaar van een host.|
|maxQueuePollingInterval|30 seconden|Het polling-interval van de maximale controle- en werkitemwachtrij in de indeling *uu:mm:ss*. Hogere waarden kunnen resulteren in hogere berichtverwerkingslatenties. Lagere waarden kunnen resulteren in hogere opslagkosten omdat er een toename is in opslagtransacties.|
|azureStorageConnectionStringName |AzureWebJobsStorage|De naam van de app-instelling met de Azure Storage-verbindingstekenreeks die wordt gebruikt voor het beheren van de onderliggende Azure Storage-resources.|
|trackingStoreConnectionStringName||De naam van een verbindingstekenreeks die moet worden gebruikt voor de tabellen Geschiedenis en Exemplaren. Als u dit niet opgeeft, wordt de `connectionStringName` (Durable 2.x) of `azureStorageConnectionStringName` (Durable 1.x)-verbinding gebruikt.|
|trackingStoreNamePrefix||Het voorvoegsel dat moet worden gebruikt voor de tabellen Geschiedenis en Exemplaren wanneer `trackingStoreConnectionStringName` is opgegeven. Als deze niet is ingesteld, wordt de standaard voorvoegselwaarde `DurableTask`. Als `trackingStoreConnectionStringName` niet is opgegeven, dan gebruiken de tabellen Geschiedenis en Exemplaren de waarde `hubName` als voorvoegsel en wordt elke instelling voor `trackingStoreNamePrefix` genegeerd.|
|traceInputsAndOutputs |false|Een waarde die aangeeft of de invoer en uitvoer voor functieaanroepen moet worden getraceerd. Het standaardgedrag bij het traceren van functie-uitvoeringsgebeurtenissen is om het aantal bytes in de geserialiseerde invoer en uitvoer voor functieaanroepen op te nemen. Dit gedrag biedt minimale informatie over hoe de invoer en uitvoer eruitzien zonder bloating van de logboeken te veroorzaken of per ongeluk gevoelige informatie weer te geven. Als u deze eigenschap instelt op waar, wordt de volledige inhoud van functie-invoer en -uitvoer vastgelegd door de standaardfunctie logboekregistratie.|
|logReplayEvents|false|Een waarde die aangeeft of indelingsreplay-gebeurtenissen naar Application Insights moeten worden geschreven.|
|eventGridTopicEndpoint ||De URL van een eindpunt met aangepast onderwerp van Azure Event Grid. Wanneer deze eigenschap is ingesteld, worden meldingsgebeurtenissen van de indelings-cyclus naar dit eindpunt gepubliceerd. Deze eigenschap ondersteunt de resolutie van de App-instellingen.|
|eventGridKeySettingName ||De naam van de app-instelling met de sleutel die wordt gebruikt voor de verificatie met het aangepaste onderwerp van Azure Event Grid op `EventGridTopicEndpoint`.|
|eventGridPublishRetryCount|0|Het aantal keren dat het opnieuw moet worden geprobeerd als het publiceren naar het Event Grid-onderwerp mislukt.|
|eventGridPublishRetryInterval|5 minuten|Het interval voor nieuwe pogingen van Event Grid wordt gepubliceerd in de notatie *uu: mm: ss*.|
|eventGridPublishEventTypes||Een lijst met gebeurtenistypen die moeten worden gepubliceerd op Event Grid. Als u niets opgeeft, worden alle gebeurtenistypen gepubliceerd. Toegestane waarden zijn `Started`, `Completed`, `Failed`, `Terminated`.|
|useAppLease|true|Wanneer het is ingesteld op `true`, moeten apps een blob-lease op app-niveau verkrijgen voordat taakhub-berichten worden verwerkt. Zie voor meer informatie de documentatie over [herstel na noodgeval en geo-distributie](../articles/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution.md). Vanaf v2.3.0 beschikbaar.
|useLegacyPartitionManagement|true|Wanneer het is ingesteld op `false`, wordt een algoritme voor partitiebeheer gebruikt die het uitvoeren van dubbele functies vermindert bij het uitschalen.  Vanaf v2.3.0 beschikbaar. De standaardinstelling wordt in een toekomstige versie gewijzigd in `false`.|
|useGracefulShutdown|false|(Preview) Schakel probleemloos afsluiten in om de kans op het afsluiten van de host te verminderen tijdens het verwerken van functies die in uitvoer zijn.|

Veel van deze instellingen zijn voor het optimaliseren van de prestaties. Zie [Prestaties en schaal aanpassen](../articles/azure-functions/durable/durable-functions-perf-and-scale.md) voor meer informatie.