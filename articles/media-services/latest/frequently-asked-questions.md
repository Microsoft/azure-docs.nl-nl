---
title: Veelgestelde vragen over Azure Media Services v3
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: b9a4c15c0942a0a5dffe2cfdb30dbea4184057ed
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565585"
---
# <a name="media-services-v3-frequently-asked-questions"></a>Veelgestelde vragen over Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel vindt u antwoorden op veelgestelde vragen over Azure Media Services v3.

## <a name="general"></a>Algemeen

### <a name="does-media-services-store-any-customer-data-outside-of-the-service-region"></a>Worden er klant gegevens buiten de service regio opgeslagen Media Services?

- Klanten voegen hun eigen opslag accounts toe aan hun Azure Media Services-account.  Alle Asset-gegevens worden opgeslagen in deze gekoppelde opslag accounts en de klant beheert de locatie en het replicatie type van deze opslag.
- Aanvullende gegevens die zijn gekoppeld aan het Media Services account (inclusief coderings sleutels voor inhoud, sleutels voor token verificatie, JobInputHttp url's en andere entiteits meta gegevens) worden opgeslagen in micro soft-opslag in eigendom binnen de regio die is geselecteerd voor het Media Services-account.
    - Vanwege de [vereisten voor gegevens locatie](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) in Brazilië-Zuid en Zuidoost-Azië, worden de extra account gegevens opgeslagen op een zone-redundante manier en bevinden zich in één regio. Voor Zuidoost-Azië worden alle extra account gegevens opgeslagen in Singapore en voor Brazilië-zuid worden de gegevens opgeslagen in Brazilië.
    - In andere regio's dan Brazilië-zuid en Zuidoost-Azië kunnen de extra account gegevens ook worden opgeslagen in de opslag van micro soft in de [gekoppelde regio](../../best-practices-availability-paired-regions.md).
- Azure Media Services is een regionale service en biedt geen [hoge Beschik baarheid](media-services-high-availability-encoding.md) of gegevens replicatie. Klanten die deze functies nodig hebben, worden nadrukkelijk aangemoedigd om een oplossing te bouwen met behulp van Media Services accounts in meerdere regio's.  Een voor beeld waarin wordt getoond hoe u een oplossing bouwt voor hoge Beschik baarheid met Media Services video op aanvraag is beschikbaar als hand leiding.

### <a name="what-are-the-azure-portal-limitations-for-media-services-v3"></a>Wat zijn de Azure Portal beperkingen voor Media Services v3?

U kunt de [Azure Portal](https://portal.azure.com/) gebruiken om v3 Live-gebeurtenissen te beheren, v3-assets en-taken weer te geven, informatie over het openen van api's te verkrijgen, inhoud te versleutelen. <br/>Voor alle andere beheer taken (bijvoorbeeld het beheren van trans formaties en taken of het analyseren van v3-inhoud), gebruikt u de [rest API](/rest/api/media/accountfilters), [cli](/cli/azure/ams)of een van de ondersteunde [sdk's](media-services-apis-overview.md#sdks).

Als uw video eerder is geüpload naar het Media Services-account met behulp van de Media Services v3-API of als de inhoud is gegenereerd op basis van live-uitvoer, zijn de knoppen **Coderen**, **Analyseren** of **Versleutelen** niet zichtbaar in Azure Portal. Gebruik de Media Services v3-API's om deze taken uit te voeren.  

### <a name="what-azure-roles-can-perform-actions-on-azure-media-services-resources"></a>Wat Azure-rollen kunnen acties uitvoeren op Azure Media Services resources? 

Zie [op rollen gebaseerd toegangs beheer voor Azure (Azure RBAC) voor Media Services accounts](rbac-overview.md).

### <a name="how-do-i-stream-to-apple-ios-devices"></a>Hoe kan ik streamen naar Apple iOS-apparaten?

Zorg ervoor dat u **(Format = M3U8-AAPL)** aan het einde van uw pad (na het gedeelte **/manifest** van de URL) hebt om de streaming-bron server te laten weten http live streaming (HLS)-inhoud te retour neren voor het gebruik van Apple IOS systeem eigen apparaten. Zie [inhoud leveren](dynamic-packaging-overview.md)voor meer informatie.

### <a name="what-is-the-recommended-method-to-process-videos"></a>Wat is de aanbevolen methode om Video's te verwerken?

[Trans formaties](/rest/api/media/transforms) gebruiken om algemene taken te configureren voor het coderen of analyseren van Video's. Elke trans formatie beschrijft een recept of een werk stroom van taken voor het verwerken van uw video-of audio bestanden. Een [taak](/rest/api/media/jobs) is de daad werkelijke aanvraag om Media Services om de trans formatie toe te passen op een invoer video of audio-inhoud. Nadat de trans formatie is gemaakt, kunt u taken verzenden met behulp van Media Services Api's of een van de gepubliceerde Sdk's. Zie [Transformaties en taken](transforms-jobs-concept.md) voor meer informatie.

### <a name="i-uploaded-encoded-and-published-a-video-why-wont-the-video-play-when-i-try-to-stream-it"></a>Ik heb een video geüpload, gecodeerd en gepubliceerd. Waarom wordt de video niet afgespeeld wanneer ik deze probeer te streamen?

Een van de meest voorkomende oorzaken is dat u het streaming-eind punt niet hebt van waaruit u wilt afspelen in de actieve status.

### <a name="how-does-pagination-work"></a>Hoe werkt de paginering?

Wanneer u paginering gebruikt, moet u altijd de volgende koppeling gebruiken om de verzameling op te sommen en niet afhankelijk van een bepaalde pagina grootte. Zie [filteren, ordenen, paginering](entities-overview.md)voor meer informatie en voor beelden.

### <a name="what-features-are-not-yet-available-in-azure-media-services-v3"></a>Welke functies zijn nog niet beschikbaar in Azure Media Services v3?

Zie [de migratie handleiding](migrate-v-2-v-3-migration-introduction.md)voor meer informatie.

### <a name="what-is-the-process-of-moving-a-media-services-account-between-subscriptions"></a>Wat is het proces van het verplaatsen van een Media Services account tussen abonnementen?  

Zie [een Media Services account verplaatsen tussen abonnementen](media-services-account-concept.md)voor meer informatie.

## <a name="live-streaming"></a>Live streamen 

### <a name="how-do-i-stop-the-live-stream-after-the-broadcast-is-done"></a>De Live Stream Hoe kan ik stoppen nadat de uitzending is voltooid?

U kunt het benadert aan de client zijde of aan de server zijde.

#### <a name="client-side"></a>Client zijde

Uw webtoepassing moet de gebruiker vragen of de uitzending moet worden beëindigd wanneer de browser wordt gesloten. Dit is een browser gebeurtenis die uw webtoepassing kan verwerken.

#### <a name="server-side"></a>Server zijde

U kunt live-gebeurtenissen bewaken door zich te abonneren op Azure Event Grid-gebeurtenissen. Zie het [EventGrid-gebeurtenis schema](monitoring/media-services-event-schemas.md#live-event-types)voor meer informatie.

U hebt de volgende mogelijkheden:

* [Abonneer](monitoring/reacting-to-media-services-events.md) u op de gebeurtenissen op het niveau van [micro soft. media. LiveEventEncoderDisconnected](monitoring/media-services-event-schemas.md#liveeventencoderdisconnected) en controleer of er gedurende een tijdje geen reconnects beschikbaar zijn om uw live-gebeurtenis te stoppen en te verwijderen.
* [Abonneer](monitoring/reacting-to-media-services-events.md) u op de [heartbeat](monitoring/media-services-event-schemas.md#liveeventingestheartbeat) -gebeurtenissen op traceer niveau. Als alle sporen een binnenkomende bitrate hebben die niet langer is dan 0 of als de laatste tijds tempel niet meer stijgt, kunt u de live-gebeurtenis veilig afsluiten. De heartbeat-gebeurtenissen bevinden zich elke 20 seconden voor elk spoor, waardoor het mogelijk een beetje uitgebreid is.

###  <a name="how-do-i-insert-breaksvideos-and-image-slates-during-a-live-stream"></a>Hoe kan ik invoegen van pauzes/Video's en afbeeldings pastels tijdens een live stream?

Media Services v3 Live encoding biedt nog geen ondersteuning voor het invoegen van video-of afbeeldings pastels tijdens live stream. 

U kunt een [Live on-premises encoder](recommended-on-premises-live-encoders.md) gebruiken om de bron video te scha kelen. Veel apps bieden de mogelijkheid om bronnen te wisselen, waaronder Telestream Wirecast, Switch Studio (op iOS) en IB Studio (gratis app).

## <a name="content-protection"></a>Inhoudsbeveiliging

### <a name="should-i-use-aes-128-clear-key-encryption-or-a-drm-system"></a>Moet ik AES-128 Clear Key Encryption of een DRM-systeem gebruiken?

Klanten vragen vaak of ze AES-versleuteling of een DRM-systeem moeten gebruiken. Het belangrijkste verschil tussen de twee systemen is dat met AES-versleuteling, de inhouds sleutel wordt via TLS verzonden naar de client, zodat de sleutel tijdens de overdracht wordt versleuteld, maar zonder extra versleuteling (' in de Clear '). Als gevolg hiervan is de sleutel die wordt gebruikt om de inhoud te ontsleutelen toegankelijk voor de client speler en kan deze worden weer gegeven in een netwerk tracering op de client als tekst zonder opmaak. AES-128 Clear Key Encryption is geschikt voor gebruik waarbij de viewer een vertrouwde partij is (bijvoorbeeld het versleutelen van bedrijfs Video's die binnen een bedrijf worden gedistribueerd om door werk nemers te worden weer gegeven).

DRM-systemen zoals PlayReady, Widevine en FairPlay bieden allemaal een extra versleutelings niveau voor de sleutel die wordt gebruikt voor het ontsleutelen van de inhoud, vergeleken met een sleutel met AES-128. De inhouds sleutel wordt versleuteld met een sleutel die wordt beveiligd door de DRM-runtime naast eventuele versleuteling op transport niveau die door TLS wordt verschaft. Daarnaast wordt ontsleuteling afgehandeld in een beveiligde omgeving op het niveau van het besturings systeem, waar het moeilijker is voor een kwaadwillende gebruiker om aan te vallen. U wordt aangeraden DRM te gebruiken voor gevallen waarin de viewer mogelijk geen vertrouwde partij is en u het hoogste beveiligings niveau nodig hebt.

### <a name="how-do-i-show-a-video-to-only-users-who-have-a-specific-permission-without-using-azure-ad"></a>Hoe kan ik een video alleen weer geven voor gebruikers die een specifieke machtiging hebben, zonder Azure AD te gebruiken?

U hoeft geen specifieke token provider te gebruiken, zoals Azure Active Directory (Azure AD). U kunt met behulp van asymmetrische sleutel versleuteling een eigen [JWT](https://jwt.io/) -provider (de zogenaamde Secure token service of STS) maken. In uw aangepaste STS kunt u claims toevoegen op basis van uw bedrijfs logica.

Zorg ervoor dat de verlener, de doel groep en de claims exact overeenkomen met de inhoud van de JWT en de `ContentKeyPolicyRestriction` waarde die wordt gebruikt in `ContentKeyPolicy` .

Zie [uw inhoud beveiligen met Media Services Dynamic Encryption](content-protection-overview.md)(Engelstalig) voor meer informatie.

### <a name="how-and-where-did-i-get-a-jwt-token-before-using-it-to-request-a-license-or-key"></a>Hoe en wanneer krijg ik een JWT-token voordat ik dit gebruik om een licentie of sleutel aan te vragen?

Voor productie moet u een beveiligde token service (dat wil zeggen, een webservice) hebben die een JWT-token op een HTTPS-aanvraag afgeeft. Voor test kunt u de code gebruiken die wordt weer gegeven in de methode die is `GetTokenAsync` gedefinieerd in [programma. cs](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs).

De speler maakt een aanvraag nadat een gebruiker is geverifieerd, op STS voor een dergelijk token en wijst deze toe als de waarde van het token. U kunt de [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/)gebruiken.

Voor een voor beeld van het uitvoeren van een STS met een symmetrische sleutel of een asymmetrische sleutel raadpleegt u het [JWT-hulp programma](https://aka.ms/jwt). Voor een voor beeld van een speler op basis van Azure Media Player met een dergelijk JWT-token raadpleegt u het [Azure media test-hulp programma](https://aka.ms/amtest). (Vouw de **player_settings** koppeling uit om de token invoer te zien.)

### <a name="how-do-i-authorize-requests-to-stream-videos-with-aes-encryption"></a>Hoe kan ik autorisatie aanvragen voor het streamen van Video's met AES-versleuteling?

De juiste aanpak is het gebruik van Secure token service. In STS, afhankelijk van het gebruikers profiel, kunt u verschillende claims toevoegen (zoals ' Premium User ', ' basis gebruiker ', ' gebruiker met gratis proef versie '). Met verschillende claims in een JWT kan de gebruiker andere inhoud zien. Voor andere inhoud of activa, heeft `ContentKeyPolicyRestriction` de bijbehorende `RequiredClaims` waarde.

Gebruik Azure Media Services Api's voor het configureren van de levering van licenties en sleutels en het versleutelen van uw assets (zoals weer gegeven in [dit voor beeld](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs)).

Zie voor meer informatie:

- [Overzicht van inhoudsbeveiliging](content-protection-overview.md)
- [Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer](design-multi-drm-system-with-access-control.md)

### <a name="should-i-use-http-or-https"></a>Moet ik HTTP of HTTPS gebruiken?
De ASP.NET MVC Player-toepassing moet het volgende ondersteunen:

* Gebruikers verificatie via Azure AD, dat zich onder HTTPS bevindt.
* JWT-uitwisseling tussen de client en Azure AD, die zich onder HTTPS bevindt.
* DRM-licentie verwerving door de client, die onder HTTPS moet vallen als licentie levering wordt geleverd door Media Services. De PlayReady-product suite vereist geen HTTPS voor licentie levering. Als uw PlayReady-licentie server zich buiten Media Services bevindt, kunt u HTTP of HTTPS gebruiken.

De ASP.NET Player-toepassing maakt gebruik van HTTPS als een best practice, dus Media Player bevindt zich op een pagina onder HTTPS. HTTP verdient echter de voor keur voor streaming, dus u moet deze problemen overwegen met gemengde inhoud:

* De browser staat geen gemengde inhoud toe. Invoeg toepassingen zoals Silverlight en de OSMF-invoeg toepassing voor Smooth en DASH bieden dit wel. Gemengde inhoud is een beveiligings probleem vanwege de dreiging van de mogelijkheid om schadelijke Java script te injecteren, waardoor klant gegevens risico lopen. Browsers blok keren deze functie standaard. De enige manier om deze te omzeilen, is op de server (oorsprong) naast elkaar door alle domeinen toe te staan (ongeacht HTTPS of HTTP). Dit is waarschijnlijk geen goed idee.
* Vermijd gemengde inhoud. Zowel de toepassing van de speler als de Media Player moet HTTP of HTTPS gebruiken. Wanneer u gemengde inhoud afspeelt, moet de Silverlight-technologie een waarschuwing voor gemengde inhoud wissen. De IT-techniek behandelt gemengde inhoud zonder waarschuwing over gemengde inhoud.
* Als uw streaming-eind punt is gemaakt vóór 2014 augustus, wordt er geen ondersteuning geboden voor HTTPS. In dit geval maakt en gebruikt u een nieuw streaming-eind punt voor HTTPS.

### <a name="what-about-live-streaming"></a>Hoe zit het met live streamen?

U kunt precies hetzelfde ontwerp en dezelfde implementatie gebruiken om live streamen in Media Services te beveiligen door de activa die aan een programma zijn gekoppeld als een VOD-Asset te behandelen. Als u een multi-DRM-beveiliging van de live-inhoud wilt bieden, moet u dezelfde instelling/verwerking Toep assen op de Asset alsof het een VOD-Asset was voordat u de Asset koppelt aan de live uitvoer.

### <a name="what-about-license-servers-outside-media-services"></a>Hoe zit het met licentie servers buiten Media Services?

Klanten hebben vaak geïnvesteerd in een licentie server farm in hun eigen Data Center of op een host die wordt gehost door DRM-service providers. Met Media Services inhouds beveiliging kunt u in de hybride modus uitvoeren. Inhoud kan worden gehost en dynamisch worden beveiligd in Media Services, terwijl DRM-licenties worden geleverd door servers buiten Media Services. In dit geval moet u rekening houden met de volgende wijzigingen:

* STS moet tokens uitgeven die acceptabel zijn en kunnen worden geverifieerd door de licentie server-farm. De Widevine-licentie servers die door Axinom worden verschaft, vereisen bijvoorbeeld een specifieke JWT die een toeslag bericht bevat. U moet een STS hebben om een dergelijke JWT te kunnen uitgeven. 
* U hoeft de service voor het leveren van licenties niet meer te configureren in Media Services. U moet de Url's voor de aangeschafte licenties (voor PlayReady, Widevine en FairPlay) opgeven wanneer u configureert `ContentKeyPolicy` .

> [!NOTE]
> Widevine is een service van Google en is onderworpen aan de service voorwaarden en het privacybeleid van Google.

## <a name="media-services-v2-vs-v3"></a>Media Services v2 versus v3 

### <a name="can-i-use-the-azure-portal-to-manage-v3-resources"></a>Kan ik de Azure Portal gebruiken om v3-resources te beheren?

Op dit moment kunt u het [Azure Portal](https://portal.azure.com/) gebruiken voor het volgende:

* [Live Events](live-events-outputs-concept.md) beheren in Media Services v3. 
* V3- [assets](assets-concept.md)weer geven (niet beheren). 
* [Krijg informatie over het openen van api's](./access-api-howto.md). 

Gebruik voor alle andere beheer taken (bijvoorbeeld [trans formaties en taken](transforms-jobs-concept.md) en [inhouds beveiliging](content-protection-overview.md)) de [rest API](/rest/api/media/), de [Azure cli](/cli/azure/ams)of een van de ondersteunde [sdk's](media-services-apis-overview.md#sdks).

### <a name="is-there-an-assetfile-concept-in-v3"></a>Is er een AssetFile-concept in v3?

Het `AssetFile` concept is uit de Media Services-API verwijderd om Media Services van de afhankelijkheid van de opslag-SDK te scheiden. De gegevens die horen bij de opslag-SDK, worden nu Azure Storage, niet Media Services. 

Zie [Migrate to Media Services v3](migrate-v-2-v-3-migration-introduction.md)(Engelstalig) voor meer informatie.

### <a name="where-did-client-side-storage-encryption-go"></a>Waar is de opslag versleuteling aan de client zijde gebleven?

U wordt aangeraden om opslag versleuteling aan de server zijde te gebruiken (deze is standaard ingeschakeld). Zie [Azure Storage-service versleuteling voor Data-at-rest](../../storage/common/storage-service-encryption.md)voor meer informatie.

## <a name="offline-streaming"></a>Offline streaming

### <a name="fairplay-streaming-for-ios"></a>FairPlay streaming voor iOS

De volgende veelgestelde vragen bieden hulp bij het oplossen van problemen met offline FairPlay streaming voor iOS.

#### <a name="why-does-only-audio-play-but-not-video-during-offline-mode"></a>Waarom wordt audio alleen afgespeeld maar niet video tijdens de offline modus?

Dit gedrag lijkt inherent aan het ontwerp van de voor beeld-app. Wanneer er een alternatief audio spoor aanwezig is (dit is het geval voor HLS) tijdens de offline modus, wordt de standaard instelling voor het alternatieve audio spoor door iOS 10 en iOS 11 ingesteld. Als u dit gedrag voor de offline modus van FPS wilt compenseren, verwijdert u het alternatieve audio spoor uit de stroom. Als u dit wilt doen op Media Services, voegt u het dynamische manifest filter toe **Audio-only = False**. Met andere woorden, een HLS-URL eindigt op **. ISM/manifest (Format = M3U8-AAPL, alleen audio = false)**. 

#### <a name="why-does-it-still-play-audio-only-without-video-during-offline-mode-after-i-add-audio-onlyfalse"></a>Waarom wordt alleen audio zonder video afgespeeld tijdens de offline modus nadat ik alleen audio heb toegevoegd = False?

Afhankelijk van het cache sleutel ontwerp voor het Content Delivery Network, kan de inhoud in de cache worden opgeslagen. Verwijder de cache.

#### <a name="is-fps-offline-mode-supported-on-ios-11-in-addition-to-ios-10"></a>Wordt de offline modus voor FPS ondersteund op iOS 11 naast iOS 10?

Ja. De offline modus van FPS wordt ondersteund voor iOS 10 en iOS 11.

#### <a name="why-cant-i-find-the-document-offline-playback-with-fairplay-streaming-and-http-live-streaming-in-the-fps-server-sdk"></a>Waarom kan ik het document ' offline afspelen met FairPlay streaming en HTTP Live Streaming ' niet vinden in de server-SDK van FPS?

Sinds FPS Server SDK versie 4 werd dit document samengevoegd in de programmeer handleiding voor FairPlay-gegevens stromen.

#### <a name="what-is-the-downloadedoffline-file-structure-on-ios-devices"></a>Wat is de gedownloade/offline bestands structuur op iOS-apparaten?

De gedownloade bestands structuur op een iOS-apparaat ziet eruit als in de volgende scherm afbeelding. De `_keys` map slaat de gedownloade fps-licenties op, met één archief bestand voor elke host van de licentie service. De `.movpkg` map slaat audio-en video-inhoud op. 

De eerste map met een naam die eindigt op een streepje gevolgd door een getal bevat video-inhoud. De numerieke waarde is de piek bandbreedte van de video weergaven. De tweede map met een naam die eindigt op een streepje gevolgd door 0, bevat audio-inhoud. De derde map met de naam `Data` bevat de hoofd-afspeel lijst van de fps-inhoud. Ten slotte biedt boot.xml een volledige beschrijving van de `.movpkg` inhoud van de map. 

![Structuur van offline bestanden voor de FairPlay iOS-voor beeld-app](media/offline-fairplay-for-ios/offline-fairplay-file-structure.png)

Hier volgt een voor beeld van een boot.xml bestand:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<HLSMoviePackage xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://apple.com/IMG/Schemas/HLSMoviePackage" xsi:schemaLocation="http://apple.com/IMG/Schemas/HLSMoviePackage /System/Library/Schemas/HLSMoviePackage.xsd">
  <Version>1.0</Version>
  <HLSMoviePackageType>PersistedStore</HLSMoviePackageType>
  <Streams>
    <Stream ID="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" Path="1-4DTFY3A3VDRCNZ53YZ3RJ2NPG2AJHNBD-0" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(127000)/Manifest(aac_eng_2_127,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
    <Stream ID="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" Path="0-HC6H5GWC5IU62P4VHE7NWNGO2SZGPKUJ-310656" NetworkURL="https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/QualityLevels(161000)/Manifest(video,format=m3u8-aapl)">
      <Complete>YES</Complete>
    </Stream>
  </Streams>
  <MasterPlaylist>
    <NetworkURL>https://willzhanmswest.streaming.mediaservices.windows.net/e7c76dbb-8e38-44b3-be8c-5c78890c4bb4/MicrosoftElite01.ism/manifest(format=m3u8-aapl,audio-only=false)</NetworkURL>
  </MasterPlaylist>
  <DataItems Directory="Data">
    <DataItem>
      <ID>CB50F631-8227-477A-BCEC-365BBF12BCC0</ID>
      <Category>Playlist</Category>
      <Name>master.m3u8</Name>
      <DataPath>Playlist-master.m3u8-CB50F631-8227-477A-BCEC-365BBF12BCC0.data</DataPath>
      <Role>Master</Role>
    </DataItem>
  </DataItems>
</HLSMoviePackage>
```

### <a name="widevine-streaming-for-android"></a>Widevine streaming voor Android

#### <a name="how-can-i-deliver-persistent-licenses-offline-enabled-for-some-clientsusers-and-non-persistent-licenses-offline-disabled-for-others-do-i-have-to-duplicate-the-content-and-use-separate-content-keys"></a>Hoe kan ik permanente licenties leveren (offline ingeschakeld) voor sommige clients/gebruikers en niet-permanente licenties (offline uitgeschakeld) voor anderen? Moet ik de inhoud dupliceren en afzonderlijke inhouds sleutels gebruiken?

Omdat Media Services v3 een Asset toestaat meerdere instanties te hebben `StreamingLocator` , kunt u het volgende hebben:

* Een `ContentKeyPolicy` exemplaar met `license_type = "persistent"` , `ContentKeyPolicyRestriction` met een claim over `"persistent"` en de `StreamingLocator` .
* Een ander `ContentKeyPolicy` exemplaar met `license_type="nonpersistent"` , met een `ContentKeyPolicyRestriction` claim over `"nonpersistent` , en de `StreamingLocator` .
* Twee `StreamingLocator` exemplaren met verschillende `ContentKey` waarden.

Afhankelijk van de bedrijfs logica van een aangepaste STS, worden er verschillende claims uitgegeven in het JWT-token. Met het token kan alleen de bijbehorende licentie worden verkregen en alleen de bijbehorende URL kan worden afgespeeld.

#### <a name="what-is-the-mapping-between-the-widevine-and-media-services-drm-security-levels"></a>Wat is de toewijzing tussen de Widevine en Media Services DRM-beveiligings niveaus?

In het Widevine DRM-architectuur overzicht van Google worden drie beveiligings niveaus gedefinieerd. De Azure Media Services- [documentatie op de Widevine-licentie sjabloon](widevine-license-template-overview.md) bevat echter een overzicht van vijf beveiligings niveaus (vereisten voor client robuustheid voor afspelen). In deze sectie wordt uitgelegd hoe de beveiligings niveaus worden toegewezen.

Beide sets beveiligings niveaus worden gedefinieerd door Google Widevine. Het verschil bevindt zich in het gebruiks niveau: architectuur of API. De vijf beveiligings niveaus worden gebruikt in de Widevine-API. Het `content_key_specs` object, dat bevat `security_level` , wordt gedeserialiseerd en door gegeven aan de Widevine Global Delivery Service van de Azure Media Services Widevine-licentie service. In de volgende tabel ziet u de toewijzing tussen de twee sets beveiligings niveaus.

| **Beveiligings niveaus die zijn gedefinieerd in de Widevine-architectuur** |**Beveiligings niveaus die worden gebruikt in de Widevine-API**|
|---|---| 
| **Beveiligings niveau 1**: alle verwerking van inhoud, crypto grafie en beheer worden uitgevoerd binnen de Trusted Execution Environment (Tee). In sommige implementatie modellen kan beveiligings verwerking op verschillende chips worden uitgevoerd.|**security_level = 5**: de crypto grafie, het decoderen en alle verwerking van de media (gecomprimeerd en niet-gecomprimeerd) moeten worden afgehandeld in een tee-apparaat.<br/><br/>**security_level = 4**: de crypto grafie en de code ring van inhoud moet worden uitgevoerd binnen een Tee met door hardware opgeslagen.|
**Beveiligings niveau 2**: crypto grafie (maar geen video verwerking) wordt uitgevoerd in het Tee. Ontsleutelde buffers worden geretourneerd naar het toepassings domein en verwerkt via afzonderlijke video-hardware of-software. Op niveau 2 wordt echter alleen cryptografische informatie verwerkt in het TEE.| **security_level = 3**: het sleutel materiaal en de cryptografische bewerkingen moeten worden uitgevoerd binnen een Tee met een back-up. |
| **Beveiligings niveau 3**: er is geen tee op het apparaat. Passende maat regelen kunnen worden genomen om de cryptografische informatie en ontsleutelde inhoud op het hostbesturingssysteem te beveiligen. Een level 3-implementatie kan ook een hardware Cryptographic engine bevatten, maar die alleen de prestaties verbetert, niet op beveiliging. | **security_level = 2**: software-crypto en een verborgen decoder zijn vereist.<br/><br/>**security_level = 1**: op software gebaseerde witbalans is crypto grafie vereist.|

#### <a name="why-does-content-download-take-so-long"></a>Waarom kan het downloaden van inhoud zo lang duren?

Er zijn twee manieren om de download snelheid te verbeteren:

* Een netwerk voor content levering inschakelen, zodat gebruikers waarschijnlijk meer kunnen aankomen dan het oorspronkelijke/streaming-eind punt voor het downloaden van inhoud. Als een gebruiker een streaming-eind punt heeft, wordt elk HLS segment of koppel teken dynamisch verpakt en versleuteld. Hoewel deze latentie in milliseconden voor elk segment of fragment wordt geschaald, kan de verzamelde latentie groot zijn en een langere down load veroorzaken, wanneer u een uur-lange video hebt.
* Geef gebruikers de mogelijkheid om de video kwaliteits lagen en audio tracks selectief te downloaden in plaats van alle inhoud. Voor de offline modus is er geen punt om alle kwaliteits lagen te downloaden. Er zijn twee manieren om dit te doen:

  * Door client beheerd: de app voor de speler selecteert automatisch, of de gebruiker selecteert, de laag met de video kwaliteit en de audio nummers die moeten worden gedownload.
  * Beheerde service: u kunt de functie voor dynamische manifesten in Azure Media Services gebruiken om een (globaal) filter te maken, waarmee de HLS-afspeel lijst of-STREEPJES-MPD wordt beperkt tot één video kwaliteit en geselecteerde audio sporen. De download-URL die aan gebruikers wordt gepresenteerd, omvat dit filter.

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Media Services v3](media-services-overview.md)