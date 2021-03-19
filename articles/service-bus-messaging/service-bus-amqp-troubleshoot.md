---
title: Problemen oplossen met AMQP-fouten in Azure Service Bus | Microsoft Docs
description: Biedt een lijst met AMQP-fouten die u kunt ontvangen wanneer u Azure Service Bus gebruikt en de oorzaak van deze fouten.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 88b10940e0b910f50e6ccf7f8c53134fa7f0ba2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88064346"
---
# <a name="amqp-errors-in-azure-service-bus"></a>AMQP-fouten in Azure Service Bus
Dit artikel bevat enkele van de fouten die worden weer gegeven wanneer u AMQP gebruikt met Azure Service Bus. Ze zijn allemaal standaard gedrag van de service. U kunt ze vermijden door verzend-en ontvangst aanroepen te maken op de verbinding/koppeling, waardoor de verbinding/koppeling automatisch opnieuw wordt gemaakt.

## <a name="link-is-closed"></a>Koppeling is gesloten 
U ziet de volgende fout wanneer de AMQP-verbinding en de koppeling actief zijn, maar geen aanroepen (bijvoorbeeld verzenden of ontvangen) worden gemaakt met behulp van de koppeling gedurende tien minuten. De koppeling wordt dus gesloten. De verbinding is nog geopend.

```
amqp:link:detach-forced:The link 'G2:7223832:user.tenant0.cud_00000000000-0000-0000-0000-00000000000000' is force detached by the broker due to errors occurred in publisher(link164614). Detach origin: AmqpMessagePublisher.IdleTimerExpired: Idle timeout: 00:10:00. TrackingId:00000000000000000000000000000000000000_G2_B3, SystemTracker:mynamespace:Topic:MyTopic, Timestamp:2/16/2018 11:10:40 PM
```

## <a name="connection-is-closed"></a>Verbinding is gesloten
U ziet de volgende fout melding op de AMQP-verbinding wanneer alle koppelingen in de verbinding zijn gesloten omdat er geen activiteit is (inactief) en er in vijf minuten geen nieuwe koppeling is gemaakt.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 300000 milliseconds and is closed by container 'LinkTracker'. TrackingId:00000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T17:32:00', info=null}
```

## <a name="link-is-not-created"></a>Koppeling is niet gemaakt 
U ziet deze fout wanneer er een nieuwe AMQP-verbinding wordt gemaakt, maar er wordt geen koppeling gemaakt binnen 1 minuut na het maken van de AMQP-verbinding.

```
Error{condition=amqp:connection:forced, description='The connection was inactive for more than the allowed 60000 milliseconds and is closed by container 'LinkTracker'. TrackingId:0000000000000000000000000000000000000_G21, SystemTracker:gateway5, Timestamp:2019-03-06T18:41:51', info=null}
```

## <a name="next-steps"></a>Volgende stappen

Ga voor meer informatie over AMQP en Service Bus naar de volgende koppelingen:

* [Overzicht van Service Bus AMQP]
* [AMQP 1.0-protocolhandleiding]
* [AMQP in Service Bus voor Windows Server]

[Overzicht van Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0-protocolhandleiding]: service-bus-amqp-protocol-guide.md
[AMQP in Service Bus voor Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)