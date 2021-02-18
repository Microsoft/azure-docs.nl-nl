---
title: Opties voor Azure IoT Hub Cloud-naar-apparaat | Microsoft Docs
description: 'Hand leiding voor ontwikkel aars: richt lijnen voor het gebruik van directe methoden, de gewenste eigenschappen van apparaten en Cloud-naar-apparaat-berichten voor communicatie tussen Cloud en apparaat.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: ad4f5dcd137a9be6dfc764385802792026c0297d
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093012"
---
# <a name="cloud-to-device-communications-guidance"></a>Richt lijnen voor communicatie tussen Cloud en apparaat

IoT Hub biedt drie opties voor apparaat-apps om functionaliteit beschikbaar te maken voor een back-end-app:

* [Directe methoden](iot-hub-devguide-direct-methods.md) voor communicatie waarvoor directe bevestiging van het resultaat is vereist. Directe methoden worden vaak gebruikt voor interactieve controle van apparaten, zoals het inschakelen van een ventilator.

* De [gewenste eigenschappen](iot-hub-devguide-device-twins.md) voor langlopende opdrachten die het apparaat in een bepaalde status willen zetten. Stel bijvoorbeeld het verzend interval van de telemetrie in op 30 minuten.

* [Cloud-naar-apparaat-berichten](iot-hub-devguide-messages-c2d.md) voor eenrichtings meldingen naar de apparaat-app.

Zie [iot Plug en Play service Developer Guide (Engelstalig](../iot-pnp/concepts-developer-guide-service.md)) voor meer informatie over hoe [Azure IOT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md) deze opties gebruikt om IOT Plug en Play-apparaten te beheren.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Hier volgt een gedetailleerde vergelijking van de verschillende communicatie opties van Cloud naar apparaat.

| Categorieën | Directe methoden | Gewenste eigenschappen van twee | Cloud-naar-apparaat-berichten |
| ---------- | -------------- | ------------------------- | ------------------------ |
| Scenario | Opdrachten waarvoor onmiddellijke bevestiging is vereist, zoals het inschakelen van een ventilator. | Langlopende opdrachten die bedoeld zijn om het apparaat in een bepaalde status te zetten. Stel bijvoorbeeld het verzend interval van de telemetrie in op 30 minuten. | Eenrichtings meldingen naar de apparaat-app. |
| Gegevensstroom | Twee richtingen. De apparaat-app kan direct op de methode reageren. De back-end van de oplossing ontvangt het resultaat context afhankelijk van de aanvraag. | Eén richting. De app van het apparaat ontvangt een melding met de eigenschaps wijziging. | Eén richting. De app van het apparaat ontvangt het bericht
| Duurzaamheid | Er is geen verbinding gemaakt met apparaten die niet zijn verbonden. Er wordt een melding weer gegeven dat het apparaat niet is verbonden met de back-end van de oplossing. | Eigenschapwaarden worden bewaard in het dubbele apparaat. Het apparaat wordt tijdens de volgende keer opnieuw verbinding gelezen. Eigenschaps waarden kunnen worden opgehaald met de [IOT hub query taal](iot-hub-devguide-query-language.md). | Berichten kunnen Maxi maal 48 uur worden bewaard door IoT Hub. |
| Doelen | Eén apparaat met behulp van **deviceId** of meerdere apparaten met behulp van [taken](iot-hub-devguide-jobs.md). | Eén apparaat met behulp van **deviceId** of meerdere apparaten met behulp van [taken](iot-hub-devguide-jobs.md). | Eén apparaat op **deviceId**. |
| Grootte | De maximale grootte van de directe methode lading is 128 KB. | De maximale grootte van de gewenste eigenschappen is 32 KB. | Maxi maal 64 KB-berichten. |
| Frequentie | Hoog. Zie [IOT hub limieten](iot-hub-devguide-quotas-throttling.md)voor meer informatie. | Gemiddeld. Zie [IOT hub limieten](iot-hub-devguide-quotas-throttling.md)voor meer informatie. | Laag. Zie [IOT hub limieten](iot-hub-devguide-quotas-throttling.md)voor meer informatie. |
| Protocol | Beschikbaar via MQTT of AMQP. | Beschikbaar via MQTT of AMQP. | Beschikbaar op alle protocollen. Het apparaat moet worden gecontroleerd wanneer HTTPS wordt gebruikt. |

Meer informatie over het gebruik van directe methoden, gewenste eigenschappen en Cloud-naar-apparaat-berichten in de volgende zelf studies:

* [Directe methoden gebruiken](quickstart-control-device-node.md)
* [Gewenste eigenschappen gebruiken om apparaten te configureren](tutorial-device-twins.md) 
* [Cloud-naar-apparaat-berichten verzenden](iot-hub-node-node-c2d.md)
