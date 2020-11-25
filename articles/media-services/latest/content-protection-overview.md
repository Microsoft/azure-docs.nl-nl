---
title: Bescherm uw inhoud met Media Services v3 dynamische versleuteling
titleSuffix: Azure Media Services
description: Meer informatie over beveiliging van inhoud met dynamische versleuteling, streaming-protocollen en versleutelings typen in Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5d6530cf7b8d8611ff23a3517112cb0aa7442d6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971160"
---
# <a name="protect-your-content-with-media-services-dynamic-encryption"></a>Uw inhoud beveiligen met Media Services dynamische versleuteling

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Met Azure Media Services kunt u uw media beveiligen vanaf het moment dat deze uw computer verlaten en ze worden opgeslagen, verwerkt en afgeleverd. Met Media Services kunt u uw live en on-demand inhoud dynamisch versleutelen met Advanced Encryption Standard (AES-128) of een van de drie belangrijkste Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine en Apple FairPlay. Media Services biedt ook een service voor het leveren van AES-sleutels en DRM-licenties (PlayReady, Widevine en FairPlay) aan geautoriseerde klanten. Als inhoud is versleuteld met een AES-sleutel en via HTTPS wordt verzonden, wordt deze niet duidelijk totdat de client wordt bereikt.

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

In Media Services v3 is een inhouds sleutel gekoppeld aan streaming-Locator (Zie [dit voor beeld](protect-with-aes128.md)). Als u de Media Services key delivery-service gebruikt, kunt u Azure Media Services de inhouds sleutel voor u laten genereren. De inhouds sleutel moet zelf worden gegenereerd als u de service voor de levering van sleutels gebruikt, of als u een scenario met een hoge Beschik baarheid wilt afhandelen, waarbij u dezelfde inhouds sleutel moet hebben in twee data centers.

Wanneer een stream wordt aangevraagd door een speler, gebruikt Media Services de opgegeven sleutel om uw inhoud dynamisch te versleutelen met AES clear key of DRM-versleuteling. Voor het ontsleutelen van de stream vraagt de speler de sleutel aan bij Media Services-sleutelleveringsservice of de sleutelleveringsservice die u hebt opgegeven. Om te bepalen of de gebruiker gemachtigd is om de sleutel op te halen, evalueert de service het beleid voor de inhouds sleutel die u hebt opgegeven voor de sleutel.

U kunt de REST API of een Media Services-client bibliotheek gebruiken om autorisatie-en verificatie beleid te configureren voor uw licenties en sleutels.

In de volgende afbeelding ziet u de werk stroom voor Media Services inhouds beveiliging:

![Werk stroom voor Media Services beveiliging van inhoud](./media/content-protection/content-protection.svg)
  
&#42; *Dynamic Encryption ondersteunt AES-128 Clear Key, CBCS en CENC. Zie de [ondersteunings matrix](#streaming-protocols-and-encryption-types)voor meer informatie.*

In dit artikel worden de concepten en terminologie beschreven die u helpen de beveiliging van inhoud met Media Services te begrijpen.

## <a name="main-components-of-a-content-protection-system"></a>Belangrijkste onderdelen van een systeem voor beveiliging van inhoud

Als u uw inhouds beveiligingssysteem wilt volt ooien, moet u het bereik van de inspanningen volledig begrijpen. De volgende secties geven een overzicht van de drie onderdelen die u moet implementeren.

> [!NOTE]
> We raden u ten zeerste aan om elk deel in de volgende secties te richten en volledig te testen voordat u verdergaat met het volgende gedeelte. Als u uw inhouds beveiligingssysteem wilt testen, gebruikt u de hulpprogram ma's die in de secties zijn opgegeven.

### <a name="media-services-code"></a>Media Services code
  
Het [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) -voor beeld laat zien hoe u een multi-DRM-systeem met Media Services v3 implementeert met behulp van .net. Ook wordt uitgelegd hoe u de Media Services licentie/key delivery service kunt gebruiken.
  
U kunt elke asset met meerdere versleutelingstypen versleutelen (AES-128, PlayReady, Widevine, FairPlay). Zie [streaming protocollen en versleutelings typen](#streaming-protocols-and-encryption-types)om te zien wat zinvol is om te combi neren.

Het voor beeld laat zien hoe u:

1. Een [beleid voor inhouds sleutels](content-key-policy-concept.md)maken en configureren.

   U maakt een inhouds sleutel beleid om te configureren hoe de inhouds sleutel (die beveiligde toegang tot uw assets biedt) wordt geleverd aan eind clients:  

   * Geef de licentie leverings autorisatie op. Geef de logica van de autorisatie controle op op basis van claims in JSON Web Token (JWT).
   * [PlayReady](playready-license-template-overview.md)-, [Widevine](widevine-license-template-overview.md)-en/of [Fairplay](fairplay-license-overview.md) -licenties configureren. Met de sjablonen kunt u rechten en machtigingen voor elk van de DRMs configureren.

     ```
     ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
     ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
     ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
     ```

2. Maak een [streaming-Locator](streaming-locators-concept.md) die is geconfigureerd voor het streamen van het versleutelde activum.
  
   De streaming-Locator moet worden gekoppeld aan een [streaming-beleid](streaming-policy-concept.md). In het voor beeld is `StreamingLocator.StreamingPolicyName` het beleid ' Predefined_MultiDrmCencStreaming ' ingesteld.

   De PlayReady-en Widevine-versleuteling worden toegepast en de sleutel wordt aan de client voor afspelen geleverd op basis van de geconfigureerde DRM-licenties. Als u uw stroom ook wilt versleutelen met CBCS (FairPlay), gebruikt u het beleid ' Predefined_MultiDrmStreaming '.

   De streaming-Locator is ook gekoppeld aan het inhouds sleutel beleid dat u hebt gedefinieerd.

3. Maak een test token.

   De- `GetTokenAsync` methode laat zien hoe u een test token maakt.
4. De streaming-URL bouwen.

   De- `GetDASHStreamingUrlAsync` methode laat zien hoe u de streaming-URL bouwt. In dit geval wordt de streep inhoud door de URL gestreamd.

### <a name="player-with-an-aes-or-drm-client"></a>Speler met een AES-of DRM-client

Een video speler-app op basis van een speler (native of op basis van een browser) moet voldoen aan de volgende vereisten:

* De Player SDK ondersteunt de benodigde DRM-clients.
* De Player SDK ondersteunt de vereiste streaming protocollen: Smooth, DASH en/of HTTP Live Streaming (HLS).
* De SDK van de Player kan verwerken van een JWT-token in een aanvraag voor het ophalen van licenties.

U kunt een speler maken met behulp van de [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/). Gebruik de [Azure Media Player PROTECTIONINFO API](https://amp.azure.net/libs/amp/latest/docs/) om op te geven welke DRM-technologie op verschillende DRM-platforms moet worden gebruikt.

Voor het testen van AES-of CENC (Widevine en/of PlayReady) versleutelde inhoud kunt u [Azure Media Player](https://aka.ms/azuremediaplayer)gebruiken. Zorg ervoor dat u **Geavanceerde opties** selecteert en controleer uw versleutelings opties.

Als u FairPlay versleutelde inhoud wilt testen, gebruikt u [deze test speler](https://aka.ms/amtest). De speler ondersteunt Widevine, PlayReady en FairPlay DRMs, samen met AES-128 Clear Key encryption.

Kies de juiste browser om verschillende DRMs te testen:

* Chrome, Opera of Firefox voor Widevine.
* Micro soft Edge of Internet Explorer 11 voor PlayReady.
* Safari op macOS voor FairPlay.

### <a name="security-token-service"></a>Beveiligings token service

Een STS (Security Token Service) heeft JWT als het toegangs token voor back-end-toegang tot bronnen. U kunt de Azure Media Services licentie/key delivery-service gebruiken als de back-end-resource. Een STS moet de volgende dingen definiëren:

* Verlener en doel groep (of bereik).
* Claims, die afhankelijk zijn van de bedrijfs vereisten in Content Protection.
* Symmetrische of asymmetrische verificatie voor handtekening verificatie.
* Ondersteuning voor sleutel rollover (indien nodig).

U kunt [Dit hulp programma STS](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) gebruiken om de STS te testen. Het ondersteunt alle drie typen verificatie sleutels: symmetrisch, asymmetrisch of Azure Active Directory (Azure AD) met sleutel rollover.

## <a name="streaming-protocols-and-encryption-types"></a>Protocollen voor streaming en versleutelingstypen

U kunt Media Services gebruiken om uw inhoud dynamisch te versleutelen met AES Clear Key of DRM-versleuteling door gebruik te maken van PlayReady, Widevine of FairPlay. Op dit moment kunt u de indelingen HLS, MPEG DASH en Smooth Streaming versleutelen. Elk protocol ondersteunt de volgende versleutelings methoden.

### <a name="hls"></a>HLS

Het HLS-protocol ondersteunt de volgende container indelingen en versleutelings schema's:

|Container indeling|Versleutelings schema|Voor beeld van URL|
|---|---|---|
|Alles|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (inclusief HEVC/H. 265) wordt ondersteund op de volgende apparaten:

* iOS 11 of hoger.
* iPhone 8 of hoger.
* macOS High Sierra met Intel 7 Generation CPU.

### <a name="mpeg-dash"></a>MPEG-DASH

Het MPEG-DASH-protocol ondersteunt de volgende container indelingen en versleutelings schema's:

|Container indeling|Versleutelings schema|URL-voor beelden
|---|---|---|
|Alles|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|KVP (fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Het Smooth Streaming-Protocol ondersteunt de volgende container indelingen en versleutelings schema's.

|Protocol|Container indeling|Versleutelings schema|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|
|fMP4 | PIFF 1,1 (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> Ondersteuning voor PIFF 1,1 wordt geboden als een achterwaarts compatibele oplossing voor slimme TV (Samsung, LG) waarmee de vroege versie van Silverlight van Common Encryption is geïmplementeerd. U wordt aangeraden alleen de PIFF-indeling te gebruiken waar dat nodig is voor ondersteuning van legacey Samsung of LG Smart Tv's die worden verzonden tussen 2009-2015 en die de PIFF 1,1-versie van PlayReady-versleuteling ondersteunen. 

### <a name="browsers"></a>Browsers

Algemene browsers ondersteunen de volgende DRM-clients:

|Browser|Versleuteling|
|---|---|
|Chrome|Widevine|
|Micro soft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="controlling-content-access"></a>Toegang tot inhoud beheren

U kunt bepalen wie toegang heeft tot uw inhoud door het beleid voor inhouds sleutels te configureren. Media Services ondersteunt meerdere manieren om gebruikers te autoriseren die sleutels aanvragen. De-client (Player) moet voldoen aan het beleid voordat de sleutel aan de client kan worden geleverd. Het beleid voor inhouds sleutels kan *Open* of *token* beperking hebben.

Een open-beperkt beleid voor inhouds sleutels kan worden gebruikt wanneer u een licentie wilt verlenen aan iemand zonder autorisatie. Als uw omzet bijvoorbeeld op ad of op basis van een abonnement is.  

Met een token-beperkt beleid voor inhouds sleutels wordt de inhouds sleutel alleen verzonden naar een client die een geldige JWT-token of een eenvoudige webtoken (SWT) in de licentie/sleutel aanvraag levert. Dit token moet worden uitgegeven door een STS.

U kunt Azure AD als STS gebruiken of een [aangepaste STS](#using-a-custom-sts)implementeren. De STS moet worden geconfigureerd om een token te maken dat is ondertekend met de opgegeven sleutel en claims uitgeven die u hebt opgegeven in de configuratie van de token beperking. De Media Services licentie/key delivery service retourneert de aangevraagde licentie of sleutel bij de client als aan beide volgende voor waarden wordt voldaan:

* Het token is geldig.
* De claims in het token komen overeen met die zijn geconfigureerd voor de licentie of sleutel.

Wanneer u het token beperkt beleid configureert, moet u de para meters voor de primaire verificatie sleutel, verlener en doel groep opgeven. De primaire verificatie sleutel bevat de sleutel waarmee het token is ondertekend. De uitgever is de STS die het token uitgeeft. De doel groep, ook wel bereik genoemd, beschrijft de bedoeling van het token of de bron waartoe het token toegang verleent. De Media Services licentie/key delivery-service controleert of deze waarden in het token overeenkomen met de waarden in de sjabloon.

### <a name="token-replay-prevention"></a>Token replay voor komen

Met de functie voor het voor *komen van tokens* kunnen Media Services klanten een limiet instellen voor het aantal keren dat hetzelfde token kan worden gebruikt om een sleutel of licentie aan te vragen. De klant kan een claim van het type `urn:microsoft:azure:mediaservices:maxuses` in het token toevoegen, waarbij de waarde het aantal keren is dat het token kan worden gebruikt om een licentie of sleutel te verkrijgen. Bij alle volgende aanvragen met dezelfde token voor de sleutel levering wordt een niet-geautoriseerde reactie geretourneerd. Zie hoe u de claim toevoegt in het [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs#L601)-voor beeld.
 
#### <a name="considerations"></a>Overwegingen

* Klanten moeten controle hebben over het genereren van tokens. De claim moet in het token zelf worden geplaatst.
* Wanneer u deze functie gebruikt, worden aanvragen met tokens waarvan de verval tijd meer dan één uur duurt, verwijderd met een niet-geautoriseerde reactie.
* Tokens worden uniek geïdentificeerd door hun hand tekening. Elke wijziging in de payload (bijvoorbeeld bijwerken naar de verloop tijd of de claim) wijzigt de hand tekening van het token en telt als een nieuw token dat de levering van sleutels nog niet eerder is.
* Afspelen mislukt als het token de `maxuses` waarde die is ingesteld door de klant heeft overschreden.
* Deze functie kan worden gebruikt voor alle bestaande beveiligde inhoud (alleen het token dat is uitgegeven moet worden gewijzigd).
* Deze functie werkt met zowel de JWT-als de SWT.

## <a name="using-a-custom-sts"></a>Een aangepaste STS gebruiken

Een klant kan ervoor kiezen om een aangepaste STS te gebruiken om tokens op te geven. Redenen zijn onder andere:

* De ID-provider (IDP) die wordt gebruikt door de klant, biedt geen ondersteuning voor STS. In dit geval kan een aangepaste STS een optie zijn.
* De klant heeft mogelijk meer flexibeler of nauw keurigere controle over het integreren van STS met het facturerings systeem voor abonnees van de klant.

   Een [Ott](https://en.wikipedia.org/wiki/Over-the-top_media_services) -service operator kan bijvoorbeeld meerdere abonnements pakketten aanbieden, zoals Premium, Basic en sport. De operator zou mogelijk moeten overeenkomen met de claims in een token met het pakket van een abonnee, zodat alleen de inhoud van een specifiek pakket beschikbaar wordt gemaakt. In dit geval biedt een aangepaste STS de benodigde flexibiliteit en controle.

* Om aangepaste claims in het token op te nemen om te selecteren tussen verschillende ContentKeyPolicyOptions met verschillende DRM-licentie parameters (een abonnements licentie versus een huur licentie).
* Om een claim op te geven met de id van de inhouds sleutel van de sleutel waartoe het token toegang verleent.

Wanneer u een aangepaste STS gebruikt, moeten er twee wijzigingen worden aangebracht:

* Wanneer u de service voor het leveren van licenties voor een Asset configureert, moet u de beveiligings sleutel opgeven die wordt gebruikt voor verificatie door de aangepaste STS in plaats van de huidige sleutel van Azure AD.
* Wanneer er een JTW-token wordt gegenereerd, wordt een beveiligings sleutel opgegeven in plaats van de persoonlijke sleutel van het huidige x509-certificaat in azure AD.

Er zijn twee soorten beveiligings sleutels:

* Symmetrische sleutel: dezelfde sleutel wordt gebruikt om een JWT te genereren en te verifiëren.
* Asymmetrische sleutel: een combi natie van open bare en persoonlijke sleutels in een x509-certificaat wordt gebruikt met een persoonlijke sleutel voor het versleutelen/genereren van een JWT en met de open bare sleutel om het token te verifiëren.

Als u .NET Framework/C# als uw ontwikkel platform gebruikt, moet het x509-certificaat dat wordt gebruikt voor een asymmetrische beveiligings sleutel, een sleutel lengte hebben van ten minste 2048. Deze sleutel lengte is een vereiste van de klasse System. Identity model. tokens. X509AsymmetricSecurityKey in .NET Framework. Anders wordt de volgende uitzonde ring gegenereerd: IDX10630: de ' System. Identity model. tokens. X509AsymmetricSecurityKey ' voor ondertekening mag niet kleiner zijn dan ' 2048 ' bits.

## <a name="custom-key-and-license-acquisition-url"></a>Aangepaste sleutel en licentie voor aanschaf-URL

Gebruik de volgende sjablonen als u een andere licentie/key delivery service wilt opgeven (niet Media Services). De twee Vervang bare velden in de sjablonen zijn daar, zodat u uw streaming-beleid kunt delen in veel assets in plaats van een streaming-beleid per activum te maken. 

* `EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate`: De sjabloon voor de URL van de aangepaste service die sleutels levert aan spelers van eind gebruikers. Het is niet vereist wanneer u Azure Media Services gebruikt voor het uitgeven van sleutels. 

   De sjabloon ondersteunt vervangbaar tokens die door de service worden bijgewerkt tijdens runtime met de waarde die specifiek is voor de aanvraag.  De momenteel ondersteunde token waarden zijn:
   * `{AlternativeMediaId}`, die wordt vervangen door de waarde van StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, die wordt vervangen door de waarde van de id van de aangevraagde sleutel.
* `StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate`: De sjabloon voor de URL van de aangepaste service die licenties levert aan deel nemers aan eind gebruikers. Het is niet vereist wanneer u Azure Media Services gebruikt voor het uitgeven van licenties.

   De sjabloon ondersteunt vervangbaar tokens die door de service worden bijgewerkt tijdens runtime met de waarde die specifiek is voor de aanvraag. De momenteel ondersteunde token waarden zijn:  
   * `{AlternativeMediaId}`, die wordt vervangen door de waarde van StreamingLocatorId. AlternativeMediaId.
   * `{ContentKeyId}`, die wordt vervangen door de waarde van de id van de aangevraagde sleutel. 
* `StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate`: Hetzelfde als de vorige sjabloon, alleen voor Widevine. 
* `StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate`: Hetzelfde als de vorige sjabloon, alleen voor FairPlay.  

Bijvoorbeeld:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

`ContentKeyId` heeft een waarde van de aangevraagde sleutel. U kunt gebruiken `AlternativeMediaId` Als u de aanvraag aan een entiteit aan uw zijde wilt toewijzen. `AlternativeMediaId`Kan bijvoorbeeld worden gebruikt om u te helpen bij het opzoeken van machtigingen.

Zie [streaming-beleid-maken](/rest/api/media/streamingpolicies/create)voor rest-voor beelden die gebruikmaken van url's voor aangepaste licentie/sleutel overname.

> [!NOTE]
> Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="troubleshoot"></a>Problemen oplossen

Als u de `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` fout melding krijgt, moet u ervoor zorgen dat u het juiste streaming-beleid opgeeft.

Als er fouten optreden die eindigen op `_NOT_SPECIFIED_IN_URL` , zorg er dan voor dat u de versleutelings indeling opgeeft in de URL. Een voorbeeld is `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Zie [streaming protocollen en versleutelings typen](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Vragen stellen, feedback geven, updates ophalen

Ga naar het artikel van de [Azure Media Services-community](media-services-community.md) voor verschillende manieren om vragen te stellen, feedback te geven en updates voor Media Services op te halen.

## <a name="next-steps"></a>Volgende stappen

* [Beveiligen met AES-versleuteling](protect-with-aes128.md)
* [Beschermen met DRM](protect-with-drm.md)
* [Multi-DRM-inhouds beschermings systeem ontwerpen met toegangs beheer](design-multi-drm-system-with-access-control.md)
* [Versleuteling van opslag side](storage-account-concept.md#storage-side-encryption)
* [Veelgestelde vragen](frequently-asked-questions.md)
* [JSON Web Token-handler](/dotnet/framework/security/json-web-token-handler)
