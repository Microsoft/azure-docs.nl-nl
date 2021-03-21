---
title: Een video afspelen bij het coderen met Media Services REST
description: In dit onderwerp wordt beschreven hoe u een video bijhoudt tijdens het coderen met Azure Media Services met behulp van REST
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 6c287e168289082b2bd717a2706dd011c7115691
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955663"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Een video bijsnijden bij het coderen met Media Services-REST

U kunt een video knippen of subfragmenteren wanneer u deze codeert met behulp van een [taak](/rest/api/media/jobs). Deze functionaliteit werkt met elke [trans formatie](/rest/api/media/transforms) die is gebouwd met behulp van de [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -voor instellingen of de [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) -voor waarden. 

In het REST-voor beeld in dit onderwerp wordt een taak gemaakt die een video bijsnijdt bij het verzenden van een coderings taak. 

## <a name="prerequisites"></a>Vereisten

Voor het uitvoeren van de stappen die in dit onderwerp worden beschreven, moet u het volgende doen:

- [Maak een Azure Media Services-account](./create-account-howto.md).
- [Postman configureren voor Azure Media Services rest API-aanroepen](media-rest-apis-with-postman.md).
    
    Zorg ervoor dat u de laatste stap in het onderwerp [Azure AD-Token ophalen](media-rest-apis-with-postman.md#get-azure-ad-token)volgt. 
- Maak een trans formatie en een uitvoer activa. U kunt zien hoe u een trans formatie maakt en een uitvoer activum in het [bestand code Ring a op basis van URL en de video-rest zelf studie streamen](stream-files-tutorial-with-rest.md) .
- Bekijk het [Codeer concept](encoding-concept.md) onderwerp.

## <a name="create-a-subclipping-job"></a>Een subknipsel taak maken

1. Selecteer in de Postman-verzameling die u hebt gedownload, **trans formaties en taken**  ->  **maken taak met subknipsel**.
    
    De **put** -aanvraag ziet er als volgt uit:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Werk de waarde van de omgevings variabele "transformator" bij met de naam van uw trans formatie. 
1. Selecteer het tabblad **hoofd tekst** en werk de myOutputAsset bij met de naam van uw uitvoer activum.

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Druk op **Verzenden**.

    U ziet het **antwoord** met de informatie over de taak die is gemaakt en verzonden en de status van de taak. 

## <a name="next-steps"></a>Volgende stappen

[Coderen met een aangepaste trans formatie](custom-preset-rest-howto.md) 
