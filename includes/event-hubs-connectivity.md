---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/21/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 44afd8ea4ef2ab06ec31b7528e9776faebc3b4dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98690039"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Welke poorten moet ik op de firewall openen? 
U kunt de volgende protocollen met Azure Event Hubs gebruiken om gebeurtenissen te verzenden en te ontvangen:

- Advanced Message Queueing Protocol 1,0 (AMQP)
- Hypertext Transfer Protocol 1,1 met TLS (HTTPS)
- Apache Kafka

Zie de volgende tabel voor de uitgaande poorten die u moet openen om deze protocollen te gebruiken om te communiceren met Azure Event Hubs. 

| Protocol | Poorten | Details | 
| -------- | ----- | ------- | 
| AMQP | 5671 en 5672 | Zie [AMQP protocol Guide (Engelstalig](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) ) | 
| HTTPS | 443 | Deze poort wordt gebruikt voor de HTTP/REST API en voor AMQP-over-websockets. |
| Kafka | 9093 | Zie [Event hubs gebruiken in Kafka-toepassingen](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

De HTTPS-poort is vereist voor uitgaande communicatie, ook wanneer AMQP wordt gebruikt via poort 5671, omdat verschillende beheer bewerkingen door de client-Sdk's en het verkrijgen van tokens van Azure Active Directory (indien gebruikt) via HTTPS worden uitgevoerd. 

De officiële Azure-Sdk's gebruiken meestal het AMQP-protocol voor het verzenden en ontvangen van gebeurtenissen van Event Hubs. De optie AMQP-over-websockets wordt via poort TCP 443 uitgevoerd, net als de HTTP-API, maar is op een andere manier hetzelfde als gewone AMQP. Deze optie heeft een hogere aanvankelijke verbindings latentie vanwege extra Handshake-rond Reiss en iets meer naarmate er meer overhead is voor het delen van de HTTPS-poort. Als deze modus is ingeschakeld, is de TCP-poort 443 voldoende voor communicatie. Met de volgende opties kunt u de modus voor de AMQP of AMQP-websockets inschakelen:

| Taal | Optie   |
| -------- | ----- |
| .NET     | Eigenschap [EventHubConnectionOptions. transport type](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype) met [EventHubsTransportType. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) of [EventHubsTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) |
| Java     | [com. micro soft. Azure. Event hubs. EventProcessorClientBuilder. transport type](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype) met [AmqpTransportType. AMQP](/java/api/com.azure.core.amqp.amqptransporttype) of [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Knooppunt  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions) heeft een `webSocketOptions` eigenschap. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient) met [transport type. AMQP](/python/api/azure-eventhub/azure.eventhub.transporttype) of [transport type. AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Welke IP-adressen moet ik toestaan?
Wanneer u met Azure werkt, moet u soms specifieke IP-adresbereiken of Url's in uw bedrijfs firewall of-proxy toestaan om toegang te krijgen tot alle Azure-Services die u gebruikt of probeert te gebruiken. Controleer of het verkeer is toegestaan op IP-adressen die worden gebruikt door Event Hubs. Voor IP-adressen die worden gebruikt door Azure Event Hubs: Zie [Azure IP-adresbereiken en service Tags-open bare Cloud](https://www.microsoft.com/download/details.aspx?id=56519).

Controleer ook of het IP-adres voor uw naam ruimte is toegestaan. Voer de volgende stappen uit om de juiste IP-adressen te vinden die u voor uw verbindingen wilt toestaan:

1. Voer de volgende opdracht uit vanaf een opdracht prompt: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Noteer het IP-adres dat is geretourneerd in `Non-authoritative answer` . 

Als u de **zone redundantie** voor uw naam ruimte gebruikt, moet u een paar extra stappen uitvoeren: 

1. Eerst voert u Nslookup uit op de naam ruimte.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Noteer de naam in de sectie **niet-bindende antwoord** , die een van de volgende indelingen heeft: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Voer nslookup uit voor elk met achtervoegsels S1, S2 en S3 om de IP-adressen te verkrijgen van alle drie de instanties die worden uitgevoerd in drie beschikbaarheids zones, 

    > [!NOTE]
    > Het IP-adres dat door de `nslookup` opdracht wordt geretourneerd, is geen statisch IP-adres. Het blijft echter constant totdat de onderliggende implementatie wordt verwijderd of verplaatst naar een ander cluster.

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>Welke client Ip's verzenden gebeurtenissen naar of ontvangen gebeurtenissen van mijn naam ruimte?
Schakel eerst [IP-filtering](../articles/event-hubs/event-hubs-ip-filtering.md) in voor de naam ruimte. 

Schakel vervolgens de volgende instructies in [Diagnostische logboeken inschakelen](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs)in om Diagnostische logboeken in te scha kelen voor [Event hubs gebeurtenissen van een virtueel netwerk](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) . U ziet het IP-adres waarvoor verbinding wordt geweigerd.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> Virtuele netwerk logboeken worden alleen gegenereerd als de naam ruimte toegang tot **specifieke IP-adressen** (IP-filter regels) toestaat. Als u de toegang tot uw naam ruimte niet wilt beperken met behulp van deze functies en toch virtuele netwerk logboeken wilt ophalen voor het bijhouden van IP-adressen van clients die verbinding maken met de naam ruimte van Event Hubs, kunt u de volgende tijdelijke oplossing gebruiken: IP-filtering inschakelen en het totale adresseer bare IPv4-bereik toevoegen (1.0.0.0/1-255.0.0.0/1). Event Hubs biedt geen ondersteuning voor IPv6-adresbereiken. 

> [!NOTE]
> Op dit moment is het niet mogelijk om het bron-IP-adres van een afzonderlijk bericht of gebeurtenis te bepalen. 