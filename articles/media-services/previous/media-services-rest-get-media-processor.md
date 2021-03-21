---
title: Een exemplaar van een media processor ophalen met behulp van REST | Microsoft Docs
description: Meer informatie over het maken van een media processor onderdeel voor het coderen, converteren van de indeling, het versleutelen of ontsleutelen van media-inhoud voor Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f9ff1997-0da6-4528-aaed-792837e5be41
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 7eb4647ae5ba40688cbf39cbfe8449f59275e7e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014312"
---
# <a name="how-to-get-a-media-processor-instance"></a>Een media processor-exemplaar ophalen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-get-media-processor.md)
> * [REST](media-services-rest-get-media-processor.md)


## <a name="overview"></a>Overzicht

Media processors zijn een onderdeel dat een specifieke video-of audio verwerkings taak verwerkt, zoals code ring, indelings conversie, versleuteling of ontsleuteling van media-inhoud. Voor alle taken die naar Media Services worden verzonden, is een media processor vereist voor het coderen, versleutelen of converteren van de video-of audio-inhoud.

## <a name="azure-media-processors"></a>Azure media-processors

Het volgende onderwerp bevat een lijst met media processors:

* [Mediaprocessors coderen](scenarios-and-availability.md)
* [Mediaprocessors voor analyse](scenarios-and-availability.md)

>[!NOTE]
>Wanneer u entiteiten in Media Services opent, moet u specifieke header-velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het maken van een verbinding met de AMS-API. 


## <a name="get-a-media-processor"></a>Een media processor ophalen

De volgende REST-aanroep laat zien hoe u een media processor exemplaar krijgt op naam (in dit geval **Media Encoder Standard**). 

Aanvraag:

```console
GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <token>
x-ms-version: 2.19
Host: media.windows.net
```

Reactie:

```console
. . .

{  
   "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
   "value":[  
      {  
         "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "Description":"Media Encoder Standard",
         "Name":"Media Encoder Standard",
         "Sku":"",
         "Vendor":"Microsoft",
         "Version":"1.1"
      }
   ]
}
```


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u een exemplaar van een media processor krijgt, gaat u naar het artikel [een Asset coderen](media-services-rest-get-started.md) , waarin wordt gedemonstreerd hoe u de Media Encoder Standard kunt gebruiken om een Asset te coderen.

