---
title: Azure Event Hubs-client-Sdk's | Microsoft Docs
description: Dit artikel bevat informatie over de client-Sdk's voor Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9f4eec603245f1e4ea6fa4d97b843ca6a770f2ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930873"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs-client-Sdk's
Dit artikel bevat de volgende informatie voor de Sdk's die worden ondersteund door Azure Event Hubs: 

- De locatie van het pakket dat u kunt gebruiken in uw toepassingen 
- GitHub locatie waar u de bron code, voor beelden, Leesmij, het wijzigingslog bestand, gerapporteerde problemen en ook nieuwe problemen kunt vinden 
- Koppelingen naar zelf studies voor Snelstartgids 

## <a name="client-sdks"></a>Client-SDK 's
In de volgende tabel worden alle momenteel beschik bare Azure Event Hubs runtime-clients beschreven. Hoewel sommige van deze bibliotheken ook beperkte beheer functionaliteit bevatten, zijn er ook specifieke bibliotheken die zijn toegewezen aan beheer bewerkingen. De belangrijkste focus van deze bibliotheken is het **verzenden en ontvangen van berichten** van een event hub.

| Taal | Pakket | Referentie | 
| -------- | ------- | --------------- | 
| . NET Standard (**meest recent** en ondersteunt zowel .net Core als .NET Framework) | [Azure. Messa ging. Event hubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Zelfstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure. Messa ging. Event hubs. processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Zelfstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Standard (**verouderd** en ondersteunt zowel .net Core als .NET Framework) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Zelfstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Zelfstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework (**oud**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[Zelfstudie](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Zelfstudie](event-hubs-java-get-started-send.md)</li></ul> |
|      | [Azure-Event hubs](https://search.maven.org/search?q=a:azure-eventhubs) **(verouderd)** | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Zelfstudie](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [Azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Zelfstudie](event-hubs-python-get-started-send.md)</li></ul> |
|        | [Azure-eventhub-checkpointstoreblob-AIO](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Zelfstudie](event-hubs-python-get-started-send.md)</li></ul> |
| Javascript | [Azure/Event-hubs](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Zelfstudie](event-hubs-node-get-started-send.md)</li></ul> |
|            | [Azure-Event hubs-checkpointstore-BLOB](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Zelfstudie](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [Azure-Event-hubs-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-event-hubs-go)</li><li>[Zelfstudie](event-hubs-go-get-started-send.md)</li></ul> |
| C | [Azure-Event-hubs-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[Locatie van GitHub](https://github.com/Azure/azure-event-hubs-c)</li><li>[Zelfstudie](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>Management-Sdk's
De volgende tabel geeft een lijst van alle momenteel beschik bare beheer bibliotheken. Geen van deze bibliotheken bevat runtime-bewerkingen en is uitsluitend bedoeld voor het **beheren van Event hubs entiteiten**.

| Taal | Pakket | Referentie | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[Locatie van GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[Zelfstudie](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](./event-hubs-about.md)
* [Een event hub maken](event-hubs-create.md)
* [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
