---
title: Inhoud Azure Media Services publiceren met REST
description: Meer informatie over het maken van een locator die wordt gebruikt om een streaming-URL te bouwen. De code maakt gebruik van REST API.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 650c0847942635e2a6a901db40ed0e51e9412057
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600043"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Inhoud Azure Media Services publiceren met REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portal](media-services-portal-publish.md)
> 
> 

U kunt een Adaptive Bitrate MP4-set streamen door een OnDemand-streaming-locator te maken en een streaming-URL te bouwen. In [het artikel Een asset coderen ziet](media-services-rest-encode-asset.md) u hoe u codeert in een Adaptive Bitrate MP4-set. Als uw inhoud is versleuteld, configureert u het leveringsbeleid voor activa (zoals beschreven in [dit](media-services-rest-configure-asset-delivery-policy.md) artikel) voordat u een locator maakt. 

U kunt ook een OnDemand-streaminglocator gebruiken om URL's te bouwen die naar MP4-bestanden wijzen die progressief kunnen worden gedownload.  

In dit artikel wordt beschreven hoe u een OnDemand-streaming-locator maakt om uw asset te publiceren en een Smooth-, MPEG DASH- en HLS-streaming-URL te bouwen. U ziet ook hoe u URL's voor progressief downloaden bouwt.

In [de volgende](#types) sectie ziet u de typen enum waarvan de waarden worden gebruikt in de REST-aanroepen.   

> [!NOTE]
> Bij het openen van entiteiten in Media Services, moet u specifieke headervelden en -waarden instellen in uw HTTP-aanvragen. Zie Setup for [Media Services REST API Development voor meer informatie.](media-services-rest-how-to-use.md)
> 

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie Access the Azure Media Services API with Azure AD authentication (Toegang tot de API van de Azure Media Services met [Azure AD-verificatie) voor](media-services-use-aad-auth-to-access-ams-api.md)meer informatie over het maken van verbinding met de AMS-API. 

>[!NOTE]
>Nadat u verbinding hebt gemaakt met `https://media.windows.net` , ontvangt u een 301-omleiding die een andere URI Media Services opgeven. U moet volgende aanroepen naar de nieuwe URI maken.

## <a name="create-an-ondemand-streaming-locator"></a>Een OnDemand-streaminglocator maken
Als u de OnDemand-streaminglocator wilt maken en URL's wilt op halen, moet u het volgende doen:

1. Als de inhoud is versleuteld, definieert u een toegangsbeleid.
2. Maak een OnDemand-streaminglocator.
3. Als u van plan bent te streamen, moet u het streamingmanifestbestand (.ism) in de asset op halen. 
   
   Als u van plan bent progressief te downloaden, haal dan de namen van MP4-bestanden op in de asset. 
4. BOUW URL's naar het manifestbestand of MP4-bestanden. 
5. U kunt geen streaming-locator maken met behulp van een AccessPolicy die schrijf- of verwijdermachtigingen bevat.

### <a name="create-an-access-policy"></a>Een toegangsbeleid maken

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-id als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om lang te blijven bestaan (niet-uploadbeleid). Zie dit artikel voor [meer](media-services-dotnet-manage-entities.md#limit-access-policies) informatie.

Aanvraag:

```console
POST https://media.windows.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
Host: media.windows.net
Content-Length: 68

{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

Reactie:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 311
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
Server: Microsoft-IIS/8.5
request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:52:09 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

### <a name="create-an-ondemand-streaming-locator"></a>Een OnDemand-streaminglocator maken
Maak de locator voor het opgegeven asset- en assetbeleid.

Aanvraag:

```console
POST https://media.windows.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
Host: media.windows.net
Content-Length: 181

{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}
```
Reactie:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 637
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
Server: Microsoft-IIS/8.5
request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:58:37 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}
```

### <a name="build-streaming-urls"></a>Streaming-URL's bouwen
Gebruik de **padwaarde** die wordt geretourneerd na het maken van de locator om de SMOOTH-, HLS- en MPEG DASH-URL's te bouwen. 

Smooth Streaming: **Pad** + manifestbestandsnaam + "/manifest"

voorbeeld:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`

HLS: **Pad** + manifestbestandsnaam + "/manifest(format=m3u8-hebtl)"

voorbeeld:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`


DASH: **Pad** + manifestbestandsnaam + "/manifest(format=mpd-time-csf)"

voorbeeld:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


### <a name="build-progressive-download-urls"></a>URL's voor progressief downloaden bouwen
Gebruik de **padwaarde** die wordt geretourneerd na het maken van de locator om de URL voor progressief downloaden te bouwen.   

URL: **Path** + asset file mp4 name

voorbeeld:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

## <a name="enum-types"></a><a id="types"></a>Enumtypen

```console
[Flags]
public enum AccessPermissions
{
    None = 0,
    Read = 1,
    Write = 2,
    Delete = 4,
    List = 8,
}

public enum LocatorType
{
    None = 0,
    Sas = 1,
    OnDemandOrigin = 2,
}
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook
[Media Services operations REST API overzicht](media-services-rest-how-to-use.md)

[Leveringsbeleid voor activa configureren](media-services-rest-configure-asset-delivery-policy.md)

