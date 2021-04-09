---
title: Informatie over het bijwerken van het apparaat voor Azure IoT Hub configuratie bestand | Microsoft Docs
description: Informatie over het bijwerken van het apparaat voor Azure IoT Hub configuratie bestand.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662340"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>Update van het apparaat voor IoT Hub configuratie bestand

De adu-conf.txt is een optioneel bestand dat kan worden gemaakt voor het beheren van de volgende configuraties.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["fabrikant"]
* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["model"]
* DeviceInformation. Manufacturer
* DeviceInformation. model
* Verbindings reeks voor apparaat (als dit niet bekend is bij de Update-Agent van het apparaat).

## <a name="purpose"></a>Doel
De Update Agent van het apparaat probeert eerst de `manufacturer` waarden en op `model` te halen van het apparaat dat voor de [Interface-laag](device-update-agent-overview.md#the-interface-layer)moet worden gebruikt. Als dat niet lukt, zoekt de Update Agent van het apparaat de volgende keer naar het bestand ' adu-conf.txt ' en worden de waarden van deze gebruikt. Als beide pogingen niet lukken, worden de [standaard](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) waarden gebruikt voor de Update Agent van het apparaat.

Meer informatie over de [Adu-kern interface](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) en de [interface voor apparaatgegevens](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

## <a name="file-location"></a>Bestands locatie

Maak in Linux-systeem in de partitie of schijf `adu` met de naam een tekst bestand met de naam ' adu-conf.txt ' met de volgende velden.

## <a name="list-of-fields"></a>Lijst velden

|Naam|Beschrijving|
|-----------|--------------------|
|connection_string|Vooraf ingerichte connection string het apparaat kan gebruiken om verbinding te maken met de IoT Hub. Opmerking: niet vereist als u de Update Agent van het apparaat inricht via de [Azure IOT-identiteits service](https://azure.github.io/iot-identity-service/)|
|aduc_manufacturer|Gemeld door de `AzureDeviceUpdateCore:4.ClientMetadata:4` interface voor het classificeren van het apparaat voor het doel van de update-implementatie.|
|aduc_model|Gemeld door de `AzureDeviceUpdateCore:4.ClientMetadata:4` interface voor het classificeren van het apparaat voor het doel van de update-implementatie.|
|manufacturer|Gemeld door de apparaat Update Agent als onderdeel van de `DeviceInformation` interface.|
|model|Gemeld door de apparaat Update Agent als onderdeel van de `DeviceInformation` interface.|

## <a name="example-adu-conftxt-file-contents"></a>Voor beeld van bestands inhoud adu-conf.txt

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
