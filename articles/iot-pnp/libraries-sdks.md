---
title: IoT Plug en Play-bibliotheken en Sdk's
description: Informatie over de apparaten en service bibliotheken die beschikbaar zijn voor het ontwikkelen van IoT Plug en Play-oplossingen.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: e8320c0b482daa6371736588250ad0bd8d9b7491
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435372"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Micro soft Sdk's voor IoT Plug en Play

Met de IoT Plug en Play-bibliotheken en Sdk's kunnen ontwikkel aars IoT-oplossingen bouwen met behulp van verschillende programmeer talen op meerdere platforms. De volgende tabel bevat koppelingen naar voor beelden en Quick starts waarmee u aan de slag kunt gaan:

## <a name="device-sdks"></a>Apparaat-SDK's

| Taal | Pakket | Code opslagplaats | Voorbeelden | Snelstart | Naslaginformatie |
|---|---|---|---|---|---|
| C-apparaat | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device.md) | [Verwijzing](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-apparaat | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Verbinding maken met IoT Hub](quickstart-connect-device.md) | [Verwijzing](/dotnet/api/microsoft.azure.devices.client) |
| Java-apparaat | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Verbinding maken met IoT Hub](quickstart-connect-device.md) | [Verwijzing](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python-apparaat | [PIP 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device.md) | [Verwijzing](/python/api/azure-iot-device/azure.iot.device?preserve-view=true&view=azure-python) |
| Knoop punt-apparaat | [NPM 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Verbinding maken met IoT Hub](quickstart-connect-device.md) | [Verwijzing](/javascript/api/azure-iot-device/) |
| Inge sloten C-apparaat | N.v.t. | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Voorbeelden](howto-use-embedded-c.md#samples) | [Embedded C gebruiken](howto-use-embedded-c.md) | N.v.t.

## <a name="service-sdks"></a>Service-SDK's

| Platform  | Pakket | Code opslagplaats | Voorbeelden | Snelstart | Naslaginformatie |
|---|---|---|---|---|---|
| .NET-IoT Hub-service | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N.v.t. | [Verwijzing](/dotnet/api/microsoft.azure.devices) |
| Java-IoT Hub-service | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N.v.t. | [Verwijzing](/java/api/com.microsoft.azure.sdk.iot.service) |
| Node-IoT Hub-service | [NPM 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N.v.t. | [Verwijzing](/javascript/api/azure-iothub/) |
| Python-Digital Apparaatdubbels-service | [PIP 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interactie met IoT Hub Digital Apparaatdubbelse API](quickstart-service.md) | N.v.t. |
| Node-Digital Apparaatdubbels-service | [NPM 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Voorbeelden](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interactie met IoT Hub Digital Apparaatdubbelse API](quickstart-service.md) | N.v.t. |

## <a name="next-steps"></a>Volgende stappen

Als u de Sdk's en bibliotheken wilt uitproberen, raadpleegt u de  [hand leiding voor ontwikkel aars](concepts-developer-guide-device.md) en de Quick Start van het [apparaat](quickstart-connect-device.md) en de [service Snelstartgids](quickstart-service.md).