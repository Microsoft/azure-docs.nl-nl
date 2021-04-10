---
title: "Snelstartgids: de Defender-IoT-micro agent voor Azure RTO'S configureren en inschakelen"
description: In deze Quick Start leert u hoe u de Defender-IoT-micro agent voor Azure RTO'S-service in uw Azure-IoT Hub kunt voorbereiden en inschakelen.
services: defender-for-iot
ms.topic: quickstart
ms.date: 01/24/2021
ms.openlocfilehash: 0575e9303068b203a5d8cd51c8a8cc1843de6f4d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384632"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>Snelstartgids: Defender-IoT-micro-agent voor Azure RTO'S (preview)

Dit artikel bevat een uitleg van de vereisten voordat u aan de slag gaat en legt uit hoe u de Defender-IoT-micro agent voor Azure RTO'S-service kunt inschakelen op een IoT Hub. Als u momenteel niet over een Azure IoT-hub beschikt, raadpleegt u [Een Azure IoT-hub maken met Azure Portal](../iot-hub/iot-hub-create-through-portal.md) om aan de slag te gaan.

## <a name="prerequisites"></a>Vereisten 

### <a name="supported-devices"></a>Ondersteunde apparaten

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Down load, compileer en voer een van de zip-bestanden uit voor het specifieke bord en hulp programma (IAR, semi-IDE of PC) van uw keuze uit de [Defender-IOT-micro agent voor Azure Rto's github-resource](https://github.com/azure-rtos/azure-iot-preview/releases).

### <a name="azure-resources"></a>Azure-resources

In de volgende fase voor het aan de slag gaan bereidt u de Azure-resources voor. U hebt een Azure IoT-hub nodig en we raden een Log Analytics-werkruimte aan. Voor Azure IoT Hub hebt u de verbindingstekenreeks voor Azure IoT Hub nodig om verbinding te maken met uw apparaat. 
  
### <a name="iot-hub-connection"></a>Azure IoT Hub-verbinding

Er is een Azure IoT Hub-verbinding nodig om aan de slag te gaan. 

1. Open uw **Azure IoT-hub** in Azure Portal.

1. Navigeer naar **IOT-apparaten**.

1. Selecteer **Maken**.

1. Kopieer de IoT-verbindingstekenreeks naar het [configuratiebestand](how-to-azure-rtos-security-module.md).

De referenties voor verbindingen worden opgehaald uit de configuratie van de gebruikers toepassing **HOST_NAME**, **DEVICE_ID** en **DEVICE_SYMMETRIC_KEY**.

De Defender-IoT-micro-agent voor Azure RTO'S maakt gebruik van Azure IoT-middleware verbindingen op basis van het **MQTT** -protocol.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om het configureren en aanpassen van uw oplossing te voltooien.

> [!div class="nextstepaction"]
> [Defender-IoT-micro agent voor Azure RTO'S (preview) configureren en aanpassen](how-to-azure-rtos-security-module.md)
