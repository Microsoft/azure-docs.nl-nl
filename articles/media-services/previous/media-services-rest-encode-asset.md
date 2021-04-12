---
title: Een Azure-Asset coderen met behulp van Media Encoder Standard | Microsoft Docs
description: Meer informatie over het gebruik van Media Encoder Standard voor het coderen van media-inhoud op Azure Media Services. Code voorbeelden gebruiken REST API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d4b7a188a9509c3510de2843b232f57e3437177b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009824"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Een Asset coderen met behulp van Media Encoder Standard

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Overzicht

Als u digitale video via internet wilt leveren, moet u het medium comprimeren. Digitale video bestanden zijn groot en zijn mogelijk te groot om te leveren via internet of voor de apparaten van uw klanten om goed te kunnen worden weer gegeven. Encoding is het proces van het comprimeren van video en audio zodat uw klanten uw media kunnen bekijken.

Coderings taken zijn een van de meest voorkomende verwerkings bewerkingen in Azure Media Services. U maakt coderings taken voor het converteren van media bestanden van de ene code ring naar een andere. Wanneer u codeert, kunt u het ingebouwde coderings programma (Media Encoder Standard) van Media Services gebruiken. U kunt ook een coderings programma van een Media Services partner gebruiken. Coderings Programma's van derden zijn beschikbaar via de Azure Marketplace. U kunt de details van coderings taken opgeven met behulp van vooraf gedefinieerde teken reeksen die zijn gedefinieerd voor uw encoder, of met behulp van vooraf ingestelde configuratie bestanden. Zie [taak voorinstellingen voor Media Encoder Standard voor](./media-services-mes-presets-overview.md)een overzicht van de beschik bare soorten voor instellingen.

Elke taak kan een of meer taken bevatten, afhankelijk van het type verwerking dat u wilt uitvoeren. Via de REST API kunt u op een van de volgende twee manieren taken en de bijbehorende taken maken:

* Taken kunnen inline worden gedefinieerd via de taak navigatie-eigenschap voor taak entiteiten.
* Gebruik OData-batch verwerking.

U wordt aangeraden om uw bron bestanden altijd te coderen in een adaptieve bitsnelheid MP4 en de set vervolgens te converteren naar de gewenste indeling door [dynamische pakketten](media-services-dynamic-packaging-overview.md)te gebruiken.

Als uw uitvoer activum is versleuteld, moet u het beleid voor de levering van assets configureren. Zie voor meer informatie beleid voor het [leveren van assets configureren](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Overwegingen

Wanneer u entiteiten in Media Services opent, moet u specifieke header-velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie.

Controleer voordat u begint met het verwijzen naar media processors of u de juiste media processor-ID hebt. Zie [Media processors ophalen](media-services-rest-get-media-processor.md)voor meer informatie.

## <a name="connect-to-media-services"></a>Verbinding met Media Services maken

Zie [toegang tot de Azure Media Services-API met Azure AD-verificatie](media-services-use-aad-auth-to-access-ams-api.md)voor meer informatie over het maken van een verbinding met de AMS-API. 

## <a name="create-a-job-with-a-single-encoding-task"></a>Een taak met één coderings taak maken

> [!NOTE]
> Wanneer u werkt met de Media Services REST API, zijn de volgende overwegingen van toepassing:
>
> Wanneer u entiteiten in Media Services opent, moet u specifieke header-velden en-waarden in uw HTTP-aanvragen instellen. Zie [Setup for Media Services rest API Development](media-services-rest-how-to-use.md)(Engelstalig) voor meer informatie.
>
> Wanneer u JSON gebruikt en opgeeft dat u het sleutel woord **__metadata** gebruikt in de aanvraag (bijvoorbeeld om te verwijzen naar een gekoppeld object), moet u de **Accept** -header instellen op [JSON-indeling](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): accepteren: Application/JSON; odata = verbose.
>
>

In het volgende voor beeld ziet u hoe u een taak maakt en post met één taakset voor het coderen van een video met een specifieke resolutie en kwaliteit. Wanneer u codeert met Media Encoder Standard, kunt u [hier](./media-services-mes-presets-overview.md)opgegeven voor instellingen voor taak configuratie gebruiken.

Aanvraag:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net

{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}
```

Reactie:

```console
HTTP/1.1 201 Created

. . .
```

### <a name="set-the-output-assets-name"></a>De naam van het uitvoer activum instellen
In het volgende voor beeld ziet u hoe u het kenmerk assets instelt:

```console
{ "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}`
```

### <a name="considerations"></a>Overwegingen
* TaskBody-eigenschappen moeten letterlijke XML gebruiken om het aantal invoer-of uitvoer activa te definiëren dat door de taak wordt gebruikt. Het taak artikel bevat de XML-schema definitie voor de XML.
* In de TaskBody-definitie moet elke interne waarde voor `<inputAsset>` en `<outputAsset>` worden ingesteld op JobInputAsset (waarde) of JobOutputAsset (waarde).
* Een taak kan meerdere uitvoer assets hebben. Een JobOutputAsset (x) kan slechts één keer worden gebruikt als uitvoer van een taak in een taak.
* U kunt JobInputAsset of JobOutputAsset als invoer element van een taak opgeven.
* Taken mogen geen cyclus vormen.
* De waarde-para meter die u doorgeeft aan JobInputAsset of JobOutputAsset vertegenwoordigt de index waarde voor een Asset. De werkelijke activa worden gedefinieerd in de navigatie-eigenschappen InputMediaAssets en OutputMediaAssets van de definitie van de taak entiteit.
* Omdat Media Services is gebouwd op OData v3, wordt verwezen naar de afzonderlijke assets in de InputMediaAssets-en OutputMediaAssets-navigatie-eigenschappen verzamelingen via een ' __metadata: URI-naam/waarde-paar.
* InputMediaAssets wordt toegewezen aan een of meer assets die u hebt gemaakt in Media Services. OutputMediaAssets worden gemaakt door het systeem. Ze verwijzen niet naar een bestaande Asset.
* OutputMediaAssets kan worden benoemd met behulp van het kenmerk activanaam. Als dit kenmerk niet aanwezig is, is de naam van de OutputMediaAsset gelijk aan de interne tekst waarde van het `<outputAsset>` element, met een achtervoegsel van de waarde van de taak naam, of de waarde van de taak-id (in het geval waar de eigenschap naam niet is gedefinieerd). Als u bijvoorbeeld een waarde instelt voor ' voor beeld ', wordt de eigenschap OutputMediaAsset name ingesteld op ' voor beeld '. Als u echter geen waarde hebt ingesteld voor de OutputMediaAsset, maar de taak naam had ingesteld op ' NewJob ', zou de naam van de JobOutputAsset zijn _NewJob (waarde).

## <a name="create-a-job-with-chained-tasks"></a>Een taak maken met gekoppelde taken
Ontwikkel aars willen in veel toepassings scenario's een reeks verwerkings taken maken. In Media Services kunt u een reeks gekoppelde taken maken. Elke taak voert verschillende verwerkings stappen uit en kan verschillende media processors gebruiken. De gekoppelde taken kunnen een Asset van de ene taak naar de andere overdragen, waarbij een lineaire volg orde van taken op de Asset wordt uitgevoerd. De taken die in een taak worden uitgevoerd, hoeven echter geen volg orde te zijn. Wanneer u een keten taak maakt, worden de geketende **ITask** -objecten gemaakt in één **IJob** -object.

> [!NOTE]
> Er is momenteel een limiet van 30 taken per taak. Als u meer dan 30 taken wilt koppelen, moet u meer dan één taak maken die de taken bevat.
>
>

```console
POST https://media.windows.net/api/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Name":"NewTestJob",
   "InputMediaAssets":[  
      {  
         "__metadata":{  
            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
         }
      }
   ],
   "Tasks":[  
      {  
         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
      },
      {  
         "Configuration":"H264 Smooth Streaming 720p",
         "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
         "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
      }
   ]
}
```

### <a name="considerations"></a>Overwegingen
Taak koppeling inschakelen:

* Een taak moet ten minste twee taken bevatten.
* Er moet ten minste één taak zijn waarvan de invoer de uitvoer van een andere taak in de taak is.

## <a name="use-odata-batch-processing"></a>OData-batch verwerking gebruiken
In het volgende voor beeld ziet u hoe u OData-batch verwerking gebruikt om een taak en taken te maken. Zie voor meer informatie over batch verwerking [Open Data Protocol (OData) batch processing (Engelstalig)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

```console
POST https://media.windows.net/api/$batch HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Accept: multipart/mixed
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
Host: media.windows.net


--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/Jobs HTTP/1.1
Content-ID: 1
Content-Type: application/json
Accept: application/json
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d
Content-Type: application/http
Content-Transfer-Encoding: binary

POST https://media.windows.net/api/$1/Tasks HTTP/1.1
Content-ID: 2
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

{  
   "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
   "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
   "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
}

--changeset_122fb0a4-cd80-4958-820f-346309967e4d--
--batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--
```


## <a name="create-a-job-by-using-a-jobtemplate"></a>Een taak maken met behulp van een JobTemplate
Wanneer u meerdere assets verwerkt met behulp van een algemene set taken, gebruikt u een JobTemplate om de standaard instellingen voor de taak op te geven, of om de volg orde van de taken in te stellen.

In het volgende voor beeld ziet u hoe u een JobTemplate maakt met een TaskTemplate die in line is gedefinieerd. De TaskTemplate gebruikt de Media Encoder Standard als MediaProcessor voor het coderen van het activa bestand. Andere MediaProcessors kunnen echter ook worden gebruikt.

```console
POST https://media.windows.net/API/JobTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
```

> [!NOTE]
> In tegens telling tot andere Media Services entiteiten moet u een nieuwe GUID-id definiëren voor elke TaskTemplate en deze in de eigenschap taskTemplateId en id in de aanvraag tekst plaatsen. Het inhouds identificatie schema moet volgen volgens het schema dat wordt beschreven in Azure Media Services entiteiten identificeren. Ook kan JobTemplates niet worden bijgewerkt. In plaats daarvan moet u een nieuw abonnement maken met de bijgewerkte wijzigingen.
>
>

Als dit lukt, wordt het volgende antwoord geretourneerd:

```console
HTTP/1.1 201 Created

. . .
```

In het volgende voor beeld ziet u hoe u een taak maakt die verwijst naar een JobTemplate-id:

```console
POST https://media.windows.net/API/Jobs HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net


{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
```

Als dit lukt, wordt het volgende antwoord geretourneerd:

```console
HTTP/1.1 201 Created

. . .
```


## <a name="advanced-encoding-features-to-explore"></a>Geavanceerde coderings functies voor verkennen
* [Miniaturen genereren](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniaturen genereren tijdens het coderen](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Video's bijsnijden tijdens het coderen](media-services-crop-video.md)
* [Coderings definities aanpassen](media-services-custom-mes-presets-with-dotnet.md)
* [Een video met een afbeelding bedekkend of in een water merk](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u een taak kunt maken om een Asset te coderen, raadpleegt u de [voortgang van de taak controleren met Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Zie ook
[Media processors ophalen](media-services-rest-get-media-processor.md)