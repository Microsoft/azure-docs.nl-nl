---
title: IoT Plug en Play huidige versie | Microsoft Docs
description: Meer informatie over wat er in de huidige IoT Plug en Play-versie is opgenomen.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7b6669ca55dc9b94dc5d702e54b42011120b8812
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831872"
---
# <a name="what-is-in-the-current-iot-plug-and-play-release"></a>Wat is er in de huidige IoT Plug and Play-versie opgenomen?

In dit artikel vindt u een overzicht van de tools, SDK's en API's die ondersteuning bieden voor de huidige IoT Plug en Play-versie. Versienummers die worden weergegeven, zijn gebaseerd op het versienummer op het moment dat IoT Plug en Play algemeen beschikbaar werd. Het is mogelijk dat versienummers na de release worden verhoogd.

## <a name="modeling-language"></a>Modelleringstaal

[DTDL (Digital Twins Definition Language) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Zie [Conventies voor IoT Plug en Play](concepts-convention.md) voor meer informatie over hoe IoT Plug en Play-apparaten met DTDL werken.

## <a name="tools-and-utilities"></a>Hulpprogramma's

- Azure IoT explorer 0.12.0.

    Zie [Azure IoT Explorer installeren en gebruiken](howto-use-iot-explorer.md) voor meer informatie.

- VS Code-extensie 1.0.0.

    Zie voor meer informatie [De DTDL-ontwerptools installeren en gebruiken](howto-use-dtdl-authoring-tools.md).

- Visual Studio 2019 extensie1.0.0.

    Zie voor meer informatie [De DTDL-ontwerptools installeren en gebruiken](howto-use-dtdl-authoring-tools.md).

- Azure CLI IoT-extensie 0.10.0.

    De Azure IoT-extensie bevat opdrachten waarmee u apparaten kunt certificeren. Zie `az iot product -h`.

## <a name="libraries-and-sdks"></a>Bibliotheken en SDK's

Zie [Microsoft SDK's voor IoT Plug en Play](libraries-sdks.md) voor meer informatie over de bibliotheken en SDK's.

- Apparaat-SDK voor C [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- Ingesloten apparaat-SDK voor C [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- Apparaat-SDK voor .NET [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- Apparaat-SDK voor Java [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Apparaat-SDK voor Python [PIP 2.3.0](https://pypi.org/project/azure-iot-device/)
- Apparaat-SDK voor Node.js [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET - IoT Hub-service [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java - IoT Hub-service [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js - IoT Hub-service [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python - IoT Hub/Digital Twins-service [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL-model-parser [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

## <a name="rest-apis"></a>REST-API’s

REST API [2020-09-30](/rest/api/iothub).

Zie [IoT Plug en Play-ontwikkelaarshandleiding](concepts-developer-guide-service.md) voor meer informatie.

## <a name="iot-hub"></a>IoT Hub

IoT Plug en Play wordt ondersteund door IoT Hub in alle regio's. IoT Plug en Play wordt alleen ondersteund door standaard of gratis IoT-hubs.

## <a name="announcements"></a>Aankondigingen

Raadpleeg de volgende blogberichten voor actuele en eerdere IoT Plug en Play-aankondigingen:

- [Vernieuwing openbare preview (gepost op 29 augustus 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Uw apparaten voorbereiden en certificeren voor IoT Plug en Play (gepost op 26 augustus 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT Plug en Play is nu beschikbaar als preview (gepost op 22 augustus 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Bouwen met Azure IoT Central en IoT Plug en Play (gepost op 7 mei 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

## <a name="next-steps"></a>Volgende stappen

De voorgestelde volgende stap is het controleren van [Wat is IoT Plug en Play?](overview-iot-plug-and-play.md).