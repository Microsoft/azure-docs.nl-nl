---
title: Een Asset coderen met behulp van Media Encoder Standard in het Azure Portal | Microsoft Docs
description: In deze zelf studie wordt u begeleid bij de stappen voor het coderen van een Asset door gebruik te maken van Media Encoder Standard in de Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 107d9e9a-71e9-43e5-b17c-6e00983aceab
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 5f4bb3c9b23ffd68939f1088b1252c6e31c1dad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010504"
---
# <a name="encode-an-asset-by-using-media-encoder-standard-in-the-azure-portal"></a>Een asset coderen met behulp van Media Encoder Standard in de Azure-portal

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> U hebt een Azure-account nodig om deze zelfstudie te voltooien. Zie [gratis proef versie van Azure](https://azure.microsoft.com/pricing/free-trial/)voor meer informatie. 
> 
> 

Een van de meest voorkomende scenario's voor het werken met Azure Media Services levert Adaptive Bitrate Streaming aan uw klanten. Media Services ondersteunt de volgende Adaptive Bitrate Streaming technologieën: Apple HTTP Live Streaming (HLS), micro soft Smooth Streaming en dynamisch adaptief streamen via HTTP (DASH, ook wel MPEG-DASH) genoemd. Als u uw Video's voor Adaptive Bitrate Streaming wilt voorbereiden, moet u eerst uw bron video coderen als multi-bitrate bestanden. U kunt Media Encoder Standard gebruiken om uw Video's te coderen.  

Media Services biedt dynamische pakketten. Met dynamische verpakking kunt u uw multi-bitrate Mp4's in HLS, Smooth Streaming en MPEG-DASH leveren zonder dat u deze streaming-indelingen hoeft te verpakken. Wanneer u dynamische pakketten gebruikt, kunt u de bestanden opslaan en betalen in een indeling met één opslag. Media Services bouwt voort en verzendt de juiste reactie op basis van de aanvraag van een client.

U moet het bronbestand coderen in een set multi-bitrate MP4-bestanden om van dynamische pakketten gebruik te maken. De coderings stappen worden verderop in dit artikel toegelicht.

Zie [Media verwerking schalen met behulp van de Azure Portal](media-services-portal-scale-media-processing.md)voor meer informatie over het schalen van media verwerking.

## <a name="encode-in-the-azure-portal"></a>Code ring in de Azure Portal

Uw inhoud coderen met behulp van Media Encoder Standard:

1. Selecteer uw Azure Media Services-account in [Azure Portal](https://portal.azure.com/).
2. Selecteer **Instellingen** > **Assets**. Selecteer de asset die u wilt coderen.
3. Selecteer de knop **Coderen**.
4. Selecteer in het deelvenster **Een asset coderen** de processor **Media Encoder Standard** en een standaardinstelling. Zie [automatisch een bitrate ladder genereren](media-services-autogen-bitrate-ladder-with-mes.md) en [Standaardinstellingen voor taken in Media Encoder Standard](media-services-mes-presets-overview.md) voor informatie over standaardinstellingen. Het is belangrijk om de standaardinstelling te kiezen die het meest geschikt is voor uw invoervideo. Als u bijvoorbeeld weet dat uw invoervideo een resolutie van 1920 x 1080 pixels heeft, kunt u de standaardinstelling **H264 Multiple Bitrate 1080p** gebruiken. Als u een video met lage resolutie hebt (640 x 360), kunt u beter niet de standaardinstelling **H264 Multiple Bitrate 1080p** gebruiken.
   
   U kunt de naam van de uitvoerasset en de naam van de taak bewerken, zodat u uw resources beter kunt beheren.
   
   ![Assets coderen](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Selecteer **Maken**.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
* [Controleer de voortgang van uw coderings taak](media-services-portal-check-job-progress.md) in de Azure Portal.  

