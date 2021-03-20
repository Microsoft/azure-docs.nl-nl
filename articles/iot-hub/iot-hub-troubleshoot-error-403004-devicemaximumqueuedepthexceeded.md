---
title: Problemen met Azure IoT Hub-fout 403004 DeviceMaximumQueueDepthExceeded
description: Meer informatie over het oplossen van fout 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 421066ef30e23a79b26f97939cdfffb5be83afb5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148240"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

In dit artikel worden de oorzaken en oplossingen voor **403004 DeviceMaximumQueueDepthExceeded** -fouten beschreven.

## <a name="symptoms"></a>Symptomen

Wanneer u een Cloud-naar-apparaat-bericht probeert te verzenden, mislukt de aanvraag met de fout **403004** of **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Oorzaak

De onderliggende oorzaak is dat het aantal berichten in de wachtrij voor het apparaat groter is dan de limiet voor de [wacht rijen (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

De meest waarschijnlijke reden hiervoor is dat u HTTPS gebruikt om het bericht te ontvangen, wat leidt tot doorlopende polling met `ReceiveAsync` , wat resulteert in IOT hub het beperken van de aanvraag.

## <a name="solution"></a>Oplossing

Het ondersteunde patroon voor Cloud-naar-apparaat-berichten met HTTPS is op een regel matig verbonden apparaten die op berichten te controleren zijn (minder dan elke 25 minuten). Als u de kans op het uitvoeren van de wachtrij limiet wilt beperken, schakelt u over naar AMQP of MQTT voor Cloud-naar-apparaat-berichten.

U kunt de logica van een apparaat ook verbeteren om snel een bericht in de wachtrij te plaatsen, te weigeren of te annuleren, de tijd te verkorten of te overwegen om minder berichten te verzenden. Zie [C2D message time to Live](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live) (TTL van C2D-bericht) voor meer informatie.

U kunt eventueel de API voor het leegmaken van de [wachtrij](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) gebruiken om regel matig wachtende berichten op te schonen voordat de limiet is bereikt.