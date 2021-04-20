---
title: Informatie over apparaatupdates Azure IoT Hub agent| Microsoft Docs
description: Informatie over apparaatupdates voor Azure IoT Hub agent.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d97287657b1e1fe7d540e8811c90794aaa5fece
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739562"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Overzicht van Device Update for IoT Hub Agent

De Agent voor apparaatupdates bestaat uit twee conceptuele lagen:

* De interfacelaag bouwt voort op [Azure IoT Plug en Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) zodat berichten kunnen worden gestroomd tussen de Agent voor apparaatupdates en Apparaatupdateservices.
* De platformlaag is verantwoordelijk voor de updateacties op hoog niveau van Downloaden, Installeren en Toepassen, die mogelijk platform- of apparaatsspecifieke acties zijn.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Agent-implementaties." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>De interfacelaag

De interfacelaag bestaat uit de [ADU Core Interface en](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) [de Device Information Interface](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

Deze interfaces zijn afhankelijk van een configuratiebestand voor standaardwaarden. De standaardwaarden zijn aduc_manufacturer en aduc_model voor de AzureDeviceUpdateCore-interface en het model en de fabrikant voor de DeviceInformation-interface. [Meer informatie over](device-update-configuration-file.md) het configuratiebestand.

### <a name="adu-core-interface"></a>ADU Core Interface

De interface 'ADU Core' is het primaire communicatiekanaal tussen de agent voor apparaatupdates en services. [Meer informatie](device-update-plug-and-play.md#adu-core-interface) over deze interface.

### <a name="device-information-interface"></a>Interface voor apparaatgegevens

De Interface voor apparaatgegevens wordt gebruikt om de interface te `Azure IoT PnP DeviceInformation` implementeren. [Meer informatie](device-update-plug-and-play.md#device-information-interface) over deze interface.

## <a name="the-platform-layer"></a>De platformlaag

Er zijn twee implementaties van de platformlaag. De simulatorplatformlaag heeft een triviale implementatie van de updateacties en wordt gebruikt voor het snel testen en evalueren van apparaatupdates voor IoT Hub en installatie. Wanneer de agent voor apparaatupdates is gebouwd met de simulatorplatformlaag, wordt deze de Agent voor de apparaatupdatesimulator of gewoon simulator gebruikt. [Meer informatie over](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) het gebruik van de simulatoragent. De Linux-platformlaag kan worden geïntegreerd [met Delivery Optimization](https://github.com/microsoft/do-client) voor downloads en wordt gebruikt in onze Raspberry Pi-referentieafbeelding en alle clients die worden uitgevoerd op Linux-systemen.

### <a name="simulator-platform-layer"></a>Simulatorplatformlaag

De implementatie van de simulatorplatformlaag vindt u in de en kan worden gebruikt voor het testen en evalueren van apparaatupdates `src/platform_layers/simulator_platform_layer` voor IoT Hub.  Veel van de acties in de simulator-implementatie worden nage mocked om fysieke wijzigingen te verminderen om te experimenteren met Apparaatupdate voor IoT Hub.  Een end-to-end gesimuleerde update kan worden uitgevoerd met behulp van deze platformlaag. [Meer informatie over](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) het uitvoeren van de simulatoragent.

### <a name="linux-platform-layer"></a>Linux-platformlaag

De implementatie van de Linux-platformlaag vindt u in de en kan worden geïntegreerd met de Delivery Optimization-client voor downloads en wordt gebruikt in onze Raspberry Pi-referentieafbeelding en alle clients die worden uitgevoerd op `src/platform_layers/linux_platform_layer` Linux-systemen. [](https://github.com/microsoft/do-client/releases)

Deze laag kan worden geïntegreerd met verschillende update-handlers om het installatieprogramma te implementeren. De update-handler roept bijvoorbeeld een shellscript aan om het uitvoerbare bestand aan te roepen `SWUpdate` om een update uit te `SWUpdate` voeren.

## <a name="update-handlers"></a>Handlers bijwerken

Update-handlers zijn onderdelen die inhoud of onderdelen van de update verwerken die specifiek zijn voor het installatieprogramma. Implementaties van update-handler zijn in `src/content_handlers` .

### <a name="simulator-update-handler"></a>Simulatorupdate-handler

De simulatorupdate-handler wordt gebruikt door de simulatorplatformlaag en kan worden gebruikt met de Linux-platformlaag om valse interacties met een inhouds-handler uit te voeren. De Simulator Update Handler implementeert de Update Handler-API's met voornamelijk no-ops. De implementatie van de Simulator Update Handler vindt u hieronder:
* [Simulator voor het bijwerken van afbeeldingen](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [APT-simulator voor pakketupdates](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Opmerking: het veld InstalledCriteria in de PnP-interface AzureDeviceUpdateCore moet de sha256-hash van de inhoud zijn. Dit is dezelfde hash die aanwezig is in het [importmanifestobject](import-update.md#create-a-device-update-import-manifest). [Meer informatie](device-update-plug-and-play.md) over `installedCriteria` en de `AzureDeviceUpdateCore` interface.

### <a name="swupdate-update-handler"></a>`SWUpdate` Handler bijwerken

De update-handler is geïntegreerd met het uitvoerbare opdrachtregelbare bestand en andere shell-opdrachten om A/B-updates specifiek te implementeren voor de `SWUpdate` `SWUpdate` Raspberry Pi-referentieafbeelding. U vindt de meest recente Raspberry Pi-referentieafbeelding [hier.](https://github.com/Azure/iot-hub-device-update/releases) De `SWUpdate` update-handler wordt geïmplementeerd in [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>APT-update-handler

De APT-update-handler verwerkt een APT-specifiek updatemanifest en roept APT aan om de opgegeven Debian-pakketten te installeren of bij te werken.

## <a name="self-update-device-update-agent"></a>Agent voor apparaatupdates zelf bijwerken

De agent voor apparaatupdates en de afhankelijkheden ervan kunnen worden bijgewerkt via de apparaatupdate voor IoT Hub pijplijn. Als u een update op basis van afbeeldingen gebruikt, moet u de meest recente agent voor apparaatupdates opnemen in de nieuwe afbeelding. Als u een update op basis van een pakket gebruikt, moet u de agent voor apparaatupdates en de gewenste versie in het apt-manifest opnemen, net als elk ander pakket. [Meer informatie](device-update-apt-manifest.md) over apt-manifest. U kunt de geïnstalleerde versie van de Agent voor apparaatupdates en de Delivery Optimization-agent controleren in de sectie Apparaateigenschappen van uw [IoT-apparaat dubbel.](../iot-hub/iot-hub-devguide-device-twins.md) [Meer informatie over apparaateigenschappen onder ADU Core Interface](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Volgende stappen
[Informatie over het configuratiebestand van de agent voor apparaatupdates](device-update-configuration-file.md)