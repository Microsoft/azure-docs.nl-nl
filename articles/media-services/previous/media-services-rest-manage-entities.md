---
title: Media Services entiteiten beheren met REST | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Media Services entiteiten beheert met REST API.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2821c4c2b13edaf5dfb34664c4b9f78ab56d98a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103015553"
---
# <a name="managing-media-services-entities-with-rest"></a>Media Services entiteiten beheren met REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services is een op REST gebaseerde service die is gebouwd op OData v3. U kunt op ongeveer dezelfde manier entiteiten toevoegen, query's, bijwerken en verwijderen als bij elke andere OData-service. Uitzonde ringen worden indien van toepassing aangeroepen. Zie [documentatie voor open data protocol](https://www.odata.org/documentation/)voor meer informatie over OData.

In dit onderwerp wordt beschreven hoe u Azure Media Services entiteiten met REST beheert.

>[!NOTE]
> Vanaf 1 april 2017 wordt elke taakrecord in uw account die ouder is dan 90 dagen, automatisch verwijderd, samen met de bijbehorende onderliggende taakrecords, zelfs als het totale aantal records lager is dan het maximale quotum. Bijvoorbeeld, op 1 april 2017, wordt een taak record in uw account die ouder is dan 31 december 2016 automatisch verwijderd. Als u de taak/taak gegevens wilt archiveren, kunt u de code gebruiken die in dit onderwerp wordt beschreven.

## <a name="considerations"></a>Overwegingen  

Wanneer u entiteiten in Media Services opent, moet u specifieke header-velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het maken van een verbinding met de AMS-API. 

## <a name="adding-entities"></a>Entiteiten toevoegen
Elke entiteit in Media Services wordt toegevoegd aan een entiteitset, zoals assets, via een POST-HTTP-aanvraag.

In het volgende voor beeld ziet u hoe u een AccessPolicy maakt.

```console
POST https://media.windows.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

## <a name="querying-entities"></a>Entiteiten opvragen
Het uitvoeren van query's en het weer geven van entiteiten is eenvoudig en omvat alleen een GET HTTP-aanvraag en optionele OData-bewerkingen.
In het volgende voor beeld wordt een lijst met alle MediaProcessor-entiteiten opgehaald.

```console
GET https://media.windows.net/API/MediaProcessors HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

U kunt ook een specifieke entiteit ophalen of alle entiteits sets die zijn gekoppeld aan een specifieke entiteit, zoals in de volgende voor beelden:

```console
GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net

GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

In het volgende voor beeld wordt alleen de eigenschap State van alle taken geretourneerd.

```console
GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

In het volgende voor beeld wordt alle JobTemplates met de naam ' SampleTemplate ' geretourneerd.

```console
GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

> [!NOTE]
> De $expand-bewerking wordt niet ondersteund in Media Services en de niet-ondersteunde LINQ-methoden die worden beschreven in LINQ overwegingen (WCF Data Services).
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Inventariseren via grote verzamelingen entiteiten
Bij het uitvoeren van een query op entiteiten is er een limiet van 1000 entiteiten die tegelijk worden geretourneerd, omdat open bare REST v2 de query resultaten beperkt tot 1000 resultaten. Gebruik **overs Laan** en **boven** om de grote verzameling entiteiten te inventariseren. 

In het volgende voor beeld ziet u hoe u **overs Laan** en **Top** kunt gebruiken om de eerste 2000-taken over te slaan en de volgende 1000-taken op te halen.  

```console
GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
```

## <a name="updating-entities"></a>Entiteiten bijwerken
Afhankelijk van het entiteits type en de status waarin deze zich bevindt, kunt u de eigenschappen van die entiteit bijwerken via een PATCH, PUT of MERGe HTTP-aanvragen. Zie [patch/put/merge](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893)voor meer informatie over deze bewerkingen.

In het volgende code voorbeeld ziet u hoe u de eigenschap name van een activum entiteit bijwerkt.

```console
MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 21
Expect: 100-continue

{"Name" : "NewName" }
```

## <a name="deleting-entities"></a>Entiteiten verwijderen
Entiteiten kunnen worden verwijderd in Media Services met behulp van een HTTP-aanvraag verwijderen. Afhankelijk van de entiteit, kan de volg orde waarin u entiteiten verwijdert, belang rijk zijn. Entiteiten zoals assets vereisen bijvoorbeeld dat u alle Locators die verwijzen naar de betreffende activa, intrekt (of verwijdert) voordat u het activum verwijdert.

In het volgende voor beeld ziet u hoe u een Locator verwijdert die is gebruikt om een bestand te uploaden naar Blob Storage.

```console
DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 0
```

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
