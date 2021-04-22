---
title: Een apparaat verplaatsen naar Azure IoT Central van IoT Hub
description: Apparaat verplaatsen naar Azure IoT Central van IoT Hub
author: philmea
ms.author: philmea
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 79aead5b374714e7856897a9b85349198341cb3d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872727"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Een apparaat overdragen naar Azure IoT Central van IoT Hub

*Dit artikel is van toepassing op operators en apparaatontwikkelaars.*  

In dit artikel wordt beschreven hoe u een apparaat naar een Azure IoT Central van een IoT Hub. 

Een apparaat maakt eerst verbinding met een DPS-eindpunt om de informatie op te halen die nodig is om verbinding te maken met uw toepassing. Intern gebruikt uw IoT Central een IoT-hub voor het afhandelen van apparaatconnectiviteit.  

Een apparaat kan rechtstreeks met een IoT-hub worden verbonden met behulp van connection string of DPS. [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) is de route voor IoT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Het apparaat verplaatsen naar Azure IoT Central

Als u een apparaat wilt verbinden IoT Central de IoT Hub moet een apparaat worden bijgewerkt met:

* De [bereik-id](../../iot-dps/concepts-service.md) van de IoT Central toepassing.
* Een sleutel die is afgeleid van de [GROEPS-SAS-sleutel](concepts-get-connected.md) of [het X.509-certificaat](../../iot-hub/iot-hub-x509ca-overview.md)

Als u met IoT Central wilt werken, moet er een apparaatsjabloon zijn die de eigenschappen/telemetrie/opdrachten die het apparaat implementeert, model modeleert. Zie Voor meer informatie [Verbinding maken met](concepts-get-connected.md) IoT Central en Wat zijn [apparaatsjablonen?](concepts-device-templates.md)

## <a name="next-steps"></a>Volgende stappen

Als u een apparaatontwikkelaar bent, zijn enkele voorgestelde volgende stappen:

- Bekijk enkele voorbeeldcodes die laten zien hoe u SAS-tokens gebruikt in Zelfstudie: Een clienttoepassing maken en verbinden [met uw Azure IoT Central toepassing](tutorial-connect-device.md)
- Meer informatie over het verbinden van apparaten met [X.509-certificaten met behulp van Node.js device SDK voor IoT Central Application](how-to-connect-devices-x509.md)
- Meer informatie over het [bewaken van apparaatconnectiviteit met behulp van Azure CLI](./howto-monitor-devices-azure-cli.md)
- Meer informatie over [het definiëren van een nieuw IoT-apparaattype in uw Azure IoT Central toepassing](./howto-set-up-template.md)
- Meer informatie [Azure IoT Edge apparaten en Azure IoT Central](./concepts-iot-edge.md)