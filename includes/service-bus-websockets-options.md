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
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98693393"
---
De optie AMQP-over-websockets wordt via poort TCP 443 uitgevoerd, net als de HTTP/REST API, maar is in andere gevallen hetzelfde als gewone AMQP. Deze optie heeft een iets hogere latentie van de eerste verbinding vanwege extra Handshake-retour wegen en iets meer naarmate er meer overhead is voor het delen van de HTTPS-poort. Als deze modus is ingeschakeld, is de TCP-poort 443 voldoende voor communicatie. Met de volgende opties kunt u de modus voor de AMQP of AMQP-websockets inschakelen:

| Taal | Optie   |
| -------- | ----- |
| .NET     | Eigenschap [ServiceBusConnection. transport type](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) met [transport type. AMQP](/dotnet/api/microsoft.azure.servicebus.transporttype) of [transport type. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) |
| Java     | [com. micro soft. Azure. servicebus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) met [com. micro soft. Azure. servicebus. primitieven. transport type. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) of [com.Microsoft.Azure.servicebus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Knooppunt  | [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) heeft een `webSocket` constructor-argument. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) met [transport type. AMQP](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) of [transport type. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |