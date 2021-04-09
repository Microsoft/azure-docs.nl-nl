---
title: Video's coderen met het standaard coderings programma in Media Services
description: In dit onderwerp wordt uitgelegd hoe u het standaard coderings programma in Media Services kunt gebruiken om een invoer video te coderen met een automatisch gegenereerde bitrate ladder, op basis van de invoer resolutie en bitrate.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 5b973d17e10f3dbb75f5208d9003b4f8118b37c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98891400"
---
#  <a name="encode-with-an-auto-generated-bitrate-ladder"></a>Coderen met een automatisch gegenereerde bitrate voor bitsnelheid

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Overzicht

In dit artikel wordt uitgelegd hoe u het standaard coderings programma in Media Services kunt gebruiken voor het coderen van een invoer video in een automatisch gegenereerde bitsnelheid (ring-omzettings paren) op basis van de invoer resolutie en bitrate. Deze ingebouwde coderings instelling, of vooraf ingesteld, overschrijdt nooit de invoer resolutie en bitrate. Als de invoer bijvoorbeeld 720p is op 3 Mbps, blijft de uitvoer 720p het beste en begint deze met lagere tarieven dan 3 Mbps.

### <a name="encoding-for-streaming"></a>Code ring voor streaming

Wanneer u de **AdaptiveStreaming** -voor instelling in **transform** gebruikt, krijgt u een uitvoer die geschikt is voor levering via streaming-protocollen zoals HLS en streepje. Wanneer u deze voor instelling gebruikt, bepaalt de service intelligent het aantal video lagen dat moet worden gegenereerd en op welke bitrate en oplossing. De uitvoer inhoud bevat MP4-bestanden waarin met AAC gecodeerde audio en H. 264 gecodeerde video niet is geleaveerd.

Zie [een bestand streamen](stream-files-dotnet-quickstart.md)voor een voor beeld van hoe deze standaard instelling wordt gebruikt.

## <a name="output"></a>Uitvoer

In deze sectie worden drie voor beelden van de video lagen voor uitvoer gegenereerd door de Media Services encoder als resultaat van de code ring met de voor instelling **AdaptiveStreaming** . In alle gevallen bevat de uitvoer een MP4-bestand met audio-indeling met stereo audio dat is gecodeerd met 128 kbps.

### <a name="example-1"></a>Voorbeeld 1
Bron met de hoogte "1080" en de frame snelheid "29,970" produceert 6 video lagen:

|Laag|Hoogte|Breedte|Bitrate (kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Voorbeeld 2
Bron met de hoogte "720" en de frame snelheid "23,970" produceert 5 video lagen:

|Laag|Hoogte|Breedte|Bitrate (kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Voorbeeld 3
Bron met de hoogte "360" en de frame snelheid "29,970" produceert 3 video lagen:

|Laag|Hoogte|Breedte|Bitrate (kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een bestand streamen](stream-files-dotnet-quickstart.md)
