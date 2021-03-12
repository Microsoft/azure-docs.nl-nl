---
title: Aan de slag met het leveren van inhoud op aanvraag met behulp van REST | Microsoft Docs
description: In deze zelf studie wordt u begeleid bij het implementeren van een on-demand toepassing voor content levering met Azure Media Services met behulp van REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: ccf4bc18270dcdbcae02f7b45bea2a05ed663227
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008872"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Aan de slag met het leveren van inhoud op aanvraag met behulp van REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In deze Quick Start leert u de stappen voor het implementeren van een VoD-inhoud (video-on-demand) met behulp van Azure Media Services (AMS) REST Api's.

In deze zelfstudie maakt u kennis met de algemene werkstroom voor Media Services en de meest algemene programmeerobjecten en -taken die zijn vereist voor het ontwikkelen van Media Services. Na het volt ooien van de zelf studie kunt u een voor beeld-media bestand dat u hebt geüpload, gecodeerd en gedownload streamen of progressief downloaden.

In de volgende afbeelding ziet u een aantal van de meest gebruikte objecten bij het ontwikkelen van VoD-toepassingen in het Media Services OData-model.

Klik op de afbeelding om deze in volledig formaat weer te geven.  

[![Diagram met een aantal van de meest gebruikte objecten in het object gegevens model van Azure Media Services voor het ontwikkelen van video op aanvraag toepassingen.](./media/media-services-rest-get-started/media-services-overview-object-model-small.png)](./media/media-services-rest-get-started/media-services-overview-object-model.png#lightbox)

## <a name="prerequisites"></a>Vereisten
De volgende vereisten zijn vereist om te beginnen met het ontwikkelen met Media Services met REST-Api's.

* Een Azure-account. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* Een Media Services-account. Zie [Een Media Services-account maken](media-services-portal-create-account.md) voor meer informatie over het maken van een Media Services-account.
* Meer informatie over hoe u kunt ontwikkelen met Media Services REST API. Zie [Media Services rest API overview](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie.
* Een toepassing van uw keuze waarmee HTTP-aanvragen en-antwoorden kunnen worden verzonden. In deze zelf studie wordt gebruikgemaakt van [Fiddler](https://www.telerik.com/download/fiddler).

In deze Quick Start worden de volgende taken weer gegeven.

1. Streaming-eindpunt starten (vanuit Azure Portal).
2. Maak verbinding met het Media Services-account met REST API.
3. Een nieuwe Asset maken en een video bestand uploaden met REST API.
4. Het bron bestand coderen in een set Adaptive bitrate MP4-bestanden met REST API.
5. Publiceer de Asset en ontvang Url's voor streaming en progressieve down loads met REST API.
6. Uw inhoud afspelen.

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). Gebruik dezelfde beleids-ID als u altijd dezelfde dagen/toegangs machtigingen gebruikt, bijvoorbeeld beleids regels voor Locators die zijn bedoeld om gedurende een lange periode te blijven (niet-upload beleid). Zie [Dit](media-services-dotnet-manage-entities.md#limit-access-policies) artikel voor meer informatie.

Zie [Azure Media Services rest API Reference](/rest/api/media/operations/azure-media-services-rest-api-reference)(Engelstalig) voor meer informatie over AMS rest-entiteiten die in dit artikel worden gebruikt. Zie ook [Azure Media Services-concepten](media-services-concepts.md).

>[!NOTE]
>Wanneer u entiteiten in Media Services opent, moet u specifieke header-velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie.

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Streaming-eindpunten starten met behulp van Azure Portal

Bij het werken met Azure Media Services is een van de meest voorkomende scenario's voor het leveren van video via Adaptive Bitrate Streaming. Media Services biedt dynamische pakketten waarmee u uw Adaptive Bitrate MP4-inhoud 'just in time' kunt leveren in de streaming-indelingen die door Media Services worden ondersteund (MPEG DASH, HLS, Smooth Streaming), zonder dat u vooraf verpakte versies van elk van deze streaming-indelingen hoeft op te slaan.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben.

U start het streaming-eindpunt als volgt:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Klik in het venster Instellingen op Streaming-eindpunten.
3. Klik op het standaardstreaming-eindpunt.

    Het venster DETAILS VAN STANDAARDSTREAMING-EINDPUNT wordt weergegeven.

4. Klik op het pictogram Start.
5. Klik op de knop Opslaan om uw wijzigingen op te slaan.

## <a name="connect-to-the-media-services-account-with-rest-api"></a><a id="connect"></a>Verbinding maken met het Media Services-account met REST API

Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het maken van een verbinding met de AMS-API. 

## <a name="create-a-new-asset-and-upload-a-video-file-with-rest-api"></a><a id="upload"></a>Een nieuwe Asset maken en een video bestand uploaden met REST API

In Media Services uploadt u de digitale bestanden naar (of neemt u deze op in) een asset. De **Asset** -entiteit kan video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden (en de meta gegevens over deze bestanden) bevatten.  Zodra de bestanden in de Asset zijn geüpload, wordt uw inhoud veilig opgeslagen in de Cloud voor verdere verwerking en streaming.

Een van de waarden die u moet opgeven bij het maken van een Asset, is opties voor het maken van activa. De eigenschap **Options** is een opsommings waarde die de versleutelings opties beschrijft waarmee een activum kan worden gemaakt. Een geldige waarde is een van de waarden uit de onderstaande lijst, niet een combi natie van waarden uit deze lijst:

* **Geen**  =  **0** : er wordt geen versleuteling gebruikt. Wanneer u deze optie gebruikt, wordt uw inhoud niet door Voer of op rest in de opslag beveiligd.
    Als u een MP4-bestand wilt leveren via progressief downloaden, gebruikt u deze optie.
* **StorageEncrypted**  =  **1** -Hiermee versleutelt u uw ongecodeerde inhoud lokaal met AES-256-bits versleuteling en uploadt u deze naar Azure Storage waar deze wordt opgeslagen op rest. De versleuteling van assets die zijn beveiligd met Storage Encryption, wordt automatisch ongedaan gemaakt en de assets worden automatisch in een versleuteld bestandssysteem geplaatst voordat ze worden gecodeerd. Eventueel kunnen ze opnieuw worden versleuteld voordat ze opnieuw worden geüpload als een nieuwe uitvoerasset. Storage Encryption wordt voornamelijk gebruikt om uw invoerbestanden met media van hoge kwaliteit die zijn opgeslagen op de schijf, te beveiligen met een sterke versleuteling.
* **CommonEncryptionProtected**  =  **2** : gebruik deze optie als u inhoud uploadt die al is versleuteld en beveiligd met common Encryption of playready DRM (bijvoorbeeld Smooth streaming beveiligd met PlayReady DRM).
* **EnvelopeEncryptionProtected**  =  **4** : gebruik deze optie als u HLS versleuteld met AES uploadt. De bestanden moeten zijn gecodeerd en versleuteld door trans formatie Manager.

### <a name="create-an-asset"></a>Een asset maken
Een Asset is een container voor meerdere typen of verzamelingen van objecten in Media Services, zoals video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden. In de REST API vereist het maken van een Asset POST-aanvraag verzenden naar Media Services en het plaatsen van eigenschaps informatie over uw asset in de hoofd tekst van de aanvraag.

In het volgende voor beeld ziet u hoe u een Asset maakt.

**HTTP-aanvraag**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 45

{"Name":"BigBuckBunny.mp4", "Options":"0"}
```

**HTTP-antwoord**

Als dit lukt, wordt het volgende geretourneerd:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 452
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
request-id: e98be122-ae09-473a-8072-0ccd234a0657
x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT

{  
    odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny2.mp4",
   "Options":0,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

### <a name="create-an-assetfile"></a>Een AssetFile maken
De entiteit [AssetFile](/rest/api/media/operations/assetfile) vertegenwoordigt een video-of audio bestand dat is opgeslagen in een BLOB-container. Een activum bestand is altijd gekoppeld aan een Asset en een Asset kan een of meer AssetFiles bevatten. De Media Services encoder-taak mislukt als een object van een activa bestand niet is gekoppeld aan een digitaal bestand in een BLOB-container.

Nadat u uw digitale media bestand naar een BLOB-container hebt geüpload, gebruikt u de HTTP-aanvraag voor **samen voegen** om de AssetFile bij te werken met informatie over uw media bestand (zoals verderop in het onderwerp wordt weer gegeven).

**HTTP-aanvraag**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 164

{  
   "IsEncrypted":"false",
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP-antwoord**

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 535
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
Server: Microsoft-IIS/8.5
request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 00:34:07 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "Name":"BigBuckBunny.mp4",
   "ContentFileSize":"0",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "EncryptionVersion":null,
   "EncryptionScheme":null,
   "IsEncrypted":false,
   "EncryptionKeyId":null,
   "InitializationVector":null,
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```

### <a name="creating-the-accesspolicy-with-write-permission"></a>De AccessPolicy maken met schrijf machtiging
Voordat u bestanden naar Blob Storage uploadt, stelt u de rechten voor het toegangs beleid in voor het schrijven naar een Asset. Hiertoe plaatst u een HTTP-aanvraag naar de AccessPolicies-entiteitset. Definieer een DurationInMinutes-waarde bij het maken of ontvang een fout bericht 500 interne server als reactie. Zie [AccessPolicy](/rest/api/media/operations/accesspolicy)voor meer informatie over AccessPolicies.

In het volgende voor beeld ziet u hoe u een AccessPolicy maakt:

**HTTP-aanvraag**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 74

{"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}
```

**HTTP-antwoord**

Als dit lukt, wordt het volgende antwoord geretourneerd:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 312
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
Server: Microsoft-IIS/8.5
request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:18:06 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
   "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "Created":"2015-01-18T22:18:06.6370575Z",
   "LastModified":"2015-01-18T22:18:06.6370575Z",
   "Name":"NewUploadPolicy",
   "DurationInMinutes":440.0,
   "Permissions":2
}
```

### <a name="get-the-upload-url"></a>De upload-URL ophalen

Als u de daad werkelijke upload-URL wilt ontvangen, maakt u een SAS-Locator. Locators definiëren de begin tijd en het type van het verbindings eindpunt voor clients die toegang willen krijgen tot bestanden in een Asset. U kunt meerdere Locator-entiteiten maken voor een bepaald AccessPolicy en activa paar voor het afhandelen van verschillende client aanvragen en-behoeften. Elk van deze locators gebruikt de waarde StartTime plus de DurationInMinutes-waarde van de AccessPolicy om te bepalen hoe lang een URL kan worden gebruikt. Zie [Locator](/rest/api/media/operations/locator)voor meer informatie.

Een SAS-URL heeft de volgende indeling:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

Hierbij geldt het volgende:

* U kunt niet meer dan vijf unieke Locators tegelijk koppelen aan een bepaalde Asset. 
* Als u uw bestanden onmiddellijk moet uploaden, moet u de waarde voor StartTime instellen op vijf minuten voor de huidige tijd. Dit komt omdat er mogelijk een klok verschil tussen uw client computer en Media Services. De waarde voor StartTime moet ook de volgende datum notatie hebben: JJJJ-MM-DDTUU: mm: ssZ (bijvoorbeeld "2014-05-23T17:53:50Z").    
* Er kan een vertraging van 30-40 seconden optreden nadat een Locator is gemaakt op het moment dat deze beschikbaar is voor gebruik. Dit probleem is van toepassing op zowel [SAS URL](../../storage/common/storage-sas-overview.md) als Origin Locators.

In het volgende voor beeld ziet u hoe u een SAS URL-Locator maakt, zoals gedefinieerd door de eigenschap type in de aanvraag tekst ("1" voor een SAS-Locator en "2" voor een bron-Locator op aanvraag). De geretourneerde eigenschap **Path** bevat de URL die u moet gebruiken om het bestand te uploaden.

**HTTP-aanvraag**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 178

{  
   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StartTime":"2015-02-18T16:45:53",
   "Type":1
}
```

**HTTP-antwoord**

Als dit lukt, wordt het volgende antwoord geretourneerd:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 949
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
Server: Microsoft-IIS/8.5
request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 03:01:29 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
   "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
   "ExpirationDateTime":"2015-02-19T00:05:53",
   "Type":1,
   "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
   "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
   "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
   "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
   "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StartTime":"2015-02-18T16:45:53",
   "Name":null
}
```

### <a name="upload-a-file-into-a-blob-storage-container"></a>Een bestand uploaden naar een BLOB storage-container
Zodra u de AccessPolicy en de Locator hebt ingesteld, wordt het daad werkelijke bestand geüpload naar een Azure Blob Storage-container met behulp van de Azure Storage REST-Api's. U moet de bestanden uploaden als blok-blobs. Pagina-blobs worden niet ondersteund door Azure Media Services.  

> [!NOTE]
> U moet de bestands naam van het bestand dat u wilt uploaden, toevoegen aan de waarde van het **pad** naar de locatie die in de vorige sectie is ontvangen. Bijvoorbeeld `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Zie [BLOB Service rest API](/rest/api/storageservices/blob-service-rest-api)voor meer informatie over het werken met Azure Storage-blobs.

### <a name="update-the-assetfile"></a>De AssetFile bijwerken
Nu u het bestand hebt geüpload, werkt u de FileAsset-grootte (en andere) bij. Bijvoorbeeld:

```console
MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net

{  
   "ContentFileSize":"1186540",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP-antwoord**

Als dit lukt, wordt het volgende geretourneerd:

```console
HTTP/1.1 204 No Content
...
```

## <a name="delete-the-locator-and-accesspolicy"></a>De Locator en AccessPolicy verwijderen
**HTTP-aanvraag**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-antwoord**

Als dit lukt, wordt het volgende geretourneerd:

```console
HTTP/1.1 204 No Content
...
```

**HTTP-aanvraag**

```console
DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-antwoord**

Als dit lukt, wordt het volgende geretourneerd:

```console
HTTP/1.1 204 No Content
...
```

## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a><a id="encode"></a>Het bron bestand coderen in een set Adaptive bitrate MP4-bestanden

Nadat u activa in Media Services hebt opgenomen, kunnen media worden gecodeerd, transmuxed, met een water merk, enzovoort voordat deze aan clients worden geleverd. Deze activiteiten worden gepland en uitgevoerd op meerdere achtergrondrolinstanties om hoge prestaties en een hoge beschikbaarheid te garanderen. Deze activiteiten worden taken genoemd en elke taak bestaat uit atomische taken die het werkelijke werk doen aan het activa bestand (Zie [taak](/rest/api/media/operations/job) [beschrijvingen)](/rest/api/media/operations/task) voor meer informatie.

Zoals eerder is vermeld, wordt bij het werken met Azure Media Services een van de meest voorkomende scenario's Adaptive Bitrate Streaming aan de clients geleverd. Media Services kunt een set Adaptive bitrate MP4-bestanden dynamisch inpakken in een van de volgende indelingen: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH.

In de volgende sectie ziet u hoe u een taak maakt die één coderings taak bevat. De taak geeft aan dat het mezzanine-bestand moet worden transcoderen naar een set adaptieve bitrate mp4's met behulp van **Media Encoder Standard**. In deze sectie wordt ook uitgelegd hoe u de voortgang van de taak verwerking kunt controleren. Wanneer de taak is voltooid, kunt u Locators maken die nodig zijn om toegang te krijgen tot uw activa.

### <a name="get-a-media-processor"></a>Een media processor ophalen
In Media Services is een media processor een onderdeel dat een specifieke verwerkings taak verwerkt, zoals code ring, indelings conversie, versleuteling of ontsleuteling van media-inhoud. Voor de coderings taak die in deze zelf studie wordt weer gegeven, gaan we de Media Encoder Standard gebruiken.

Met de volgende code wordt de id van het coderings programma aangevraagd.

**HTTP-aanvraag**

```console
    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.19
    Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-antwoord**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 273
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 07:54:09 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
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

### <a name="create-a-job"></a>Een taak maken
Elke taak kan een of meer taken bevatten, afhankelijk van het type verwerking dat u wilt uitvoeren. Via de REST API kunt u taken en de bijbehorende taken op een van de volgende twee manieren maken: taken kunnen inline worden gedefinieerd via de taak navigatie-eigenschap op taak entiteiten of via OData batch-verwerking. De Media Services SDK gebruikt batch verwerking. Voor de Lees baarheid van de code voorbeelden in dit artikel, worden de taken echter in line gedefinieerd. Zie voor meer informatie over batch verwerking [Open Data Protocol (OData) batch processing (Engelstalig)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

In het volgende voor beeld ziet u hoe u een taak maakt en post met één Taakset voor het coderen van een video met een specifieke resolutie en kwaliteit. De volgende documentatie sectie bevat de lijst met alle [taak voorinstellingen](./media-services-mes-presets-overview.md) die door de Media Encoder Standard-processor worden ondersteund.  

**HTTP-aanvraag**

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 482

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"Adaptive Streaming",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
            <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      }
   ]
}
```

**HTTP-antwoord**

Als dit lukt, wordt het volgende antwoord geretourneerd:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1215
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
Server: Microsoft-IIS/8.5
request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:04:35 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
      },
      "Tasks":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
         }
      },
      "OutputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
         }
      },
      "InputMediaAssets":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
         }
      },
      "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "Name":"NewTestJob",
      "Created":"2015-01-19T08:04:34.3287057Z",
      "LastModified":"2015-01-19T08:04:34.3287057Z",
      "EndTime":null,
      "Priority":0,
      "RunningDuration":0,
      "StartTime":null,
      "State":0,
      "TemplateId":null,
      "JobNotificationSubscriptions":{  
         "__metadata":{  
            "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
         },
         "results":[  

         ]
      }
   }
}
```

Er zijn enkele belang rijke dingen die u moet weten in elke taak aanvraag:

* TaskBody-eigenschappen moeten letterlijke XML gebruiken om het aantal invoer-of uitvoer activa te definiëren dat door de taak wordt gebruikt. Het taak artikel bevat de XML-schema definitie voor de XML.
* In de TaskBody-definitie moet elke interne waarde voor `<inputAsset>` en `<outputAsset>` worden ingesteld op JobInputAsset (waarde) of JobOutputAsset (waarde).
* Een taak kan meerdere uitvoer assets hebben. Een JobOutputAsset (x) kan slechts één keer worden gebruikt als uitvoer van een taak in een taak.
* U kunt JobInputAsset of JobOutputAsset als invoer element van een taak opgeven.
* Taken mogen geen cyclus vormen.
* De waarde-para meter die u doorgeeft aan JobInputAsset of JobOutputAsset vertegenwoordigt de index waarde voor een Asset. De werkelijke activa worden gedefinieerd in de navigatie-eigenschappen InputMediaAssets en OutputMediaAssets van de definitie van de taak entiteit.

> [!NOTE]
> Omdat Media Services is gebouwd op OData v3, wordt verwezen naar de afzonderlijke assets in de InputMediaAssets-en OutputMediaAssets-navigatie-eigenschappen verzamelingen via een ' __metadata: URI-naam/waarde-paar.
>
>

* InputMediaAssets wordt toegewezen aan een of meer assets die u hebt gemaakt in Media Services. OutputMediaAssets worden gemaakt door het systeem. Ze verwijzen niet naar een bestaande Asset.
* OutputMediaAssets kan worden benoemd met het kenmerk activanaam. Als dit kenmerk niet aanwezig is, is de naam van de OutputMediaAsset gelijk aan de interne tekst waarde van het `<outputAsset>` element, met een achtervoegsel van de waarde van de taak naam, of de waarde van de taak-id (in het geval waar de eigenschap naam niet is gedefinieerd). Als u bijvoorbeeld een waarde instelt voor ' voor beeld ', wordt de eigenschap OutputMediaAsset name ingesteld op ' voor beeld '. Als u echter geen waarde hebt ingesteld voor de OutputMediaAsset, maar de taak naam had ingesteld op ' NewJob ', zou de naam van de eigenschap ' JobOutputAsset (waarde) _NewJob ' zijn.

    In het volgende voor beeld ziet u hoe u het kenmerk assets instelt:

    ```console
    "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
    ```
* Taak koppeling inschakelen:

  * Een taak moet ten minste twee taken bevatten
  * Er moet ten minste één taak zijn waarvan de invoer van een andere taak in de taak wordt uitgevoerd.

Zie [een coderings taak maken met de Media Services rest API](media-services-rest-encode-asset.md)voor meer informatie.

### <a name="monitor-processing-progress"></a>Voortgang van verwerking bewaken
U kunt de taak status ophalen met behulp van de eigenschap State, zoals wordt weer gegeven in het volgende voor beeld:

**HTTP-aanvraag**

```console
GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 0
```

**HTTP-antwoord**

Als dit lukt, wordt het volgende antwoord geretourneerd:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 17
Content-Type: application/json;odata=verbose;charset=utf-8
Server: Microsoft-IIS/7.5
x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Sun, 13 May 2012 05:16:53 GMT

{"d":{"State":2}}
```

### <a name="cancel-a-job"></a>Een taak annuleren
Met Media Services kunt u actieve taken annuleren met de functie CancelJob. Deze aanroep retourneert een 400-fout code als u probeert een taak te annuleren wanneer de status wordt geannuleerd, geannuleerd, fout of voltooid.

In het volgende voor beeld ziet u hoe u CancelJob aanroept.

**HTTP-aanvraag**

```console
GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
```

Als dit lukt, wordt een 204-respons code geretourneerd zonder hoofd tekst van het bericht.

> [!NOTE]
> U moet een URL opgeven voor de taak-id (normaal NB: JID: UUID: eenwaarde) wanneer u deze als een para meter aan CancelJob door gegeven.
>
>

### <a name="get-the-output-asset"></a>Het uitvoer activum ophalen
De volgende code laat zien hoe u de id van de uitvoer activum kunt aanvragen.

**HTTP-aanvraag**

```console
GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: wamsbayclus001rest-hs.cloudapp.net
```

**HTTP-antwoord**

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 445
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 08:28:13 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
   "value":[  
      {  
         "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "State":0,
         "Created":"2015-01-19T07:52:15.603",
         "LastModified":"2015-01-19T07:52:15.603",
         "AlternateId":null,
         "Name":"Multibitrate MP4s",
         "Options":0,
         "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
         "StorageAccountName":"storagetestaccount001"
      }
   ]
}
```

## <a name="publish-the-asset-and-get-streaming-and-progressive-download-urls-with-rest-api"></a><a id="publish_get_urls"></a>De Asset publiceren en Url's voor streamen en progressief downloaden ophalen met REST API

Als u een asset wilt streamen of downloaden, moet u deze eerste publiceren door een locator te maken. Locators bieden toegang tot bestanden in de asset. Media Services ondersteunt twee typen locators: OnDemandOrigin-locators, voor het streamen van media (bijvoorbeeld MPEG DASH, HLS, of Smooth Streaming) en SAS-locators (Shared Access Signature), voor het downloaden van media-bestanden. 

Wanneer u de Locators hebt gemaakt, kunt u de Url's maken die worden gebruikt om uw bestanden te streamen of te downloaden.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben.

Een streaming-URL voor Smooth Streaming heeft de volgende indeling:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest
```

Een streaming-URL voor HLS heeft de volgende indeling:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)
```

Een streaming-URL voor MPEG DASH heeft de volgende indeling:

```console
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)
```

Een SAS-URL die wordt gebruikt om bestanden te downloaden, heeft de volgende indeling:

```console
{blob container name}/{asset name}/{file name}/{SAS signature}
```

In deze sectie ziet u hoe u de volgende taken kunt uitvoeren die nodig zijn om uw assets te publiceren.  

* De AccessPolicy maken met de machtiging lezen
* Een SAS-URL maken voor het downloaden van inhoud
* Een oorsprong-URL maken voor streaming-inhoud

### <a name="creating-the-accesspolicy-with-read-permission"></a>De AccessPolicy maken met de machtiging lezen
Voordat u media-inhoud downloadt of streaming, definieert u eerst een AccessPolicy met lees machtigingen en maakt u de juiste Locator-entiteit die het type leverings mechanisme specificeert dat u wilt inschakelen voor uw clients. Zie [AccessPolicy entity Properties](/rest/api/media/operations/accesspolicy#accesspolicy_properties)(Engelstalig) voor meer informatie over de beschik bare eigenschappen.

In het volgende voor beeld ziet u hoe u de AccessPolicy opgeeft voor lees machtigingen voor een bepaalde Asset.

```console
POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

Als de bewerking is geslaagd, wordt een 201-succes code geretourneerd die de AccessPolicy-entiteit die u hebt gemaakt beschrijft. Vervolgens gebruikt u de AccessPolicy-id samen met de Asset-id van de Asset die het bestand bevat dat u wilt leveren (zoals een uitvoer activum) om de Locator-entiteit te maken.

> [!NOTE]
> Deze basis werk stroom is hetzelfde als het uploaden van een bestand bij het opnemen van een asset (zoals eerder in dit onderwerp is besproken). Net als bij het uploaden van bestanden moet u, als u (of uw clients), direct toegang hebben tot uw bestanden, de waarde voor StartTime instellen op vijf minuten voor de huidige tijd. Deze actie is nodig omdat er mogelijk een klok verschil tussen de client en Media Services is. De waarde voor StartTime moet de volgende datum notatie hebben: JJJJ-MM-DDTUU: mm: ssZ (bijvoorbeeld "2014-05-23T17:53:50Z").
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Een SAS-URL maken voor het downloaden van inhoud
De volgende code laat zien hoe u een URL krijgt die kan worden gebruikt voor het downloaden van een media bestand dat eerder is gemaakt en geüpload. De AccessPolicy heeft lees machtigingen ingesteld en het pad naar de locatie verwijst naar een SAS-download-URL.

```console
POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs.net
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}
```

Als dit lukt, wordt het volgende antwoord geretourneerd:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1150
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
Server: Microsoft-IIS/7.5
x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:32 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
         }
      },
      "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
      "ExpirationDateTime":"\/Date(1337049393000)\/",
      "Type":1,
      "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
      "StartTime":"\/Date(1337031393000)\/"
   }
}
```

De eigenschap van het geretourneerde **pad** bevat de SAS-URL.

> [!NOTE]
> Als u versleutelde inhoud hebt gedownload, moet u deze hand matig ontsleutelen voordat u deze kunt weer geven. u kunt ook de MediaProcessor in een verwerkings taak gebruiken voor het uitvoeren van verwerkte bestanden in de Clear to a OutputAsset en vervolgens downloaden van die Asset. Zie voor meer informatie over het verwerken van een coderings taak maken met de Media Services REST API. Ook kunnen SAS URL-Locators niet worden bijgewerkt nadat ze zijn gemaakt. U kunt bijvoorbeeld niet dezelfde Locator met een bijgewerkte waarde voor StartTime opnieuw gebruiken. Dit komt door de manier waarop SAS-Url's worden gemaakt. Als u toegang wilt krijgen tot een Asset om te downloaden nadat een Locator is verlopen, moet u een nieuwe actief maken met een nieuwe StartTime.
>
>

### <a name="download-files"></a>Bestanden downloaden
Zodra u de AccessPolicy en de Locator hebt ingesteld, kunt u bestanden downloaden met behulp van de Azure Storage REST-Api's.  

> [!NOTE]
> U moet de bestands naam van het bestand dat u wilt downloaden, toevoegen aan de waarde van het **pad** naar de locatie die in de vorige sectie is ontvangen. Bijvoorbeeld `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4` ? . . .

Zie [BLOB Service rest API](/rest/api/storageservices/blob-service-rest-api)voor meer informatie over het werken met Azure Storage-blobs.

Als gevolg van de coderings taak die u eerder hebt uitgevoerd (encoding to Adaptive MP4 set), hebt u meerdere MP4-bestanden die u geleidelijk kunt downloaden. Bijvoorbeeld:    

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

* `https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z`

### <a name="creating-a-streaming-url-for-streaming-content"></a>Een streaming-URL maken voor het streamen van inhoud
De volgende code laat zien hoe u een streaming-URL-Locator maakt:

```console
POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: wamsbayclus001rest-hs
Content-Length: 182
Expect: 100-continue

{"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}
```

Als dit lukt, wordt het volgende antwoord geretourneerd:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 981
Content-Type: application/json;odata=verbose;charset=utf-8
Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
Server: Microsoft-IIS/7.5
x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
X-Content-Type-Options: nosniff
DataServiceVersion: 1.0;
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Mon, 14 May 2012 21:41:39 GMT

{  
   "d":{  
      "__metadata":{  
         "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
         "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
      },
      "AccessPolicy":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
         }
      },
      "Asset":{  
         "__deferred":{  
            "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
         }
      },
      "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
      "ExpirationDateTime":"\/Date(1337049395000)\/",
      "Type":2,
      "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
      "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
      "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
      "StartTime":"\/Date(1337031395000)\/"
   }
}
```

Als u een URL voor Smooth Streaming oorsprong wilt streamen in een Streaming Media Player, moet u de eigenschap pad toevoegen met de naam van het Smooth Streaming manifest bestand, gevolgd door '/manifest '.

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest`

Om HLS, Append (Format = M3U8-AAPL) toe te voegen na de "/manifest".

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)`

Als u MPEG DASH wilt streamen, voegt u (Format = mpd-time-KVP) na de '/manifest ' toe.

`http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


## <a name="play-your-content"></a><a id="play"></a>Uw inhoud afspelen
Gebruik [Azure Media Services Player](https://aka.ms/azuremediaplayer) om uw video te streamen.

Plak een URL in een browser (bijvoorbeeld Internet Explorer, Chrome, Safari) om progressieve down load te testen.

## <a name="next-steps-media-services-learning-paths"></a>Volgende stappen: Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]