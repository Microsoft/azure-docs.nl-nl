---
title: Update van het apparaat voor IoT Hub update-manifest | Microsoft Docs
description: Meer informatie over hoe eigenschappen tijdens een update van de Device Update service naar het apparaat worden verzonden
author: andbrown
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ea438a4a8db8dce9cf2acb0b5b3ff4e250c3cf39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662515"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>Update-manifest voor het apparaat voor IoT Hub

## <a name="overview"></a>Overzicht

Het bijwerken van het apparaat voor IoT Hub gebruikt een _Update manifest_ voor het communiceren van acties en ook meta gegevens die deze acties ondersteunen via de eigenschappen [AzureDeviceUpdateCore. OrchestratorMetadata: 4](./device-update-plug-and-play.md)interface.
In dit document worden de basis principes beschreven van de manier waarop de `updateManifest` eigenschap in de `AzureDeviceUpdateCore.OrchestratorMetadata:4` interface wordt gebruikt door de apparaat Update Agent. De `AzureDeviceUpdateCore.OrchestratorMetadata:4` interface-eigenschappen worden verzonden vanaf de update van het apparaat voor IOT hub service naar de Update Agent van het apparaat. Het `updateManifest` is een GESERIALISEERD JSON-object dat wordt geparseerd door de apparaat Update Agent.

### <a name="an-example-update-manifest"></a>Een voor beeld van een update manifest

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

Het doel van het update manifest is om de inhoud van een update te beschrijven, namelijk de identiteit, het type, de geïnstalleerde criteria en de meta gegevens van het bestand bij te werken. Daarnaast is het update manifest cryptografisch ondertekend zodat de Update Agent van het apparaat de echtheid kan controleren. Raadpleeg de beveiligings documentatie over het bijwerken van het [apparaat](./device-update-security.md) voor meer informatie over hoe het update manifest wordt gebruikt voor het veilig importeren van inhoud.

## <a name="import-manifest-vs-update-manifest"></a>Manifest exporteren versus update manifest

Het is belang rijk om te begrijpen wat de verschillen zijn tussen het import manifest en de concepten van het update manifest in het bijwerken van het apparaat voor IoT Hub. 
* Het [import manifest](./import-concepts.md) wordt gemaakt door degene die de overeenkomstige update maakt. Hierin wordt de inhoud beschreven van de update die wordt geïmporteerd in update van het apparaat voor IoT Hub. 
* Het update manifest wordt automatisch gegenereerd door de update van het apparaat voor IoT Hub service, met een aantal van de eigenschappen die zijn gedefinieerd in het import manifest. Het wordt gebruikt om relevante informatie te communiceren met de Update Agent van het apparaat tijdens het update proces. 

Elk manifest type heeft zijn eigen schema en schema versie.

## <a name="update-manifest-properties"></a>Manifest eigenschappen bijwerken

De high-level definities van de manifest eigenschappen van de update zijn te vinden in de interface definities die [hier](./device-update-plug-and-play.md)worden gevonden. Om een diepere context te bieden, gaan we eens kijken naar de eigenschappen en hoe ze in het systeem worden gebruikt.

### <a name="updateid"></a>updateId

Bevat de `provider` , `name` en, `version` die de exacte update van het apparaat vertegenwoordigt voor IOT hub update-identiteit die wordt gebruikt om compatibele apparaten te bepalen voor de update.

### <a name="updatetype"></a>updateType

Vertegenwoordigt het type update dat wordt verwerkt door een specifiek type updatehandler. Het volgt de vorm van `microsoft/swupdate:1` voor een op een installatie kopie gebaseerde update en `microsoft/apt:1` voor een update op basis van een pakket (Zie de `Update Handler Types` sectie hieronder).

### <a name="installedcriteria"></a>installedCriteria

Een teken reeks die informatie bevat die nodig is voor de updatehandler van de apparaat-Update agent om te bepalen of de update op het apparaat is geïnstalleerd. In de `Update Handler Types` sectie wordt de indeling van de `installedCriteria` , voor elk update type dat wordt ondersteund door de apparaat bijwerken voor IOT hub, gedocumenteerd.

### <a name="files"></a>bestanden

Geeft de Update Agent van het apparaat aan welke bestanden moeten worden gedownload en de hash die wordt gebruikt om te controleren of de bestanden correct zijn gedownload.
Bekijk de inhoud van de eigenschap nader bekeken `files` :

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

Buiten het `updateManifest` is de `fileUrls` matrix van het JSON-object.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

Zowel `FILE_ID_STRING` in, in `fileUrls` en `files` zijn hetzelfde (bijvoorbeeld ' 0000 ' in `files` heeft de URL op ' 0000 ' in `fileUrls` ).

### <a name="manifestversion"></a>manifestVersion

Een teken reeks die de schema versie vertegenwoordigt.

## <a name="update-handler-types"></a>Typen Updatehandler

|Update methode|Type Updatehandler|Update type|Geïnstalleerde criteria|Verwachte bestanden voor publicatie|
|-------------|-------------------|----------|-----------------|--------------|
|Op basis van een installatie kopie|SWUpdate|"micro soft/swupdate: versie"|De referentie afbeelding slaat de hint van de versie op in het/etc/Adu-version-bestand.  |. SWU-bestand dat SWUpdate-installatie kopie bevat|
|Op pakket gebaseerd|APT|"micro soft/apt: versie"|`<name>` + '-' + `<version>` (gedefinieerde eigenschappen in het manifest bestand apt|`<APT Update Manifest>`. json dat de APT-configuratie en pakket lijst bevat|

