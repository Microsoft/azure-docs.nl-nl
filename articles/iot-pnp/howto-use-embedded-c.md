---
title: IoT-Plug en Play op beperkte apparaten | Microsoft Docs
description: Leer hoe u IoT-Plug en Play op beperkte apparaten kunt implementeren met behulp van de SDK voor Embedded C of Azure RTOS.
author: EliotSeattle
ms.author: eliotgra
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6c792fbbb44b7dc769e7cdc56850684bd69ea40b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864133"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>IoT-Plug en Play op beperkte apparaten implementeren

Als u ontwikkelt voor beperkte *apparaten,* kunt u IoT Plug en Play gebruiken met de [Azure SDK voor ingesloten C IoT-clientbibliotheken](https://aka.ms/embeddedcsdk) of [met Azure RTOS](/azure/rtos/overview-rtos). Dit artikel bevat koppelingen en resources voor deze beperkte scenario's.

## <a name="use-the-sdk-for-embedded-c"></a>De SDK voor Embedded C gebruiken

De SDK voor Embedded C biedt een lichtgewicht oplossing om beperkte apparaten te verbinden met Azure IoT-services, waaronder het gebruik van de [IoT Plug en Play conventies](concepts-convention.md). De volgende koppelingen bevatten voorbeelden voor echte apparaten en voor onderwijs- en probleemopsporingsdoeleinden.

### <a name="use-a-real-device"></a>Een echt apparaat gebruiken

Zie [Repurpose PIC-IoT Wx Development Board to Connect to Azure](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)through IoT Hub Device Provisioning Service (Een nieuwe PIC-IoT Wx Development Board gebruiken om verbinding te maken met Azure via IoT Hub Device Provisioning Service) voor een volledige end-to-end zelfstudie met behulp van de SDK voor Embedded C, Device Provisioning Service en IoT Plug en Play op een echt apparaat.

### <a name="introductory-samples"></a>Inleidende voorbeelden

De SDK voor ingesloten C-opslagplaats bevat [verschillende voorbeelden](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) die laten zien hoe u IoT-Plug en Play:

> [!NOTE]
> Deze voorbeelden worden weergegeven in Windows en Linux voor onderwijs- enbugdoeleinden. In een productiescenario zijn de voorbeelden alleen bedoeld voor beperkte apparaten.

- [Voorbeeld van thermostaat met SDK voor ingesloten C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Voorbeeld van temperatuurcontroller met de SDK voor ingesloten C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Met Azure RTOS

Azure RTOS bevat een lichtgewicht laag die native connectiviteit toevoegt aan Azure IoT-cloudservices. Deze laag biedt een eenvoudig mechanisme om beperkte apparaten te verbinden met Azure IoT terwijl u de geavanceerde functies van Azure RTOS. Zie Wat is rtos Microsoft Azure [voor meer informatie.](/azure/rtos/overview-rtos)

### <a name="toolchains"></a>Hulpprogrammaketens

De Azure RTOS worden geleverd met verschillende soorten combinaties van IDE en hulpprogrammaketens, zoals:

- IAR: IDE [ingesloten Workbench](https://www.iar.com/iar-embedded-workbench/) van IAR
- GCC/CMake: bouw op de open source [CMake-buildsysteem](https://cmake.org/) en [GNU Arm Embedded-toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: [MCUXpresso IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) van NXP
- STM32Cube: [STM32Cube IDE](https://www.st.com/en/development-tools/stm32cubeide.html) van STMicroelec electronica
- MPLAB: [MPLAB X IDE van](https://www.microchip.com/mplab/mplab-x-ide)

### <a name="samples"></a>Voorbeelden

Zie de volgende tabel voor voorbeelden die laten zien hoe u op verschillende apparaten aan de slag kunt Azure RTOS en IoT Plug en Play:

Fabrikant | Apparaat | Voorbeelden |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| Nxp | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelecelecs | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelecelecs | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelec electronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over de opties voor het implementeren van IoT Plug en Play op beperkte apparaten, is een voorgestelde volgende stap om meer te weten te komen over de [IoT Plug en Play conventies](concepts-convention.md).