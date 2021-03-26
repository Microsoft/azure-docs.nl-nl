---
title: Opmerkingen bij de release van Azure Media Services | Microsoft Docs
description: In dit artikel vindt u informatie over de release opmerkingen voor Microsoft Azure Media Services versie 2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 801d810a82e4e484ca1ab30c5c6d510860dbab6b
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566010"
---
# <a name="azure-media-services-release-notes"></a>Opmerkingen bij de release Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

In deze release opmerkingen voor Azure Media Services worden wijzigingen in eerdere releases en bekende problemen toegelicht.

> [!NOTE]
> Er worden geen nieuwe functies meer aan Media Services v2 toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

We horen graag van onze klanten, zodat we zich kunnen richten op het oplossen van problemen die van invloed zijn op u. Als u een probleem wilt melden of vragen wilt stellen, verzendt u een bericht in het MSDN-forum van [Azure Media Services]. 

## <a name="known-issues"></a><a name="issues"></a>Bekende problemen
### <a name="media-services-general-issues"></a><a name="general_issues"></a>Algemene problemen Media Services

| Probleem | Beschrijving |
| --- | --- |
| Er zijn geen enkele algemene HTTP-headers in de REST API. |Als u Media Services toepassingen ontwikkelt met behulp van de REST API, worden enkele veelvoorkomende HTTP-header velden (met inbegrip van CLIENT-REQUEST-ID, aanvraag-ID en retour-CLIENT-REQUEST-ID) niet ondersteund. De kopteksten worden toegevoegd in een toekomstige update. |
| Percentage-code ring is niet toegestaan. |Media Services gebruikt de waarde van de eigenschap IAssetFile.Name bij het maken van Url's voor de streaming-inhoud (bijvoorbeeld `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters` ). Daarom is het percentage code ring niet toegestaan. De waarde van de eigenschap name mag niet de volgende tekens voor [percentage versleuteling](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)bevatten:! * ' ();: @ &= + $,/?% # [] '. Daarnaast kan er slechts één '. ' zijn voor de bestandsnaam extensie. |
| De methode ListBlobs die deel uitmaakt van de Azure Storage SDK-versie 3. x is mislukt. |Media Services genereert SAS-Url's op basis van de [2012-02-12](/rest/api/storageservices/version-2012-02-12) -versie. Als u de opslag-SDK wilt gebruiken om blobs in een BLOB-container weer te geven, gebruikt u de methode [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) die deel uitmaakt van de Storage SDK versie 2. x. |
| Het Media Services beperkings mechanisme beperkt het resource gebruik voor toepassingen die overmatige aanvragen naar de service doen. De service kan de HTTP-status code ' service niet beschikbaar 503 ' retour neren. |Zie de beschrijving van de 503 HTTP-status code in [Media Services fout codes](media-services-encoding-error-codes.md)voor meer informatie. |
| Wanneer u een query uitvoert op entiteiten, wordt een limiet van 1.000 entiteiten tegelijk geretourneerd, omdat de open bare REST versie 2 de query resultaten beperkt tot 1.000 resultaten. |Gebruik overs Laan en ondernemen (.NET)/top (REST) zoals beschreven in [dit .net-voor beeld](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) en [Dit rest API voor beeld](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Sommige clients kunnen een herhaald label probleem hebben in het Smooth Streaming-manifest. |Zie [deze sectie](media-services-deliver-content-overview.md#known-issues)voor meer informatie. |
| Media Services .NET SDK-objecten kunnen niet worden geserialiseerd en werken daarom niet met Azure cache voor redis. |Als u probeert het SDK AssetCollection-object te serialiseren om het toe te voegen aan Azure-cache voor redis, wordt er een uitzonde ring gegenereerd. |
|De REST API reageert met een fout bericht met de melding dat het filter niet kan worden geopend door deze versie van REST API wanneer wordt geprobeerd een filter op activa-of account niveau op te halen.|Het filter is gemaakt of gewijzigd met een nieuwere API-versie dan wordt gebruikt om het filter op te halen. Dit kan gebeuren als er twee API-versies worden gebruikt door code of hulpprogram ma's die door de klant worden gebruikt.  De beste oplossing is hier om de code of hulpprogram ma's bij te werken voor het gebruik van de nieuwere of de twee API-versies.|

## <a name="rest-api-version-history"></a><a name="rest_version_history"></a>Geschiedenis van REST API-versie
Zie de [referentie Azure Media Services rest API]voor informatie over de versie geschiedenis van Media Services rest API.

## <a name="february-2021"></a>Februari 2021

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Aankondiging van Azure Media Services v2 API en Sdk's-afschaffing

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Werk uw Azure Media Services REST API en Sdk's bij naar v3 op 29 februari 2024

Omdat versie 3 van Azure Media Services REST API en client-Sdk's voor .NET en Java meer mogelijkheden hebben dan versie 2, wordt versie 2 van de Azure Media Services REST API en de client-Sdk's voor .NET en Java buiten gebruik gesteld. We raden u aan de switch sneller te laten profiteren van de uitgebreide voor delen van versie 3 van Azure Media Services REST API en de client-Sdk's voor .NET en Java.
Versie 3 biedt:
 
- 24x7 ondersteuning voor Live-gebeurtenissen
- ARM REST Api's, client-Sdk's voor .NET core, Node.js, Python, Java, Go en Ruby.
- Door de klant beheerde sleutels, integratie van vertrouwde opslag, ondersteuning voor persoonlijke koppelingen en [meer](https://review.docs.microsoft.com/en-us/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>Actie vereist:

Als u de onderbreking van uw werk belastingen tot een minimum wilt beperken, raadpleegt u de [migratie handleiding](../latest/migrate-v-2-v-3-migration-introduction.md) om uw code over te stappen van versie 2 API en sdk's naar versie 3 API en SDK vóór 29 februari 2024.
**Na 29 februari 2024** wordt door Azure Media Services geen verkeer meer geaccepteerd op de rest API van versie 2, de arm Account Management API-versie 2015-10-01 of van de sdk's van versie 2 .net client. Dit geldt ook voor open source client-SDK'S van derden die de API van versie 2 kunnen aanroepen.  

Zie de officiële [aankondiging van Azure-updates](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

## <a name="september-2020"></a>September 2020

De volgende v2-eigenschappen worden niet meer gevuld met historische taak voortgangs gegevens:

* [HistoricalEvents](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.historicalevents)
* [PerfMessage](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.perfmessage)

Als u de taak geschiedenis wilt ophalen, moet u de v2-taak meldingen via webhooks of wachtrij berichten gebruiken met behulp van meldings eindpunten. Zie voor meer informatie:

* [Azure Queue-opslag gebruiken voor het bewaken van Media Services taak meldingen](media-services-dotnet-check-job-progress-with-queues.md)
* [Azure-webhooks gebruiken voor het bewaken van Media Services taak meldingen](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="february-2020"></a>Februari 2020

Sommige analyse media-processors worden buiten gebruik gesteld. Zie het onderwerp [oudere onderdelen](legacy-components.md) voor de pensioen datums.

## <a name="september-2019"></a>September 2019

### <a name="deprecation-of-media-processors"></a>Afschaffing van media processors

Er wordt een afschaffing van *Azure media indexer* en *Azure media indexer 2 Preview* aangekondigd. [Azure Media Services video indexer](../video-indexer/index.yml) vervangt deze verouderde media processors.

Voor de pensioen datums raadpleegt u dit onderwerp over [oudere onderdelen](legacy-components.md) .

Zie ook [migreren van Azure media indexer en Azure media indexer 2 naar Azure Media Services video indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Augustus 2019

### <a name="deprecation-of-media-processors"></a>Afschaffing van media processors

Er wordt een afschaffing van de media processors van *Windows Azure Media Encoder* (WAME) en *Azure Media Encoder* (AAM) aangekondigd. Voor de pensioen datums raadpleegt u dit onderwerp over [oudere onderdelen](legacy-components.md) .

Zie [WAME migreren naar Media Encoder Standard](./migrate-windows-azure-media-encoder.md) en [aam migreren naar Media Encoder Standard](./migrate-azure-media-encoder.md)voor meer informatie.

## <a name="march-2019"></a>Maart 2019

De preview-functie voor media Hyperlapse van Azure Media Services is afgeschaft.

## <a name="december-2018"></a>December 2018

De preview-functie voor media Hyperlapse van Azure Media Services wordt binnenkort buiten gebruik gesteld. Vanaf 19 december 2018 worden er geen wijzigingen meer of verbeteringen aangebracht in de media Media Services-Hyperlapse. Op 29 maart 2019 wordt het buiten gebruik gesteld en niet meer beschikbaar.

## <a name="october-2018"></a>Oktober 2018

### <a name="cmaf-support"></a>CMAF-ondersteuning

Ondersteuning voor CMAF en ' cbcs-versleuteling voor Apple HLS (iOS 11 +) en MPEG-DASH-spelers die CMAF ondersteunen.

### <a name="web-vtt-thumbnail-sprites"></a>Web-VTT thumbnail-sprites

U kunt nu Media Services gebruiken om Web VTT thumbnail-sprites te genereren met behulp van onze v2-Api's. Zie [een miniatuur genereren](generate-thumbnail-sprite.md)voor meer informatie.

## <a name="july-2018"></a>Juli 2018

Met de nieuwste service release zijn er kleine wijzigingen in de opmaak van de fout berichten die door de service worden geretourneerd wanneer een taak mislukt, met betrekking tot hoe deze is opgesplitst in twee of meer regels.

## <a name="may-2018"></a>Mei 2018 

Vanaf 12 mei 2018 krijgen Live kanalen niet langer ondersteuning voor het RTP/MPEG-2 Trans Port stream opname protocol. Migreer van RTP/MPEG-2 naar RTMP-of gefragmenteerde MP4 (Smooth Streaming)-opname protocollen.

## <a name="october-2017-release"></a>Release van oktober 2017
> [!IMPORTANT] 
> Media Services is ondersteuning voor het afzeggen van de verificatie sleutels voor Azure Access Control Service. Op 22 juni 2018 kunt u niet langer verifiëren met de Media Services back-end via code met behulp van Access Control Service sleutels. U moet uw code bijwerken om Azure Active Directory (Azure AD) te gebruiken per [op Azure AD gebaseerde verificatie](media-services-use-aad-auth-to-access-ams-api.md). Bekijk waarschuwingen over deze wijziging in de Azure Portal.

### <a name="updates-for-october-2017"></a>Updates voor oktober 2017
#### <a name="sdks"></a>SDK's
* De .NET SDK is bijgewerkt voor ondersteuning van Azure AD-verificatie. Ondersteuning voor Access Control Service-verificatie is verwijderd uit de nieuwste .NET SDK op Nuget.org om snellere migratie naar Azure AD te stimuleren. 
* De JAVA-SDK is bijgewerkt ter ondersteuning van Azure AD-verificatie. Ondersteuning voor Azure AD-verificatie is toegevoegd aan de Java-SDK. Voor informatie over het gebruik van de Java SDK met Media Services raadpleegt u aan [de slag met de Java-client-SDK voor Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Op bestanden gebaseerde code ring
* U kunt nu de Premium Encoder gebruiken om uw inhoud te coderen naar de HEVC-videocodec (High-efficiëntie video codering) van H. 265. Er zijn geen gevolgen voor de prijzen als u H. 265 over andere codecs kiest, zoals H. 264. Zie voor meer informatie over HEVC patent licenties [Online Services-voor waarden](https://azure.microsoft.com/support/legal/).
* Voor bron video die is gecodeerd met de video-codec H. 265 (HEVC), zoals video die is vastgelegd met iOS11 of GoPro held 6, kunt u nu de Premium Encoder of de Standard Encoder gebruiken om deze Video's te coderen. Zie voor meer informatie over patent licenties [Online Services-voor waarden](https://azure.microsoft.com/support/legal/).
* Voor inhoud die audio tracks met meerdere talen bevat, moeten de taal waarden op de juiste manier worden gelabeld volgens de bijbehorende specificatie van de bestands indeling (bijvoorbeeld ISO MP4). Vervolgens kunt u de standaard Encoder gebruiken om de inhoud voor streaming te coderen. De resulterende streaming-Locator bevat een lijst met de beschik bare audio talen.
* De standaard encoder ondersteunt nu twee nieuwe alleen-audio systeem voorinstellingen, "AAC-audio" en "AAC goede kwaliteit". " Beide produceert een programma voor het uitvoeren van een AAC-uitvoer (stereo Advanced Audio code ring), met een bitsnelheid van 128 kbps en 192 kbps.
* De Premium encoder ondersteunt nu QuickTime/MOV-bestands indelingen als invoer. De videocodec moet een van de [Apple ProRes-typen zijn die in dit github-artikel worden vermeld](./media-services-media-encoder-standard-formats.md). De audio moet AAC of Pulse Code modulatie (PCM) zijn. De Premium Encoder biedt geen ondersteuning voor bijvoorbeeld DVC/DVCPro video die in QuickTime/MOV-bestanden is verpakt als invoer. De standaard encoder ondersteunt deze video-codecs.
* De volgende oplossingen voor fouten zijn gemaakt in coderings Programma's:

    * U kunt nu taken verzenden met behulp van een invoer Asset. Nadat deze taken zijn voltooid, kunt u de Asset wijzigen (bijvoorbeeld, bestanden toevoegen, verwijderen of een andere naam geven in het activum) en aanvullende taken verzenden.
    * De kwaliteit van JPEG-miniaturen die worden geproduceerd door de Standard encoder is verbeterd.
    * Het standaard coderings programma verwerkt de invoer van meta gegevens en miniaturen verbeterd in zeer korte Video's.
    * Verbeteringen in de H. 264-decoder die in de Standard-Encoder wordt gebruikt, elimineren bepaalde zeldzame artefacten. 

#### <a name="media-analytics"></a>Media Analytics
Algemene Beschik baarheid van de Azure Media Redactor: deze media processor voert anoniem maken uit door de gezichten van geselecteerde individuen te vervagen en is ideaal voor gebruik in de scenario's voor open bare veiligheid en nieuws media. 

Zie [dit blog bericht](https://azure.microsoft.com/blog/azure-media-redactor/)voor een overzicht van deze nieuwe processor. Zie voor meer informatie over documentatie en instellingen [redactie gezichten met Azure Media Analytics](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Release van juni 2017

Media Services ondersteunt nu [Azure AD-gebaseerde verificatie](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Media Services ondersteunt momenteel het Access Control Service-verificatie model. Access Control Service autorisatie wordt op 1 juni 2018 afgeschaft. We raden u aan om zo snel mogelijk naar het Azure Active Directory-verificatiemodel te migreren.

## <a name="march-2017-release"></a>Release van maart 2017

U kunt nu de standaard Encoder gebruiken om [een bitrate ladder automatisch te genereren](media-services-autogen-bitrate-ladder-with-mes.md) door de vooraf ingestelde teken reeks voor ' adaptieve streaming ' op te geven wanneer u een coderings taak maakt. Als u een video wilt coderen voor streaming met Media Services, gebruikt u de vooraf ingestelde ' adaptieve streaming '. Als u een voor instelling voor versleuteling wilt aanpassen voor uw specifieke scenario, kunt u beginnen met [deze voor instellingen](media-services-mes-presets-overview.md).

U kunt nu Media Encoder Standard of Media Encoder Premium Workflow gebruiken voor [het maken van een coderings taak voor het genereren van fMP4-segmenten](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Release van februari 2017

Vanaf 1 april 2017 worden alle taken records in uw account die ouder zijn dan 90 dagen automatisch verwijderd, samen met de bijbehorende taak records. De verwijdering vindt ook plaats als het totale aantal records lager is dan het maximum quotum. Als u de taak/taak gegevens wilt archiveren, kunt u de code die wordt beschreven in [activa en gerelateerde entiteiten beheren met de Media Services .NET SDK](media-services-dotnet-manage-entities.md)gebruiken.

## <a name="january-2017-release"></a>Release van januari 2017

In Media Services vertegenwoordigt een streaming-eind punt een streaming-service die inhoud rechtstreeks kan leveren aan een client speler of een CDN (Content Delivery Network) voor verdere distributie. Media Services biedt ook naadloze integratie van Azure Content Delivery Network. De uitgaande stroom van een StreamingEndpoint-service kan een live stream, een video op aanvraag of een progressief downloaden van uw asset in uw Media Services-account zijn. Elk Media Services-account bevat een standaard streaming-eind punt. Er kunnen extra streaming-eind punten worden gemaakt onder het-account. 

Er zijn twee versies van streaming-eind punten, 1,0 en 2,0. Vanaf 10 januari 2017 zijn nieuw gemaakte Media Services-accounts het standaard streaming-eind punt van versie 2,0. Extra streaming-eind punten die u aan dit account toevoegt, zijn ook versie 2,0. Deze wijziging heeft geen invloed op bestaande accounts. Bestaande streaming-eind punten zijn versie 1,0 en kunnen worden bijgewerkt naar versie 2,0. Er zijn gedrags-, facturerings-en functie wijzigingen met deze wijziging. Zie [Streaming endpoints overview](media-services-streaming-endpoints-overview.md) (Overzicht van streaming-eindpunten) voor meer informatie.

Vanaf de 2,15-versie Media Services de volgende eigenschappen toegevoegd aan de entiteit van het streaming-eind punt:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Zie [StreamingEndpoint](/rest/api/media/operations/streamingendpoint)voor meer informatie over deze eigenschappen. 

## <a name="december-2016-release"></a>Release van december 2016

 U kunt Media Services nu gebruiken voor toegang tot gegevens over telemetrie/metrieken voor de services. U kunt de huidige versie van Media Services gebruiken om telemetriegegevens te verzamelen voor Live Channel, streaming-eind punt en archief entiteiten. Zie [Media Services telemetrie](media-services-telemetry-overview.md)voor meer informatie.

## <a name="july-2016-release"></a><a name="july_changes16"></a>Release van juli 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Updates voor het manifest bestand (*. ISM) gegenereerd door coderings taken
Wanneer een coderings taak wordt verzonden naar Media Encoder Standard of Media Encoder Premium, genereert de coderings taak een [streaming-manifest bestand](media-services-deliver-content-overview.md) (*. ISM) in de uitvoer Asset. Met de nieuwste service release is de syntaxis van dit bestand met het streaming-manifest bijgewerkt.

> [!NOTE]
> De syntaxis van het streaming-manifest bestand (. ISM) is gereserveerd voor intern gebruik. Het is onderhevig aan wijzigingen in toekomstige releases. Wijzig of bewerk de inhoud van dit bestand niet.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Een nieuw client manifest (*. ISMC) wordt gegenereerd in het uitvoer activum wanneer een coderings taak een of meer MP4-bestanden uitvoert
Na het volt ooien van een coderings taak waarmee een of meer MP4-bestanden worden gegenereerd, bevat het uitvoer activum ook een streaming-client manifest bestand (*. ismc), te beginnen met de nieuwste service release. Het. ismc-bestand helpt de prestaties van dynamische streaming te verbeteren. 

> [!NOTE]
> De syntaxis van het client manifest bestand (. ismc) is gereserveerd voor intern gebruik. Het is onderhevig aan wijzigingen in toekomstige releases. Wijzig of bewerk de inhoud van dit bestand niet.
> 
> 

Lees [deze blog](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file) voor meer informatie.

### <a name="known-issues"></a>Bekende problemen
Sommige clients kunnen een herhaald label probleem hebben in het Smooth Streaming-manifest. Zie [deze sectie](media-services-deliver-content-overview.md#known-issues)voor meer informatie.

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Release van april 2016
### <a name="media-analytics"></a>Media Analytics
 Media Services geïntroduceerde Media Analytics voor krachtige video-informatie. Zie [Media Services Analytics Overview](./legacy-components.md)(Engelstalig) voor meer informatie.

### <a name="apple-fairplay-preview"></a>Apple FairPlay (preview-versie)
U kunt Media Services nu gebruiken om uw HTTP Live Streaming-inhoud (HLS) dynamisch te versleutelen met Apple FairPlay. U kunt ook de Media Services License delivery service gebruiken om FairPlay-licenties te leveren aan clients. Zie ' Azure Media Services gebruiken om uw HLS-inhoud te streamen die is beveiligd met Apple FairPlay ' voor meer informatie.

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Release van februari 2016
De nieuwste versie van de Media Services SDK voor .NET (3.5.3) bevat een fout oplossing die betrekking heeft op Google Widevine. Het is niet mogelijk om AssetDeliveryPolicy te hergebruiken voor meerdere activa die zijn versleuteld met Widevine. Als onderdeel van deze fout oplossing is de volgende eigenschap toegevoegd aan de SDK: WidevineBaseLicenseAcquisitionUrl.

```csharp
Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
{
    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

};
```

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Release van januari 2016
Gereserveerde eenheden voor code ring zijn gewijzigd om Verwar ring met namen van code ring te verminderen.

De gereserveerde eenheden Basic, Standard en Premium encoding hebben respectievelijk de naam S1, S2 en S3 gereserveerde eenheden. Klanten die gebruikmaken van standaard gereserveerde encoding-eenheden, zien S1 als het label in het Azure Portal (en in de factuur). Klanten die gebruikmaken van Standard en Premium, zien respectievelijk de labels S2 en S3. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Release van december 2015

### <a name="media-encoder-deprecation-announcement"></a>Aankondiging van de afschaffing van Media Encoder

 Media Encoder wordt vanaf ongeveer 12 maanden na de release van Media Encoder Standard verouderd.

### <a name="azure-sdk-for-php"></a>Azure-SDK voor PHP
Het Azure SDK-team heeft een nieuwe release gepubliceerd van het pakket [Azure SDK voor php](https://github.com/Azure/azure-sdk-for-php) met updates en nieuwe functies voor Media Services. Met name de Media Services SDK voor PHP ondersteunt nu de nieuwste functies voor [inhouds beveiliging](media-services-content-protection-overview.md) . Deze functies zijn dynamische versleuteling met AES en DRM (PlayReady en Widevine) met en zonder token beperkingen. Het biedt ook ondersteuning voor het schalen van [coderings eenheden](media-services-dotnet-encoding-units.md).

Zie voor meer informatie:

* De volgende [code voorbeelden](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) helpen u snel aan de slag te gaan:
  * **vodworkflow_aes. php**: in dit php-bestand ziet u hoe u met aes-128 dynamische versleuteling en de key delivery-service kunt gebruiken. Het is gebaseerd op het .NET-voor beeld dat is uitgelegd in [use AES-128 Dynamic Encryption en de key delivery service](media-services-protect-with-aes128.md).
  * **vodworkflow_aes. php**: in dit php-bestand ziet u hoe u dynamische versleuteling van PlayReady en de service voor licentie levering kunt gebruiken. Het is gebaseerd op het .NET-voor beeld dat wordt uitgelegd in het [gebruik van PlayReady en/of Widevine Dynamic common Encryption](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units. php**: in dit php-bestand ziet u hoe u gereserveerde encoding-eenheden kunt schalen.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Release van november 2015
 Media Services biedt nu de Widevine-licentie leverings service in de Cloud. Lees [deze blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/) voor meer informatie. Zie ook [deze zelf studie](media-services-protect-with-playready-widevine.md) en de [github-opslag plaats](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Widevine License Delivery Services van Media Services zijn beschikbaar als preview-versie. Lees [deze blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/) voor meer informatie.

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Release van oktober 2015
Media Services is nu live in de volgende data centers: Brazilië-zuid, India-West, India-Zuid en India-centraal. U kunt nu de Azure Portal gebruiken om [media service-accounts te maken](media-services-portal-create-account.md) en diverse taken uit te voeren die worden beschreven op de [webpagina van Media Services documentatie](https://azure.microsoft.com/documentation/services/media-services/). Live Encoding is niet ingeschakeld in deze data centers. Verder zijn niet alle typen gereserveerde encoding-eenheden beschikbaar in deze data centers.

* Brazilië-zuid: er zijn alleen gereserveerde Standard en Basic Encoding-eenheden beschikbaar.
* India-West, India-Zuid en India centraal: er zijn alleen gereserveerde basis-encoding-eenheden beschikbaar.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Release van september 2015
Media Services biedt nu de mogelijkheid om zowel video on demand als live streams te beveiligen met Widevine Modular DRM-technologie. U kunt de volgende partners voor leverings Services gebruiken om Widevine-licenties te leveren:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Lees [deze blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/) voor meer informatie.
  
U kunt de [.NET SDK van Media Services](https://www.nuget.org/packages/windowsazure.mediaservices/) (vanaf versie 3.5.1) of de REST API gebruiken om AssetDeliveryConfiguration voor het gebruik van Widevine te configureren. 
* Media Services heeft ondersteuning toegevoegd voor Apple ProRes-Video's. U kunt nu uw QuickTime-bron Video's uploaden die gebruikmaken van Apple ProRes of andere codecs. Lees [deze blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/) voor meer informatie.
* U kunt nu Media Encoder Standard gebruiken om subfragmenten en het uitpakken van Live-Archief uit te voeren. Lees [deze blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/) voor meer informatie.
* De volgende filter-updates zijn uitgevoerd: 
  
  * U kunt nu de Apple HLS-indeling met een filter voor alleen audio gebruiken. U kunt deze update gebruiken om een audio-alleen track te verwijderen door (alleen audio = false) in de URL op te geven.
  * Wanneer u filters voor uw assets definieert, kunt u nu meerdere (Maxi maal drie) filters combi neren in één URL.
    
    Lees [deze blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) voor meer informatie.
* Media Services ondersteunt nu I-frames in HLS versie 4. Met de ondersteuning van I-frames optimaliseert u bewerkingen voor snel vooruitspoelen en terugspoelen. Alle uitvoer van de HLS-versie 4 bevatten standaard de I-frame-afspeel lijst (EXT-X-I-FRAME-STREAM-INF).
Lees [deze blog](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) voor meer informatie.

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Release van augustus 2015
* De Media Services SDK voor de 0.8.0-release van Java versie en nieuwe voor beelden zijn nu beschikbaar. Zie voor meer informatie:
    
* De Azure Media Player is bijgewerkt met ondersteuning voor meerdere audio stromen. Lees [dit blogbericht](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/) voor meer informatie.

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Release van juli 2015
* De algemene Beschik baarheid van Media Encoder Standard is aangekondigd. Lees [dit blogbericht](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/) voor meer informatie.
  
    Media Encoder Standard maakt gebruik van voor instellingen, zoals beschreven in [deze sectie](./media-services-mes-presets-overview.md). Wanneer u een vooraf ingestelde voor 4 KB-code ring gebruikt, haalt u het gereserveerde type voor de Premium-eenheid op. Zie [schaal codering](media-services-scale-media-processing-overview.md)voor meer informatie.
* Er zijn live real-time bijschriften gebruikt met Media Services en de Media Player. Lees [dit blogbericht](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/) voor meer informatie.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.4.0.0. De volgende updates zijn uitgevoerd: 

* Ondersteuning voor Live Archive is geïmplementeerd. U kunt geen Asset downloaden die een live-archief bevat.
* Er is ondersteuning voor dynamische filters geïmplementeerd.
* De functionaliteit is geïmplementeerd zodat gebruikers een opslag container kunnen bewaren tijdens het verwijderen van een Asset.
* Er zijn oplossingen voor problemen gemaakt met betrekking tot beleid voor opnieuw proberen in kanalen.
* Media Encoder Premium Workflow is ingeschakeld.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Release van juni 2015
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.3.0.0. De volgende updates zijn uitgevoerd: 

* Er is ondersteuning toegevoegd voor de detectie specificatie OpenID Connect Connect.
* Er is ondersteuning toegevoegd voor het afhandelen van sleutels overschakeling aan de kant van de identiteits provider.

Als u een id-provider gebruikt die een OpenID Connect Connect-detectie document (als Azure AD, Google en Sales Force) beschikbaar maakt, kunt u Media Services de opdracht geven om handtekening sleutels te verkrijgen voor de validatie van JSON-webtokens (JWTs) van de OpenID Connect Connect Discovery-specificatie. 

Zie [JSON-websleutels van de OpenID Connect Connect Discovery-specificatie gebruiken om te werken met JWT-verificatie in Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)voor meer informatie.

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Kan 2015-release
De volgende nieuwe functies zijn aangekondigd:

* [Een preview van Live encoding met Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamisch manifest](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Release van april 2015
### <a name="general-media-services-updates"></a>Algemene Media Services updates
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) is aangekondigd.
* Met ingang van de Media Services REST 2,10 worden kanalen die zijn geconfigureerd om een Real-Time Messa ging Protocol (RTMP) op te nemen, gemaakt met primaire en secundaire opname-Url's. Zie voor meer informatie [kanaal opname configuraties](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer is bijgewerkt.
* Ondersteuning voor Spaanse taal is toegevoegd.
* Er is een nieuwe configuratie voor de XML-indeling toegevoegd.

Lees [deze blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/) voor meer informatie.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.2.0.0. De volgende updates zijn uitgevoerd:

* Laatste wijziging: TokenRestrictionTemplate. Issuer en TokenRestrictionTemplate. Audience zijn gewijzigd in een teken reeks type.
* Er zijn updates gemaakt met betrekking tot het maken van een aangepast beleid voor opnieuw proberen.
* Er zijn oplossingen voor fouten gemaakt met betrekking tot het uploaden en downloaden van bestanden.
* De MediaServicesCredentials-klasse accepteert nu de primaire en secundaire toegangs beheer eindpunten voor verificatie op basis van.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Release van maart 2015
### <a name="general-media-services-updates"></a>Algemene Media Services updates
* Media Services biedt nu Content Delivery Network-integratie. Ter ondersteuning van de integratie is de eigenschap CdnEnabled toegevoegd aan StreamingEndpoint. CdnEnabled kan worden gebruikt met REST Api's vanaf versie 2,9. Zie [StreamingEndpoint](/rest/api/media/operations/streamingendpoint)voor meer informatie. CdnEnabled kan worden gebruikt met de .NET SDK vanaf versie 3.1.0.2. Zie [StreamingEndpoint](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file)voor meer informatie.
* De Media Encoder Premium Workflow is aangekondigd. Zie voor meer informatie [introductie van Premium-code ring in azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Release van februari 2015
### <a name="general-media-services-updates"></a>Algemene Media Services updates
De Media Services REST API is nu versie 2,9. Vanaf deze versie kunt u de integratie van Content Delivery Network met streaming-eind punten inschakelen. Zie [StreamingEndpoint](/rest/api/media/operations/streamingendpoint)voor meer informatie.

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Release van januari 2015
### <a name="general-media-services-updates"></a>Algemene Media Services updates
De algemene Beschik baarheid van de beveiliging van inhoud met dynamische versleuteling is aangekondigd. Zie voor meer informatie [Media Services streaming-beveiliging verbetert met de algemene Beschik baarheid van DRM-technologie](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.1.0.1.

In deze release is de standaard micro soft. WindowsAzure. Media Services. client. ContentKeyAuthorization. TokenRestrictionTemplate constructor gemarkeerd als verouderd. De nieuwe constructor gebruikt token type als een argument.

```csharp
TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
```


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Release van december 2014
### <a name="general-media-services-updates"></a>Algemene Media Services updates
* Sommige updates en nieuwe functies zijn toegevoegd aan de Media Indexer. Zie [Azure media indexer version 1.1.6.7 release opmerkingen](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)voor meer informatie.
* Er is een nieuwe REST API toegevoegd die u kunt gebruiken om gereserveerde encoding-eenheden bij te werken. Zie [EncodingReservedUnitType with rest](/rest/api/media/operations/encodingreservedunittype)(Engelstalig) voor meer informatie.
* CORS-ondersteuning is toegevoegd voor de key delivery-service.
* Er zijn prestatie verbeteringen aangebracht bij het opvragen van de opties voor het autorisatie beleid.
* In het China-Data Center is de [key delivery-URL](/rest/api/media/operations/contentkey#get_delivery_service_url) nu per klant (net zoals in andere data centers).
* De automatische doel duur van HLS is toegevoegd. Bij het uitvoeren van live streamen wordt HLS altijd dynamisch verpakt. Standaard berekent Media Services de HLS-segment verpakkings ratio (FragmentsPerSegment) automatisch op basis van het interval voor het keyframe (KeyFrameInterval). Deze methode wordt ook wel een groep afbeeldingen (GOP terug) genoemd die wordt ontvangen van het Live coderings programma. Zie [werken met Media Services live streamen](/previous-versions/azure/dn783466(v=azure.100))voor meer informatie.

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-updates
De [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) is nu versie 3.1.0.0. De volgende updates zijn uitgevoerd:

* De .NET SDK-afhankelijkheid is bijgewerkt naar het .NET 4,5-Framework.
* Er is een nieuwe API toegevoegd die u kunt gebruiken om gereserveerde encoding-eenheden bij te werken. Zie [gereserveerde-eenheids typen bijwerken en gereserveerde encoding-eenheden verhogen met behulp van .net](media-services-dotnet-encoding-units.md)voor meer informatie.
* JWT-ondersteuning voor token verificatie is toegevoegd. Zie [JWT-token verificatie in Media Services en dynamische versleuteling](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)voor meer informatie.
* Relatieve verschuivingen voor BeginDate en ExpirationDate in de PlayReady-licentie sjabloon zijn toegevoegd.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Release van november 2014
* U kunt nu Media Services gebruiken om live Smooth Streaming-inhoud (fMP4) op te nemen via een TLS-verbinding. Zorg ervoor dat u de opname-URL naar HTTPS bijwerkt voor opname via TLS. Media Services ondersteunt momenteel geen TLS met aangepaste domeinen. Zie [werken met Azure Media Services live streamen](/previous-versions/azure/dn783466(v=azure.100))voor meer informatie over live streamen.
* Op dit moment kunt u geen RTMP live stream opnemen via een TLS-verbinding.
* U kunt alleen streamen via TLS als het streaming-eind punt van waaruit u uw inhoud levert, is gemaakt na 10 september 2014. Als uw streaming-Url's zijn gebaseerd op de streaming-eind punten die zijn gemaakt na 10 september 2014, bevat de URL ' streaming.mediaservices.windows.net ' (de nieuwe indeling). Streaming-Url's die "origin.mediaservices.windows.net" (de oude indeling) bevatten, bieden geen ondersteuning voor TLS. Als uw URL de oude indeling heeft en u via TLS wilt streamen, [maakt u een nieuw streaming-eind punt](media-services-portal-manage-streaming-endpoints.md). Als u uw inhoud via TLS wilt streamen, gebruikt u Url's op basis van het nieuwe streaming-eind punt.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Media Services .NET SDK
De Media Services SDK voor .NET-extensies is nu versie 2.0.0.3.

De Media Services SDK voor .NET is nu versie 3.0.0.8. De volgende updates zijn uitgevoerd:

* Herstructurering is geïmplementeerd in beleids klassen voor opnieuw proberen.
* Er is een teken reeks voor de gebruikers agent toegevoegd aan de HTTP-aanvraag headers.
* Er is een stap voor het maken van een NuGet-herstel bewerking toegevoegd.
* Scenario tests zijn vastgelegd voor gebruik van een x509-certificaat uit de opslag plaats.
* Validatie-instellingen zijn toegevoegd voor wanneer het kanaal-en streaming-eind bijwerken.

### <a name="new-github-repository-to-host-media-services-samples"></a>Nieuwe GitHub-opslag plaats voor het hosten van Media Services-voor beelden
Voor beelden vindt u in de [github-opslag plaats Media Services](https://github.com/Azure/Azure-Media-Services-Samples)-voor beelden.

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Release van september 2014
De Media Services REST-meta gegevens zijn nu versie 2,7. Zie voor meer informatie over de meest recente REST-updates de [referentie Media Services rest API](/rest/api/media/operations/azure-media-services-rest-api-reference).

De Media Services SDK voor .NET is nu versie 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Wijzigingen die fouten veroorzaken
* De naam van de oorsprong is gewijzigd in [StreamingEndpoint].
* Er is een wijziging aangebracht in het standaard gedrag wanneer u de Azure Portal gebruikt om MP4-bestanden te coderen en vervolgens te publiceren.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Nieuwe functies/scenario's die deel uitmaken van de release van de algemene Beschik baarheid
* De Media Indexer-media processor is geïntroduceerd. Zie [Media bestanden indexeren met de media indexer](/previous-versions/azure/dn783455(v=azure.100))voor meer informatie.
* U kunt de entiteit [StreamingEndpoint] gebruiken om aangepaste domein namen (hostnamen) toe te voegen.
  
    Als u een aangepaste domein naam wilt gebruiken als de Media Services streaming-eindpunt naam, voegt u aangepaste hostnamen toe aan het streaming-eind punt. Gebruik de Media Services REST-Api's of de .NET SDK om aangepaste hostnamen toe te voegen.
  
    De volgende overwegingen zijn van toepassing:
  
  * U moet het eigendom van de aangepaste domein naam hebben.
  * Het eigendom van de domein naam moet worden gevalideerd door Media Services. Als u het domein wilt valideren, maakt u een CName die het MediaServicesAccountId-bovenliggende domein toewijst om DNS-Media Services-DNS-zone te verifiëren.
  * U moet een andere CName maken die de aangepaste hostnaam (bijvoorbeeld sports.contoso.com) toewijst aan de hostnaam van uw Media Services StreamingEndpoint (bijvoorbeeld amstest.streaming.mediaservices.windows.net).

    Zie de eigenschap CustomHostNames in het artikel [StreamingEndpoint](/rest/api/media/operations/streamingendpoint) voor meer informatie.

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Nieuwe functies/scenario's die deel uitmaken van de open bare preview-versie
* Live streaming preview. Zie [werken met Media Services live streamen](/previous-versions/azure/dn783466(v=azure.100))voor meer informatie.
* Key delivery service. Zie [AES-128 Dynamic Encryption en de key delivery-service gebruiken](/previous-versions/azure/dn783457(v=azure.100))voor meer informatie.
* AES dynamische versleuteling. Zie [AES-128 Dynamic Encryption en de key delivery-service gebruiken](/previous-versions/azure/dn783457(v=azure.100))voor meer informatie.
* PlayReady-service voor het leveren van licenties. 
* PlayReady dynamische versleuteling. 
* Media Services PlayReady-licentie sjabloon. Zie het overzicht van de [Media Services PlayReady-licentie sjablonen]voor meer informatie.
* Opslag-versleutelde assets streamen. Zie [opslag van versleutelde inhoud streamen](/previous-versions/azure/dn783451(v=azure.100))voor meer informatie.

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Release van augustus 2014
Wanneer u een Asset codeert, wordt een uitvoer activum geproduceerd wanneer de coderings taak is voltooid. Tot deze release heeft de Media Services encoder meta gegevens over uitvoer assets geproduceerd. Vanaf deze release produceert het coderings programma ook meta gegevens over invoer assets. Zie [invoer van meta gegevens] en uitvoer van meta [gegevens]voor meer informatie.

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Release van juli 2014
De volgende oplossingen voor fouten zijn gemaakt voor de Azure Media Services packager en versleutelings programma:

* Wanneer een Asset van een live-archief wordt verzonden naar HLS, wordt alleen audio afgespeeld: dit probleem is opgelost en nu kunnen audio en video worden afgespeeld.
* Wanneer een Asset is verpakt naar HLS en AES 128-bits envelop versleuteling, worden de verpakte streams niet afgespeeld op Android-apparaten: deze bug is opgelost en de verpakte stroom wordt afgespeeld op Android-apparaten die ondersteuning bieden voor HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Kan 2014-release
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Algemene Media Services updates
U kunt nu [dynamische pakketten] gebruiken voor het streamen van HLS versie 3. Als u HLS-versie 3 wilt streamen, voegt u de volgende indeling toe aan het pad van de oorsprong-Locator: *. ISM/manifest (Format = M3U8-AAPL-v3). Zie [dit forum](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3)voor meer informatie.

Dynamische verpakking biedt nu ook ondersteuning voor het leveren van HLS (versie 3 en versie 4) die is versleuteld met PlayReady op basis van Smooth Streaming statisch versleuteld met PlayReady. Voor informatie over het versleutelen van Smooth Streaming met PlayReady, raadpleegt u [Smooth streaming beveiligen met playready](/previous-versions/azure/dn189154(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Media Services .NET SDK-updates
De Media Services .NET SDK is nu versie 3.0.0.5. De volgende updates zijn uitgevoerd:

* Snelheid en flexibiliteit zijn beter wanneer u media-assets uploadt en downloadt.
* Er zijn verbeteringen aangebracht in de logica voor opnieuw proberen en tijdelijke uitzonde ringen: 
  
  * Detectie van tijdelijke fouten en pogings logica zijn verbeterd voor uitzonde ringen die worden veroorzaakt wanneer u een query uitvoert, wijzigingen opslaat en bestanden uploadt of downloadt. 
  * Wanneer u webuitzonderingen ontvangt (bijvoorbeeld tijdens een Access Control Service-token aanvraag), mislukken fatale fouten nu sneller.

Zie [logica opnieuw proberen in de Media Services SDK voor .net]voor meer informatie.

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Releases van januari/februari 2014
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 en 3.0.0.3
De wijzigingen in 3.0.0.1 en 3.0.0.2 zijn onder andere:

* Problemen met betrekking tot het gebruik van LINQ-query's met OrderBy-instructies zijn opgelost.
* Test oplossingen in [github] zijn gesplitst in op eenheden gebaseerde tests en op scenario's gebaseerde tests.

Zie voor meer informatie over de wijzigingen de [versies van Media Services .NET SDK 3.0.0.1 en 3.0.0.2](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

De volgende wijzigingen zijn aangebracht in versie 3.0.0.3:

* Er zijn Azure Storage-afhankelijkheden bijgewerkt voor het gebruik van versie 3.0.3.0.
* Er is een probleem opgelost met achterwaartse compatibiliteit voor 3,0. *.* shell.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Release van december 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Media Services .NET SDK 3.0.0.0
> [!NOTE]
> De versies van 3.0. x. x zijn niet achterwaarts compatibel met 2,4. x. x-releases.
> 
> 

De nieuwste versie van de Media Services SDK is nu 3.0.0.0. U kunt het meest recente pakket downloaden van NuGet of de bits ophalen van [github].

Vanaf de Media Services SDK-versie 3.0.0.0 kunt u de [Azure AD Access Control service](/previous-versions/azure/azure-services/hh147631(v=azure.100)) -tokens opnieuw gebruiken. Zie voor meer informatie de sectie "hergebruik Access Control Service-tokens" in [Connect to Media Services met de Media Services SDK voor .net](/previous-versions/azure/jj129571(v=azure.100)).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK-extensies 2.0.0.0
 De Media Services .NET SDK-uitbrei dingen zijn een set uitbreidings methoden en hulp functies waarmee u uw code kunt vereenvoudigen en de ontwikkeling van Media Services kunt vergemakkelijken. U kunt de meest recente bits ophalen van [Media Services .NET SDK-extensies](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Release van november 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Media Services .NET SDK-wijzigingen
Vanaf deze versie verwerkt de Media Services SDK voor .NET tijdelijke fouten die zich kunnen voordoen wanneer aanroepen naar de Media Services REST API-laag worden gedaan.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Release van augustus 2013
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Media Services Power shell-cmdlets die zijn opgenomen in azure SDK-hulpprogram ma's
De volgende Media Services Power shell-cmdlets zijn nu opgenomen in de [Azure SDK-hulpprogram ma's](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Bijvoorbeeld: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Bijvoorbeeld: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Bijvoorbeeld: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Bijvoorbeeld: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Release van juni 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Media Services wijzigingen
De volgende wijzigingen die in deze sectie worden vermeld, zijn updates die zijn opgenomen in de Media Services releases van juni 2013:

* De mogelijkheid om meerdere opslag accounts te koppelen aan een media service-account. 
    * StorageAccount
    * Activa. StorageAccountName en activum. Storage account
* De mogelijkheid om de taak. Priority bij te werken. 
* Aan meldingen gerelateerde entiteiten en eigenschappen: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Taak
* Activum. URI 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Media Services .NET SDK-wijzigingen
De volgende wijzigingen zijn opgenomen in de releases van juni 2013 Media Services SDK. De nieuwste Media Services SDK is beschikbaar op GitHub.

* Vanaf versie 2.3.0.0 ondersteunt de Media Services SDK het koppelen van meerdere opslag accounts aan een Media Services-account. De volgende Api's ondersteunen deze functie:
  
    * Type IStorageAccount
    * Eigenschap micro soft. WindowsAzure. Media Services. client. Cloudmediacontext maakt. Storage accounts
    * Eigenschap Storage account
    * Eigenschap StorageAccountName
  
      Zie [Media Services-assets beheren in meerdere opslag accounts](/previous-versions/azure/dn271889(v=azure.100))voor meer informatie.
* Aan meldingen gerelateerde Api's. Vanaf versie 2.2.0.0 kunt u naar Azure Queue-opslag meldingen Luis teren. Zie [Media Services taak meldingen verwerken](/previous-versions/azure/dn261241(v=azure.100))voor meer informatie.
  
    * Eigenschap micro soft. WindowsAzure. Media Services. client. IJob. JobNotificationSubscriptions
    * Het type micro soft. WindowsAzure. Media Services. client. INotificationEndPoint
    * Het type micro soft. WindowsAzure. Media Services. client. IJobNotificationSubscription
    * Het type micro soft. WindowsAzure. Media Services. client. NotificationEndPointCollection
    * Het type micro soft. WindowsAzure. Media Services. client. NotificationEndPointType
* Afhankelijkheid van de Storage client SDK 2,0 (Microsoft.WindowsAzure.StorageClient.dll)
* Afhankelijkheid op OData 5,5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Release van december 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Media Services .NET SDK-wijzigingen
* IntelliSense: er is een ontbrekende IntelliSense-documentatie toegevoegd voor veel typen.
* Micro soft. practices. TransientFaultHandling. core: er is een probleem opgelost waarbij de SDK nog steeds een oude versie van deze assembly had. De SDK verwijst nu naar versie 5.1.1209.1 van deze assembly.

Oplossingen voor problemen die in de SDK van november 2012 zijn gevonden:

* IAsset. Locators. Count: dit aantal wordt nu correct gerapporteerd voor nieuwe IAsset-interfaces nadat alle locators zijn verwijderd.
* IAssetFile. ContentFileSize: deze waarde is nu correct ingesteld na een upload door IAssetFile. upload (filepath).
* IAssetFile. ContentFileSize: deze eigenschap kan nu worden ingesteld wanneer u een activa bestand maakt. Het was eerder alleen-lezen.
* IAssetFile. upload (filepath): er is een probleem opgelost waarbij deze synchrone upload methode de volgende fout heeft veroorzaakt toen er meerdere bestanden werden geüpload naar de Asset. De fout is de server kan de aanvraag niet verifiëren. Zorg ervoor dat de waarde van autorisatie-header correct is opgemaakt, inclusief de hand tekening.
* IAssetFile. UploadAsync: er is een probleem opgelost waarbij de gelijktijdige upload van bestanden naar vijf bestanden is beperkt.
* IAssetFile. UploadProgressChanged: deze gebeurtenis wordt nu door de SDK verschaft.
* IAssetFile. DownloadAsync (String, BlobTransferClient, ILocator, CancellationToken): deze methode-overload is nu gegeven.
* IAssetFile. DownloadAsync: er is een probleem opgelost waarbij de gelijktijdige down load van bestanden naar vijf bestanden is beperkt.
* IAssetFile. Delete (): er is een probleem opgelost waarbij het aanroepen van verwijderen mogelijk een uitzonde ring genereert als er geen bestand is geüpload voor de IAssetFile.
* Taken: er is een probleem opgelost waarbij het koppelen van een ' MP4 to Smooth streaming Task ' met een ' PlayReady Protection-taak ' met behulp van een taak sjabloon geen taken heeft gemaakt.
* EncryptionUtils. GetCertificateFromStore (): deze methode genereert geen null-referentie-uitzonde ring vanwege een fout bij het vinden van het certificaat op basis van problemen met de certificaat configuratie.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Release van november 2012
De wijzigingen die in deze sectie worden vermeld, zijn updates opgenomen in de SDK van november 2012 (versie 2.0.0.0). Deze wijzigingen kunnen ertoe leiden dat code die is geschreven voor de preview SDK-versie van juni 2012, wordt gewijzigd of herschreven.

* Assets
  
    * IAsset. Create (assets) is de *enige* functie voor het maken van activa. IAsset. Create uploadt geen bestanden meer als onderdeel van de methode aanroep. Gebruik IAssetFile om te uploaden.
    * De methode IAsset. publish en de opsommings waarde AssetState. publish zijn verwijderd uit de Services SDK. Alle code die afhankelijk is van deze waarde moet worden herschreven.
* File info
  
    * Deze klasse is verwijderd en vervangen door IAssetFile.
  
* IAssetFiles
  
    * IAssetFile vervangt File Info en heeft een ander gedrag. Als u dit wilt gebruiken, maakt u een exemplaar van het IAssetFiles-object, gevolgd door een bestand dat u kunt uploaden met behulp van de Media Services SDK of de opslag-SDK. De volgende IAssetFile. upload-Overloads kunnen worden gebruikt:
  
        * IAssetFile. upload (filePath): deze synchrone methode blokkeert de thread en wordt alleen aangeraden als u één bestand uploadt.
        * IAssetFile. UploadAsync (filePath, blobTransferClient, Locator, cancellationToken): deze asynchrone methode is het voorkeurs mechanisme voor uploaden. 
    
            Bekende fout: als u het annulerings token gebruikt, wordt het uploaden geannuleerd. De taken kunnen veel ANNULERINGS statussen hebben. U moet uitzonde ringen goed ondervangen en verwerken.
* Locators
  
    * De origin-specifieke versies zijn verwijderd. De SAS-specifieke context. Locators. CreateSasLocator (Asset, accessPolicy) wordt gemarkeerd als afgeschaft of verwijderd door algemene Beschik baarheid. Zie de sectie ' Locators ' onder ' nieuwe functionaliteit ' voor bijgewerkt gedrag.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>Preview-versie van juni 2012
De volgende functionaliteit was nieuw in de release van november van de SDK:

* Entiteiten verwijderen
  
    * IAsset-, IAssetFile-, ILocator-, IAccessPolicy-en IContentKey-objecten worden nu verwijderd op object niveau, dat wil zeggen, IObject. Delete (), in plaats van een verwijdering te vereisen in de verzameling, dat wil zeggen Cloudmediacontext maakt. ObjCollection. Delete (objInstance).
* Locators
  
    * Locators nu moeten worden gemaakt met behulp van de methode CreateLocator. Ze moeten de Enum-waarden LocatorType. SAS of LocatorType. OnDemandOrigin gebruiken als argument voor het specifieke type Locator dat u wilt maken.
    * Er zijn nieuwe eigenschappen toegevoegd aan Locators om het gemakkelijker te maken om bruikbare Uri's voor uw inhoud te verkrijgen. Dit nieuwe ontwerp van Locators biedt meer flexibiliteit voor de toekomst uitbreid baarheid van derden en verhoogt het gebruiks gemak voor media-client toepassingen.
* Asynchrone methode ondersteuning
  
    * Asynchrone ondersteuning is toegevoegd aan alle methoden.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: /answers/topics/azure-media-services.html
[Naslag informatie over Azure Media Services REST API]: /rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Invoermetagegevens]: ./media-services-input-metadata-schema.md
[Uitvoermetagegevens]: ./media-services-output-metadata-schema.md
[Deliver content]: /previous-versions/azure/hh973618(v=azure.100)
[Index media files with the Azure Media Indexer]: /previous-versions/azure/dn783455(v=azure.100)
[StreamingEndpoint]: /rest/api/media/operations/streamingendpoint
[Work with Media Services live streaming]: /previous-versions/azure/dn783466(v=azure.100)
[Use AES-128 dynamic encryption and the key delivery service]: /previous-versions/azure/dn783457(v=azure.100)
[Use PlayReady dynamic encryption and the license delivery service]: /previous-versions/azure/dn783467(v=azure.100)
[Preview features]: https://azure.microsoft.com/services/preview/
[Overzicht van sjablonen voor Media Services PlayReady-licentie]: /previous-versions/azure/dn783459(v=azure.100)
[Stream storage-encrypted content]: /previous-versions/azure/dn783451(v=azure.100)
[Azure portal]: https://portal.azure.com
[Dynamische verpakking]: /previous-versions/azure/jj889436(v=azure.100)
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: /previous-versions/azure/dn189154(v=azure.100)
[Logica voor opnieuw proberen in de Media Services SDK voor .NET]: ./media-services-retry-logic-in-dotnet-sdk.md
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: /previous-versions/azure/dn303341(v=azure.100)
[Create overlays]: /previous-versions/azure/dn640496(v=azure.100)
[Stitch video segments]: /previous-versions/azure/dn640504(v=azure.100)
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: /previous-versions/azure/azure-services/hh147631(v=azure.100)
[Connect to Media Services with the Media Services SDK for .NET]: /previous-versions/azure/jj129571(v=azure.100)
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: /previous-versions/azure/dn271889(v=azure.100)
[Handle Media Services job notifications]: /previous-versions/azure/dn261241(v=azure.100)