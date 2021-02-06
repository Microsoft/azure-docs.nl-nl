---
title: Azure Communication Services-bekende problemen
description: Meer informatie over bekende problemen met Azure Communication Services
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628143"
---
# <a name="known-issues-azure-communication-services"></a>Bekende problemen: Azure Communication Services

Dit artikel bevat informatie over bekende problemen met Azure Communication Services.

## <a name="video-streaming-quality-on-chromeandroid"></a>Kwaliteit van video-streaming op Chrome/Android 

Video streaming prestaties kunnen worden verslechterd op Chrome Android.

### <a name="possible-causes"></a>Mogelijke oorzaken
De kwaliteit van externe streams is afhankelijk van de resolutie van de client-side renderer die is gebruikt om die stroom te initiëren. Wanneer u zich abonneert op een externe stream, bepaalt een ontvanger zijn eigen oplossing op basis van de afmetingen van de client zijde van de afzender.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Er worden geen Bluetooth-hoofd telefoons gedetecteerd

U kunt problemen ondervinden bij het verbinden van uw Bluetooth-headset met een communicatie service-oproep.

### <a name="possible-causes"></a>Mogelijke oorzaken
Er is geen optie voor het selecteren van Bluetooth-microfoon op iOS.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Het herhaaldelijk wisselen van video apparaten kan ertoe leiden dat video-streaming tijdelijk stopt

Scha kelen tussen video apparaten kan ertoe leiden dat uw video stroom wordt onderbroken terwijl de stroom wordt opgehaald van het geselecteerde apparaat.

### <a name="possible-causes"></a>Mogelijke oorzaken
Het streamen van en scha kelen tussen media apparaten is reken intensief. Een regel matig overschakelen kan leiden tot verminderde prestaties. Ontwikkel aars wordt geadviseerd om één apparaat stroom te stoppen voordat ze een nieuwe starten.
