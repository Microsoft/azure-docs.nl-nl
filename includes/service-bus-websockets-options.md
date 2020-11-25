---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: fe205524c5aef6a43771cc09c810da217678d108
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022134"
---
De optie AMQP-over-websockets wordt via poort TCP 443 uitgevoerd, net als de HTTP/REST API, maar is in andere gevallen hetzelfde als gewone AMQP. Deze optie heeft een iets hogere latentie van de eerste verbinding vanwege extra Handshake-retour wegen en iets meer naarmate er meer overhead is voor het delen van de HTTPS-poort. Als deze modus is ingeschakeld, is de TCP-poort 443 voldoende voor communicatie. Met de volgende opties kunt u de modus voor de AMQP of AMQP-websockets inschakelen:

| Taal | Optie   |
| -------- | ----- |
| .NET     | Eigenschap [ServiceBusConnection. transport type](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype?view=azure-dotnet) met [transport type. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) of [transport type. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype?view=azure-dotnet) |
| Java     | [com. micro soft. Azure. servicebus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings?view=azure-java-stable) met [com. micro soft. Azure. servicebus. primitieven. transport type. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) of [com.Microsoft.Azure.servicebus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype?view=azure-java-stable) |
| Knooppunt  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions?view=azure-node-latest) heeft een `webSocket` constructor-argument. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) met [transport type. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) of [transport type. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |