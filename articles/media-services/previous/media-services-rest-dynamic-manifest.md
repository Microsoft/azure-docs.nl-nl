---
title: Filters maken met Azure Media Services REST API | Microsoft Docs
description: In dit onderwerp wordt beschreven hoe u filters maakt, zodat uw client deze kan gebruiken om specifieke secties van een stroom te streamen. Media Services REST API dynamische manifesten maakt om deze selectieve streaming te verzorgen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewr: cenkdin
ms.openlocfilehash: b9286260d0fead11c7f55eff213c8ad0241a8cba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103008787"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Filters maken met Azure Media Services REST API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

Met ingang van 2,17 versie Media Services kunt u filters voor uw assets definiëren. Deze filters zijn regels aan de server zijde waarmee uw klanten kunnen kiezen voor het uitvoeren van dingen als: alleen een sectie van een video afspelen (in plaats van de volledige video afspelen), of alleen een subset van audio-en video weergaven opgeven die het apparaat van de klant kan verwerken (in plaats van alle vertoningen die aan de Asset zijn gekoppeld). Dit filter van uw assets wordt gearchiveerd via de **dynamische manifesten** die worden gemaakt op de aanvraag van uw klant om een video te streamen op basis van opgegeven filter (s).

Zie [overzicht van dynamische manifesten](media-services-dynamic-manifest-overview.md)voor meer informatie over filters en dynamische manifesten.

In dit artikel wordt beschreven hoe u REST-Api's gebruikt om filters te maken, bij te werken en te verwijderen. 

## <a name="types-used-to-create-filters"></a>Typen die worden gebruikt om filters te maken
De volgende typen worden gebruikt bij het maken van filters:  

* [Filter](/rest/api/media/operations/filter)
* [AssetFilter](/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect en FilterTrackPropertyCondition](/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> Wanneer u entiteiten in Media Services opent, moet u specifieke header-velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het maken van een verbinding met de AMS-API. 

## <a name="create-filters"></a>Filters maken
### <a name="create-global-filters"></a>Globale filters maken
Als u een globaal filter wilt maken, gebruikt u de volgende HTTP-aanvragen:  

#### <a name="http-request"></a>HTTP-aanvraag
Aanvraag headers

```console
POST https://media.windows.net/API/Filters HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host:media.windows.net 
```

Aanvraagbody 

```console
{  
   "Name":"GlobalFilter",
   "PresentationTimeRange":{  
      "StartTimestamp":"0",
      "EndTimestamp":"9223372036854775807",
      "PresentationWindowDuration":"12000000000",
      "LiveBackoffDuration":"0",
      "Timescale":"10000000"
   },
   "Tracks":[  
      {  
         "PropertyConditions":
              [  
            {  
               "Property":"Type",
               "Value":"audio",
               "Operator":"Equal"
            },
            {  
               "Property":"Bitrate",
               "Value":"0-2147483647",
               "Operator":"Equal"
            }
         ]
      }
   ]
}
```



#### <a name="http-response"></a>HTTP-antwoord

```console
HTTP/1.1 201 Created 
```

### <a name="create-local-assetfilters"></a>Lokale AssetFilters maken
Gebruik de volgende HTTP-aanvragen om een lokale AssetFilter te maken:  

#### <a name="http-request"></a>HTTP-aanvraag
Aanvraag headers

```console
POST https://media.windows.net/API/AssetFilters HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net  
```

Aanvraagbody 

```console
{   
   "Name":"AssetFilter", 
   "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
   "PresentationTimeRange":{   
      "StartTimestamp":"0", 
      "EndTimestamp":"9223372036854775807", 
      "PresentationWindowDuration":"12000000000", 
      "LiveBackoffDuration":"0", 
      "Timescale":"10000000" 
   }, 
   "Tracks":[   
      {   
         "PropertyConditions": 
              [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

#### <a name="http-response"></a>HTTP-antwoord

```console
HTTP/1.1 201 Created 
. . . 
```

## <a name="list-filters"></a>Filters weer geven
### <a name="get-all-global-filters-in-the-ams-account"></a>Alle globale **filter** s ophalen in het AMS-account
Als u filters wilt weer geven, gebruikt u de volgende HTTP-aanvragen: 

#### <a name="http-request"></a>HTTP-aanvraag

```console
GET https://media.windows.net/API/Filters HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
Host: media.windows.net 
```

### <a name="get-assetfilters-associated-with-an-asset"></a>**AssetFilter** s ophalen die aan een Asset zijn gekoppeld
#### <a name="http-request"></a>HTTP-aanvraag

```console
GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
```

### <a name="get-an-assetfilter-based-on-its-id"></a>Een **AssetFilter** ophalen op basis van de id
#### <a name="http-request"></a>HTTP-aanvraag

```console
GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000
```

## <a name="update-filters"></a>Filters bijwerken
Gebruik PATCH, PUT of MERGe om een filter met nieuwe eigenschaps waarden bij te werken.  Zie [patch, put en merge](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893)voor meer informatie over deze bewerkingen.

Als u een filter bijwerkt, kan het tot twee minuten duren voordat het streaming-eind punt de regels heeft vernieuwd. Als de inhoud is geleverd met dit filter (en in cache is opgeslagen in proxy's en CDN-caches), kan het bijwerken van dit filter leiden tot fouten in de speler. Wis de cache na het bijwerken van het filter. Als deze optie niet mogelijk is, kunt u overwegen een ander filter te gebruiken.  

### <a name="update-global-filters"></a>Algemene filters bijwerken
Als u een globaal filter wilt bijwerken, gebruikt u de volgende HTTP-aanvragen: 

#### <a name="http-request"></a>HTTP-aanvraag
Aanvraag headers: 

```console
MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
Content-Length: 384
```

Aanvraagtekst: 

```console
{ 
   "Tracks":[   
      {   
         "PropertyConditions": 
         [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

### <a name="update-local-assetfilters"></a>Lokale AssetFilters bijwerken
Als u een lokaal filter wilt bijwerken, gebruikt u de volgende HTTP-aanvragen: 

#### <a name="http-request"></a>HTTP-aanvraag
Aanvraag headers: 

```console
MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
```

Aanvraagtekst: 

```console
{ 
   "Tracks":[   
      {   
         "PropertyConditions": 
         [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

## <a name="delete-filters"></a>Filters verwijderen
### <a name="delete-global-filters"></a>Globale filters verwijderen
Als u een globaal filter wilt verwijderen, gebruikt u de volgende HTTP-aanvragen:

#### <a name="http-request"></a>HTTP-aanvraag

```console
DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN>  
x-ms-version: 2.19 
Host: media.windows.net 
```

### <a name="delete-local-assetfilters"></a>Lokale AssetFilters verwijderen
Gebruik de volgende HTTP-aanvragen om een lokale AssetFilter te verwijderen:

#### <a name="http-request"></a>HTTP-aanvraag

```console
DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
Host: media.windows.net 
```

## <a name="build-streaming-urls-that-use-filters"></a>Streaming-Url's bouwen die filters gebruiken
Zie voor meer informatie over het publiceren en leveren van uw assets [overzicht inhoud aan klanten leveren](media-services-deliver-content-overview.md).

In de volgende voor beelden ziet u hoe u filters kunt toevoegen aan uw streaming-Url's.

**MPEG DASH** 

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v4**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v3**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)`

**Smooth Streaming**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)`

    
## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Overzicht van dynamische manifesten](media-services-dynamic-manifest-overview.md)
