---
title: Informatie over het bijwerken van het apparaat voor Azure IoT Hub agent | Microsoft Docs
description: Informatie over het bijwerken van het apparaat voor Azure IoT Hub-agent.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e932238849baf267983fb3ca1ebb082db169d9fd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679598"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Overzicht van Device Update for IoT Hub Agent

De Update-Agent van het apparaat bestaat uit twee conceptuele lagen:

* De interface-laag bouwt voort op [Azure IoT Plug en Play (PnP)](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play) zodat berichten kunnen stromen tussen de Update-Agent van het apparaat en de Update Services van het apparaat.
* De laag van het platform is verantwoordelijk voor het downloaden, installeren en Toep assen van de Update acties op hoog niveau die mogelijk platform of apparaat specifiek zijn.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Agent implementaties." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>De interface-laag

De interface-laag bestaat uit de [Adu-kern interface](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) en de [apparaatgegevens interface](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

Deze interfaces zijn afhankelijk van een configuratie bestand voor standaard waarden. De standaard waarden zijn aduc_manufacturer en aduc_model voor de interface en het model en de fabrikant van de AzureDeviceUpdateCore voor de DeviceInformation-interface. [Meer informatie over](device-update-configuration-file.md) het configuratie bestand.

### <a name="adu-core-interface"></a>ADU-kern interface

De ADU core-interface is het primaire communicatie kanaal tussen de Update Agent en-services van het apparaat. Meer [informatie](device-update-plug-and-play.md#adu-core-interface) over deze interface.

### <a name="device-information-interface"></a>Interface voor apparaatgegevens

De apparaatgegevens interface wordt gebruikt voor het implementeren van de `Azure IoT PnP DeviceInformation` interface. Meer [informatie](device-update-plug-and-play.md#device-information-interface) over deze interface.

## <a name="the-platform-layer"></a>De laag van het platform

Er zijn twee implementaties van de laag van het platform. De laag van het Simulator-platform heeft een triviale implementatie van de Update acties en wordt gebruikt voor het snel testen en evalueren van de update van het apparaat voor IoT Hub Services en Setup. Wanneer de Update Agent van het apparaat is gebouwd met de Simulator-platform-laag, verwijzen we naar de update Simulator-agent of alleen Simulator. Meer [informatie](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) over het gebruik van de Simulator-agent. De Linux-platform laag is geïntegreerd met [Delivery Optimization](https://github.com/microsoft/do-client) voor down loads en wordt gebruikt in onze Raspberry Pi-referentie kopie en alle clients die worden uitgevoerd op Linux-systemen.

### <a name="simulator-platform-layer"></a>Simulator-platform-laag

De implementatie van de Simulator-platform laag vindt u in de `src/platform_layers/simulator_platform_layer` en kan worden gebruikt voor het testen en evalueren van de update van het apparaat voor IOT hub.  Veel van de acties in de ' Simulator '-implementatie worden gesimuleerd om fysieke wijzigingen te verminderen met het bijwerken van het apparaat voor IoT Hub.  Een end-to-end ' gesimuleerde ' update kan worden uitgevoerd met deze laag van het platform. Meer [informatie](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) over het uitvoeren van de Simulator-agent.

### <a name="linux-platform-layer"></a>Linux-platform-laag

De implementatie van de Linux-platform laag vindt u in de en kan worden `src/platform_layers/linux_platform_layer` geïntegreerd met de [Delivery Optimization-client](https://github.com/microsoft/do-client/releases) voor down loads en wordt gebruikt in onze Raspberry Pi-referentie kopie en op alle clients die worden uitgevoerd op Linux-systemen.

Deze laag kan worden geïntegreerd met verschillende update-handlers voor het implementeren van het installatie programma. Zo roept de updatehandler `SWUpdate` een shell script aan om het `SWUpdate` uitvoer bare bestand aan te roepen om een update uit te voeren.

## <a name="update-handlers"></a>Handlers bijwerken

Update-handlers zijn onderdelen die inhoud of Installer-specifieke onderdelen van de update verwerken. Er zijn update-handler-implementaties in `src/content_handlers` .

### <a name="simulator-update-handler"></a>Simulator-updatehandler

De update-handler van de simulator wordt gebruikt door de laag van het Simulator-platform en kan worden gebruikt met de laag van het Linux-platform tot valse interacties met een content handler. De update-handler van de update implementeert de Api's van de updatehandler zonder OPS. De implementatie van de update-handler voor Simulator vindt u hieronder:
* [Image update Simulator](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Pakket update apt Simulator](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Opmerking: het veld InstalledCriteria in de AzureDeviceUpdateCore PnP-interface moet de sha256 hash van de inhoud zijn. Dit is dezelfde hash die aanwezig is in het [manifest object voor importeren](import-update.md#create-device-update-import-manifest). Meer [informatie](device-update-plug-and-play.md) over `installedCriteria` en de `AzureDeviceUpdateCore` interface.

### <a name="swupdate-update-handler"></a>`SWUpdate` Updatehandler

De updatehandler `SWUpdate` wordt geïntegreerd met het `SWUpdate` uitvoer bare bestand van de opdracht regel en andere shell opdrachten voor het implementeren van A/B-updates specifiek voor de Raspberry Pi-referentie afbeelding. Zoek [hier](https://github.com/Azure/iot-hub-device-update/releases)de meest recente Raspberry Pi-referentie afbeelding. De updatehandler `SWUpdate` wordt geïmplementeerd in [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler).

### <a name="apt-update-handler"></a>APT-updatehandler

De APT-updatehandler verwerkt een APT-specifiek update manifest en roept APT aan om de opgegeven Debian-pakket (en) te installeren of bij te werken.

## <a name="self-update-device-update-agent"></a>Update-Agent voor apparaat zelf bijwerken

De Update-Agent van het apparaat en de bijbehorende afhankelijkheden kunnen worden bijgewerkt via de update van het apparaat voor IoT Hub pijp lijn. Als u een update op basis van een installatie kopie gebruikt, neemt u de meest recente apparaat Update Agent op in de nieuwe installatie kopie. Als u een update op basis van een pakket gebruikt, neemt u de Update Agent voor het apparaat en de gewenste versie op in het apt-manifest, zoals elk ander pakket. Meer [informatie](device-update-apt-manifest.md) over apt-manifest. U kunt de geïnstalleerde versie van de Update Agent van het apparaat en de agent voor de leverings optimalisatie controleren in het gedeelte apparaateigenschappen van uw [IOT-apparaat](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins). Meer [informatie over apparaateigenschappen onder de Adu core-interface](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Volgende stappen
[Informatie over het configuratie bestand van de apparaat-Update Agent](device-update-configuration-file.md)

