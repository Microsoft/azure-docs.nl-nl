---
title: Meer informatie over Azure IoT Hub Messa ging | Microsoft Docs
description: 'Ontwikkelaars handleiding: apparaat-naar-Cloud en Cloud-naar-apparaat-berichten verzenden met IoT Hub. Bevat informatie over bericht indelingen en ondersteunde communicatie protocollen.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: e25b9d6cbe8446fb2f10150f17f7e2b0ccf85d3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147633"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Apparaat-naar-Cloud-en Cloud-naar-apparaat-berichten verzenden met IoT Hub

Met IoT Hub kan bidirectionele communicatie met uw apparaten worden toegestaan. Gebruik IoT Hub berichten om met uw apparaten te communiceren door berichten van uw apparaten te verzenden naar de back-end van uw oplossingen en opdrachten van uw IoT-oplossingen back-end naar uw apparaten te verzenden. Meer informatie over de [IOT hub bericht indeling](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Apparaat-naar-Cloud-berichten verzenden naar IoT Hub

IoT Hub heeft een ingebouwd Service-eind punt dat door de back-end-services kan worden gebruikt om telemetriegegevens van uw apparaten te lezen. Dit eind punt is compatibel met [Event hubs](../event-hubs/index.yml) en u kunt standaard IOT hub-sdk's gebruiken om [dit ingebouwde eind punt te lezen](iot-hub-devguide-messages-read-builtin.md).

IoT Hub biedt ook ondersteuning voor [aangepaste eind punten](iot-hub-devguide-endpoints.md#custom-endpoints) die door gebruikers kunnen worden gedefinieerd voor het verzenden van telemetriegegevens van apparaten en gebeurtenissen naar Azure-Services met behulp van [bericht routering](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Cloud-naar-apparaat-berichten verzenden vanuit IoT Hub

U kunt [Cloud-naar-apparaat-](iot-hub-devguide-messages-c2d.md) berichten verzenden vanuit de back-end van de oplossing naar uw apparaten.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Kern eigenschappen van de functionaliteit van IoT Hub-berichten zijn de betrouw baarheid en duurzaamheid van berichten. Met deze eigenschappen kunt u een tolerantie voor onregelmatige connectiviteit op het apparaat inschakelen en pieken in gebeurtenis verwerking aan de Cloud kant laden. IoT Hub implementeert *ten minste één keer* de leverings garanties voor zowel apparaat-naar-Cloud als Cloud-naar-apparaat-berichten.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Het juiste type IoT Hub berichten kiezen

Gebruik apparaat-naar-Cloud berichten voor het verzenden van telemetrie van de tijd reeks en waarschuwingen van uw apparaat-app en Cloud-naar-apparaat-berichten voor eenrichtings meldingen naar uw apparaat-app.

* Raadpleeg de informatie over apparaat- [naar-Cloud-communicatie](./iot-hub-devguide-d2c-guidance.md) om te kiezen tussen apparaat-naar-Cloud-berichten, gerapporteerde eigenschappen of het uploaden van bestanden.

* Raadpleeg de [communicatie richtlijnen van Cloud naar apparaat](./iot-hub-devguide-c2d-guidance.md) om te kiezen tussen Cloud-naar-apparaat-berichten, gewenste eigenschappen of directe methoden.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over IoT Hub [berichten routeren](iot-hub-devguide-messages-d2c.md).

* Meer informatie over [het IOT hub van Cloud-naar-apparaat-berichten](iot-hub-devguide-messages-c2d.md).