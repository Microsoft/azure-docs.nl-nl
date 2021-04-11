---
title: Aangepaste trans formatie .NET coderen
description: In dit onderwerp wordt uitgelegd hoe u Azure Media Services V3 gebruikt om een aangepaste trans formatie te coderen met behulp van .NET.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 9d6b147d4dc71e345022f9f9e8dc2776ca564183
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105962404"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Coderen met een aangepaste trans formatie-.NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Wanneer u code ring met Azure Media Services, kunt u snel aan de slag met een van de aanbevolen standaard instellingen op basis van best practices in de branche, zoals wordt getoond in de zelf studie voor [streaming-bestanden](stream-files-tutorial-with-api.md) . U kunt ook een aangepaste voor instelling maken om uw specifieke scenario-of apparaat vereisten te bereiken.

## <a name="considerations"></a>Overwegingen

Bij het maken van aangepaste voor instellingen gelden de volgende overwegingen:

* Alle waarden voor de hoogte en breedte van AVC-inhoud moeten een meervoud van 4 zijn.
* In Azure Media Services v3 bevinden alle coderings bitsnelheden zich in bits per seconde. Dit wijkt af van de voor instellingen met onze v2 Api's, die kilobits/seconde als eenheid hebben gebruikt. Als de bitrate in v2 bijvoorbeeld is opgegeven als 128 (kilobits/seconde), wordt deze in v3 ingesteld op 128000 (bits/seconde).

## <a name="prerequisites"></a>Vereisten

[Een Azure Media Services-account maken](./account-create-how-to.md)

## <a name="download-the-sample"></a>Het voorbeeld downloaden

Kloon een GitHub-opslag plaats met het volledige .NET core-voor beeld naar uw machine met behulp van de volgende opdracht:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```
 
Het voor beeld van een aangepaste voor instelling bevindt zich in de [code ring met een aangepaste voor instelling](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/EncodingWithMESCustomPreset_H264) met de .net-map.

## <a name="create-a-transform-with-a-custom-preset"></a>Een trans formatie maken met een aangepaste voor instelling

Wanneer u een nieuwe [trans formatie](/rest/api/media/transforms)maakt, moet u opgeven wat u wilt produceren als uitvoer. De vereiste parameter is een [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput)-object, zoals weergegeven in de onderstaande code. Elke **transformatie-uitvoer** bevat een **voorinstelling**. In de **voor instelling** worden de stapsgewijze instructies beschreven van de bewerkingen voor video en/of audio verwerking die moeten worden gebruikt om de gewenste **TransformOutput** te genereren. Met de volgende **TransformOutput** worden aangepaste codec-en laag uitvoer instellingen gemaakt.

Bij het maken van een [transformatie](/rest/api/media/transforms) moet u controleren of er al een bestaat met de methode **Ophalen** zoals weergegeven in de volgende code. In Media Services v3, **Get** -methoden op entiteiten retour neren **Null** als de entiteit niet bestaat (een niet-hoofdletter gevoelige controle op de naam).

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt een set uitvoer gedefinieerd die we willen genereren wanneer deze trans formatie wordt gebruikt. We voegen eerst een AacAudio-laag toe voor de audio codering en twee H264Video-lagen voor de video codering. In de video lagen worden labels toegewezen zodat ze kunnen worden gebruikt in de naam van het uitvoer bestand. Vervolgens willen we dat de uitvoer ook miniaturen bevat. In het onderstaande voor beeld geven we afbeeldingen op in PNG-indeling, gegenereerd om 50% van de oplossing van de video-invoer en met drie time Stamps-{25%, 50%, 75%} van de lengte van de invoer video. Ten slotte geven we de indeling voor de uitvoer bestanden op: één voor video en audio, en een andere voor de miniaturen. Omdat we meerdere H264Layers hebben, moeten we macro's gebruiken waarmee unieke namen per laag worden geproduceerd. We kunnen een `{Label}` or `{Bitrate}` -macro gebruiken, maar in het voor beeld wordt het voormalige weer gegeven.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/EncodingWithMESCustomPreset_H264/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>Volgende stappen

[Bestanden streamen](stream-files-tutorial-with-api.md) 
