---
title: Een multi-DRM-inhouds beschermings systeem
description: In deze artikelen vindt u een gedetailleerde beschrijving van het ontwerpen van een multi-DRM-inhouds beschermings systeem met Azure Media Services.
services: media-services
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: seodec18
ms.openlocfilehash: 48ae7b2356f7313acbfb298b7a492a0482e3106b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068129"
---
# <a name="design-of-a-multi-drm-content-protection-system-with-access-control"></a>Ontwerp van een inhoudsbeveiligingssysteem van een multi-DRM met toegangsbeheer

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Het ontwerpen en bouwen van een Digital Rights Management (DRM)-subsysteem voor een over-the-top (OTT) of online streaming-oplossing is een complexe taak. Opera tors/online video providers doen deze taak doorgaans door aan gespecialiseerde DRM-service providers. Het doel van dit document is om een referentie-ontwerp en een referentie-implementatie van een end-to-end DRM-subsysteem in een OTT of online streaming-oplossing te presen teren.

De doel lezers voor dit document zijn technici die werken in DRM-subsystemen van OTT of online streaming/multiscreen-oplossingen of lezers die geïnteresseerd zijn in DRM-subsystemen. De veronderstelling is dat lezers bekend zijn met ten minste één van de DRM-technologieën op de markt, zoals PlayReady, Widevine, FairPlay of Adobe Access.

In deze bespreking bevatten we multi-DRM de drie DRMs die worden ondersteund door Azure Media Services: Common Encryption (CENC) voor PlayReady en Widevine, FairPlay en AES-128 Clear Key encryption. Een belang rijke trend in online streaming en de OTT-industrie is om systeem eigen DRMs te gebruiken op verschillende client platforms. Deze trend is een verschuiving van de vorige die één DRM en de client-SDK heeft gebruikt voor verschillende client platforms. Wanneer u CENC gebruikt met multi-native DRM, worden zowel PlayReady als Widevine versleuteld volgens de specificatie van de [common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

De voor delen van het gebruik van systeem eigen multi-DRM voor inhouds beveiliging zijn:

* Verlaagt de versleutelings kosten omdat één versleutelings proces wordt gebruikt om verschillende platforms te richten met de eigen DRMs.
* Vermindert de kosten voor het beheren van assets omdat er slechts één exemplaar van de Asset nodig is in de opslag.
* Elimineert de kosten voor DRM-client licenties omdat de systeem eigen DRM-client doorgaans vrij is op het systeem eigen platform.

### <a name="goals-of-the-article"></a>Doel stellingen van het artikel

De doel stellingen van dit artikel zijn:

* Geef een referentie ontwerp op van een DRM-subsysteem dat gebruikmaakt van alle drie DRMs (CENC for DASH, FairPlay voor HLS en PlayReady voor smooth streaming).
* Geef een referentie-implementatie op in Azure en Azure Media Services platform.
* Bespreek enkele onderwerpen over ontwerpen en implementeren.

De volgende tabel bevat een overzicht van systeem eigen DRM-ondersteuning op verschillende platforms en EME-ondersteuning in verschillende browsers.

| **Client platform** | **Systeem eigen DRM** | **EME** |
| --- | --- | --- |
| **Smart Tv's, STBs** | PlayReady, Widevine en/of andere | Embedded browser/EME voor PlayReady en/of Widevine|
| **Windows 10** | PlayReady | Micro soft Edge/IE11 voor PlayReady|
| **Android-apparaten (telefoon, Tablet, TV)** |Widevine |Chrome voor Widevine |
| **iOS** | FairPlay | Safari voor FairPlay (sinds iOS 11,2) |
| **MacOS** | FairPlay | Safari voor FairPlay (sinds Safari 9 + op Mac OS X 10.11 + El Capitan)|
| **tvOS** | FairPlay | |

Als u de huidige status van de implementatie voor elke DRM overweegt, wil een service doorgaans twee of drie DRMs implementeren om ervoor te zorgen dat u alle typen eind punten op de beste manier adresseert.

Er is een verhouding tussen de complexiteit van de service logica en de complexiteit aan de client zijde om een bepaald niveau van gebruikers ervaring op de verschillende clients te bereiken.

Denk aan het volgende om uw keuze te maken:

* PlayReady is systeem eigen geïmplementeerd in elk Windows-apparaat, op sommige Android-apparaten en beschikbaar via software-Sdk's op vrijwel elk platform.
* Widevine wordt systeem eigen geïmplementeerd in elk Android-apparaat, in Chrome en op andere apparaten. Widevine wordt ook ondersteund in Firefox en Opera-browsers via een streepje.
* FairPlay is beschikbaar op iOS, macOS en tvOS.

## <a name="a-reference-design"></a>Een referentie ontwerp

Deze sectie bevat een referentie ontwerp dat neutraal is voor de technologieën die worden gebruikt om het te implementeren.

Een DRM-subsysteem kan de volgende onderdelen bevatten:

* Sleutelbeheer
* DRM-versleutelings pakket
* Levering van DRM-licentie
* Controle of toegangs beheer recht
* Gebruikers verificatie/-autorisatie
* Player-app
* Herkomst/Content Delivery Network (CDN)

In het volgende diagram ziet u de interactie op hoog niveau tussen de onderdelen in een DRM-subsysteem:

![DRM-subsysteem met CENC](./media/architecture-design-multi-drm-system/media-services-generic-drm-subsystem-with-cenc.png)

Het ontwerp heeft drie basis lagen:

* Een back-uplaag (zwart) wordt niet extern weer gegeven.
* Een DMZ-laag (donker blauw) bevat alle eind punten die het publiek belichten.
* Een open bare Internet-laag (licht blauw) bevat de CDN en spelers met verkeer via het open bare Internet.

Er moet ook een hulp programma voor inhouds beheer zijn om DRM-beveiliging te beheren, ongeacht of het statisch of dynamisch versleutelen is. De invoer voor DRM-versleuteling omvat:

* MBR-video-inhoud
* Inhouds sleutel
* Url's voor licenties ophalen

Dit is de stroom op hoog niveau tijdens de afspeel tijd:

* De gebruiker is geverifieerd.
* Er wordt een autorisatie token voor de gebruiker gemaakt.
* Met DRM beveiligde inhoud (manifest) wordt gedownload naar de speler.
* De speler verzendt een aanvraag voor het ophalen van licenties naar licentie servers, samen met een sleutel-ID en een autorisatie token.

In de volgende sectie wordt het ontwerp van sleutel beheer besproken.

| **ContentKey-to-Asset** | **Scenario** |
| --- | --- |
| 1-op-1 |Het eenvoudigste geval. Het biedt het kleinste besturings element. Maar deze rang schikking resulteert in het algemeen in de hoogste verzend kosten van de licentie. Er is mini maal één licentie aanvraag vereist voor elke beveiligde Asset. |
| 1-op-veel |U kunt dezelfde inhouds sleutel voor meerdere assets gebruiken. Voor alle assets in een logische groep, zoals een genre of de subset van een genre (of film gen), kunt u bijvoorbeeld één inhouds sleutel gebruiken. |
| Veel-op-1 |Er zijn meerdere inhouds sleutels nodig voor elke Asset. <br/><br/>Als u bijvoorbeeld dynamische CENC-beveiliging met multi-DRM wilt Toep assen voor MPEG-DASH en dynamische AES-128-versleuteling voor HLS, hebt u twee afzonderlijke inhouds sleutels nodig. Elke inhouds sleutel heeft zijn eigen ContentKeyType nodig. (Gebruik ContentKeyType. CommonEncryption voor de inhouds sleutel die wordt gebruikt voor dynamische CENC-beveiliging. Gebruik ContentKeyType. EnvelopeEncryption voor de inhouds sleutel die wordt gebruikt voor dynamische AES-128-versleuteling.)<br/><br/>Een ander voor beeld: in CENC-beveiliging van de onderbroken inhoud kunt u in theorie één inhouds sleutel gebruiken om de video stroom en een andere inhouds sleutel te beveiligen om de audio stroom te beveiligen. |
| Veel-op-veel |Combi natie van de voor gaande twee scenario's. Er wordt één set inhouds sleutels gebruikt voor elk van de meerdere activa in dezelfde activa groep. |

Een andere belang rijke factor om rekening mee te houden is het gebruik van permanente en niet-permanente licenties.

Waarom zijn deze overwegingen belang rijk?

Als u een open bare Cloud gebruikt voor het leveren van licenties, hebben permanente en niet-permanente licenties direct invloed op de leverings kosten van licenties. De volgende twee verschillende ontwerp cases dienen ter illustratie:

* Maandelijks abonnement: gebruik een permanente licentie en een 1-op-veel-inhouds sleutel-naar-Asset-toewijzing. Voor de films van alle kinderen gebruiken we bijvoorbeeld één inhouds sleutel voor versleuteling. In dat geval:

    Totaal aantal aangevraagde licenties voor alle kinderen films/apparaat = 1

* Maandelijks abonnement: gebruik een niet-permanente licentie en een 1-op-1-toewijzing tussen inhouds sleutel en Asset. In dat geval:

    Totaal aantal aangevraagde licenties voor alle kinderen films/apparaat = [aantal gevolgde films] x [aantal sessies]

De twee verschillende ontwerpen resulteren in zeer verschillende licentie-aanvraag patronen. De verschillende patronen resulteren in verschillende leverings kosten voor licenties als de licentie leverings service wordt geleverd door een open bare Cloud, zoals Media Services.

## <a name="map-design-to-technology-for-implementation"></a>Ontwerp van de technologie voor implementatie
Vervolgens wordt het algemene ontwerp toegewezen aan technologieën op het Azure/Media Services-platform door op te geven welke technologie voor elke bouw steen moet worden gebruikt.

De volgende tabel toont de toewijzing.

| **Bouw steen** | **Technologie** |
| --- | --- |
| **Speler** |[Azure Media Player](https://azure.microsoft.com/services/media-services/media-player/) |
| **ID-provider (IDP)** |Azure Active Directory (Azure AD) |
| **Secure token service (STS)** |Azure AD |
| **DRM-beveiligings werk stroom** |Dynamische beveiliging Azure Media Services |
| **Levering van DRM-licentie** |* Media Services licentie levering (PlayReady, Widevine, FairPlay) <br/>* Axinom-licentie server <br/>* Aangepaste PlayReady-licentie server |
| **Oorsprong** |Azure Media Services streaming-eind punt |
| **Sleutelbeheer** |Niet nodig voor referentie-implementatie |
| **Inhoudbeheer** |Een C#-console toepassing |

Met andere woorden, zowel IDP als STS worden geleverd door Azure AD. De [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) wordt gebruikt voor de speler. Zowel Azure Media Services als Azure Media Player ondersteunen CENC via DASH, FairPlay via HLS, PlayReady over smooth streaming en AES-128-versleuteling voor DASH, HLS en Smooth.

In het volgende diagram ziet u de algehele structuur en stroom met de vorige technologie toewijzing:

![CENC op Media Services](./media/architecture-design-multi-drm-system/media-services-cenc-subsystem-on-AMS-platform.png)

Als u DRM-inhouds beveiliging wilt instellen, gebruikt het hulp programma voor inhouds beheer de volgende invoer:

* Inhoud openen
* Inhouds sleutel van sleutel beheer
* Url's voor licenties ophalen
* Een lijst met gegevens uit Azure AD, zoals een doel groep, certificaat verlener en Token claims

Dit is de uitvoer van het hulp programma voor inhouds beheer:

* ContentKeyPolicy beschrijft een DRM-licentie sjabloon voor elk type DRM dat wordt gebruikt;
* ContentKeyPolicyRestriction beschrijft het toegangs beheer voordat een DRM-licentie wordt uitgegeven
* Streamingpolicy beschrijft de verschillende combi Naties van DRM-versleutelings modus-streaming protocol-container indeling, voor streaming
* StreamingLocator beschrijft de inhouds sleutel/IV die wordt gebruikt voor versleuteling en streaming-Url's 

Dit is de stroom tijdens runtime:

* Bij de verificatie van de gebruiker wordt er een JWT gegenereerd.
* Een van de claims in de JWT is een groepering claim die de groeps object-ID EntitledUserGroup bevat. Deze claim wordt gebruikt om de controle op rechten uit te geven.
* De speler downloadt het client manifest van met CENC beveiligde inhoud en identificeert het volgende:
   * Sleutel-ID.
   * De inhoud is DRM beveiligd.
   * Url's voor het aanschaffen van licenties.
* Er wordt een aanvraag voor het ophalen van licenties gemaakt op basis van de browser/DRM die wordt ondersteund. De sleutel-ID en de JWT worden ook in de aanvraag voor licentie aanschaf verzonden. De service licentie levering verifieert de JWT en de claims die zijn opgenomen voordat de benodigde licentie wordt uitgegeven.

## <a name="implementation"></a>Implementatie
### <a name="implementation-procedures"></a>Implementatie procedures
De implementatie omvat de volgende stappen:

1. Test assets voorbereiden. Een test video coderen/verpakken naar gefragmenteerde MP4 met multi-bitrate in Media Services. Deze asset is *niet* beveiligd met DRM. DRM-beveiliging wordt later door dynamische beveiliging uitgevoerd.

2. Maak een sleutel-ID en een inhouds sleutel (optioneel vanuit een sleutel Seed). In dit geval is het sleutel beheersysteem niet nodig omdat er slechts één sleutel-ID en inhouds sleutel zijn vereist voor een aantal test assets.

3. Gebruik de Media Services-API voor het configureren van multi-DRM-licentie leverings Services voor het test activum. Als u aangepaste licentie servers gebruikt door uw bedrijf of leveranciers van uw bedrijf in plaats van licentie services in Media Services, kunt u deze stap overs Laan. U kunt Url's voor aanschaf van licenties opgeven in de stap wanneer u licentie levering configureert. De Media Services-API is vereist om een aantal gedetailleerde configuraties op te geven, zoals beperking van het autorisatie beleid en sjablonen voor licentie reacties voor verschillende DRM-licentie services. Op dit moment biedt de Azure Portal niet de benodigde gebruikers interface voor deze configuratie. Zie voor informatie over het API-niveau en voorbeeld code [PlayReady en/of Widevine Dynamic common Encryption gebruiken](drm-protect-with-drm-tutorial.md).

4. Gebruik de Media Services-API om het beleid voor de levering van assets voor de test Asset te configureren. Zie voor informatie over het API-niveau en voorbeeld code [PlayReady en/of Widevine Dynamic common Encryption gebruiken](drm-protect-with-drm-tutorial.md).

5. Een Azure AD-Tenant maken en configureren in Azure.

6. Maak een paar gebruikers accounts en-groepen in uw Azure AD-Tenant. Maak ten minste een groep getiteld gebruiker en voeg een gebruiker toe aan deze groep. Gebruikers in deze groep geven de rechten controle bij het aanschaffen van licenties door. Gebruikers die niet in deze groep zijn, kunnen de verificatie controle niet door geven en kunnen geen licentie verkrijgen. Lidmaatschap van deze groep ' getiteld gebruiker ' is een vereiste claim voor groepen in de JWT die is uitgegeven door Azure AD. U geeft deze claim vereiste op in de stap wanneer u multi-DRM-licentie leverings Services configureert.

7. Maak een ASP.NET MVC-app om uw video speler te hosten. Deze ASP.NET-app wordt beveiligd met de gebruikers verificatie voor de Azure AD-Tenant. De juiste claims zijn opgenomen in de toegangs tokens die zijn verkregen na de verificatie van de gebruiker. U kunt OpenID Connect Connect API voor deze stap aanbevelen. Installeer de volgende NuGet-pakketten:

   * Install-Package Microsoft. Azure. ActiveDirectory. GraphClient
   * Install-Package micro soft. Owin. Security. OpenIdConnect
   * Install-Package micro soft. Owin. Security. cookies
   * Install-Package Microsoft.Owin.Host.SystemWeb
   * Install-Package micro soft. Identity model. clients. ActiveDirectory

8. Een speler maken met behulp van de [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/). Gebruik de [Azure Media Player PROTECTIONINFO API](https://amp.azure.net/libs/amp/latest/docs/) om op te geven welke DRM-technologie op verschillende DRM-platforms moet worden gebruikt.

9. In de volgende tabel wordt de test matrix weer gegeven.

    | **Digital** | **Browser** | **Resultaat van gebruiker met recht** | **Resultaat voor onbevoegd gebruiker** |
    | --- | --- | --- | --- |
    | **PlayReady** |Micro soft Edge of Internet Explorer 11 op Windows 10 |Anders |Mislukt |
    | **Widevine** |Chrome, Firefox, Opera |Anders |Mislukt |
    | **FairPlay** |Safari op macOS      |Anders |Mislukt |
    | **AES-128** |Meest moderne browsers  |Anders |Mislukt |

Voor informatie over het instellen van Azure AD voor een ASP.NET MVC Player-app raadpleegt u [een Azure Media Services op OWIN MVC gebaseerde app integreren met Azure Active Directory en de levering van de inhouds sleutel beperken op basis van JWT-claims](http://gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

Zie [JWT-token verificatie in azure Media Services en dynamische versleuteling](http://gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)voor meer informatie.  

Voor informatie over Azure AD:

* In de [hand leiding voor ontwikkel aars van Azure Active Directory](../../active-directory/develop/v2-overview.md)vindt u informatie voor ontwikkel aars.
* U kunt beheerders informatie vinden in [uw Azure AD-Tenant Directory beheren](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Enkele problemen in de implementatie

Gebruik de volgende informatie voor probleem oplossing voor hulp bij implementatie problemen.

* De URL van de uitgever moet eindigen op '/'. De doel groep moet de client-ID van de Player-toepassing zijn. Voeg ook '/' toe aan het einde van de URL van de uitgever.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    In de [JWT-decoder](http://jwt.calebb.net/)ziet u **AUD** en **ISS**, zoals wordt weer gegeven in de JWT:

    ![JWT](./media/architecture-design-multi-drm-system/media-services-1st-gotcha.png)

* Voeg machtigingen toe aan de toepassing in azure AD op het tabblad **configureren** van de toepassing. Er zijn machtigingen vereist voor elke toepassing, zowel lokale als geïmplementeerde versies.

    ![Machtigingen](./media/architecture-design-multi-drm-system/media-services-perms-to-other-apps.png)

* Gebruik de juiste verlener wanneer u dynamische CENC-beveiliging instelt.

    ```xml
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/"/>
    ```

    Het volgende werkt niet:

    ```xml
    <add key="ida:issuer" value="https://willzhanad.onmicrosoft.com/" />
    ```

    De GUID is de ID van de Azure AD-Tenant. De GUID vindt u in het pop-upmenu **endpoints** in het Azure Portal.

* Claim machtigingen voor groepslid maatschap verlenen. Controleer of het volgende voor komt in het manifest bestand van de Azure AD-toepassing: 

    "groupMembershipClaims": "all" (de standaard waarde is null)

* Stel het juiste token type in wanneer u beperkings vereisten maakt.

    `objTokenRestrictionTemplate.TokenType = TokenType.JWT;`

    Omdat u ondersteuning voor JWT (Azure AD) toevoegt naast SWT (ACS), is het standaard token type token. JWT. Als u SWT/ACS gebruikt, moet u het token instellen op token type. SWT.

## <a name="the-completed-system-and-test"></a>Het voltooide systeem en de test

In deze sectie worden de volgende scenario's in het voltooide end-to-end-systeem door lopen, zodat u een basis afbeelding van het gedrag kunt hebben voordat u een aanmeldings account krijgt:

* Als u een niet-geïntegreerd scenario nodig hebt:

    * Voor video-assets die worden gehost in Media Services die niet zijn beveiligd of die zijn beveiligd met DRM, maar zonder token verificatie (waarbij een licentie wordt verleend aan wie het is aangevraagd), kunt u deze testen zonder u aan te melden. Schakel over naar HTTP als uw video-streaming via HTTP is.

* Als u een end-to-end geïntegreerd scenario nodig hebt:

    * Voor video-assets onder dynamische DRM-beveiliging in Media Services, waarbij de token verificatie en JWT worden gegenereerd door Azure AD, moet u zich aanmelden.

Zie [deze website](https://openidconnectweb.azurewebsites.net/)voor de webtoepassing van de speler en de aanmelding.

### <a name="user-sign-in"></a>Gebruikersaanmelding
Als u het end-to-end geïntegreerde DRM-systeem wilt testen, moet u een account hebben gemaakt of toegevoegd.

Welk account?

Hoewel Azure oorspronkelijk alleen toegang heeft toegestaan door Microsoft-account gebruikers, is toegang nu toegestaan door gebruikers van beide systemen. Alle Azure-eigenschappen vertrouwen nu Azure AD voor verificatie en Azure AD verifieert organisatorische gebruikers. Er is een Federatie relatie gemaakt waarbij Azure AD het Microsoft-account Consumer-identiteits systeem vertrouwt om consumenten gebruikers te verifiëren. Als gevolg hiervan kan Azure AD gast-micro soft-accounts en systeem eigen Azure AD-accounts verifiëren.

Omdat Azure AD het Microsoft-account domein vertrouwt, kunt u accounts uit de volgende domeinen toevoegen aan de aangepaste Azure AD-Tenant en het account gebruiken om u aan te melden:

| **Domeinnaam** | **Domein** |
| --- | --- |
| **Aangepast Azure AD-Tenant domein** |somename.onmicrosoft.com |
| **Bedrijfs domein** |microsoft.com |
| **Microsoft-account domein** |outlook.com, live.com, hotmail.com |

U kunt contact opnemen met een van de auteurs om een account voor u te maken of toe te voegen.

In de volgende scherm afbeeldingen worden verschillende aanmeldings pagina's weer gegeven die worden gebruikt door verschillende domein accounts:

**Aangepast domein account voor Azure AD-Tenant**: de aangepaste aanmeldings pagina van het aangepaste domein van de Azure AD-Tenant.

![Aangepast Azure AD-Tenant domein account één](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain1.png)

**Micro soft-domein account met Smart Card**: de aanmeldings pagina die door micro soft wordt aangepast met twee ledige verificatie.

![Aangepast Azure AD-Tenant domein account twee](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain2.png)

**Microsoft-account**: de aanmeldings pagina van de Microsoft-account voor consumenten.

![Aangepast Azure AD-Tenant domein account drie](./media/architecture-design-multi-drm-system/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Versleutelde Media-extensies gebruiken voor PlayReady

In een moderne browser met versleutelde Media-extensies (EME) voor PlayReady-ondersteuning, zoals Internet Explorer 11 op Windows 8,1 of hoger en micro soft Edge-browser op Windows 10, is PlayReady het onderliggende DRM voor EME.

![EME gebruiken voor PlayReady](./media/architecture-design-multi-drm-system/media-services-eme-for-playready1.png)

Het donkere gebied van de speler is omdat PlayReady Protection voor komt dat u een scherm opname van beveiligde video maakt.

De volgende scherm afbeelding toont de invoeg toepassingen van de speler en de ondersteuning van MSE (micro soft Security Essentials)/EME:

![Player-invoeg toepassingen voor PlayReady](./media/architecture-design-multi-drm-system/media-services-eme-for-playready2.png)

EME in micro soft Edge en Internet Explorer 11 op Windows 10 toestaan [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) te worden aangeroepen op Windows 10-apparaten die dit ondersteunen. PlayReady SL3000 ontgrendelt de stroom van uitgebreide Premium-inhoud (4.000, HDR) en nieuwe inhouds leverings modellen (voor uitgebreide inhoud).

PlayReady is het enige DRM-apparaat in de hardware die beschikbaar is op Windows-apparaten (PlayReady SL3000) om zich te richten op de Windows-apparaten. Een streaming-service kan PlayReady gebruiken via EME of via een Universeel Windows-platform toepassing en een hogere video kwaliteit bieden met PlayReady SL3000 dan een ander DRM. Inhoud van Maxi maal 2.000 stromen via Chrome of Firefox, en inhoud tot 4.000 stromen via micro soft Edge/Internet Explorer 11 of een Universeel Windows-platform-toepassing op hetzelfde apparaat. De hoeveelheid is afhankelijk van de service-instellingen en-implementatie.

#### <a name="use-eme-for-widevine"></a>EME gebruiken voor Widevine

In een moderne browser met ondersteuning voor EME/Widevine, zoals Chrome 41 + op Windows 10, Windows 8,1, Mac OSX Yosemite en Chrome op Android 4.4.4, is Google Widevine de DRM achter EME.

![EME gebruiken voor Widevine](./media/architecture-design-multi-drm-system/media-services-eme-for-widevine1.png)

Widevine voor komt niet dat u een scherm opname van beveiligde video maakt.

![Player-invoeg toepassingen voor Widevine](./media/architecture-design-multi-drm-system/media-services-eme-for-widevine2.png)

#### <a name="use-eme-for-fairplay"></a>EME gebruiken voor FairPlay

Op dezelfde manier kunt u FairPlay beveiligde inhoud testen in deze test speler in Safari op macOS of iOS 11,2 en hoger.

Zorg ervoor dat u "FairPlay" als protectionInfo. Typ en plaats de juiste URL voor uw toepassings certificaat in FPS AC-pad (FairPlay streaming toepassings certificaat pad).

### <a name="unentitled-users"></a>Gebruikers onrechtden

Als een gebruiker geen lid is van de groep getiteld gebruikers, wordt de controle niet door gegeven aan de gebruiker. De multi-DRM-licentie service weigert vervolgens de aangevraagde licentie te verlenen, zoals wordt weer gegeven. De gedetailleerde beschrijving is ' licentie ophalen mislukt ', zoals is ontworpen.

![Gebruikers onrechtden](./media/architecture-design-multi-drm-system/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Een aangepaste beveiligings token service uitvoeren

Als u een aangepaste STS uitvoert, wordt de JWT door de aangepaste STS uitgegeven door een symmetrische of asymmetrische sleutel te gebruiken.

De volgende scherm afbeelding toont een scenario dat gebruikmaakt van een symmetrische sleutel (met Chrome):

![Aangepaste STS met een symmetrische sleutel](./media/architecture-design-multi-drm-system/media-services-running-sts1.png)

De volgende scherm afbeelding toont een scenario dat gebruikmaakt van een asymmetrische sleutel via een x509-certificaat (met behulp van een micro soft moderne browser):

![Aangepaste STS met een asymmetrische sleutel](./media/architecture-design-multi-drm-system/media-services-running-sts2.png)

In beide gevallen blijft de verificatie van de gebruiker hetzelfde. Het wordt uitgevoerd via Azure AD. Het enige verschil is dat JWTs worden uitgegeven door de aangepaste STS in plaats van met Azure AD. Wanneer u dynamische CENC-beveiliging configureert, wordt door de licentie delivery service-beperking het type JWT opgegeven, ofwel een symmetrische of een asymmetrische sleutel.
