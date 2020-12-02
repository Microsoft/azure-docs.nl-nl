---
title: Coarse-relokalisatie in Java
description: Uitgebreide uitleg over het aanmaken en vinden van ankers met behulp van coarse-relokalisatie in Java.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.custom: devx-track-java
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: fcc88699f4d464362e6d31d99bd028f538d161a5
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95999838"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-java"></a>Ankers maken en vinden met coarse-relokalisatie in Java

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Azure Spatial Anchors kunnen worden gekoppeld aan een apparaat, positioneringssensorgegevens met de ankers die u maakt. Deze gegevens kunnen ook worden gebruikt om snel te bepalen of er ankers zich in de buurt van uw apparaat bevinden. Zie [Coarse-relokalisatie](../concepts/coarse-reloc.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien, moet u ervoor zorgen dat u:

- Basiskennis van Java.
- het [Overzicht Azure Spatial Anchors](../overview.md) hebt doorgelezen.
- een van de [Quickstarts van 5 minuten](../index.yml) hebt voltooid.
- Lees de [Instructies voor het maken en zoeken van ankers](../create-locate-anchors-overview.md).

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Allow GPS
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);

// Allow WiFi scanning
sensors.setWifiEnabled(true);

// Populate the set of known BLE beacons' UUIDs
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

// Allow the set of known BLE beacons
sensors.setBluetoothEnabled(true);
sensors.setKnownBeaconProximityUuids(uuids);
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```java
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);

// Configure the near-device criteria
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();
nearDeviceCriteria.setDistanceInMeters(5.0f);
nearDeviceCriteria.setMaxResultCount(25);

// Set the session's locate criteria
AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]