---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304411"
---
De optie AMQP-over-websockets wordt via poort TCP 443 uitgevoerd, net als de HTTP/REST API, maar is in andere gevallen hetzelfde als gewone AMQP. Deze optie heeft een hogere latentie van de eerste verbinding vanwege extra Handshake-retour wegen en iets meer naarmate er meer overhead is voor het delen van de HTTPS-poort. Als deze modus is ingeschakeld, is de TCP-poort 443 voldoende voor communicatie. Met de volgende opties kunt u de AMQP-websockets-modus selecteren. 

| Taal | Optie   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | Maak [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) met behulp van een constructor die [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) als para meter gebruikt. Stel [ServiceBusClientOptions. transport type](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) in op [ServiceBusTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) |
| .NET (Microsoft.Azure.ServiceBus)    | Gebruik bij het maken van client objecten constructors die [transport type](/dotnet/api/microsoft.azure.servicebus.transporttype), [ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection)of [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) als para meters gebruiken. <p>Voor de constructie die `transportType` als para meter wordt gebruikt, stelt u de para meter in op [transport type. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Voor de constructor die `ServiceBusConnection` als para meter wordt gebruikt, stelt u de [ServiceBusConnection. transport type](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) in op [transport type. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype).</p> <p>Als u gebruikt `ServiceBusConnectionStringBuilder` , maakt u gebruik van constructors waarmee u de kunt opgeven `transportType` .</p> |
| Java (com. Azure. Messa ging. servicebus)     | Wanneer u clients maakt, stelt u [ServiceBusClientBuilder. transport type](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) in op [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Java (com. micro soft. Azure. servicebus)    | Wanneer u-clients maakt, stelt `transportType` u in [com. micro soft. Azure. Servicebus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_)  in op [com.Microsoft.Azure.servicebus.Primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Javascript  | Wanneer u Service Bus-client objecten maakt, gebruikt u de `webSocketOptions` eigenschap in [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions). |
| Python | Wanneer u Service Bus-clients maakt, stelt u [ServiceBusClient.transport_type](/python/api/azure-servicebus/azure.servicebus.servicebusclient) in op [transport type. AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype) |

