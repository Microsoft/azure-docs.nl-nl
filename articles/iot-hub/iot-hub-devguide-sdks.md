---
title: Meer informatie over de Azure IoT Sdk's | Microsoft Docs
description: "Hand leiding voor ontwikkel aars: informatie over en koppelingen naar de verschillende Azure IoT-apparaten en service-Sdk's die u kunt gebruiken om apps voor apparaten en back-end-apps te bouwen."
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: d35535c87ca20bfc573995bf15f79bc149619776
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611586"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Azure IoT Hub SDK's leren kennen en gebruiken

Er zijn twee categorieën met Sdk's (Software Development Kits) voor het werken met IoT Hub:

* Met **IOT hub apparaat-sdk's** kunt u apps bouwen die worden uitgevoerd op uw IOT-apparaten met behulp van de apparaatclient of de module-client. Deze apps verzenden telemetrie naar uw IoT-hub en kunnen eventueel berichten, taken, methoden of dubbele updates ontvangen van uw IoT-hub. U kunt deze Sdk's gebruiken om apparaat-apps te bouwen die gebruikmaken van [Azure IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md) conventies en modellen om hun mogelijkheden te adverteren voor IoT-Plug en Play-toepassingen. U kunt module client ook gebruiken om [modules](../iot-edge/iot-edge-modules.md) te schrijven voor [Azure IOT Edge runtime](../iot-edge/about-iot-edge.md).

* Met **IOT hub service-sdk's** kunt u back-end-toepassingen bouwen voor het beheren van uw IOT-hub en optioneel berichten verzenden, taken plannen, directe methoden aanroepen of gewenste eigenschappen updates verzenden naar uw IOT-apparaten of-modules.

Daarnaast bieden we ook een set Sdk's voor het werken met [Device Provisioning Service](../iot-dps/about-iot-dps.md).

* **Apparaat-sdk's voor apparaten** bieden u de mogelijkheid om apps te bouwen die worden uitgevoerd op uw IOT-apparaten om te communiceren met de Device Provisioning-Service.

* Met de **inrichtings service-sdk's** kunt u back-end-toepassingen bouwen voor het beheren van uw inschrijvingen in Device Provisioning Service.

Meer informatie over de [voor delen van het ontwikkelen met Azure IOT sdk's](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="os-platform-and-hardware-compatibility"></a>BESTURINGSSYSTEEM platform en hardwarecompatibiliteit

Ondersteunde platforms voor de Sdk's vindt u in [Azure IOT sdk's-platform ondersteuning](iot-hub-device-sdk-platform-support.md).

Voor meer informatie over SDK-compatibiliteit met specifieke hardwareapparaten raadpleegt u de [Azure Certified voor IOT-Apparaatbeheer](https://catalog.azureiotsolutions.com/) of een afzonderlijke opslag plaats.

## <a name="azure-iot-hub-device-sdks"></a>Sdk's van Azure IoT Hub apparaat

De Microsoft Azure IoT-apparaat-Sdk's bevatten code die het bouwen van toepassingen vereenvoudigt die verbinding maken met en worden beheerd door Azure IoT Hub Services.

Azure IoT Hub apparaat-SDK voor .NET: 

* Downloaden van [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  De naam ruimte is micro soft. Azure. devices. clients, die IoT Hub Apparaatclients bevat (DeviceClient, ModuleClient).
* [Broncode](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-naslaginformatie](/dotnet/api/microsoft.azure.devices)
* [Module verwijzing](/dotnet/api/microsoft.azure.devices.client.moduleclient)


Azure IoT Hub apparaat-SDK voor Embedded C (ANSI C-C99):
* [De embedded C SDK bouwen](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Broncode](https://github.com/Azure/azure-sdk-for-c)
* [Grootte grafiek](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart) voor beperkte apparaten.
* [API-naslaginformatie](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Identity/1.0.0/api/index.html)


Azure IoT Hub Device SDK voor C (ANSI C-C99):

* Installeren vanaf [apt-get, MBED, ARDUINO IDE of IOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Broncode](https://github.com/Azure/azure-iot-sdk-c)
* [De SDK voor C-apparaten compileren](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API-naslaginformatie](/azure/iot-hub/iot-c-sdk-ref/)
* [Module verwijzing](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Poort van de C SDK naar andere platforms](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentatie voor ontwikkel aars](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) voor meer informatie over meerdere compilaties, aan de slag gaat op verschillende platformen, enzovoort.
* [Informatie over het gebruik van Azure IoT Hub C SDK-resources](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Azure IoT Hub apparaat-SDK voor Java:

* Toevoegen aan [maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) -project
* [Broncode](https://github.com/Azure/azure-iot-sdk-java)
* [API-naslaginformatie](/java/api/com.microsoft.azure.sdk.iot.device)
* [Module verwijzing](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

Azure IoT Hub apparaat-SDK voor Node.js:

* Installeren vanaf [NPM](https://www.npmjs.com/package/azure-iot-device)
* [Broncode](https://github.com/Azure/azure-iot-sdk-node)
* [API-naslaginformatie](/javascript/api/azure-iot-device/)
* [Module verwijzing](/javascript/api/azure-iot-device/moduleclient)

Azure IoT Hub apparaat-SDK voor python:

* Installeren vanaf [PIP](https://pypi.org/project/azure-iot-device/)
* [Broncode](https://github.com/Azure/azure-iot-sdk-python)
* [API-naslaginformatie](/python/api/azure-iot-device)

Azure IoT Hub apparaat-SDK voor iOS:

* Installeren vanaf [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-verwijzing: Zie [C API-naslag informatie](/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Sdk's van Azure IoT Hub-service

De Sdk's van de Azure IoT-service bevatten code voor het ontwikkelen van toepassingen die rechtstreeks communiceren met IoT Hub om apparaten en beveiliging te beheren.

Azure IoT Hub Service-SDK voor .NET:

* Downloaden van [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  De naam ruimte is micro soft. Azure. devices, die IoT Hub service-clients bevat (RegistryManager, ServiceClients).
* [Broncode](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-naslaginformatie](/dotnet/api/microsoft.azure.devices)

Azure IoT Hub Service-SDK voor Java:

* Toevoegen aan [maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) -project
* [Broncode](https://github.com/Azure/azure-iot-sdk-java)
* [API-naslaginformatie](/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub Service-SDK voor Node.js:

* Downloaden van [NPM](https://www.npmjs.com/package/azure-iothub)
* [Broncode](https://github.com/Azure/azure-iot-sdk-node)
* [API-naslaginformatie](/javascript/api/azure-iothub/)

Azure IoT Hub Service SDK voor python:

* Downloaden van [PIP](https://pypi.python.org/pypi/azure-iot-hub/)
* [Broncode](https://github.com/Azure/azure-iot-sdk-python/tree/master)
* [API-naslaginformatie](/python/api/azure-iot-hub)

Azure IoT Hub Service-SDK voor C:

De Azure IoT Service SDK voor C wordt niet meer geactiveerd.
We zullen kritieke bugs blijven corrigeren, zoals crashes, beschadiging van gegevens en beveiligings problemen. We zullen geen nieuwe functies toevoegen of fouten corrigeren die niet essentieel zijn.

Ondersteuning voor Azure IOT Service SDK is beschikbaar in talen van een hoger niveau ([C#](https://github.com/Azure/azure-iot-sdk-csharp), [Java](https://github.com/Azure/azure-iot-sdk-java), [node](https://github.com/Azure/azure-iot-sdk-node), [python](https://github.com/Azure/azure-iot-sdk-python)).

* Downloaden van [apt-get, MBED, ARDUINO IDE of NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Broncode](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub Service SDK voor iOS:

* Installeren vanaf [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Raadpleeg de Leesmij-bestanden in de GitHub-opslag plaatsen voor informatie over het gebruik van taal-en platformspecifieke pakket beheerders om binaire bestanden en afhankelijkheden op uw ontwikkel machine te installeren.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure inrichten Sdk's

Met de **Microsoft Azure inrichten sdk's** kunt u apparaten inrichten op uw IOT hub met behulp van de [Device Provisioning Service](../iot-dps/about-iot-dps.md).

Apparaat-en service-Sdk's voor Azure inrichten voor C#:

* Downloaden van de SDK en [Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) van het [apparaat](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) vanuit NuGet.
* [Broncode](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API-naslaginformatie](/dotnet/api/microsoft.azure.devices.provisioning.client)

Apparaat-en service-Sdk's voor Azure inrichten voor C:

* Installeren vanaf [apt-get, MBED, ARDUINO IDE of IOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Broncode](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API-naslaginformatie](/azure/iot-hub/iot-c-sdk-ref/)

Apparaat-en service-Sdk's voor Azure inrichten voor Java:

* Toevoegen aan [maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk) -project
* [Broncode](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API-naslaginformatie](/java/api/com.microsoft.azure.sdk.iot.provisioning.device)

Apparaat-en service-Sdk's voor Azure inrichten voor Node.js:

* [Broncode](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API-naslaginformatie](/javascript/api/overview/azure/iothubdeviceprovisioning)
* SDK en service- [SDK](https://badge.fury.io/js/azure-iot-provisioning-service) van het [apparaat](https://badge.fury.io/js/azure-iot-provisioning-device) downloaden van NPM

Apparaat-en service-Sdk's voor Azure inrichten voor python:

* [Broncode](https://github.com/Azure/azure-iot-sdk-python)
* SDK en service- [SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) van het [apparaat](https://pypi.org/project/azure-iot-device/) downloaden van PIP

## <a name="next-steps"></a>Volgende stappen

Azure IoT Sdk's bieden ook een aantal hulpprogram ma's waarmee u de ontwikkeling kunt helpen:

* [iothub](https://github.com/Azure/iothub-diagnostics): een platformoverschrijdende opdracht regel programma voor het vaststellen van problemen met betrekking tot de verbinding met IOT hub.
* [Azure-IOT-Explorer](https://github.com/Azure/azure-iot-explorer): een platform toepassing voor meerdere platforms om verbinding te maken met uw IOT hub en toe te voegen/te beheren/communiceren met IOT-apparaten.

Relevante documenten met betrekking tot het ontwikkelen met behulp van de Azure IoT Sdk's:

* Meer informatie over het [beheren van connectiviteit en betrouw bare berichten](iot-hub-reliability-features-in-sdks.md) met behulp van de IOT hub sdk's.
* Meer informatie over het [ontwikkelen van mobiele platforms](iot-hub-how-to-develop-for-mobile-devices.md) , zoals IOS en Android.
* [Azure IoT SDK-platform ondersteuning](iot-hub-device-sdk-platform-support.md)

Andere naslag onderwerpen in deze IoT Hub ontwikkelaars handleiding zijn:

* [IoT Hub-eindpunten](iot-hub-devguide-endpoints.md)
* [IoT Hub query taal voor apparaatdubbels, Jobs en bericht routering](iot-hub-devguide-query-language.md)
* [Quota en beperkingen](iot-hub-devguide-quotas-throttling.md)
* [Ondersteuning voor IoT Hub MQTT](iot-hub-mqtt-support.md)
* [Naslag informatie over IoT Hub REST API](/rest/api/iothub/)
