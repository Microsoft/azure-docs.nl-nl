---
title: Een microfoon matrix configureren-spraak service
titleSuffix: Azure Cognitive Services
description: Meer informatie over het configureren van een microfoon matrix, zodat de SDK voor spraak apparaten deze kan gebruiken.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: cf0580c96f5bf78f0444b2bb39088f2a417fd658
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025058"
---
# <a name="how-to-configure-a-microphone-array"></a>Een microfoonmatrix configureren

In dit artikel leert u hoe u een [microfoon matrix](./speech-devices-sdk-microphone.md)kunt configureren. Het bevat het instellen van de werk hoek en het selecteren van de microfoon die wordt gebruikt voor de speech-apparaten SDK.

De SDK voor spraak apparaten werkt het beste met een microfoon matrix die is ontworpen volgens [onze richt lijnen](./speech-devices-sdk-microphone.md). De configuratie van de microfoon matrix kan worden geleverd door het besturings systeem of worden geleverd via een van de volgende methoden.

De SDK voor spraak apparaten heeft eerst ondersteunde microfoon matrices door te selecteren uit een vaste set configuraties.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

In Windows wordt de configuratie van de microfoon matrix geleverd door het audio stuur programma.

De SDK voor spraak apparaten van v 1.11.0 ondersteunt ook de configuratie van een [JSON-bestand](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
In Windows wordt de geometrie gegevens van de microfoon matrix automatisch opgehaald uit het audio stuur programma. Dit betekent dat de eigenschappen `DeviceGeometry` ,  `SelectedGeometry` en `MicArrayGeometryConfigFile` optioneel zijn. We gebruiken het door gegeven [JSON-bestand](https://aka.ms/sdsdk-micarray-json) `MicArrayGeometryConfigFile` alleen voor het ophalen van het beamforming-bereik.

Als er een microfoon matrix wordt opgegeven met `AudioConfig::FromMicrophoneInput` , gebruiken we de opgegeven microfoon. Als een microfoon niet is opgegeven of `AudioConfig::FromDefaultMicrophoneInput` wordt aangeroepen, gebruiken we de standaard microfoon, die is opgegeven in geluids instellingen in Windows.
De micro soft audio-stack in de speech-apparaten SDK ondersteunt alleen de sampling voor voorbeeld frequenties die integraal veelvouden van 16 KHz zijn.

## <a name="linux"></a>Linux
In Linux moet de informatie over de geometrie van de microfoon worden verstrekt. Het gebruik van `DeviceGeometry` en `SelectedGeometry` blijft ondersteund. Het kan ook worden verschaft via het JSON-bestand met behulp van de `MicArrayGeometryConfigFile` eigenschap. Net als bij Windows kan het beamforming-bereik worden verstrekt door het JSON-bestand.

Als er een microfoon matrix wordt opgegeven met `AudioConfig::FromMicrophoneInput` , gebruiken we de opgegeven microfoon. Als een microfoon niet is opgegeven of `AudioConfig::FromDefaultMicrophoneInput` wordt aangeroepen, neemt u een record op van het alsa-apparaat met de naam *standaard*. Standaard verwijst *standaard* altijd naar kaart 0 apparaat 0, maar gebruikers kunnen dit wijzigen in het `asound.conf` bestand. 

De micro soft audio-stack in de speech-apparaten SDK ondersteunt alleen downsamplen voor voorbeeld frequenties die integraal veelvouden van 16 KHz zijn. Daarnaast worden de volgende indelingen ondersteund: 32-bits IEEE little endian float, 32-bits little endian ondertekende int, 24-bits little endian ondertekende int, 16-bits little endian ondertekende int en 8-bits ondertekende int.

## <a name="android"></a>Android
Momenteel wordt alleen [roobo v1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) ondersteund door de speech-apparaten SDK. Gedrag is hetzelfde als eerdere versies, met uitzonde ring van de `MicArrayGeometryConfigFile` eigenschap now kan worden gebruikt om een JSON-bestand met beamforming-bereik op te geven.

## <a name="microphone-array-configuration-json"></a>Configuratie van de microfoon matrix JSON

Het JSON-bestand voor de geometrie configuratie van de microfoon matrix volgt het [JSON-schema](https://aka.ms/sdsdk-micarray-json). Hieronder vindt u enkele voor beelden die volgen op het schema.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


of


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

of

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw spraak apparaat kiezen](get-speech-devices-sdk.md)