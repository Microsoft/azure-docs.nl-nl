---
title: Uw account configureren voor offline streaming van Widevine beveiligde inhoud-Azure
description: In dit onderwerp ziet u hoe u uw Azure Media Services-account kunt configureren voor offline streaming van Widevine beveiligde inhoud.
services: media-services
keywords: DASH, DRM, Widevine offline modus, ExoPlayer, Android
documentationcenter: ''
author: IngridAtMicrosoft
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: willzhan
ms.reviewer: dwgeo
ms.openlocfilehash: b13feeda162f9babe1958402a694b728312ea4e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015400"
---
# <a name="offline-widevine-streaming-for-android"></a>Offline Widevine-streaming voor Android

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Selecteer de versie van Media Services die u gebruikt:"]
> * [Versie 3](../latest/offline-widevine-for-android.md)
> * [Versie 2](offline-widevine-for-android.md)

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie [Media Services v3](../latest/index.yml). Zie ook [migratie richtlijnen van v2 naar v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Naast het beveiligen van inhoud voor online streaming bieden media-inhouds abonnementen en verhuur Services Download bare inhoud die werkt wanneer u niet met internet verbonden bent. Mogelijk moet u inhoud downloaden naar uw telefoon of Tablet voor het afspelen in de vliegtuig modus wanneer de verbinding met het netwerk is verbroken. Aanvullende scenario's waarin u mogelijk inhoud wilt downloaden:

- Sommige inhouds providers kunnen geen DRM-licentie levering toestaan buiten de rand van een land/regio. Als een gebruiker inhoud wil bekijken terwijl u in het buiten land reist, is offline downloaden vereist.
- In sommige landen/regio's is Internet Beschik baarheid en/of band breedte beperkt. Gebruikers kunnen ervoor kiezen om inhoud te downloaden om deze te kunnen bekijken met een hoge resolutie voor een goede weergave ervaring.

In dit artikel wordt beschreven hoe u het afspelen van offline modus kunt implementeren voor de DASH-inhoud die wordt beveiligd door Widevine op Android-apparaten. Met behulp van offline DRM kunt u voor uw inhoud abonnements-, huur-en aankoop modellen aanbieden, zodat klanten van uw services eenvoudig inhoud kunnen meenemen wanneer de verbinding met internet is verbroken.

Voor het bouwen van de Android Player-apps zijn er drie opties:

> [!div class="checklist"]
> * Een speler bouwen met behulp van de Java API van de ExoPlayer SDK
> * Een speler bouwen met Xamarin-binding van ExoPlayer SDK
> * Een speler bouwen met versleutelde media-extensie (EME) en media source extension (MSE) in Chrome Mobile browser v62 of hoger

Het artikel beantwoordt ook enkele veelgestelde vragen met betrekking tot offline streaming van beveiligde Widevine-inhoud.

## <a name="requirements"></a>Vereisten 

Voordat u offline DRM implementeert voor Widevine op Android-apparaten, moet u eerst het volgende doen:

- Vertrouwd raken met de concepten die zijn geïntroduceerd voor online Content Protection met behulp van Widevine DRM. Dit wordt gedetailleerd behandeld in de volgende documenten/voor beelden:
    - [Azure Media Services gebruiken om DRM-licenties of AES-sleutels te leveren](media-services-deliver-keys-and-licenses.md)
    - [CENC met Multi-DRM en Access Control: een referentieontwerp en -implementatie in Azure en Azure Media Services](media-services-cenc-with-multidrm-access-control.md)
    - [PlayReady en/of Widevine Dynamic Common Encryption gebruiken met .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-dynamic-encryption-with-drm/)
    - [Gebruik Azure Media Services voor het leveren van PlayReady-en/of Widevine-licenties met .NET](https://azure.microsoft.com/resources/samples/media-services-dotnet-deliver-playready-widevine-licenses/)
- Vertrouwd raken met de Google ExoPlayer SDK voor Android, een open-source video speler SDK die ondersteuning biedt voor het afspelen van offline Widevine DRM. 
    - [ExoPlayer-SDK](https://github.com/google/ExoPlayer)
    - [Ontwikkelaars handleiding voor ExoPlayer](https://google.github.io/ExoPlayer/guide.html)
    - [EoPlayer Developer-Blog](https://medium.com/google-exoplayer)

## <a name="content-protection-configuration-in-azure-media-services"></a>Configuratie van inhouds beveiliging in Azure Media Services

Wanneer u Widevine-beveiliging van een asset in Media Services configureert, moet u ContentKeyAuthorizationPolicyOption maken die de volgende drie dingen hebben opgegeven:

1. DRM-systeem (Widevine)
2. ContentKeyAuthorizationPolicyRestriction opgeven hoe de levering van de inhouds sleutel is geautoriseerd in de service voor licentie levering (open-of Token autorisatie)
3. DRM-licentie sjabloon (Widevine)

Als u de **offline** modus voor Widevine-licenties wilt inschakelen, moet u de [Widevine-licentie sjabloon](media-services-widevine-license-template-overview.md)configureren. Stel in het **policy_overrides** -object de eigenschap **can_persist** in op **True** (standaard is False). 

In het volgende code voorbeeld wordt .NET gebruikt om de **offline** modus voor Widevine-licenties in te scha kelen. De code is gebaseerd op het voor beeld [ met PlayReady en/of Widevine Dynamic common Encryption met .net](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm) . 

```
private static string ConfigureWidevineLicenseTemplateOffline(Uri keyDeliveryUrl)
{
    var template = new WidevineMessage
    {
        allowed_track_types = AllowedTrackTypes.SD_HD,
        content_key_specs = new[]
        {
            new ContentKeySpecs
            {
                required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                security_level = 1,
                track_type = "SD"
            }
        },
        policy_overrides = new
        {
            can_play = true,
            can_persist = true,
            //can_renew = true,                                //if you set can_renew = false, you do not need renewal_server_url
            //renewal_server_url = keyDeliveryUrl.ToString(),    //not mandatory, renewal_server_url is needed only if license_duration_seconds is set
            can_renew = false,
            //rental_duration_seconds = 1209600,
            //playback_duration_seconds = 1209600,
            //license_duration_seconds = 1209600
        }
        };

    string configuration = Newtonsoft.Json.JsonConvert.SerializeObject(template);
    return configuration;
}
```

## <a name="configuring-the-android-player-for-offline-playback"></a>De Android-speler configureren voor offline afspelen

De eenvoudigste manier om een systeem eigen Windows-app voor Android-apparaten te ontwikkelen, is door de [Google EXOPLAYER SDK](https://github.com/google/ExoPlayer), een open-source video speler SDK te gebruiken. ExoPlayer ondersteunt functies die momenteel niet worden ondersteund door de systeem eigen Media Player-API van Android, met inbegrip van MPEG-DASH en micro soft Smooth Streaming Delivery protocols.

ExoPlayer versie 2,6 en hoger bevat veel klassen die offline Widevine DRM-afspelen ondersteunen. Met name de OfflineLicenseHelper-klasse biedt hulp functies om het gebruik van de DefaultDrmSessionManager te vergemakkelijken voor het downloaden, vernieuwen en vrijgeven van offline licenties. De klassen die zijn opgenomen in de SDK-map "bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/offline/" ondersteunen het downloaden van offline video-inhoud.

De volgende lijst met klassen vereenvoudigt de offline modus in de ExoPlayer SDK voor Android:

- bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/DRM/OfflineLicenseHelper. java  
- bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSession. java
- bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/DRM/DefaultDrmSessionManager. java
- bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/DRM/DrmSession. java
- bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/DRM/ErrorStateDrmSession. java
- bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/DRM/ExoMediaDrm. java
- bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/offline/SegmentDownloader. java
- bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/offline/DownloaderConstructorHelper. java 
- bibliotheek/core/src/main/Java/com/Google/Android/exoplayer2/offline/Downloader. java
- bibliotheek/streepje/src/main/Java/com/Google/Android/exoplayer2/source/Dash/offline/DashDownloader. java 

Ontwikkel aars moeten tijdens de ontwikkeling van een toepassing verwijzen naar de [ExoPlayer-ontwikkelaars handleiding](https://google.github.io/ExoPlayer/guide.html) en de bijbehorende [ontwikkelaars blog](https://medium.com/google-exoplayer) . Google heeft op dit moment geen volledig gedocumenteerde referentie-implementatie of voorbeeld code vrijgegeven voor de ExoPlayer-app die Widevine offline ondersteunt. de informatie is daarom beperkt tot de ontwikkel aars-gids en blog. 

### <a name="working-with-older-android-devices"></a>Werken met oudere Android-apparaten

Voor sommige oudere Android-apparaten moet u waarden instellen voor de volgende **policy_overrides** eigenschappen (gedefinieerd in [Widevine-licentie sjabloon](media-services-widevine-license-template-overview.md): **rental_duration_seconds**, **playback_duration_seconds** en **license_duration_seconds**. U kunt ze ook instellen op nul, wat betekent oneindig/onbeperkte duur.  

De waarden moeten worden ingesteld om te voor komen dat er een geheeltallige overloop fout optreedt. Zie en voor meer uitleg over het probleem https://github.com/google/ExoPlayer/issues/3150 https://github.com/google/ExoPlayer/issues/3112 . <br/>Als u de waarden niet expliciet instelt, worden zeer grote waarden voor  **PlaybackDurationRemaining** en **LicenseDurationRemaining** toegewezen, (bijvoorbeeld 9223372036854775807, de maximale positieve waarde voor een 64-bits geheel getal). Als gevolg hiervan wordt de Widevine-licentie verouderd en daarom wordt de ontsleuteling niet uitgevoerd. 

Dit probleem treedt niet op bij Android 5,0 lollipop of hoger omdat Android 5,0 de eerste Android-versie is, die is ontworpen om ARMv8 ([Advanced RISC machine](https://en.wikipedia.org/wiki/ARM_architecture)) en 64-bits platformen volledig te ondersteunen, terwijl Android 4,4 KitKat oorspronkelijk is ontworpen ter ondersteuning van ARMv7-en 32-bits platforms als andere oudere Android-versies.

## <a name="using-xamarin-to-build-an-android-playback-app"></a>Xamarin gebruiken om een app voor het afspelen van Android te bouwen

U kunt Xamarin-bindingen voor ExoPlayer vinden met behulp van de volgende koppelingen:

- [Xamarin bindings bibliotheek voor de Google ExoPlayer-bibliotheek](https://github.com/martijn00/ExoPlayerXamarin)
- [Xamarin-bindingen voor ExoPlayer NuGet](https://www.nuget.org/packages/Xam.Plugins.Android.ExoPlayer/)

Zie ook de volgende thread: [Xamarin-binding](https://github.com/martijn00/ExoPlayerXamarin/pull/57). 

## <a name="chrome-player-apps-for-android"></a>Chrome Player-apps voor Android

Vanaf de release van [Chrome voor Android v. 62](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates)wordt de permanente licentie in EME ondersteund. [Widevine L1](https://developers.google.com/web/updates/2017/09/chrome-62-media-updates#widevine_l1) wordt nu ook ondersteund in Chrome voor Android. Zo kunt u offline afspeel toepassingen maken in Chrome als uw eind gebruikers deze (of hoger) versie van Chrome hebben. 

Daarnaast heeft Google een voor beeld van een progressieve web-app (PWA) gemaakt en het bestand geopend: 

- [Broncode](https://github.com/GoogleChromeLabs/sample-media-pwa)
- [Google gehoste versie](https://biograf-155113.appspot.com/ttt/episode-2/) (werkt alleen in Chrome v 62 en hoger op Android-apparaten)

Als u uw mobiele Chrome-browser bijwerkt naar v62 (of hoger) op een Android-telefoon en de bovenstaande gehoste voor beeld-app wilt testen, ziet u dat zowel online streaming als offline afspelen wordt uitgevoerd.

De bovenstaande open-source PWA-app is gemaakt in Node.js. Als u uw eigen versie op een Ubuntu-server wilt hosten, houdt u rekening met de volgende veelvoorkomende problemen die het afspelen kunnen voor komen:

1. CORS-probleem: de voorbeeld video in de voor beeld-app wordt gehost in https://storage.googleapis.com/biograf-video-files/videos/ . Google heeft CORS ingesteld voor alle test voorbeelden die worden gehost in Google Cloud Storage Bucket. Ze worden geleverd met CORS-headers, waarbij expliciet de CORS-vermelding wordt opgegeven: `https://biograf-155113.appspot.com` (het domein waarin Google als host fungeert voor het voor beeld), waardoor toegang door andere sites wordt voor komen. Als u probeert, wordt de volgende HTTP-fout weer geven: `Failed to load https://storage.googleapis.com/biograf-video-files/videos/poly-sizzle-2015/mp4/dash.mpd: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https:\//13.85.80.81:8080' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.`
2. Probleem met het certificaat: vanaf Chrome v 58, EME voor Widevine is HTTPS vereist. Daarom moet u de voor beeld-app via HTTPS hosten met een x509-certificaat. Een normaal test certificaat werkt niet als gevolg van de volgende vereisten: u moet een certificaat verkrijgen dat voldoet aan de volgende minimale vereisten:
    - Chrome en Firefox vereisen dat SAN-Subject alternatieve naam instelling bestaat in het certificaat
    - Het certificaat moet beschikken over een vertrouwde certificerings instantie en een zelf-ondertekend ontwikkelings certificaat niet
    - Het certificaat moet een CN hebben die overeenkomt met de DNS-naam van de webserver of gateway

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="question"></a>Vraag

Hoe kan ik permanente licenties leveren (offline ingeschakeld) voor sommige clients/gebruikers en niet-permanente licenties (offline uitgeschakeld) voor anderen? Moet ik de inhoud dupliceren en een afzonderlijke inhouds sleutel gebruiken?

### <a name="answer"></a>Antwoord
U hoeft de inhoud niet te dupliceren. U kunt slechts één exemplaar van de inhoud en één ContentKeyAuthorizationPolicy gebruiken, maar twee afzonderlijke ContentKeyAuthorizationPolicyOption:

1. IContentKeyAuthorizationPolicyOption 1: maakt gebruik van een permanente licentie en ContentKeyAuthorizationPolicyRestriction 1 met een claim zoals license_type = "persistent"
2. IContentKeyAuthorizationPolicyOption 2: maakt gebruik van niet-permanente licenties en ContentKeyAuthorizationPolicyRestriction 2 met een claim zoals license_type = "niet persistent"

Op deze manier, wanneer een licentie aanvraag afkomstig is uit de client-app, is er geen verschil van de licentie aanvraag. Voor de verschillende eind gebruiker/apparaat moet de STS echter de bedrijfs logica hebben om verschillende JWT-tokens met verschillende claims te verlenen (een van de bovenstaande twee license_type). De claim waarde in het JWT-token wordt gebruikt om te bepalen welke licentie service u wilt gebruiken om te verlenen welk type licentie: permanent of niet permanent is.

Dit betekent dat de Secure token service (STS) de bedrijfs logica en de gegevens van de client/het apparaat nodig heeft om de bijbehorende claim waarde toe te voegen aan een token.

### <a name="question"></a>Vraag

Voor Widevine-beveiligings niveaus, in de documentatie over het [WIDEVINE DRM-architectuur overzicht](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf) van Google, worden er drie verschillende beveiligings niveaus gedefinieerd. In Azure Media Services- [documentatie over de licentie sjabloon Widevine](./media-services-widevine-license-template-overview.md)worden echter vijf verschillende beveiligings niveaus beschreven. Wat is de relatie of toewijzing tussen de twee verschillende sets beveiligings niveaus?

### <a name="answer"></a>Antwoord

In het overzicht van de [WIDEVINE DRM-architectuur](https://storage.googleapis.com/wvdocs/Widevine_DRM_Architecture_Overview.pdf)van Google worden de volgende drie beveiligings niveaus gedefinieerd:

1.  Beveiligings niveau 1: alle verwerking van inhoud, crypto grafie en beheer worden uitgevoerd binnen de Trusted Execution Environment (TEE). In sommige implementatie modellen kan beveiligings verwerking worden uitgevoerd in verschillende chips.
2.  Beveiligings niveau 2: voert crypto grafie (maar geen video verwerking) uit in de TEE: ontsleutelde buffers worden geretourneerd naar het toepassings domein en verwerkt via afzonderlijke video-hardware of software. Op niveau 2 wordt echter alleen cryptografische informatie verwerkt in het TEE.
3.  Beveiligings niveau 3 heeft geen TEE op het apparaat. Er kunnen passende maat regelen worden getroffen ter bescherming van de cryptografische informatie en ontsleutelde inhoud op het hostbesturingssysteem. Een level 3-implementatie kan ook een hardware-cryptografische engine omvatten, maar dat biedt alleen betere prestaties, niet voor beveiliging.

Op hetzelfde moment kan de eigenschap security_level van content_key_specs de volgende vijf verschillende waarden hebben (in [Azure Media Services documentatie op de Widevine-licentie sjabloon](./media-services-widevine-license-template-overview.md):

1.  Crypto grafie op basis van software-WhiteBox is vereist.
2.  Software-crypto en een verborgen decoder zijn vereist.
3.  Het sleutel materiaal en de cryptografische bewerkingen moeten worden uitgevoerd in een TEE van een hardwarematige back-up.
4.  De crypto grafie en de code ring van inhoud moet worden uitgevoerd in een TEE-hardware.
5.  De crypto grafie, het decoderen en alle verwerking van de media (gecomprimeerd en niet-gecomprimeerd) moeten worden afgehandeld in een TEE-hardware.

Beide beveiligings niveaus worden gedefinieerd door Google Widevine. Het verschil bevindt zich in het gebruiks niveau: architectuur niveau of API-niveau. De vijf beveiligings niveaus worden gebruikt in de Widevine-API. Het content_key_specs-object, dat security_level bevat, wordt gedeserialiseerd en door gegeven aan de Widevine Global Delivery Service van Azure Media Services Widevine-licentie service. In de volgende tabel ziet u de toewijzing tussen de twee sets beveiligings niveaus.

| **Beveiligings niveaus die zijn gedefinieerd in de Widevine-architectuur** |**Beveiligings niveaus die worden gebruikt in de Widevine-API**|
|---|---| 
| **Beveiligings niveau 1**: alle verwerking van inhoud, crypto grafie en beheer worden uitgevoerd binnen de Trusted Execution Environment (Tee). In sommige implementatie modellen kan beveiligings verwerking worden uitgevoerd in verschillende chips.|**security_level = 5**: de crypto grafie, het decoderen en alle verwerking van de media (gecomprimeerd en niet-gecomprimeerd) moeten worden afgehandeld in een tee-apparaat.<br/><br/>**security_level = 4**: de crypto grafie en de code ring van inhoud moet worden uitgevoerd binnen een door hardware opgeslagen Tee.|
**Beveiligings niveau 2**: voert crypto grafie (maar geen video verwerking) uit in de tee: ontsleutelde buffers worden geretourneerd naar het toepassings domein en verwerkt via afzonderlijke video-hardware of software. Op niveau 2 wordt echter alleen cryptografische informatie verwerkt in het TEE.| **security_level = 3**: het sleutel materiaal en de cryptografische bewerkingen moeten worden uitgevoerd in een Tee van een hardwarematige back-up. |
| **Beveiligings niveau 3**: heeft geen tee op het apparaat. Er kunnen passende maat regelen worden getroffen ter bescherming van de cryptografische informatie en ontsleutelde inhoud op het hostbesturingssysteem. Een level 3-implementatie kan ook een hardware-cryptografische engine omvatten, maar dat biedt alleen betere prestaties, niet voor beveiliging. | **security_level = 2**: software-crypto en een verborgen decoder zijn vereist.<br/><br/>**security_level = 1**: op software gebaseerde WhiteBox-crypto grafie is vereist.|

### <a name="question"></a>Vraag

Waarom kan het downloaden van inhoud zo lang duren?

### <a name="answer"></a>Antwoord

Er zijn twee manieren om de download snelheid te verbeteren:

1.  Schakel CDN in zodat eind gebruikers waarschijnlijk het CDN van het oorspronkelijke/streaming-eind punt voor het downloaden van inhoud kunnen aanraken. Als het streaming-eind punt van de gebruiker is, wordt elk HLS segment of koppel teken dynamisch verpakt en versleuteld. Hoewel deze latentie in milliseconde is voor elk segment/fragment, wanneer u een uur lang video hebt, kan de verzamelde latentie groot worden om te worden gedownload.
2.  Geef eind gebruikers de mogelijkheid om de video kwaliteits lagen en audio tracks selectief te downloaden in plaats van alle inhoud. Voor de offline modus is er geen punt om alle kwaliteits lagen te downloaden. Er zijn twee manieren om dit te doen:
    1.  Door client beheerd: met de optie voor automatisch selecteren van de speler of gebruiker selecteert u de video kwaliteit laag en audio nummers die u wilt downloaden.
    2.  Beheerde service: één kan gebruikmaken van de dynamische manifest functie in Azure Media Services om een (globaal) filter te maken, waarmee de HLS-afspeel lijst of-streep-MPD wordt beperkt tot één video kwaliteit en geselecteerde audio tracks. De download-URL die aan eind gebruikers wordt gepresenteerd, omvat dit filter.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="summary"></a>Samenvatting

In dit artikel wordt beschreven hoe u het afspelen van offline modus kunt implementeren voor de DASH-inhoud die wordt beveiligd door Widevine op Android-apparaten.  Er zijn ook enkele veelgestelde vragen beantwoord met betrekking tot offline streaming van beveiligde Widevine-inhoud.
