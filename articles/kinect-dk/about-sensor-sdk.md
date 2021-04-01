---
title: Over de Azure Kinect sensor SDK
description: Overzicht van de Azure Kinect sensor Software Development Kit (SDK), de functies en hulpprogram ma's.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Azure, kinect, RGB, IR, opname, sensor, SDK, toegang, diepte, video, camera, IMU, beweging, sensor, audio, microfoon, Matroska, sensor SDK, downloaden
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277318"
---
# <a name="about-azure-kinect-sensor-sdk"></a>Over de Azure Kinect sensor SDK

Dit artikel bevat een overzicht van de Azure Kinect sensor Software Development Kit (SDK), de functies en hulpprogram ma's.

## <a name="features"></a>Functies

De Azure Kinect sensor-SDK biedt multi-platform toegang op laag niveau voor Azure Kinect-apparaatconfiguratie en hardware Sens oren-streams, waaronder:

- Toegang tot dieptecamera en modusbeheer (een passieve IR-modus inclusief modi voor brede en smalle weergavediepte) 
- Toegang tot de RGB-camera en beheer hiervan (bijvoorbeeld belichting en witbalans) 
- Toegang tot de bewegingssensor (gyroscoop en versnellingsmeter) 
- Gesynchroniseerde dieptecamera (RGB) die kan worden gestreamd met een configureerbare vertraging tussen camera's 
- Beheer van externe-apparaatsynchronisatie met een configureerbare vertragings-offset tussen apparaten 
- Toegang tot de metagegevens van het camerakader (met bijvoorbeeld de beeldresolutie, timestamp, etc.) 
- Toegang tot kalibratiegegevens van het apparaat 

## <a name="tools"></a>Hulpprogramma's

- Een [Azure Kinect-Viewer](azure-kinect-viewer.md) voor het bewaken van gegevens stromen van apparaten en het configureren van verschillende modi.
- Een [Azure Kinect-opname](azure-kinect-recorder.md) -en afspeel lezer-API die gebruikmaakt van de [container indeling Matroska](record-file-format.md).
- Een Azure Kinect DK- [firmware-update programma](azure-kinect-firmware-tool.md).

## <a name="sensor-sdk"></a>Sensor-SDK

- [Sensor SDK downloaden](sensor-sdk-download.md).
- De sensor SDK is beschikbaar in [open source op github](https://github.com/microsoft/Azure-Kinect-Sensor-SDK).
- Zie [sensor SDK API documentation](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html)(Engelstalig) voor meer informatie over het gebruik.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd over de Azure Kinect sensor SDK, kunt u ook het volgende doen:
>[!div class="nextstepaction"]
>[Sensor SDK-code downloaden](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[Apparaat zoeken en openen](find-then-open-device.md)
