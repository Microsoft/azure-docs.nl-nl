---
title: bestand opnemen
description: bestand opnemen
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95556204"
---
U kunt de REST API status van [eind punt ophalen](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) gebruiken om de status van de eind punten op te halen. We raden u aan om de [IOT hub routerings gegevens](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) met betrekking tot de latentie van de route ring te gebruiken om fouten op te sporen en op te sporen wanneer de status van het eind punt inactief of beschadigd is, omdat er een latentie wordt verwacht wanneer het eind punt in een van deze statussen wordt weer gegeven. Zie [IOT hub bewaken](../articles/iot-hub/monitor-iot-hub.md)voor meer informatie over het gebruik van IOT hub metrische gegevens.

|Status|Beschrijving|
|---|---|
|blijft|Het eind punt accepteert berichten zoals verwacht.|
|slechte|Het eind punt accepteert geen berichten en IoT Hub probeert berichten naar dit eind punt te verzenden.|
|unknown|IoT Hub heeft niet geprobeerd berichten te leveren aan dit eind punt.|
|gedegradeerd|Het eind punt accepteert berichten die langzamer zijn dan verwacht of worden hersteld vanaf een slechte status.|
|geval|IoT Hub levert geen berichten meer aan dit eind punt. Poging tot het verzenden van berichten naar dit eind punt is mislukt.|