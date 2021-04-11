---
title: IoT-Plug en Play op beperkte apparaten gebruiken | Microsoft Docs
description: Meer informatie over hoe u IoT-Plug en Play op beperkte apparaten kunt implementeren met behulp van de SDK voor Embedded C of Azure RTO'S.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: cfa71e02688d5bf63d894ae42dabab7da1d4174c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057311"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>IoT-Plug en Play op beperkte apparaten implementeren

Als u voor *beperkte apparaten* ontwikkelt, kunt u IoT-Plug en Play gebruiken met de [Azure SDK voor Embedded C IOT-client bibliotheken](https://aka.ms/embeddedcsdk) of met [Azure rto's](/azure/rtos/overview-rtos). Dit artikel bevat koppelingen en bronnen voor deze beperkte scenario's.

## <a name="use-the-sdk-for-embedded-c"></a>De SDK voor Embedded C gebruiken

De SDK voor Embedded C biedt een licht gewicht oplossing voor het verbinden van beperkte apparaten met Azure IoT-Services, met inbegrip van de [iot Plug en Play-conventies](concepts-convention.md). De volgende koppelingen bevatten voor beelden voor een echt apparaat en voor educatieve en fout opsporing.

### <a name="use-a-real-device"></a>Een echt apparaat gebruiken

Voor een volledige end-to-end zelf studie met de SDK voor Embedded C, de Device Provisioning Service en IoT Plug en Play op een echt apparaat, Zie [redoel PIC-IoT wx Development Board om verbinding te maken met Azure via IOT hub Device Provisioning Service](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx).

### <a name="introductory-samples"></a>Inleidende voor beelden

De SDK voor Inge sloten C-opslag plaatsen bevat [verschillende voor beelden](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) die laten zien hoe u IoT-Plug en Play kunt gebruiken:

> [!NOTE]
> Deze voor beelden worden weer gegeven in Windows en Linux voor educatieve doel einden en fout opsporing. In een productie scenario zijn de voor beelden alleen bedoeld voor beperkte apparaten.

- [Voor beeld van Thermo staat met SDK voor Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Voor beeld van temperatuur controller met de SDK voor Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Azure RTO'S gebruiken

Azure RTO'S bevat een licht gewicht laag waarmee systeem eigen connectiviteit kan worden toegevoegd aan Azure IoT-Cloud Services. Deze laag biedt een eenvoudig mechanisme om beperkte apparaten te verbinden met Azure IoT en de geavanceerde functies van Azure RTO'S te gebruiken. Zie [Wat is Microsoft Azure rto's](/azure/rtos/overview-rtos)voor meer informatie.

### <a name="toolchains"></a>Toolchains

De Azure RTO'S-voor beelden zijn voorzien van verschillende soorten IDE-en hulpprogramma keten-combi Naties, zoals:

- IAR: de [embedded Workbench](https://www.iar.com/iar-embedded-workbench/) IDE van IAR
- GCC/CMake: gebouwd op de open-source [cmake](https://cmake.org/) build system en [GNU arm embedded hulpprogramma keten](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: [MCUXPRESSO IDE](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) van NXP
- STM32Cube: [STM32CUBE IDE](https://www.st.com/en/development-tools/stm32cubeide.html) van STMicroelectronic
- MPLAB: de [MPLAB X IDE](https://www.microchip.com/mplab/mplab-x-ide) van de micro chip

### <a name="samples"></a>Voorbeelden

Voor voor beelden die laten zien hoe u aan de slag gaat met verschillende apparaten met Azure RTO'S en IoT Plug en Play, raadpleegt u de volgende tabel:

Fabrikant | Apparaat | Voorbeelden |
| --- | --- | --- |
| Micro | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [Gcc/cmake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [Gcc/cmake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [Gcc/cmake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [Gcc/cmake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over de opties voor het implementeren van IoT Plug en Play op beperkte apparaten, is een voorgestelde volgende stap meer informatie over de [IoT Plug en Play-conventies](concepts-convention.md).