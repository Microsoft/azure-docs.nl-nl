---
title: Azure Media Services concepten | Microsoft Docs
description: Dit artikel bevat een kort overzicht van Microsoft Azure Media Services concepten en koppelingen naar andere artikelen voor meer informatie.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: fbe387b3f86dfd10cbc4e80a02ee2528e43c4b67
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016879"
---
# <a name="azure-media-services-concepts"></a>Azure Media Services concepten

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

In dit onderwerp vindt u een overzicht van de belangrijkste Media Services concepten.

## <a name="assets-and-storage"></a><a name="assets"></a>Activa en opslag
### <a name="assets"></a>Assets
Een [Asset](/rest/api/media/operations/asset) bevat digitale bestanden (inclusief video, audio, afbeeldingen, miniatuur verzamelingen, tekst sporen en ondertitelings bestanden) en de meta gegevens over deze bestanden. Nadat de digitale bestanden zijn geüpload naar een Asset, kunnen ze worden gebruikt in de Media Services encoding-en streaming-werk stromen.

Een Asset wordt toegewezen aan een BLOB-container in het Azure Storage-account en de bestanden in de Asset worden opgeslagen als blok-blobs in die container. Pagina-blobs worden niet ondersteund door Azure Media Services.

Wanneer u bepaalt welke media-inhoud moet worden geüpload en opgeslagen in een Asset, zijn de volgende overwegingen van toepassing:

* Een Asset mag slechts één unieke instantie van media-inhoud bevatten. Een voor beeld: een enkele bewerking van een TV-aflevering, film of advertentie.
* Een activum mag niet meerdere vertoningen of bewerkingen van een audiovisueel bestand bevatten. Een voor beeld van een onjuist gebruik van een activum zou proberen om meer dan één TV-aflevering, advertentie of meerdere camera hoeken op te slaan vanuit één productie binnen een Asset. Het opslaan van meerdere weer gaven of bewerkingen van een audiovisueel bestand in een Asset kan leiden tot problemen met het indienen van coderings taken, het streamen en beveiligen van de levering van het activum later in de werk stroom.  

### <a name="asset-file"></a>Asset-bestand
Een [AssetFile](/rest/api/media/operations/assetfile) vertegenwoordigt een daad werkelijk video-of audio bestand dat is opgeslagen in een BLOB-container. Een activum bestand is altijd gekoppeld aan een Asset en een Asset kan een of meer bestanden bevatten. De Media Services encoder-taak mislukt als een object van een activa bestand niet is gekoppeld aan een digitaal bestand in een BLOB-container.

Het **AssetFile** -exemplaar en het daad werkelijke media bestand zijn twee verschillende objecten. Het AssetFile-exemplaar bevat meta gegevens over het Media bestand, terwijl het Media bestand de daad werkelijke media-inhoud bevat.

U moet niet proberen om de inhoud te wijzigen van BLOB-containers die zijn gegenereerd door Media Services zonder gebruik te maken van media service-Api's.

### <a name="asset-encryption-options"></a>Opties voor Asset Encryption
Afhankelijk van het type inhoud dat u wilt uploaden, opslaan en leveren, biedt Media Services verschillende versleutelings opties waaruit u kunt kiezen.

>[!NOTE]
>Er wordt geen versleuteling gebruikt. Dit is de standaardwaarde. Wanneer u deze optie gebruikt, wordt uw inhoud niet door Voer of op rest in de opslag beveiligd.

Als u van plan bent een MP4 te leveren met behulp van progressief downloaden, kunt u deze optie gebruiken om uw inhoud te uploaden.

**StorageEncrypted** : gebruik deze optie om uw ongecodeerde inhoud lokaal te versleutelen met AES 256-bits versleuteling en upload deze vervolgens naar Azure Storage waar deze wordt opgeslagen op rest. Assets die zijn beveiligd met opslag versleuteling, worden automatisch niet-versleuteld en in een versleuteld bestands systeem geplaatst vóór de code ring en eventueel opnieuw versleuteld voordat ze worden geüpload als een nieuwe uitvoer-Asset. De primaire use-case voor opslag versleuteling is wanneer u uw invoer media bestanden met een hoge kwaliteit wilt beveiligen met sterke versleuteling op de schijf. 

Als u een door opslag versleutelde Asset wilt leveren, moet u het leverings beleid van het activum configureren zodat Media Services weet hoe u uw inhoud wilt leveren. Voordat uw asset kan worden gestreamd, verwijdert de streaming-server de opslag versleuteling en streamt uw inhoud met behulp van het opgegeven bezorgings beleid (bijvoorbeeld AES, PlayReady of geen versleuteling). 

**CommonEncryptionProtected** : gebruik deze optie als u inhoud wilt versleutelen (of uploaden al versleuteld) met common Encryption of PlayReady DRM (bijvoorbeeld Smooth streaming beveiligd met PlayReady DRM).

**EnvelopeEncryptionProtected** : gebruik deze optie als u wilt beveiligen (of upload al beveiligd) http live streaming (HLS) versleuteld met Advanced Encryption Standard (AES). Als u HLS al versleuteld met AES uploadt, moet deze zijn versleuteld met trans formatie Manager.

### <a name="access-policy"></a>Toegangsbeleid
Een [AccessPolicy](/rest/api/media/operations/accesspolicy) definieert machtigingen (zoals lezen, schrijven en lijst) en de duur van toegang tot een Asset. Normaal gesp roken geeft u een AccessPolicy-object door aan een Locator die vervolgens wordt gebruikt voor toegang tot de bestanden in een Asset.

>[!NOTE]
>Er geldt een limiet van 1.000.000 beleidsregels voor verschillende AMS-beleidsitems (bijvoorbeeld voor Locator-beleid of ContentKeyAuthorizationPolicy). U moet dezelfde beleids-id gebruiken als u altijd dezelfde dagen/toegangsmachtigingen gebruikt, bijvoorbeeld beleidsregels voor locators die zijn bedoeld om gedurende een lange periode gehandhaafd te blijven (niet-upload-beleidsregels). Raadpleeg [dit](media-services-dotnet-manage-entities.md#limit-access-policies) onderwerp voor meer informatie.

### <a name="blob-container"></a>Blobcontainer
Een BLOB-container biedt een groepering van een set blobs. BLOB-containers worden in Media Services gebruikt als grens punt voor toegangs beheer en Shared Access Signature (SAS) Locators op assets. Een Azure Storage-account kan een onbeperkt aantal BLOB-containers bevatten. Een container kan een onbeperkt aantal blobs bevatten.

>[!NOTE]
> U moet niet proberen om de inhoud te wijzigen van BLOB-containers die zijn gegenereerd door Media Services zonder gebruik te maken van media service-Api's.
> 
> 

### <a name="locators"></a><a name="locators"></a>Locators
[Locator](/rest/api/media/operations/locator)s bieden een toegangs punt voor toegang tot de bestanden in een Asset. Een toegangs beleid wordt gebruikt voor het definiëren van de machtigingen en de duur die een client heeft voor toegang tot een bepaalde Asset. Locators kunnen een veel-op-een-relatie hebben met een toegangs beleid, zodat verschillende Locators verschillende start tijden en verbindings typen kunnen leveren aan verschillende clients, terwijl alle dezelfde machtigingen en duur instellingen worden gebruikt. vanwege een beperking van een gedeeld toegangs beleid dat door Azure Storage-services is ingesteld, kunt u echter niet meer dan vijf unieke Locators koppelen aan een bepaalde Asset. 

Media Services ondersteunt twee typen Locators: OnDemandOrigin-Locators, die worden gebruikt voor het streamen van media (bijvoorbeeld MPEG DASH, HLS of Smooth Streaming) of het progressief downloaden van media en SAS URL-Locators, die worden gebruikt voor het uploaden of downloaden van media bestanden to\from Azure Storage. 

>[!NOTE]
>De lijst machtiging (AccessPermissions. List) moet niet worden gebruikt bij het maken van een OnDemandOrigin-Locator. 

### <a name="storage-account"></a>Storage-account
Alle toegang tot Azure Storage wordt uitgevoerd via een opslag account. Een media service-account kan worden gekoppeld aan een of meer opslag accounts. Een account kan een onbeperkt aantal containers bevatten, zolang de totale grootte ervan onder 500TB per opslag account is.  Media Services biedt hulp middelen op het niveau van de SDK waarmee u meerdere opslag accounts kunt beheren en taak verdeling kunt Toep assen op de distributie van uw assets tijdens het uploaden naar deze accounts op basis van metrische gegevens of wille keurige distributie. Zie werken met [Azure Storage](/previous-versions/azure/dn767951(v=azure.100))voor meer informatie. 

## <a name="jobs-and-tasks"></a>Taken
Een [taak](/rest/api/media/operations/job) wordt meestal gebruikt voor het verwerken van een audio-of video presentatie (bijvoorbeeld index of code ring). Als u meerdere Video's verwerkt, maakt u een taak voor elke video die moet worden gecodeerd.

Een taak bevat meta gegevens over de verwerking die moet worden uitgevoerd. Elke taak bevat een of meer [taken](/rest/api/media/operations/task)die een Atomic-verwerkings taak opgeven, de invoer assets, uitvoer assets, een media processor en de bijbehorende instellingen. Taken in een taak kunnen samen worden gekoppeld, waarbij het uitvoer activum van een taak wordt gegeven als het invoer activum voor de volgende taak. Op deze manier kan één taak alle benodigde bewerkingen voor een media presentatie bevatten.

## <a name="encoding"></a><a id="encoding"></a>Encoding
Azure Media Services biedt meerdere opties voor het coderen van media in de Cloud.

Wanneer u met Media Services begint, is het belang rijk dat u begrijpt wat het verschil is tussen codecs en bestands indelingen.
Codecs zijn de software waarmee de algoritmen voor compressie/decompressie worden geïmplementeerd. bestands indelingen zijn containers die de gecomprimeerde video bevatten.

Media Services biedt dynamische pakketten waarmee u uw Adaptive bitrate MP4 of Smooth Streaming gecodeerde inhoud kunt leveren in streaming-indelingen die worden ondersteund door Media Services (MPEG DASH, HLS, Smooth Streaming) zonder dat u opnieuw moet inpakken in deze streaming-indelingen.

Als u gebruik wilt maken van [dynamische pakketten](media-services-dynamic-packaging-overview.md), moet u uw mezzanine-bestand (bron) coderen in een set Adaptive bitrate MP4-bestanden of adaptieve bitrate Smooth streaming-bestanden en ten minste één standaard-of Premium-streaming-eind punt in de status gestart hebben.

Media Services ondersteunt de volgende on-demand encoder die wordt beschreven in dit artikel:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)

Zie [encoder](media-services-encode-asset.md)voor meer informatie over deze ondersteunde encoder.

## <a name="live-streaming"></a>Live streamen
In Azure Media Services vertegenwoordigt een kanaal een pijp lijn voor het verwerken van live streaming-inhoud. Een kanaal ontvangt Live-invoer stromen op een van de volgende twee manieren:

* Een on-premises Live Encoder verzendt een multi-bitrate RTMP-of Smooth Streaming (gefragmenteerde MP4) naar het kanaal. U kunt de volgende Live coderings Programma's gebruiken die multi-bitrate Smooth Streaming uitvoeren: MediaExcel, Ateme, denk aan communicatie, Envivio, Cisco en elementair. De volgende Live coderings Programma's uitvoer RTMP: Adobe Flash Live Encoder, [Telestream Wirecast](media-services-configure-wirecast-live-encoder.md), Teradek, Haivision encoder. De opgenomen streams passeren kanalen zonder verdere trans codering en code ring. Desgevraagd levert Media Services de stream aan klanten.
* Een stream met één bitsnelheid (in een van de volgende indelingen: RTMP of Smooth Streaming (gefragmenteerde MP4)) wordt verzonden naar het kanaal dat is ingeschakeld voor het uitvoeren van Live code ring met Media Services. Het kanaal codeert de inkomende single-bitrate stream vervolgens live naar een (adaptieve) multi-bitrate videostream. Desgevraagd levert Media Services de stream aan klanten.

### <a name="channel"></a>Kanaal
In Media Services zijn [Channel](/rest/api/media/operations/channel)s verantwoordelijk voor het verwerken van live streaming-inhoud. Een kanaal biedt een invoer eindpunt (opname-URL) dat u vervolgens verstrekt aan een live-transcodeer. Het kanaal ontvangt Live-invoer stromen van de Live-transcodeer en maakt deze beschikbaar voor streaming via een of meer StreamingEndpoints. Kanalen bieden ook een preview-eind punt (Preview-URL) die u gebruikt om uw stroom te bekijken en te valideren vóór verdere verwerking en levering.

U kunt de opname-URL en de voor beeld-URL ophalen wanneer u het kanaal maakt. Om deze Url's op te halen, hoeft het kanaal niet de status gestart te hebben. Wanneer u klaar bent om gegevens van een live-transcodeer naar het kanaal te pushen, moet het kanaal worden gestart. Zodra de Live-code ring begint met het opnemen van gegevens, kunt u een voor beeld van de stroom bekijken.

Elk Media Services-account kan meerdere kanalen, meerdere Program Ma's en meerdere StreamingEndpoints bevatten. Afhankelijk van de behoefte aan band breedte en beveiliging, kunnen StreamingEndpoint-services worden toegewezen aan een of meer kanalen. Elk StreamingEndpoint kan uit elk kanaal halen.

### <a name="program-event"></a>Programma (gebeurtenis)
Met een [programma (gebeurtenis)](/rest/api/media/operations/program) kunt u het publiceren en opslaan van segmenten in een live stream beheren. Kanalen beheren Program Ma's (gebeurtenissen). De kanaal-en programma relatie is vergelijkbaar met traditionele media, waarbij een kanaal een constante stroom inhoud heeft en een programma een bepaalde time-outgebeurtenis op dat kanaal heeft.
U kunt het aantal uren opgeven dat u de opgenomen inhoud voor het programma wilt behouden door de eigenschap **ArchiveWindowLength** in te stellen. Deze waarde kan worden ingesteld van minimaal 5 minuten tot maximaal 25 uur.

ArchiveWindowLength bepaalt ook de maximale hoeveelheid tijd die clients vanaf de huidige Live positie op tijd kunnen zoeken. Programma's kunnen in de opgegeven tijdsduur worden uitgevoerd, maar de inhoud die achter de lengte van het venster valt, wordt altijd verwijderd. De waarde van deze eigenschap bepaalt ook hoe lang de clientmanifesten kunnen groeien.

Elk programma (gebeurtenis) is gekoppeld aan een Asset. Als u het programma wilt publiceren, moet u een Locator voor de gekoppelde Asset maken. Met deze locator kunt u een streaming-URL maken die u aan uw clients kunt leveren.

Een kanaal ondersteunt maximaal drie gelijktijdig actieve programma's, zodat u meerdere archieven van dezelfde binnenkomende stream kunt maken. Hierdoor kunt u verschillende onderdelen van een gebeurtenis naar behoefte publiceren en archiveren. Zo kan het voor uw bedrijf nodig zijn zes uur van een programma te archiveren, maar alleen de laatste tien minuten uit te zenden. Hiervoor moet u twee gelijktijdig actieve programma's maken. Het ene programma wordt ingesteld om zes uur van de gebeurtenis te archiveren, maar het programma wordt niet gepubliceerd. Het andere programma wordt ingesteld om tien minuten te archiveren en dit programma wordt wel gepubliceerd.

Zie voor meer informatie:

* [Werken met kanalen die zijn ingeschakeld om Live Encoding uit te voeren met Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Werken met kanalen die multi-bitrate Live Stream van on-premises encoders ontvangen](media-services-live-streaming-with-onprem-encoders.md)
* [Quota en beperkingen](media-services-quotas-and-limitations.md).

## <a name="protecting-content"></a>Inhoud beschermen
### <a name="dynamic-encryption"></a>Dynamische versleuteling
Met Azure Media Services kunt u uw media beveiligen vanaf het moment dat de computer de opslag, verwerking en levering verlaat. Met Media Services kunt u uw inhoud dynamisch versleutelen met Advanced Encryption Standard (AES) (met 128-bits versleutelings sleutels) en common Encryption (CENC) met PlayReady en/of Widevine DRM. Media Services biedt ook een service voor het leveren van AES-sleutels en PlayReady-licenties voor gemachtigde clients.

Op dit moment kunt u de volgende streaming-indelingen versleutelen: HLS, MPEG DASH en Smooth Streaming. U kunt progressieve down loads niet versleutelen.

Als u een Asset wilt Media Services versleutelen, moet u een versleutelings sleutel (CommonEncryption of EnvelopeEncryption) aan uw asset koppelen en ook autorisatie beleid voor de sleutel configureren.

Als u een door opslag versleutelde Asset wilt streamen, moet u het leverings beleid van het activum configureren om op te geven hoe u uw activa wilt leveren.

Wanneer een stroom wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met behulp van een envelop versleuteling (met AES) of een algemene versleuteling (met PlayReady of Widevine). Voor het ontsleutelen van de stroom vraagt de speler de sleutel aan bij de key delivery service. Om te bepalen of de gebruiker gemachtigd is om de sleutel op te halen, evalueert de service het autorisatie beleid dat u hebt opgegeven voor de sleutel.

### <a name="token-restriction"></a>Token beperking
Het autorisatie beleid voor inhouds sleutels kan een of meer autorisatie beperkingen hebben: open, token beperking of IP-beperking. Het beleid met de tokenbeperking moet vergezeld gaan van een token dat is uitgegeven door Secure Token Service (STS). Media Services ondersteunt tokens in de indelingen Simple Web Tokens (SWT) en JSON Web Token (JWT). Media Services biedt geen beveiligde token Services. U kunt een aangepaste STS maken. De STS moet worden geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel en claims uitgeven die u hebt opgegeven in de configuratie van de token beperking. De Media Services key delivery service retourneert de aangevraagde sleutel (of licentie) naar de client als het token geldig is en de claims in het token overeenkomen met de aanvragen die zijn geconfigureerd voor de sleutel (of licentie).

Wanneer u het token beperkt beleid configureert, moet u de para meters voor de primaire verificatie sleutel, verlener en doel groep opgeven. De primaire verificatie sleutel bevat de sleutel waarmee het token is ondertekend, de certificaat verlener is de beveiligde token service die het token uitgeeft. De doel groep (ook wel bereik genoemd) beschrijft de bedoeling van het token of de bron waarvan het token toegang verleent. Met de Media Services key delivery service wordt gecontroleerd of deze waarden in het token overeenkomen met de waarden in de sjabloon.

Raadpleeg voor meer informatie de volgende artikelen:
- [Overzicht van inhoud beveiligen](media-services-content-protection-overview.md)
- [Beveiligen met AES-128](media-services-protect-with-aes128.md)
- [Beveiligen met PlayReady/Widevine](media-services-protect-with-playready-widevine.md)

## <a name="delivering"></a>Leveren
### <a name="dynamic-packaging"></a><a name="dynamic_packaging"></a>Dynamische verpakking
Wanneer u werkt met Media Services, wordt aanbevolen om uw mezzanine-bestanden te coderen in een adaptieve bitsnelheid MP4 en de set vervolgens te converteren naar de gewenste indeling met behulp van de [dynamische verpakking](media-services-dynamic-packaging-overview.md).

### <a name="streaming-endpoint"></a>Streaming-eindpunt
Een StreamingEndpoint vertegenwoordigt een streaming-service die inhoud rechtstreeks kan leveren aan een client speler of een Content Delivery Network (CDN) voor verdere distributie (Azure Media Services biedt nu de Azure CDN integratie.) De uitgaande stroom van een service voor streaming-eind punten kan een live stream zijn of een video op aanvraag-asset in uw Media Services-account. Klanten van Media Services kiezen ofwel een **Standard**-streaming-eindpunt of een of meer Premium-**streaming**-eindpunten, afhankelijk van hun behoeften. Standard streaming-eind punt is geschikt voor de meeste streaming-workloads. 

Standard-streaming-eindpunten zijn geschikt voor de meeste streaming-workloads. Standard streaming-eind punten bieden de flexibiliteit voor het leveren van uw inhoud aan vrijwel elk apparaat via dynamische pakketten in HLS, MPEG-DASH en Smooth Streaming, evenals dynamische versleuteling voor micro soft PlayReady, Google Widevine, Apple FairPlay en AES128.  Ze kunnen ook van zeer kleine tot zeer grote doel groepen worden geschaald met duizenden gelijktijdige kijkers via Azure CDN integratie. Als u een geavanceerde werk belasting hebt of de vereisten voor de streaming-capaciteit niet voldoen aan de standaard streaming-doorvoer doelen voor het streamen van het apparaat of als u de capaciteit van de StreamingEndpoint-service wilt bepalen voor het afhandelen van groeiende bandbreedte behoeften, is het raadzaam om schaal eenheden toe te wijzen (ook wel Premium streaming-eenheden genoemd).

Het is raadzaam dynamische pakketten en/of dynamische versleuteling te gebruiken.

>[!NOTE]
>Wanneer uw AMS-account is gemaakt, wordt er een **standaardstreaming-eindpunt** met de status **Gestopt** toegevoegd aan uw account. Als u inhoud wilt streamen en gebruik wilt maken van dynamische pakketten en dynamische versleuteling, moet het streaming-eindpunt van waar u inhoud wilt streamen, de status **Wordt uitgevoerd** hebben. 

Raadpleeg [dit](media-services-portal-manage-streaming-endpoints.md) onderwerp voor meer informatie.

Standaard kunt u Maxi maal 2 streaming-eind punten in uw Media Services-account hebben. Zie [quota's en beperkingen](media-services-quotas-and-limitations.md)als u een hogere limiet wilt aanvragen.

Er worden alleen kosten in rekening gebracht wanneer uw StreamingEndpoint actief is.

### <a name="asset-delivery-policy"></a>Leverings beleid voor assets
Een van de stappen in de Media Services werk stroom voor het leveren van inhoud is het configureren [van leverings beleid voor assets](/rest/api/media/operations/assetdeliverypolicy)die u wilt streamen. Het beleid voor levering van activa vertelt Media Services hoe u wilt dat uw activa worden geleverd: in het streaming-protocol moet uw asset dynamisch worden verpakt (bijvoorbeeld MPEG DASH, HLS, Smooth Streaming of alle), ongeacht of u uw activa dynamisch wilt versleutelen en hoe (envelop of gemeen schappelijke versleuteling).

Als u een met opslag versleutelde Asset hebt, voordat uw asset kan worden gestreamd, verwijdert de streaming-server de opslag versleuteling en streamt uw inhoud met behulp van het opgegeven leverings beleid. Als u bijvoorbeeld uw asset versleuteld met Advanced Encryption Standard (AES) versleutelings sleutel wilt leveren, stelt u het beleids type in op DynamicEnvelopeEncryption. Als u de opslag versleuteling wilt verwijderen en het activum in de heldere stroom wilt streamen, stelt u het beleids type in op NoDynamicEncryption.

### <a name="progressive-download"></a>Progressieve down load
Met progressief downloaden kunt u beginnen met het afspelen van media voordat het hele bestand is gedownload. U kunt een MP4-bestand alleen progressief downloaden.

>[!NOTE]
>U moet versleutelde assets ontsleutelen als u wilt dat ze beschikbaar zijn voor progressief downloaden.

Als u gebruikers wilt voorzien van progressieve down load-Url's, moet u eerst een OnDemandOrigin-Locator maken. Als u de Locator maakt, krijgt u het basispad voor de Asset. Vervolgens moet u de naam van het MP4-bestand toevoegen. Bijvoorbeeld:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

### <a name="streaming-urls"></a>Streaming-Url's
Uw inhoud streamen naar clients. Als u gebruikers wilt voorzien van streaming-Url's, moet u eerst een OnDemandOrigin-Locator maken. Als u de Locator maakt, krijgt u het basispad dat de inhoud bevat die u wilt streamen. Als u deze inhoud echter wilt streamen, moet u dit pad verder aanpassen. Als u een volledige URL naar het streaming-manifest bestand wilt maken, moet u de pad-waarde van de Locator en de naam van het manifest bestand (bestandsnaam. ISM) samen voegen. Voeg vervolgens/manifest en de juiste indeling (indien nodig) toe aan het pad van de Locator.

U kunt ook uw inhoud streamen via een TLS-verbinding. Om dit te doen, moet u ervoor zorgen dat uw streaming-Url's beginnen met HTTPS. AMS biedt momenteel geen ondersteuning voor TLS met aangepaste domeinen.  

>[!NOTE]
>U kunt alleen streamen via TLS als het streaming-eind punt van waaruit u uw inhoud levert, is gemaakt na 10 september 2014. Als uw streaming-Url's zijn gebaseerd op de streaming-eind punten die zijn gemaakt na 10 september, bevat de URL ' streaming.mediaservices.windows.net ' (de nieuwe indeling). Streaming-Url's die "origin.mediaservices.windows.net" (de oude indeling) bevatten, bieden geen ondersteuning voor TLS. Als uw URL de oude indeling heeft en u via TLS wilt kunnen streamen, maakt u een nieuw streaming-eind punt. Gebruik Url's die zijn gemaakt op basis van het nieuwe streaming-eind punt om uw inhoud te streamen via TLS.

In de volgende lijst worden verschillende streaming-indelingen beschreven en vindt u voor beelden:

* Smooth Streaming

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

* MPEG DASH

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = mpd-time-KVP)

* Apple HTTP Live Streaming (HLS) v4

{streaming-eindpuntnaam-media services-accountnaam}.streaming.mediaservices.windows.net/{locator-id}/{bestandsnaam}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL)

* Apple HTTP Live Streaming (HLS) v3

{streaming-eindpunt naam-Media Services-account naam}. streaming. Media Services. Windows. net/{Locator-ID} bestands (Format = M3U8-AAPL-v3)

http: \/ /testendpoint-testaccount.streaming.mediaservices.Windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (Format = M3U8-AAPL-v3)

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
