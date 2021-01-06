---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b487dcad83ccbc31adf2d7ec2dd77c490db2c68e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935190"
---
Met Azure Functions kunt u alleen configuratie taken configureren die op een vooraf gebaseerd toegangs punt Lean zijn. De [configuratie-voor beelden op basis van configuraties voor Azure functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) laten zien hoe u [vooraf ontwikkelde helpers kunt](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) gebruiken in uw eigen code of code hoeft te verhandelen en alleen configuratie kunt gebruiken.

## <a name="create-a-replication-task"></a>Een replicatie taak maken
Als u een dergelijke replicatie taak wilt maken, maakt u eerst een nieuwe map onder de projectmap. De naam van de nieuwe map is de naam van de functie, bijvoorbeeld `europeToAsia` of `telemetry` . De naam heeft geen directe correlatie met de Messa ging-entiteiten die worden gebruikt en dient alleen voor u om ze te identificeren. U kunt tien tallen functies in hetzelfde project maken.

Maak vervolgens een `function.json` bestand in de map. Met het bestand wordt de functie geconfigureerd. Begin met de volgende inhoud:

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

In dat bestand moet u drie configuratie stappen uitvoeren die afhankelijk zijn van de entiteiten die u wilt verbinden:

1. [De invoer richting configureren](#configure-the-input-direction)
2. [De uitvoer richting configureren](#configure-the-output-direction)
3. [Het toegangs punt configureren](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>De invoer richting configureren

#### <a name="event-hub-input"></a>Event hub-invoer

Als u gebeurtenissen van een event hub wilt ontvangen, voegt u configuratie-informatie toe aan het bovenste gedeelte in ' bindingen ' die zijn ingesteld op

* **type** -het type ' eventHubTrigger '.
* **verbinding** -de naam van de waarde van de app-instellingen voor de Event Hub-Connection String. 
* **eventHubName** : de naam van de Event hub in de naam ruimte die wordt geïdentificeerd door de Connection String.
* **consumerGroup** : de naam van de Consumer groep. Houd er wel voor dat de naam wordt Inge sloten in procent tekens en verwijst daarom ook naar een waarde voor de app-instellingen.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Invoer wachtrij Service Bus

Als u gebeurtenissen van een Service Bus wachtrij wilt ontvangen, voegt u configuratie-informatie toe aan de bovenste sectie binnen ' bindingen ' die zijn ingesteld

* **type** -het type ' serviceBusTrigger '.
* **verbinding** -de naam van de waarde van de app-instellingen voor de service bus connection string.
* **wachtrijmap** : de naam van de service bus wachtrij binnen de naam ruimte die wordt geïdentificeerd door de Connection String.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Invoer van Service Bus-onderwerp

Als u gebeurtenissen van een Service Bus onderwerp wilt ontvangen, voegt u configuratie-informatie toe aan het bovenste gedeelte in ' bindingen ' die zijn ingesteld op

* **type** -het type ' serviceBusTrigger '.
* **verbinding** -de naam van de waarde van de app-instellingen voor de service bus connection string. Deze waarde moet worden `{FunctionName}-source-connection` gebruikt als u de meegeleverde scripts wilt gebruiken.
* **onderwerpnaam** : de naam van het onderwerp service bus binnen de naam ruimte die wordt geïdentificeerd door de Connection String.
* **subscriptionname** : de naam van het service bus-abonnement op het gegeven onderwerp binnen de naam ruimte die wordt geïdentificeerd door de Connection String.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>De uitvoer richting configureren

#### <a name="event-hub-output"></a>Event hub-uitvoer

Als u gebeurtenissen wilt door sturen naar een event hub, voegt u configuratie-informatie toe aan de onderste sectie binnen ' bindingen ' die zijn ingesteld

* **type** -het type ' eventHub '.
* **verbinding** -de naam van de waarde van de app-instellingen voor de Event Hub-Connection String. 
* **eventHubName** : de naam van de Event hub in de naam ruimte die wordt geïdentificeerd door de Connection String.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Uitvoer van Service Bus wachtrij

Als u gebeurtenissen wilt door sturen naar een Service Bus wachtrij, voegt u configuratie gegevens toe aan de onderste sectie binnen ' bindingen ' die zijn ingesteld

* **type** -het type ' serviceBus '.
* **verbinding** -de naam van de waarde van de app-instellingen voor de service bus connection string. 
* **wachtrijmap** : de naam van de service bus wachtrij binnen de naam ruimte die wordt geïdentificeerd door de Connection String.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Uitvoer van Service Bus-onderwerp

Als u gebeurtenissen wilt door sturen naar een Service Bus onderwerp, voegt u configuratie gegevens toe aan de onderste sectie binnen ' bindingen ' die zijn ingesteld op

* **type** -het type ' serviceBus '.
* **verbinding** -de naam van de waarde van de app-instellingen voor de service bus connection string. 
* **onderwerpnaam** : de naam van het onderwerp service bus binnen de naam ruimte die wordt geïdentificeerd door de Connection String.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Het toegangs punt configureren

De configuratie van het toegangs punt kiest een van de standaard replicatie taken. Als u het project wijzigt `Azure.Messaging.Replication` , kunt u ook taken toevoegen en deze hier raadplegen. Bijvoorbeeld:

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

De volgende tabel geeft u de juiste waarden voor combi Naties van bronnen en doelen:

| Bron      | Doel      | Ingangs punt 
|-------------|-------------|------------------------------------------------------------------------
| Event Hub   | Event Hub   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Event Hub   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Event Hub   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Beleid voor opnieuw proberen

Raadpleeg de [documentatie van Azure functions over nieuwe pogingen](/azure/azure-functions/functions-bindings-error-pages) om het beleid voor opnieuw proberen te configureren. De beleids instellingen die in de projecten in deze opslag plaats worden gekozen, configureren een exponentiële uitstel-strategie met intervallen van vijf seconden tot 5 minuten met oneindige pogingen om gegevens verlies te voor komen.

Bekijk voor Service Bus de sectie [' de ondersteuning voor nieuwe pogingen op het niveau van de trigger gebruiken '](/azure/azure-functions/functions-bindings-error-pages#using-retry-support-on-top-of-trigger-resilience) voor meer informatie over de interactie van triggers en het maximum aantal bezorgingen dat voor de wachtrij is gedefinieerd.

### <a name="build-deploy-and-configure"></a>Bouwen, implementeren en configureren

Terwijl u zich richt op de configuratie, moeten de taken nog steeds een Implementeer bare toepassing bouwen en de Azure Functions hosts zo configureren dat deze alle vereiste gegevens heeft om verbinding te maken met de opgegeven eind punten. 

Dit wordt geïllustreerd samen met herbruikbare scripts in de [op configuratie gebaseerde replicatie voorbeelden voor Azure functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config).

