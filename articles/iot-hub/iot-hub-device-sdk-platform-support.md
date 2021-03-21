---
title: Ondersteuning voor Azure IoT Device Sdk's-platform | Microsoft Docs
description: Er zijn Sdk's voor open source-apparaten beschikbaar op GitHub in C, .NET (C#), Java, Node.js en Python, om apparaten te verbinden met Azure IoT Hub en de Device Provisioning Service (DPS).
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: b8f8403617a5fc8b442e8a3f74ab9226fe1479fe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588757"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Ondersteuning voor Azure IoT Device Sdk's-platform

Micro soft streeft ernaar om het universum van Azure IoT Hub-compatibele apparaten voortdurend uit te breiden. Micro soft publiceert het open-source apparaat Sdk's op GitHub om apparaten te verbinden met Azure IoT Hub en de Device Provisioning-Service. De apparaat-Sdk's zijn beschikbaar voor C, .NET (C#), Java, Node.js en python. Micro soft test elke SDK om ervoor te zorgen dat deze wordt uitgevoerd op de ondersteunde configuraties die hiervoor worden ondersteund in de sectie [micro soft sdk's and Device platform support](#microsoft-sdks-and-device-platform-support) .

Naast de Sdk's van het apparaat biedt micro soft verschillende andere mogelijkheden om klanten en ontwikkel aars in staat te stellen hun apparaten te verbinden met Azure IoT:

* Micro soft werkt samen met verschillende partner bedrijven om hen te helpen bij het publiceren van Development Kits, op basis van de Azure IoT C SDK, voor hun hardwareplatforms.

* Micro soft werkt samen met micro soft-vertrouwde partners om een steeds groeiende set apparaten te bieden die zijn getest en gecertificeerd voor Azure IoT. Zie [Azure Certified for IOT Device Catalog](https://catalog.azureiotsolutions.com/)(Engelstalig) voor een actuele lijst van deze apparaten.

* Micro soft biedt een platform Abstraction Layer (PAL) in de Azure IoT Hub Device C SDK waarmee ontwikkel aars de SDK eenvoudig naar hun platform kunnen overbrengen. Zie de [C SDK porting guidance](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)(Engelstalig) voor meer informatie.

In dit onderwerp vindt u informatie over de micro soft-Sdk's en de platform configuraties die ze ondersteunen, evenals alle andere opties die hierboven worden vermeld.

## <a name="microsoft-sdks-and-device-platform-support"></a>Ondersteuning voor micro soft-Sdk's en-platform

Micro soft publiceert open-source Sdk's op GitHub voor de volgende talen: C, .NET (C#), Node.js, Java en python. De Sdk's en hun afhankelijkheden worden vermeld in deze sectie. De Sdk's worden ondersteund op elk platform dat voldoet aan deze afhankelijkheden.

Voor elk van de vermelde Sdk's, micro soft:

* Bouwt en voert voortdurend end-to-end-tests uit op basis van de belangrijkste vertakking van de relevante SDK in GitHub op verschillende populaire platforms.  Om een test dekking te bieden in verschillende compiler versies, testen we doorgaans op de nieuwste LTS-versie en de meest populaire versie.

* Biedt installatie richtlijnen of installatie pakketten, indien van toepassing.

* Biedt volledige ondersteuning voor de Sdk's op GitHub met open-source code, een pad voor klant bijdragen en betrokkenheid van het product team met GitHub-problemen.

### <a name="c-sdk"></a>C SDK

De [Azure IOT hub C Device SDK](https://github.com/Azure/azure-iot-sdk-c) is getest met en ondersteunt de volgende configuraties.

| Besturingssysteem                  | TLS-bibliotheek                  | Aanvullende vereisten                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL of BearSSL | Berkeley-Sockets</br></br>De Portable Operating System Interface (POSIX)                       |
| iOS 12,2            | OpenSSL                      | XCode geëmuleerd in OSX 10.13.4                                                               |
| Windows 10-serie   | Veilige                     |                                                                                             |
| Mbed OS 5,4         | Mbed TLS 2                   | [MXChip IoT dev kit](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Azure Sphere-besturings systeem     | WolfSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | BearSSL                      | [ESP32 of ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python-SDK

De [Azure IOT hub python Device SDK](https://github.com/Azure/azure-iot-sdk-python) wordt getest met en ondersteunt de volgende configuraties.

| Besturingssysteem                  | Programma's                          |
|---------------------|-----------------------------------|
| Linux               | Python 2,7. *, 3,5 of hoger |
| macOS High Sierra   | Python 2,7. *, 3,5 of hoger |
| Windows 10-serie   | Python 2,7. *, 3,5 of hoger |

Alleen python-versie 3.5.3 of hoger ondersteunen de asynchrone Api's. we raden u aan om versie 3,7 of hoger te gebruiken.

### <a name="net-sdk"></a>.NET SDK

De [SDK voor de Azure IOT hub .net (C#)-apparaat](https://github.com/Azure/azure-iot-sdk-csharp) wordt getest met en ondersteunt de volgende configuraties.

| Besturingssysteem                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| Windows 10 Desktop-en server-Sku's   | .NET Core 2,1, .NET Framework 4.5.1 of .NET Framework 4,7 |

De .NET SDK kan ook worden gebruikt met Windows IoT core met de [Azure Device agent](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) of [een aangepaste NTService waarmee RPC kan worden gebruikt om te communiceren met UWP-toepassingen](/samples/microsoft/windows-iotcore-samples/ntservice-rpc/).

### <a name="nodejs-sdk"></a>Node.js SDK

De [SDK van het Azure IoT Hub Node.js-apparaat](https://github.com/Azure/azure-iot-sdk-node) wordt getest met en ondersteunt de volgende configuraties.

| Besturingssysteem                  | Knooppunt versie    |
|---------------------|-----------------|
| Linux               | LTS en actueel |
| Windows 10-serie   | LTS en actueel |

### <a name="java-sdk"></a>Java-SDK

De [Azure IOT hub Java-apparaat-SDK](https://github.com/Azure/azure-iot-sdk-java) wordt getest met en ondersteunt de volgende configuraties.

| Besturingssysteem                     | Java-versie |
|------------------------|--------------|
| Android API 28         | Java 8       |
| Linux x64             | Java 8       |
| Windows 10-familie x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Ontwikkel kits die door partners worden ondersteund

Micro soft werkt samen met verschillende partners voor het leveren van ontwikkel Kits voor verschillende architecturen voor micro processors. Deze partners hebben de Azure IoT C SDK naar hun platform geporteerd. Partners maken en onderhouden van de platform Abstraction Layer (PAL) van de SDK. Micro soft werkt met deze partners om uitgebreide ondersteuning te bieden.

| Partner             | Apparaten                            | Koppeling                     | Ondersteuning |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [ESP-Azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT-modem     | [Qualcomm LTE voor IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| STe elektronica | STM32L4-serie <br/> STM32F4-serie <br/>  STM32F7-serie <br/>  STM32L4 Discovery Kit voor IoT-knoop punt    | [X-KUBUS-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Ondersteuning](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | LaunchPad CC3220SF </br> LaunchPad CC3220S </br> LaunchPad CC3235SF </br> LaunchPad CC3235S </br> LaunchPad MSP432E4 | [Azure IoT-invoeg toepassing voor SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [TI E2E-forum](https://e2e.ti.com) <br/> [TI E2E-forum voor CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [TI E2E-forum voor MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>De Microsoft Azure IoT C SDK

Als uw platform niet onder een van de voor gaande secties valt, kunt u overwegen de Azure IoT C-SDK te porteren. Het is de poort van de C SDK voornamelijk het implementeren van de platform Abstraction Layer (PAL) van de SDK. De PAL definieert primitieven die de lijm bieden tussen uw apparaat en de functies van een hoger niveau in de SDK. Zie [richt lijnen voor poorting](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)voor meer informatie.

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Micro soft-partners en gecertificeerde Azure IoT-apparaten

Micro soft werkt met een aantal partners om het Azure IoT-universum voortdurend uit te breiden met Azure IoT-geteste en gecertificeerde apparaten.

* Zie [Microsoft Azure Certified for IOT Device Catalog](https://catalog.azureiotsolutions.com/)voor informatie over het bladeren door Azure IOT-gecertificeerde apparaten.

* Zie voor meer informatie over het Azure Certified voor IoT-ecosysteem [lid worden van het Certified voor IOT-ecosysteem](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Verbinding maken met IoT Hub zonder een SDK

Als u een van de IoT Hub apparaat-Sdk's niet kunt gebruiken, kunt u rechtstreeks verbinding maken met IoT Hub met behulp van een ondersteund communicatie protocol. Zie [ontwikkelen zonder Azure IOT SDK](iot-hub-devguide-no-sdk.md)voor meer informatie.

## <a name="support-and-other-resources"></a>Ondersteuning en andere bronnen

Als u problemen ondervindt bij het gebruik van de Azure IoT-apparaat-Sdk's, zijn er verschillende manieren om de hieronder beschreven ondersteuning te zoeken. Zie [Azure IOT-ondersteuning en Help-opties](../iot-fundamentals/iot-support-help.md)voor volledige informatie over al uw ondersteunings opties. 

**Fouten rapporteren** : fouten in de sdk's van het apparaat kunnen worden gerapporteerd op de pagina kwesties van het relevante github-project. Oplossingen maken snel hun eigen werk van het project in op product updates.

* [Problemen met Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Problemen met de SDK voor Azure IoT Hub .NET (C#)](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problemen met Azure IoT Hub Java SDK](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problemen met Azure IoT Hub Node.js SDK](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problemen met Azure IoT Hub python SDK](https://github.com/Azure/azure-iot-sdk-python/issues)

**Technische vragen** : u kunt technische vragen stellen over [micro soft Q&A](/answers/topics/azure-iot-sdk.html) en [stack overflow](https://stackoverflow.com/questions/tagged/azure-iot-sdk) met behulp van de tag *Azure-IOT-SDK*.

**Klanten ondersteuning van micro soft** : gebruikers met een [ondersteunings plan](https://azure.microsoft.com/support/plans/) kunnen het team van micro soft-klanten ondersteuning betrekken door rechtstreeks vanuit het [Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)een nieuwe ondersteunings aanvraag te maken.

**Functie aanvragen** : Azure IOT-functie aanvragen worden bijgehouden via de [gebruikers spraak pagina](https://feedback.azure.com/forums/321918-azure-iot)van het product.

## <a name="next-steps"></a>Volgende stappen

* [Apparaat- en service-SDK's](iot-hub-devguide-sdks.md)
* [Richt lijnen voor poort](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)