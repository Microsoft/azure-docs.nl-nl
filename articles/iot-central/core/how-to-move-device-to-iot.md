---
title: Een apparaat verplaatsen naar Azure IoT Central van IoT Hub
description: Het apparaat verplaatsen naar Azure IoT Central van IoT Hub
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 7898f842529b81b80febff444c97b199fbebba3c
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98146452"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Een apparaat overdragen naar Azure IoT Central van IoT Hub

*Dit artikel is van toepassing op Opera tors en ontwikkel aars van apparaten.*  

In dit artikel wordt beschreven hoe u een apparaat overbrengt naar een Azure IoT Central-toepassing vanuit een IoT Hub. 

Een apparaat maakt eerst verbinding met een DPS-eind punt om de informatie op te halen die nodig is om verbinding te maken met uw toepassing. Intern gebruikt uw IoT Central-toepassing een IoT-hub voor het afhandelen van connectiviteit met apparaten.  

Een apparaat kan rechtstreeks worden verbonden met een IoT-hub met behulp van een connection string of DPS. [Azure IOT hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) is de route voor IOT Central.

## <a name="to-move-the-device"></a>Het apparaat verplaatsen

Als u een apparaat wilt verbinden met IoT Central van de IOT-hub, moet een apparaat worden bijgewerkt met:

* De [scope-id](../../iot-dps/concepts-service.md) van de IOT Central toepassing.
* Een sleutel die is afgeleid van de [groeps-SAS](concepts-get-connected.md) -sleutel of [het X. 509-certificaat](../../iot-hub/iot-hub-x509ca-overview.md)

Voor de interactie met IoT Central moet er een apparaatprofiel zijn dat de eigenschappen/telemetrie/opdrachten modelleert die het apparaat implementeert. Zie voor meer informatie verbinding maken met [IOT Central](concepts-get-connected.md) en [Wat zijn Device-sjablonen?](concepts-device-templates.md)

## <a name="next-steps"></a>Volgende stappen

Als u een ontwikkelaar van een apparaat bent, kunt u het volgende doen:

- Bekijk een voor beeld van een code die laat zien hoe u SAS-tokens gebruikt in [de zelf studie: een client toepassing maken en verbinden met uw Azure IOT Central-toepassing](tutorial-connect-device.md)
- Meer informatie over het [verbinden van apparaten met X. 509-certificaten met behulp van Node.js apparaat-SDK voor IOT Central toepassing](how-to-connect-devices-x509.md)
- Meer informatie over het [controleren van de connectiviteit van apparaten met behulp van Azure cli](./howto-monitor-devices-azure-cli.md)
- Meer informatie over het [definiëren van een nieuw IOT-apparaattype in uw Azure IOT Central-toepassing](./howto-set-up-template.md)
- Meer informatie over [Azure IOT edge apparaten en Azure IOT Central](./concepts-iot-edge.md)