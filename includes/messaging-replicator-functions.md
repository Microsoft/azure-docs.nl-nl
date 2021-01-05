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
ms.openlocfilehash: 279a00a6146d756e6a518dbf86b88f471d170b3a
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805648"
---
## <a name="what-is-a-replication-task"></a>Wat is een replicatie taak?

Een replicatie taak ontvangt gebeurtenissen van een bron en stuurt deze door naar een doel.
De meeste replicatie taken sturen gebeurtenissen ongewijzigd door en voeren Maxi maal de toewijzing tussen meta gegevens structuren uit als de bron-en doel protocollen anders zijn. 

Replicatie taken zijn in het algemeen stateless, wat betekent dat ze geen status of andere neven effecten delen in opeenvolgende of parallelle uitvoeringen van een taak. Dit geldt ook voor batch verwerking en koppelen, die beide kunnen worden geïmplementeerd boven op de bestaande status van een stroom. 

Dit maakt het verschil tussen replicatie taken en aggregatie taken, die in het algemeen stateful zijn, en zijn het domein van analyse frameworks en-services zoals [Azure stream Analytics](/azure/stream-analytics/stream-analytics-introduction).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Replicatie toepassingen en taken in Azure Functions

In Azure Functions wordt een replicatie taak geïmplementeerd met behulp van een [trigger](/azure/azure-functions/functions-triggers-bindings) waarmee een of meer invoer berichten worden opgehaald uit een geconfigureerde bron en een [uitvoer binding](/azure/azure-functions/functions-triggers-bindings#binding-direction) waarmee berichten die worden gekopieerd van de bron naar een geconfigureerd doel worden doorgestuurd. 

| Trigger  | Uitvoer |
|----------|--------|
| [Trigger voor Azure Event Hubs](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-trigger?tabs=csharp) | [Azure Event hubs-uitvoer binding](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-output?tabs=csharp) |
| [Azure Service Bus trigger](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-trigger?tabs=csharp) | [Uitvoer binding Azure Service Bus](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-output?tabs=csharp)
| [Trigger voor Azure IoT Hub](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-trigger?tabs=csharp) | [Azure IoT Hub-uitvoer binding](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-output?tabs=csharp)
| [Azure Event Grid trigger](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger?tabs=csharp) | [Uitvoer binding Azure Event Grid](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-output?tabs=csharp)
| [Azure Queue Storage-trigger](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp) | [Azure Queue Storage-uitvoer binding](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-output?tabs=csharp)
| [Apache Kafka trigger](https://github.com/azure/azure-functions-kafka-extension) | [Uitvoer binding Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
| [Trigger voor RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ-uitvoer binding](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Azure Notification Hubs-uitvoer binding](https://docs.microsoft.com/azure/azure-functions/functions-bindings-notification-hubs)
||[Uitvoer binding van Azure signalerings service](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service-output?tabs=csharp)
||[Twilio SendGrid-uitvoer binding](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid?tabs=csharp)

Replicatie taken worden in de replicatie toepassing geïmplementeerd met dezelfde implementatie methoden als andere Azure Functions-toepassingen. U kunt meerdere taken in dezelfde toepassing configureren. 

Met Azure Functions Premium kunnen meerdere replicatie toepassingen dezelfde onderliggende resource groep (een App Service plan) delen. Dit betekent dat u in .NET eenvoudig termijnen-replicatie taken kunt uitvoeren met replicatie taken die in Java zijn geschreven, bijvoorbeeld. Dit is belang rijk als u gebruik wilt maken van specifieke bibliotheken zoals Apache Camel die alleen beschikbaar zijn voor Java, en als dit de beste optie is voor een bepaald integratie traject, zelfs al hebt u meestal een andere taal en runtime voor andere replicatie taken. 

Indien beschikbaar, moet u de voor keur geven aan de batch gerichte triggers over triggers die afzonderlijke gebeurtenissen of berichten leveren. u moet altijd de volledige gebeurtenis of bericht structuur verkrijgen in plaats van te vertrouwen op de [para meters voor de bindings expressies](https://docs.microsoft.com/azure/azure-functions/functions-bindings-expressions-patterns)van Azure function.

De naam van de functie moet overeenkomen met het paar van de bron en het doel waarmee u verbinding maakt, en u moet voor voegsel verwijzingen naar verbindings reeksen of andere configuratie-elementen in de toepassings configuratie bestanden met die naam. 

### <a name="data-and-metadata-mapping"></a>Toewijzing van gegevens en meta gegevens

Zodra u hebt besloten over een paar invoer-en uitvoer bindingen, moet u een bepaalde toewijzing tussen de verschillende gebeurtenis-of bericht typen uitvoeren, tenzij het type van de trigger en de uitvoer hetzelfde zijn.

Voor eenvoudige replicatie taken waarmee berichten tussen Event Hubs en Service Bus worden gekopieerd, hoeft u geen eigen code te schrijven, maar kan Lean worden uitgevoerd op een [hulpprogramma bibliotheek die wordt meegeleverd](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) met de replicatie voorbeelden.

### <a name="retry-policy"></a>Beleid voor opnieuw proberen

Als u gegevens verlies tijdens Beschik baarheid wilt voor komen aan beide zijden van een replicatie functie, moet u het beleid voor opnieuw proberen zodanig configureren dat dit krachtig is. Raadpleeg de [documentatie van Azure functions over nieuwe pogingen](/azure/azure-functions/functions-bindings-error-pages) om het beleid voor opnieuw proberen te configureren. 

De beleids instellingen die zijn gekozen voor de voorbeeld projecten in de voor beeld- [opslag plaats](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) , configureren een exponentiële uitstel-strategie met intervallen van vijf seconden tot 15 minuten met oneindige pogingen om gegevens verlies te voor komen. 

Bekijk voor Service Bus de sectie [' de ondersteuning voor nieuwe pogingen op het niveau van de trigger gebruiken '](/azure/azure-functions/functions-bindings-error-pages#using-retry-support-on-top-of-trigger-resilience) voor meer informatie over de interactie van triggers en het maximum aantal bezorgingen dat voor de wachtrij is gedefinieerd.

### <a name="setting-up-a-replication-application-host"></a>Een replicatie-toepassingshost instellen

Een replicatie toepassing is een host voor uitvoering van een of meer replicatie taken. 

Het is een Azure Functions toepassing die is geconfigureerd om te worden uitgevoerd op het verbruiks abonnement of (aanbevolen) in een Azure Functions Premium-abonnement. Alle replicatie toepassingen moeten worden uitgevoerd onder een door het [systeem of de gebruiker toegewezen beheerde identiteit](/azure/app-service/overview-managed-identity). 

Met de gekoppelde Azure Resource Manager-sjablonen (ARM) kunt u een replicatie toepassing maken en configureren met:

* een Azure Storage account voor het bijhouden van de voortgang van de replicatie en voor logboeken,
* een door het systeem toegewezen beheerde identiteit en 
* Azure-bewaking en Application Insights integratie voor bewaking.

Replicatie toepassingen die toegang moeten hebben tot Event Hubs gebonden aan een virtueel Azure-netwerk (VNet), moeten gebruikmaken van het Azure Functions Premium-abonnement en moeten worden geconfigureerd om te worden gekoppeld aan hetzelfde VNet. Dit is ook een van de beschik bare opties.


|       | Implementeren | Visualiseren  
|-------|------------------|--------------|---------------|
| **Azure Functions-verbruiksabonnement** | [![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Visualiseren](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Azure Functions Premium-abonnement** |[![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Visualiseren](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **Azure Functions Premium-abonnement met VNet** | [![Implementeren in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Visualiseren](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Voorbeelden

De [opslag plaats voor beelden](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) bevat verschillende voor beelden van replicatie taken waarmee gebeurtenissen tussen Event hubs en/of tussen service bus entiteiten worden gekopieerd.

Voor het kopiëren van gebeurtenissen tussen Event Hubs gebruikt u een event hub-trigger met een event hub-uitvoer binding:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Voor het kopiëren van berichten tussen Service Bus entiteiten gebruikt u de Service Bus trigger-en uitvoer binding:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Met de hulp methoden kunt u eenvoudig repliceren tussen Event Hubs en Service Bus:

| Bron      | Doel      | Ingangs punt 
|-------------|-------------|------------------------------------------------------------------------
| Event Hub   | Event Hub   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Event Hub   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Event Hub   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Bewaking

Raadpleeg de [sectie bewaking](https://docs.microsoft.com/azure/azure-functions/configure-monitoring) van de Azure functions-documentatie voor meer informatie over het bewaken van uw replicatie-app.

Een bijzonder handig visueel hulp programma voor het controleren van replicatie taken is het Application Insights [toepassings overzicht](https://docs.microsoft.com/azure/azure-monitor/app/app-map)dat automatisch wordt gegenereerd op basis van de vastgelegde bewakings gegevens en waarmee de betrouw baarheid en prestaties van de bron-en doel overdrachten van de replicatie taak kunnen worden geverkennen.

Voor direct diagnostische gegevens kunt u werken met het portal-hulp programma voor [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) . Dit biedt een laag latentie van de logboek gegevens.

## <a name="next-steps"></a>Volgende stappen

* [Azure Functions implementaties](/azure/azure-functions/functions-deployment-technologies)
* [Diagnostische gegevens Azure Functions](/azure/azure-functions/functions-diagnostics)
* [Azure Functions-netwerk opties](/azure/azure-functions/functions-networking-options)
* [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview)