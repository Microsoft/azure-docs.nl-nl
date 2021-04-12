---
title: Problemen oplossen met AMQP-fouten in azure Event Hubs | Microsoft Docs
description: Biedt een lijst met AMQP-fouten die u kunt ontvangen wanneer u Azure Event Hubs gebruikt en de oorzaak van deze fouten.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 51b96792f6921bae9364212c6e5f9c987ff05e2a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466062"
---
# <a name="amqp-errors-in-azure-event-hubs"></a>AMQP-fouten in azure Event Hubs
Dit artikel bevat enkele van de fouten die worden weer gegeven wanneer u AMQP gebruikt met Azure Event Hubs. Ze zijn allemaal standaard gedrag van de service. U kunt ze vermijden door verzend-en ontvangst aanroepen te maken op de verbinding/koppeling, waardoor de verbinding/koppeling automatisch opnieuw wordt gemaakt.

## <a name="link-is-closed"></a>Koppeling is gesloten 
U ziet de volgende fout wanneer de AMQP-verbinding en de koppeling actief zijn, maar geen aanroepen (bijvoorbeeld verzenden of ontvangen) worden gemaakt met behulp van de koppeling gedurende 30 minuten. De koppeling wordt dus gesloten. De verbinding is nog geopend.

"AMQP: link: ontkoppelen: de koppeling ' G2:7223832: user.tenant0.cud_00000000000-0000-0000-0000-00000000000000 ' is geforceerd losgekoppeld door de Broker, omdat er fouten zijn opgetreden in Publisher (link164614). Oorsprong loskoppelen: AmqpMessagePublisher. IdleTimerExpired: time-out voor inactiviteit: 00:30:00. TrackingId: 00000000000000000000000000000000000000_G2_B3, SystemTracker: MyNamespace: onderwerp: MyTopic, time stamp: 2/16/2018 11:10:40 PM "

## <a name="connection-is-closed"></a>Verbinding is gesloten
U ziet de volgende fout melding op de AMQP-verbinding wanneer alle koppelingen in de verbinding zijn gesloten omdat er geen activiteit is (inactief) en er in vijf minuten geen nieuwe koppeling is gemaakt.

"Fout {condition = AMQP: Connection: forceed, description = ' de verbinding is inactief voor meer dan de toegestane 300000 milliseconden en is gesloten door de container LinkTracker. TrackingId: 00000000000000000000000000000000000_G21, SystemTracker: gateway5, time stamp: 2019-03-06T17:32:00, info = null} "

## <a name="link-isnt-created"></a>Koppeling is niet gemaakt 
U ziet deze fout wanneer er een nieuwe AMQP-verbinding wordt gemaakt, maar er wordt geen koppeling gemaakt binnen 1 minuut na het maken van de AMQP-verbinding.

"Fout {condition = AMQP: Connection: forceed, description = ' de verbinding is inactief voor meer dan de toegestane 60000 milliseconden en is gesloten door de container LinkTracker. TrackingId: 0000000000000000000000000000000000000_G21, SystemTracker: gateway5, time stamp: 2019-03-06T18:41:51 ', info = null} '

## <a name="next-steps"></a>Volgende stappen
Zie [AMQP 1,0 protocol Guide (Engelstalig](../service-bus-messaging/service-bus-amqp-protocol-guide.md)) voor meer informatie over AMQP.
