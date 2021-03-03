---
title: Azure percept Vision-gegevens blad
description: Bekijk het Azure percept Vision-gegevens blad voor gedetailleerde specificaties van apparaten
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 8814192274a81938d53ffc68c02dfaa9ac1da8ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662304"
---
# <a name="azure-percept-vision-datasheet"></a>Azure percept Vision-gegevens blad

De hieronder vermelde specificaties gelden voor het Azure percept Vision-apparaat, dat is opgenomen in [Azure PERCEPT DK](./azure-percept-dk-datasheet.md).

|Product specificatie           |Waarde     |
|--------------------------------|---------------------|
|Doel branches               |Productie <br> Slimme gebouwen <br> Automatisch <br> Retail |
|Held Scenario's                  |Winkel analyse <br> Beschik baarheid op schap <br> Verlaging verkleinen <br> Beveiliging/toezicht <br> Veiligheid werk plek|
|Dimensies                      |42mm x 42mm x 40mm (Azure percept Vision-assembly met behuizing) <br> 42mm x 42mm x 6mm (Vision SoM-chip)|
|Beheer beheergebied        |Update van Azure-apparaat (ADU)          |
|Ondersteunde software en services |[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX Runtime](https://www.onnxruntime.ai/) <br> [Openmijn wijn](https://docs.openvinotoolkit.org/latest/index.html) <br> Update van Azure-apparaat |
|AI-versnelling                 |Intel Movidiusis X (MA2085) Vision processing unit (VPU) met Intel camera ISP Integrated, 0,7 DOPPEN |
|Sens oren en visuele indica toren   |Universeel Vision 5670-camera <br> GSO GS8882AA lens (resolutie: 5MP op 30FPS, afstand: 50cm-oneindig, FoV: 120 graden, kleur: breed dynamisch bereik, gefocuste voortschrijdende luiken)          |
|Camera ondersteuning                  |RGB-, IR-en diepte camera's <br> 2 camera's kunnen tegelijkertijd worden uitgevoerd |
|Beveiligings Crypto-Controller      |ST-Micro STM32L462CE      |
|Onderdeel voor versie beheer/ID       |64 KB EEPROM |
|Geheugen                          |LPDDR4 2 GB     |
|Stroom                           |3,5 W     |
|Poorten                           |1x USB 3,0 type C <br> 2x MIPI-Lane van 4 GB (Maxi maal 1,5 Gbps per Lane)     |
|Besturings interfaces              |2x I2C <br> 2x SPI <br> 6x PWM (GPIOs: 2x Clock, 2x-frame synchronisatie, 2x ongebruikt) <br> 2x reserve-GPIO |
|Certificering                   |CE <br> FCC      |
|Bedrijfs temperatuur           |0 tot 27 graden C (Azure percept Vision-assembly met behuizing) <br> -10 tot 70 graden C (Vision SoM-chip) |
|Aanraak temperatuur               |<= 48 graden C |
|Relatieve vochtigheid               |8% tot 90%    |