---
title: De taak voortgang controleren met behulp van REST API | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de taak voortgang kunt controleren met behulp van Azure Media Services v2 REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a1a1f956-c035-448a-af9c-5ac15fcce9dd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 8727c9e20a9a04dfc48d89b224d15f0cde184f72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017338"
---
# <a name="how-to-check-job-progress"></a>Procedure: de voortgang van de taak controleren

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Wanneer u taken uitvoert, hebt u vaak een manier nodig om de voortgang van de taak bij te houden. U kunt de taak status achterhalen met behulp van de eigenschap State van het project. Zie Eigenschappen van de [taak entiteit](/rest/api/media/operations/job#job_entity_properties)voor meer informatie over de eigenschap State.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het maken van een verbinding met de AMS-API. 

## <a name="check-job-progress"></a>Taakvoortgang controleren

Aanvraag:

```console
GET https://media.windows.net/api/Jobs()?$filter=Id%20eq%20'nb%3Ajid%3AUUID%3Af3c43f94-327f-2347-90bb-3bf79f8559f1'&$top=1 HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
Host: media.windows.net
```

Reactie:

```output
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 450
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d9b83c57-e26c-4d10-a20b-2be634c4b6a8
request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
x-ms-request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains

{"odata.metadata":"https://media.windows.net/api/$metadata#Jobs","value":[{"Id":"nb:jid:UUID:f3c43f94-327f-2347-90bb-3bf79f8559f1","Name":"Encoding BigBuckBunny into to H264 Adaptive Bitrate MP4 Set 720p","Created":"2015-02-11T01:46:08.897","LastModified":"2015-02-11T01:46:08.897","EndTime":null,"Priority":0,"RunningDuration":0.0,"StartTime":"2015-02-11T01:46:16.58","State":2,"TemplateId":null,"JobNotificationSubscriptions":[]}]} 
```


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Zie ook

[Overzicht van Media Services bewerkingen REST API](media-services-rest-how-to-use.md)
