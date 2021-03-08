---
title: 'Quickstart: De beveiligingsmodule voor Azure RTOS configureren en inschakelen'
description: In deze Quick Start leert u hoe u de beveiligings module voor de Azure RTO'S-service in uw Azure-IoT Hub kunt voorbereiden en inschakelen.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 19a439ec48d4a8705ffb46db7ca037b51449083d
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447296"
---
# <a name="quickstart-security-module-for-azure-rtos"></a>Snelstartgids: Security module voor Azure RTO'S 

In dit artikel worden de vereisten beschreven waaraan u moet voldoen voordat u aan de slag gaat en wordt uitgelegd hoe u de beveiligingsmodule voor Azure RTOS-service in een Azure IoT-hub inschakelt. Als u momenteel niet over een Azure IoT-hub beschikt, raadpleegt u [Een Azure IoT-hub maken met Azure Portal](../iot-hub/iot-hub-create-through-portal.md) om aan de slag te gaan.

## <a name="prerequisites"></a>Vereisten 

### <a name="supported-devices"></a>Ondersteunde apparaten

- ST STM32F746G Discovery Kit
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

U moet een van de ZIP-bestanden voor de specifieke kaart en het hulpmiddel (IAR, IDE voor semi's of pc) van uw keuze uit de [GitHub-resource voor de beveiligingsmodule voor Azure RTOS](https://github.com/azure-rtos/azure-iot-preview/releases) downloaden, compileren en uitvoeren.

### <a name="azure-resources"></a>Azure-resources

In de volgende fase voor het aan de slag gaan bereidt u de Azure-resources voor. U hebt een Azure IoT-hub nodig en we raden een Log Analytics-werkruimte aan. Voor Azure IoT Hub hebt u de verbindingstekenreeks voor Azure IoT Hub nodig om verbinding te maken met uw apparaat. 
  
### <a name="iot-hub-connection"></a>Azure IoT Hub-verbinding

Er is een Azure IoT Hub-verbinding nodig om aan de slag te gaan. 

1. Open uw **Azure IoT-hub** in Azure Portal.

1. Navigeer naar **IOT-apparaten**.

1. Selecteer **Maken**.

1. Kopieer de IoT-verbindingstekenreeks naar het [configuratiebestand](how-to-azure-rtos-security-module.md).

De referenties voor verbindingen worden opgehaald uit de configuratie van de gebruikers toepassing **HOST_NAME**, **DEVICE_ID** en **DEVICE_SYMMETRIC_KEY**.

De beveiligingsmodule voor Azure RTOS maakt gebruik van Azure IoT-middlewareverbindingen op basis van het **MQTT**-protocol.

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om het configureren en aanpassen van uw oplossing te voltooien.

> [!div class="nextstepaction"]
> [Beveiligingsmodule configureren voor Azure RTOS](how-to-azure-rtos-security-module.md)
