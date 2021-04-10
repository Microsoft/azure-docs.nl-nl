---
title: Modbus-protocollen vertalen met gateways-Azure IoT Edge | Microsoft Docs
description: Toestaan dat apparaten die Modbus TCP gebruiken, kunnen communiceren met Azure IoT Hub door een IoT-gatewayapparaat te maken
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 0388520903e208b3225375d5cee81e8321740a1b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103487731"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Modbus TCP-apparaten verbinden via een IoT Edge apparaat-gateway

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Als u IoT-apparaten wilt verbinden die gebruikmaken van Modbus TCP-of RTU-protocollen voor een Azure IoT-hub, kunt u een IoT Edge apparaat als gateway gebruiken. Het gatewayapparaat leest de gegevens van uw Modbus-apparaten en geeft deze gegevens met behulp van een ondersteund protocol door aan de cloud.

![Modbus-apparaten maken verbinding met IoT Hub via IoT Edge gateway](./media/deploy-modbus-gateway/diagram.png)

Dit artikel gaat over hoe u uw eigen containerinstallatiekopie maakt voor een Modbus-module (u kunt ook een kant- en-klaar voorbeeld gebruiken) en deze vervolgens implementeert op het IoT Edge-apparaat dat als uw gateway zal fungeren.

In dit artikel wordt ervan uitgegaan dat u het Modbus-protocol TCP gebruikt. Voor meer informatie over het configureren van de module ter ondersteuning van modbus RTU raadpleegt u het project [Modbus module Azure IOT Edge](https://github.com/Azure/iot-edge-modbus) op github.

## <a name="prerequisites"></a>Vereisten

* Een Azure IoT Edge-apparaat. Zie [Azure IOT Edge implementeren in Windows](quickstart.md) of [Linux](quickstart-linux.md)voor een overzicht van het instellen van een account.
* De verbindingsreeks van de primaire sleutel voor het IoT Edge-apparaat.
* Een fysiek of gesimuleerd Modbus-apparaat dat Modbus TCP ondersteunt. U moet het IPv4-adres kennen.

## <a name="prepare-a-modbus-container"></a>Een Modbus-container voorbereiden

Als u de functionaliteit van de Modbus-gateway wilt testen, heeft Microsoft een voorbeeldmodule die u kunt gebruiken. U kunt de module openen vanuit Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)of met de afbeeldings-URI `mcr.microsoft.com/azureiotedge/modbus:1.0` .

Als u uw eigen module wilt maken en deze wilt aanpassen voor uw omgeving, is er een open-source [Azure IOT Edge Modbus-module](https://github.com/Azure/iot-edge-modbus) project op github. Volg de instructies in dit project om uw eigen containerinstallatiekopie te maken. Als u een container installatie kopie wilt maken, raadpleegt u [C#-modules ontwikkelen in Visual Studio](./how-to-visual-studio-develop-module.md) of [modules ontwikkelen in Visual Studio code](how-to-vs-code-develop-module.md). Deze artikelen bevatten instructies voor het maken van nieuwe modules en het publiceren van container installatie kopieën in een REGI ster.

## <a name="try-the-solution"></a>Probeer de oplossing

In deze sectie wordt uitgelegd hoe u de voor beeld-Modbus-module van micro soft implementeert op uw IoT Edge-apparaat.

1. Ga in de [Azure-portal](https://portal.azure.com/) naar uw IoT-hub.

2. Ga naar **IOT Edge** en klik op uw IOT edge apparaat.

3. Selecteer **Modules instellen**.

4. Voeg in de sectie **IOT Edge modules** de module Modbus toe:

   1. Klik op de vervolg keuzelijst **toevoegen** en selecteer **Marketplace-module**.
   2. Zoek `Modbus` en selecteer de **Modbus TCP-module** van micro soft.
   3. De module wordt automatisch geconfigureerd voor uw IoT Hub en wordt weer gegeven in de lijst met IoT Edge modules. De routes worden ook automatisch geconfigureerd. Selecteer **Controleren + maken**.
   4. Controleer het implementatie manifest en selecteer **maken**.

5. Selecteer de module Modbus, `ModbusTCPModule` in de lijst en selecteer het tabblad **module dubbele instellingen** . De vereiste JSON voor de module dubbele gewenste eigenschappen wordt automatisch ingevuld.

6. Zoek naar de eigenschap **SlaveConnection** in de JSON en stel de waarde ervan in op het IPv4-adres van uw Modbus-apparaat.

7. Selecteer **Update**.

8. Selecteer **controleren + maken**, Controleer de implementatie en selecteer vervolgens **maken**.

9. Ga terug naar de detailpagina van het apparaat en selecteer **Vernieuwen**. De nieuwe `ModbusTCPModule` module wordt samen met de IOT Edge runtime weer geven.

## <a name="view-data"></a>Gegevens weergeven

Bekijk de gegevens die via de Modbus-module worden weer gegeven:

```cmd/sh
iotedge logs modbus
```

U kunt ook de telemetrie weer geven die het apparaat verzendt met behulp van de [azure IOT hub-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (voorheen Azure IOT Toolkit-extensie).

## <a name="next-steps"></a>Volgende stappen

* Zie [een IOT edge apparaat maken dat fungeert als transparante gateway](./how-to-create-transparent-gateway.md)voor meer informatie over hoe IOT edge-apparaten kunnen fungeren als gateways.
* Zie [Azure IOT Edge modules begrijpen](iot-edge-modules.md)voor meer informatie over de werking van IOT Edge modules.