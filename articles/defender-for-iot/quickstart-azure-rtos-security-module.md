---
title: 'Quickstart: De beveiligingsmodule voor Azure RTOS configureren en inschakelen'
description: Ontdek hoe u de beveiligingsmodule voor Azure RTOS-service opneemt en inschakelt in uw Azure IoT-hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2020
ms.author: rkarlin
ms.openlocfilehash: 321c8d2b9e58aba943c5bf19adf54d6359c5be96
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351773"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Quickstart: Beveiligingsmodule voor Azure RTOS (preview)

In dit artikel worden de vereisten beschreven waaraan u moet voldoen voordat u aan de slag gaat en wordt uitgelegd hoe u de beveiligingsmodule voor Azure RTOS-service in een Azure IoT-hub inschakelt. Als u momenteel niet over een Azure IoT-hub beschikt, raadpleegt u [Een Azure IoT-hub maken met Azure Portal](../iot-hub/iot-hub-create-through-portal.md) om aan de slag te gaan.

> [!NOTE]
> De beveiligingsmodule voor Azure RTOS wordt alleen ondersteund in Azure IoT-hubs van de Standard-laag.

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
1. Kopieer de IoT-verbindingstekenreeks naar het [configuratiebestand](how-to-azure-rtos-security-module.md).


De verbindingsreferenties worden opgehaald uit de gebruikerstoepassingsconfiguratie **HOST_NAME**, **DEVICE_ID** en **DEVICE_SYMMETRIC_KEY**.

De beveiligingsmodule voor Azure RTOS maakt gebruik van Azure IoT-middlewareverbindingen op basis van het **MQTT**-protocol.


### <a name="log-analytics-workspace"></a>Log Analytics-werkruimte

Log Analytics-opname in Azure IoT Hub is standaard uitgeschakeld in de Azure Defender for IoT-oplossing. U kunt dit als volgt inschakelen voor gebruik met de beveiligingsmodule voor Azure RTOS: 
1. Ga in Azure Portal naar uw Azure IoT-hub.
1. Selecteer **Instellingen** onder het menu **Beveiliging**.
   :::image type="content" source="media/quickstart/azure-rtos-hub-settings.png" alt-text="Toegang tot de optie voor het verzamelen van gegevens voor Azure RTOS"::: 
1. Selecteer **Gegevensverzameling**. 
1. Zet in de optie **Werkruimteconfiguratie** de wisselknop op **Aan**. 
1. Maak een nieuwe Log Analytics-werkruimte of koppel een bestaande werkruimte. Zorg ervoor dat de optie **Toegang tot onbewerkte beveiligingsgegevens** is geselecteerd. 
 :::image type="content" source="media/quickstart/azure-rtos-data-collection-on.png" alt-text="Azure RTOS-configuratie met de opties voor gegevensverzameling en onbewerkte beveiligingsgegevens die beide zijn geselecteerd":::
1. Selecteer **Opslaan**
1. Ga terug naar de lijst met Azure-resources en controleer of de door u gemaakte of gekoppelde Log Analytics-werkruimte is ingeschakeld voor de Azure IoT-hub.
    :::image type="content" source="media/quickstart/verify-azure-resource-list.png" alt-text="Controleer in de lijst met Azure-resources of de juiste Log Analytics-werkruimte is toegevoegd voor een Azure IoT-hub"::: 

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel om het configureren en aanpassen van uw oplossing te voltooien.

> [!div class="nextstepaction"]
> [Beveiligingsmodule configureren voor Azure RTOS](how-to-azure-rtos-security-module.md)