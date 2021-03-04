---
title: Synchronisatie van Azure Kinect-apparaat vastleggen
description: Meer informatie over het synchroniseren van Azure Kinect Capture-apparaten met behulp van de Azure Kinect sensor SDK.
author: xthexder
ms.author: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, sensor, SDK, diepte, RGB, intern, extern, synchronisatie, ringnet werk, fase verschuiving
ms.openlocfilehash: ce0c72d3d708d5696a9775b3885f278f0c23cac1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034303"
---
# <a name="capture-azure-kinect-device-synchronization"></a>Synchronisatie van Azure Kinect-apparaat vastleggen

Met de Kinect-hardware van Azure kunt u de opname tijd van kleur en diepte kopieën uitlijnen. De uitlijning tussen de camera's op hetzelfde apparaat is **interne synchronisatie**. De afstemming van de vastleg tijd op meerdere apparaten die zijn verbonden, is **externe synchronisatie**. De microfoon matrix werkt onafhankelijk van de kleur en diepte camera's.

## <a name="device-internal-synchronization"></a>Interne synchronisatie van apparaat

Het vastleggen van de installatie kopie tussen de afzonderlijke camera's wordt gesynchroniseerd in hardware. In elke [k4a_capture_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__capture__t.html) die afbeeldingen van zowel de kleur-als de diepte sensor bevat, worden de tijds tempels van de afbeeldingen uitgelijnd op basis van de besturings modus van de hardware. Standaard zijn de installatie kopieën van een Capture Center van belichtings uitlijning. De relatieve timing van diepte-en kleur opnamen kan worden aangepast met behulp `depth_delay_off_color_usec` van het veld van [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

## <a name="device-external-synchronization"></a>Externe synchronisatie van apparaat

Zie [Setup External Synchronization](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices) for hardware setup (Engelstalig).

De software voor elk aangesloten apparaat moet worden geconfigureerd om te kunnen werken in een **Master** -of **ondergeschikte** modus. Deze instelling wordt geconfigureerd op de [k4a_device_configuration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html).

Wanneer u externe synchronisatie gebruikt, moeten ondergeschikte camera's altijd worden gestart voordat de hoofd tijd van de tijds tempels correct wordt uitgelijnd.

### <a name="subordinate-mode"></a>Ondergeschikte modus

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_SUBORDINATE
```

### <a name="master-mode"></a>Master modus

```C
k4a_device_configuration_t deviceConfig;
deviceConfig.wired_sync_mode = K4A_WIRED_SYNC_MODE_MASTER;
```

### <a name="retrieving-synchronization-jack-state"></a>Status synchronisatie aansluiting ophalen

Als u de huidige status van de synchronisatie-invoer en-uitvoer aansluitingen programmatisch wilt ophalen, gebruikt u de functie [k4a_device_get_sync_jack](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0209ac87bfd055163677321b0304e962.html#ga0209ac87bfd055163677321b0304e962) .

## <a name="next-steps"></a>Volgende stappen

U weet nu hoe u de synchronisatie van apparaten kunt inschakelen en vastleggen. U kunt ook bekijken hoe u gebruikt 

>[!div class="nextstepaction"]
>[Azure Kinect sensor SDK record and Play-API](record-playback-api.md)
