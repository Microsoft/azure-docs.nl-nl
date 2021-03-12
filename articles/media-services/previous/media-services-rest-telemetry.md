---
title: Azure Media Services telemetrie configureren met REST | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de Azure Media Services telemetrie gebruikt met behulp van REST API..
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e1a314fb-cc05-4a82-a41b-d1c9888aab09
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 37276240835fe1a06928ee54383f81fa95690ece
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015485"
---
# <a name="configuring-azure-media-services-telemetry-with-rest"></a>Azure Media Services telemetrie configureren met REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

In dit onderwerp worden algemene stappen beschreven die u kunt uitvoeren bij het configureren van de telemetrie van Azure Media Services (AMS) met behulp van REST API. 

>[!NOTE]
>Zie het onderwerp [overzicht](media-services-telemetry-overview.md) voor gedetailleerde uitleg over de AMS-telemetrie en hoe u deze kunt gebruiken.

De stappen die in dit onderwerp worden beschreven, zijn:

- Ophalen van het opslag account dat is gekoppeld aan een Media Services account
- De meldings eindpunten ophalen
- Een meldings eindpunt maken voor bewaking. 

    Als u een meldings eindpunt wilt maken, stelt u de EndPointType in op AzureTable (2) en endPontAddress ingesteld op de opslag tabel (bijvoorbeeld https: \/ /telemetryvalidationstore.table.core.Windows.net/).
  
- De bewakings configuraties ophalen

    Een bewakings configuratie-instellingen maken voor de services die u wilt bewaken. Er zijn niet meer dan één bewakings configuratie-instellingen toegestaan. 

- Een bewakings configuratie toevoegen


 
## <a name="get-the-storage-account-associated-with-a-media-services-account"></a>Het opslag account ophalen dat is gekoppeld aan een Media Services-account

### <a name="request"></a>Aanvraag

```console
GET https://wamsbnp1clus001rest-hs.cloudapp.net/api/StorageAccounts HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Host: wamsbnp1clus001rest-hs.cloudapp.net
Response
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 370
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 8206e222-2a59-482c-a6a9-de6b8bda57fb
x-ms-request-id: 8206e222-2a59-482c-a6a9-de6b8bda57fb
X-Content-Type-Options: nosniff
DataServiceVersion: 2.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:40 GMT
    
{"d":{"results":[{"__metadata":{"id":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/StorageAccounts('telemetryvalidationstore')","uri":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/StorageAccounts('telemetryvalidationstore')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.StorageAccount"},"Name":"telemetryvalidationstore","IsDefault":true,"BytesUsed":null}]}}
```

## <a name="get-the-notification-endpoints"></a>De meldings eindpunten ophalen

### <a name="request"></a>Aanvraag

```console
GET https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Host: wamsbnp1clus001rest-hs.cloudapp.net
```

### <a name="response"></a>Antwoord

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 20
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: c68de2b3-0be1-4823-b622-6ca6f94a96b5
x-ms-request-id: c68de2b3-0be1-4823-b622-6ca6f94a96b5
X-Content-Type-Options: nosniff
DataServiceVersion: 2.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:40 GMT
    
{  
    "d":{  
        "results":[]
    }
}
 ```

## <a name="create-a-notification-endpoint-for-monitoring"></a>Een meldings eindpunt maken voor bewaking

### <a name="request"></a>Aanvraag

```console
POST https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Content-Type: application/json; charset=utf-8
Host: wamsbnp1clus001rest-hs.cloudapp.net
Content-Length: 115

{  
    "Name":"monitoring",
    "EndPointAddress":"https:\//telemetryvalidationstore.table.core.windows.net/",
    "EndPointType":2
}
```

> [!NOTE]
> Vergeet niet om de waarde ' https: \/ /telemetryvalidationstore.table.core.Windows.net ' te wijzigen in uw opslag account.

### <a name="response"></a>Antwoord

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 578
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')
Server: Microsoft-IIS/8.5
request-id: e8fa5a60-7d8b-4b00-a7ee-9b0f162fe0a9
x-ms-request-id: e8fa5a60-7d8b-4b00-a7ee-9b0f162fe0a9
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:42 GMT
    
{"d":{"__metadata":{"id":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')","uri":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/NotificationEndPoints('nb%3Anepid%3AUUID%3A76bb4faf-ea29-4815-840a-9a8e20102fc4')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.NotificationEndPoint"},"Id":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4","Name":"monitoring","Created":"\/Date(1449033042667)\/","EndPointAddress":"https://telemetryvalidationstore.table.core.windows.net/","EndPointType":2}}
 ```

## <a name="get-the-monitoring-configurations"></a>De bewakings configuraties ophalen

### <a name="request"></a>Aanvraag

```console
GET https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Host: wamsbnp1clus001rest-hs.cloudapp.net
```

### <a name="response"></a>Antwoord

```console  
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 20
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 00a3ee37-bb19-4fca-b5c7-a92b629d4416
x-ms-request-id: 00a3ee37-bb19-4fca-b5c7-a92b629d4416
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:42 GMT
    
{"d":{"results":[]}}
```

## <a name="add-a-monitoring-configuration"></a>Een bewakings configuratie toevoegen

### <a name="request"></a>Aanvraag

```console
POST https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations HTTP/1.1
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Content-Type: application/json; charset=utf-8
Host: wamsbnp1clus001rest-hs.cloudapp.net
Content-Length: 133
    
{  
   "NotificationEndPointId":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4",
   "Settings":[  
      {  
     "Component":"Channel",
     "Level":"Normal"
      }
   ]
}
```

### <a name="response"></a>Antwoord

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 825
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')
Server: Microsoft-IIS/8.5
request-id: daede9cb-8684-41b0-a921-a3af66430cbe
x-ms-request-id: daede9cb-8684-41b0-a921-a3af66430cbe
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
access-control-expose-headers: request-id, x-ms-request-id
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 02 Dec 2015 05:10:43 GMT
    
{"d":{"__metadata":{"id":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')","uri":"https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')","type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.MonitoringConfiguration"},"Id":"nb:mcid:UUID:1a8931ae-799f-45fd-8aeb-9641740295c2","NotificationEndPointId":"nb:nepid:UUID:76bb4faf-ea29-4815-840a-9a8e20102fc4","Created":"2015-12-02T05:10:43.7680396Z","LastModified":"2015-12-02T05:10:43.7680396Z","Settings":{"__metadata":{"type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.ComponentMonitoringSettings)"},"results":[{"Component":"Channel","Level":"Normal"},{"Component":"StreamingEndpoint","Level":"Disabled"}]}}}
```

## <a name="stop-telemetry"></a>Telemetrie stoppen

### <a name="request"></a>Aanvraag

```console
DELETE https://wamsbnp1clus001rest-hs.cloudapp.net/api/MonitoringConfigurations('nb%3Amcid%3AUUID%3A1a8931ae-799f-45fd-8aeb-9641740295c2')
x-ms-version: 2.19
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept: application/json; odata=verbose
Authorization: (redacted)
Content-Type: application/json; charset=utf-8
Host: wamsbnp1clus001rest-hs.cloudapp.net
```

## <a name="consuming-telemetry-information"></a>Informatie over telemetrie gebruiken

Zie dit onderwerp voor meer informatie over [het](media-services-telemetry-overview.md) gebruiken van telemetriegegevens.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
