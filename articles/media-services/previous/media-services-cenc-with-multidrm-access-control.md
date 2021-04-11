---
title: Ontwerp van een inhouds beschermings systeem met toegangs beheer met Azure Media Services | Microsoft Docs
description: Meer informatie over licenties voor de micro soft Smooth Streaming client Porting kit.
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
ms.author: willzhan
ms.reviewer: kilroyh;yanmf;juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a4f151b597b57b77fa6517c6ea0d586c1106986
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017134"
---
# <a name="design-of-a-content-protection-system-with-access-control-using-azure-media-services"></a>Ontwerp van een inhouds beschermings systeem met toegangs beheer met Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Overzicht

Het ontwerpen en bouwen van een digital rights management-subsysteem (DRM) voor een over-the-top (OTT) of online streaming-oplossing is een complexe taak. Opera tors/online video providers doen deze taak doorgaans door aan gespecialiseerde DRM-service providers. Het doel van dit document is een referentie ontwerp en implementatie van een end-to-end DRM-subsysteem in een OTT of online streaming-oplossing te presen teren.

De doel lezers voor dit document zijn technici die werken in DRM-subsystemen van OTT of online streaming/multiscreen-oplossingen of lezers die geïnteresseerd zijn in DRM-subsystemen. De veronderstelling is dat lezers bekend zijn met ten minste één van de DRM-technologieën op de markt, zoals PlayReady, Widevine, FairPlay of Adobe Access.

In deze bespreking van DRM bevatten we ook common Encryption (CENC) met multi-DRM. Een belang rijke trend in online streaming en de OTT-industrie is het gebruik van CENC met een multi-native DRM op verschillende client platforms. Deze trend is een verschuiving van de vorige die één DRM en de client-SDK heeft gebruikt voor verschillende client platforms. Wanneer u CENC gebruikt met multi-native DRM, worden zowel PlayReady als Widevine versleuteld volgens de specificatie van de [common Encryption (ISO/IEC 23001-7 CENC)](https://www.iso.org/iso/home/store/catalogue_ics/catalogue_detail_ics.htm?csnumber=65271/) .

De voor delen van CENC met multi-DRM zijn:

* Verlaagt de versleutelings kosten omdat één versleutelings proces wordt gebruikt om verschillende platforms te richten met de eigen DRMs.
* Verlaagt de kosten voor het beheren van versleutelde assets omdat er slechts één exemplaar van versleutelde activa nodig is.
* Elimineert de kosten voor DRM-client licenties omdat de systeem eigen DRM-client doorgaans vrij is op het systeem eigen platform.

Micro soft is een actieve promotie van een streepje en CENC samen met enkele grote bedrijven. Azure Media Services biedt ondersteuning voor DASH en CENC. Voor recente aankondigingen raadpleegt u de volgende blogs:

*  [Google Widevine-services voor het leveren van licenties in Azure Media Services aankondigen](https://azure.microsoft.com/blog/announcing-general-availability-of-google-widevine-license-services/)
* [Azure Media Services voegt Google Widevine-verpakking toe voor het leveren van een multi-DRM-stroom](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)  

### <a name="goals-of-the-article"></a>Doel stellingen van het artikel

De doel stellingen van dit artikel zijn:

* Geef een referentie ontwerp op van een DRM-subsysteem dat gebruikmaakt van CENC met multi-DRM.
* Geef een referentie-implementatie op in een Azure/Media Services-platform.
* Bespreek enkele onderwerpen over ontwerpen en implementeren.

In het artikel wordt de term ' multi-DRM ' behandeld in de volgende producten:

* Micro soft PlayReady
* Google Widevine
* Apple FairPlay 

De volgende tabel bevat een overzicht van de systeem eigen app en browsers die door elke DRM worden ondersteund.

| **Client platform** | **Systeem eigen DRM-ondersteuning** | **Browser/app** | **Streaming-indelingen** |
| --- | --- | --- | --- |
| **Smart Tv's, operator STBs, OTT STBs** |PlayReady voornamelijk, en/of Widevine, en/of andere |Linux, Opera, WebKit, Overig |Verschillende indelingen |
| **Windows 10-apparaten (Windows-PC, Windows-tablets, Windows Phone, Xbox)** |PlayReady |Micro soft Edge/IE11/EME<br/><br/><br/>Universeel Windows-platform |STREEPJE (voor HLS, PlayReady wordt niet ondersteund)<br/><br/>DASH, Smooth Streaming (voor HLS, PlayReady wordt niet ondersteund) |
| **Android-apparaten (telefoon, Tablet, TV)** |Widevine |Chrome/EME |STREEPJE, HLS |
| **iOS (iPhone, iPad), OS X-clients en Apple TV** |FairPlay |Safari 8 +/EME |HLS |

Als u de huidige status van de implementatie voor elke DRM overweegt, wil een service doorgaans twee of drie DRMs implementeren om ervoor te zorgen dat u alle typen eind punten op de beste manier adresseert.

Er is een verhouding tussen de complexiteit van de service logica en de complexiteit aan de client zijde om een bepaald niveau van gebruikers ervaring op de verschillende clients te bereiken.

Denk aan het volgende om uw keuze te maken:

* PlayReady is systeem eigen geïmplementeerd in elk Windows-apparaat, op sommige Android-apparaten en beschikbaar via software-Sdk's op vrijwel elk platform.
* Widevine wordt systeem eigen geïmplementeerd in elk Android-apparaat, in Chrome en op andere apparaten.
* FairPlay is alleen beschikbaar op iOS-en Mac OS-clients of via iTunes.

Er zijn twee opties voor een typische multi-DRM:

* PlayReady en Widevine
* PlayReady, Widevine en FairPlay

## <a name="a-reference-design"></a>Een referentie ontwerp
Deze sectie bevat een referentie ontwerp dat neutraal is voor de technologieën die worden gebruikt om het te implementeren.

Een DRM-subsysteem kan de volgende onderdelen bevatten:

* Sleutelbeheer
* DRM-verpakking
* Levering van DRM-licentie
* Controle op rechten
* Verificatie/autorisatie
* Speler
* Herkomst/Content Delivery Network (CDN)

In het volgende diagram ziet u de interactie op hoog niveau tussen de onderdelen in een DRM-subsysteem:

![DRM-subsysteem met CENC](./media/media-services-cenc-with-multidrm-access-control/media-services-generic-drm-subsystem-with-cenc.png)

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
| **Security Token Service (STS)** |Azure AD |
| **DRM-beveiligings werk stroom** |Dynamische beveiliging Media Services |
| **Levering van DRM-licentie** |* Media Services licentie levering (PlayReady, Widevine, FairPlay) <br/>* Axinom-licentie server <br/>* Aangepaste PlayReady-licentie server |
| **Oorsprong** |Media Services streaming-eind punt |
| **Sleutelbeheer** |Niet nodig voor referentie-implementatie |
| **Inhoudbeheer** |Een C#-console toepassing |

Met andere woorden, zowel IDP als STS worden gebruikt met Azure AD. De [Azure Media Player-API](https://amp.azure.net/libs/amp/latest/docs/) wordt gebruikt voor de speler. Zowel Media Services als Media Player ondersteunen DASH en CENC met multi-DRM.

In het volgende diagram ziet u de algehele structuur en stroom met de vorige technologie toewijzing:

![CENC op Media Services](./media/media-services-cenc-with-multidrm-access-control/media-services-cenc-subsystem-on-AMS-platform.png)

Voor het instellen van dynamische CENC-versleuteling gebruikt het hulp programma voor inhouds beheer de volgende invoer:

* Inhoud openen
* Inhouds sleutel van sleutel generatie/-beheer
* Url's voor licenties ophalen
* Een lijst met gegevens uit Azure AD

Dit is de uitvoer van het hulp programma voor inhouds beheer:

* ContentKeyAuthorizationPolicy bevat de specificatie over hoe licentie levering een JSON Web Token (JWT) en DRM-licentie specificaties verifieert.
* AssetDeliveryPolicy bevat specificaties voor de streaming-indeling, DRM-beveiliging en Url's voor het verkrijgen van licenties.

Dit is de stroom tijdens runtime:

* Bij de verificatie van de gebruiker wordt er een JWT gegenereerd.
* Een van de claims in de JWT is een groepering claim die de groeps object-ID EntitledUserGroup bevat. Deze claim wordt gebruikt om de controle op rechten uit te geven.
* De speler downloadt het client manifest van met CENC beveiligde inhoud en identificeert het volgende:
   * Sleutel-ID.
   * De inhoud is CENC beveiligd.
   * Url's voor het aanschaffen van licenties.
* Er wordt een aanvraag voor het ophalen van licenties gemaakt op basis van de browser/DRM die wordt ondersteund. De sleutel-ID en de JWT worden ook in de aanvraag voor licentie aanschaf verzonden. De service licentie levering verifieert de JWT en de claims die zijn opgenomen voordat de benodigde licentie wordt uitgegeven.

## <a name="implementation"></a>Implementatie
### <a name="implementation-procedures"></a>Implementatie procedures
De implementatie omvat de volgende stappen:

1. Test assets voorbereiden. Een test video coderen/verpakken naar gefragmenteerde MP4 met multi-bitrate in Media Services. Deze asset is *niet* beveiligd met DRM. DRM-beveiliging wordt later door dynamische beveiliging uitgevoerd.

2. Maak een sleutel-ID en een inhouds sleutel (optioneel vanuit een sleutel Seed). In dit geval is het sleutel beheersysteem niet nodig omdat er slechts één sleutel-ID en inhouds sleutel zijn vereist voor een aantal test assets.

3. Gebruik de Media Services-API voor het configureren van multi-DRM-licentie leverings Services voor het test activum. Als u aangepaste licentie servers gebruikt door uw bedrijf of leveranciers van uw bedrijf in plaats van licentie services in Media Services, kunt u deze stap overs Laan. U kunt Url's voor aanschaf van licenties opgeven in de stap wanneer u licentie levering configureert. De Media Services-API is vereist om een aantal gedetailleerde configuraties op te geven, zoals beperking van het autorisatie beleid en sjablonen voor licentie reacties voor verschillende DRM-licentie services. Op dit moment biedt de Azure Portal niet de benodigde gebruikers interface voor deze configuratie. Zie voor informatie over het API-niveau en voorbeeld code [PlayReady en/of Widevine Dynamic common Encryption gebruiken](media-services-protect-with-playready-widevine.md).

4. Gebruik de Media Services-API om het beleid voor de levering van assets voor de test Asset te configureren. Zie voor informatie over het API-niveau en voorbeeld code [PlayReady en/of Widevine Dynamic common Encryption gebruiken](media-services-protect-with-playready-widevine.md).

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

* In de [hand leiding voor ontwikkel aars van Azure Active Directory](../../active-directory/azuread-dev/v1-overview.md)vindt u informatie voor ontwikkel aars.
* U kunt beheerders informatie vinden in [uw Azure AD-Tenant Directory beheren](../../active-directory/fundamentals/active-directory-whatis.md).

### <a name="some-issues-in-implementation"></a>Enkele problemen in de implementatie
Gebruik de volgende informatie voor probleem oplossing voor hulp bij implementatie problemen.

* De URL van de uitgever moet eindigen op '/'. De doel groep moet de client-ID van de Player-toepassing zijn. Voeg ook '/' toe aan het einde van de URL van de uitgever.

    ```xml
    <add key="ida:audience" value="[Application Client ID GUID]" />
    <add key="ida:issuer" value="https://sts.windows.net/[AAD Tenant ID]/" />
    ```

    In de [JWT-decoder](http://jwt.calebb.net/)ziet u **AUD** en **ISS**, zoals wordt weer gegeven in de JWT:

    ![JWT](./media/media-services-cenc-with-multidrm-access-control/media-services-1st-gotcha.png)

* Voeg machtigingen toe aan de toepassing in azure AD op het tabblad **configureren** van de toepassing. Er zijn machtigingen vereist voor elke toepassing, zowel lokale als geïmplementeerde versies.

    ![Machtigingen](./media/media-services-cenc-with-multidrm-access-control/media-services-perms-to-other-apps.png)

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

    ```csharp
    objTokenRestrictionTemplate.TokenType = TokenType.JWT;
    ```

    Omdat u ondersteuning voor JWT (Azure AD) toevoegt naast SWT (ACS), is het standaard token type token. JWT. Als u SWT/ACS gebruikt, moet u het token instellen op token type. SWT.

## <a name="additional-topics-for-implementation"></a>Aanvullende onderwerpen voor implementatie
In deze sectie worden enkele aanvullende onderwerpen in het ontwerp en de implementatie besproken.

### <a name="http-or-https"></a>HTTP of HTTPS?
De ASP.NET MVC Player-toepassing moet het volgende ondersteunen:

* Gebruikers verificatie via Azure AD, dat zich onder HTTPS bevindt.
* JWT-uitwisseling tussen de client en Azure AD, die zich onder HTTPS bevindt.
* DRM-licentie verwerving door de client, die onder HTTPS moet vallen als licentie levering wordt geleverd door Media Services. De PlayReady-product suite vereist geen HTTPS voor licentie levering. Als uw PlayReady-licentie server zich buiten Media Services bevindt, kunt u HTTP of HTTPS gebruiken.

De ASP.NET Player-toepassing maakt gebruik van HTTPS als een best practice, dus Media Player bevindt zich op een pagina onder HTTPS. HTTP verdient echter de voor keur voor streaming, dus u moet rekening houden met het probleem van gemengde inhoud.

* De browser staat geen gemengde inhoud toe. Invoeg toepassingen zoals Silverlight en de OSMF-invoeg toepassing voor Smooth en DASH bieden dit wel. Gemengde inhoud is een beveiligings probleem vanwege de dreiging van de mogelijkheid om schadelijke Java script te injecteren. Dit kan ertoe leiden dat klant gegevens risico lopen. Browsers blok keren deze functie standaard. De enige manier om deze te omzeilen, is op de server (oorsprong) naast elkaar door alle domeinen toe te staan (ongeacht HTTPS of HTTP). Dit is waarschijnlijk geen goed idee.
* Vermijd gemengde inhoud. Zowel de toepassing van de speler als de Media Player moet HTTP of HTTPS gebruiken. Bij het afspelen van gemengde inhoud moet de Silverlight-Tech-technologie een waarschuwing voor gemengde inhoud wissen. De IT-techniek behandelt gemengde inhoud zonder waarschuwing over gemengde inhoud.
* Als uw streaming-eind punt is gemaakt vóór 2014 augustus, wordt er geen ondersteuning geboden voor HTTPS. In dit geval maakt en gebruikt u een nieuw streaming-eind punt voor HTTPS.

In de referentie-implementatie voor inhoud die is beveiligd met DRM, zijn zowel de toepassing als de streaming onder HTTPS. Voor Open inhoud heeft de speler geen verificatie of een licentie nodig, zodat u HTTP of HTTPS kunt gebruiken.

### <a name="azure-active-directory-signing-key-rollover"></a>Rollover van de handtekening sleutel Azure Active Directory
Rollover van de handtekening sleutel is een belang rijk punt om rekening mee te houden in uw implementatie. Als u deze negeert, stopt het voltooide systeem uiteindelijk binnen zes weken helemaal.

Azure AD maakt gebruik van industrie normen om een vertrouwens relatie tot stand te brengen tussen zichzelf en toepassingen die gebruikmaken van Azure AD. In het bijzonder maakt Azure AD gebruik van een handtekening sleutel die bestaat uit een openbaar en een persoonlijk sleutel paar. Wanneer Azure AD een beveiligings token maakt dat informatie over de gebruiker bevat, wordt het ondertekend door Azure AD met een persoonlijke sleutel voordat het wordt teruggestuurd naar de toepassing. Om te controleren of het token geldig is en afkomstig is van Azure AD, moet de toepassing de hand tekening van het token valideren. De toepassing maakt gebruik van de open bare sleutel die wordt weer gegeven door Azure AD die is opgenomen in het federatieve meta gegevens document van de Tenant. Deze open bare sleutel en de ondertekeningssleutel waarvan deze is afgeleid, zijn hetzelfde als de sleutel die wordt gebruikt voor alle tenants in azure AD.

Zie [belang rijke informatie over de rollover van de handtekening sleutel in azure AD](../../active-directory/develop/active-directory-signing-key-rollover.md)voor meer informatie over de sleutel rollover van Azure AD.

Tussen het [open bare-persoonlijke sleutel paar](https://login.microsoftonline.com/common/discovery/keys/):

* De persoonlijke sleutel wordt door Azure AD gebruikt voor het genereren van een JWT.
* De open bare sleutel wordt gebruikt door een toepassing zoals DRM-licentie levering Services in Media Services om de JWT te controleren.

Uit veiligheids overwegingen roteert Azure AD het certificaat regel matig (elke zes weken). In het geval van inbreuk op de beveiliging kan de sleutel rollover op elk moment worden uitgevoerd. Daarom moeten de services voor het leveren van licenties in Media Services de open bare sleutel bijwerken die als Azure AD wordt gebruikt, het sleutel paar draaien. Als dat niet het geval is, mislukt de verificatie van tokens in Media Services en wordt er geen licentie verleend.

Als u deze service wilt instellen, stelt u TokenRestrictionTemplate. OpenIdConnectDiscoveryDocument in wanneer u DRM-licentie leverings Services configureert.

Dit is de JWT-stroom:

* Azure AD verzendt de JWT met de huidige persoonlijke sleutel voor een geverifieerde gebruiker.
* Wanneer een speler een CENC met multi-DRM beveiligde inhoud ziet, zoekt deze eerst naar de JWT die is uitgegeven door Azure AD.
* De speler verzendt een aanvraag voor het ophalen van licenties met de JWT voor het leveren van licentie services in Media Services.
* De services voor het leveren van licenties in Media Services gebruiken de huidige/geldige open bare sleutel van Azure AD om de JWT te verifiëren voordat de licenties worden uitgegeven.

DRM-licentie leverings Services controleren altijd op de huidige/geldige open bare sleutel van Azure AD. De open bare sleutel die wordt gepresenteerd door Azure AD is de sleutel die wordt gebruikt om een JWT te verifiëren die is uitgegeven door Azure AD.

Wat gebeurt er als de sleutel rollover plaatsvindt nadat Azure AD een JWT heeft gegenereerd, maar voordat de JWT door spelers wordt verzonden naar DRM-services voor het leveren van licenties in Media Services voor verificatie?

Omdat er op elk moment een sleutel kan worden doorgevoerd, is er altijd meer dan één geldige open bare sleutel beschikbaar in het document met federatieve meta gegevens. Media Services licentie levering kan elk van de in het document opgegeven sleutels gebruiken. Omdat een sleutel mogelijk binnenkort wordt gedistribueerd, is het mogelijk dat de code wordt vervangen.

### <a name="where-is-the-access-token"></a>Waar bevindt zich het toegangs token?
Als u kijkt hoe een web-app een API-app aanroept onder [toepassings identiteit met OAuth 2,0-client referenties toewijzen](../../active-directory/azuread-dev/web-api.md), is de verificatie stroom als volgt:

* Een gebruiker meldt zich aan bij Azure AD in de webtoepassing. Zie webbrowser [to Web Application](../../active-directory/azuread-dev/web-app.md)(Engelstalig) voor meer informatie.
* Het Azure AD-autorisatie-eind punt stuurt de gebruikers agent terug naar de client toepassing met een autorisatie code. De gebruikers agent retourneert de autorisatie code naar de omleidings-URI van de client toepassing.
* De webtoepassing moet een toegangs token verkrijgen zodat het kan worden geverifieerd bij de Web-API en de gewenste resource kan ophalen. Hiermee wordt een aanvraag voor het Azure AD-token eindpunt gemaakt en worden de referenties, de client-ID en de URI van de toepassings-ID van de Web-API verstrekt. Het geeft de autorisatie code om te bewijzen dat de gebruiker heeft gestemd.
* Azure AD verifieert de toepassing en retourneert een JWT-toegangs token dat wordt gebruikt om de Web-API aan te roepen.
* Via HTTPS gebruikt de webtoepassing het geretourneerde JWT-toegangs token om de JWT-teken reeks toe te voegen met de aanduiding ' Bearer ' in de koptekst ' autorisatie ' van de aanvraag naar de Web-API. De Web-API valideert vervolgens de JWT. Als de validatie is geslaagd, wordt de gewenste resource geretourneerd.

In deze toepassings identiteits stroom vertrouwt de Web-API dat de gebruiker de webtoepassing heeft geverifieerd. Daarom wordt dit patroon een vertrouwd subsysteem genoemd. In het [diagram autorisatie stroom](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) wordt beschreven hoe autorisatie-granting flow werkt.

Het ophalen van licenties met token beperking volgt hetzelfde patroon van het vertrouwde subsysteem. De service voor het leveren van licenties in Media Services is de Web-API-resource of de ' back-end-bron ' die een webtoepassing nodig heeft om toegang te krijgen. Waar bevindt zich het toegangs token?

Het toegangs token wordt verkregen van Azure AD. Na een geslaagde gebruikers verificatie wordt een autorisatie code geretourneerd. De autorisatie code wordt vervolgens gebruikt samen met de client-ID en de app-sleutel voor het uitwisselen van het toegangs token. Het toegangs token wordt gebruikt voor toegang tot een pointer-toepassing die verwijst naar of die de Media Services License delivery-service vertegenwoordigt.

Als u de pointer-app in azure AD wilt registreren en configureren, voert u de volgende stappen uit:

1. In de Azure AD-Tenant:

   * Voeg een toepassing (resource) toe met de aanmeldings-URL https://[resource_name]. azurewebsites. net/. 
   * Voeg een app-ID toe met de URL https://[aad_tenant_name]. onmicrosoft. com/[resource_name].

2. Voeg een nieuwe sleutel toe voor de resource-app.

3. Werk het manifest bestand van de app bij zodat de eigenschap groupMembershipClaims de waarde "groupMembershipClaims": "all" heeft.

4. Voeg in de Azure AD-app die verwijst naar de Web-App van de speler, in de sectie **machtigingen voor andere toepassingen**, de resource-app toe die is toegevoegd in stap 1. Onder **gedelegeerde machtiging** selecteert u **toegang [RESOURCE_NAME]**. Met deze optie geeft u de Web-App toestemming om toegangs tokens te maken die toegang hebben tot de resource-app. Doe dit voor zowel de lokale als de geïmplementeerde versie van de web-app als u ontwikkelt met Visual Studio en de Azure-web-app.

De JWT die is uitgegeven door Azure AD is het toegangs token dat wordt gebruikt om toegang te krijgen tot de pointer-resource.

### <a name="what-about-live-streaming"></a>Hoe zit het met live streamen?
De vorige discussie is gericht op activa op aanvraag. Hoe zit het met live streamen?

U kunt precies hetzelfde ontwerp en dezelfde implementatie gebruiken om live streamen in Media Services te beveiligen door de activa die aan een programma zijn gekoppeld als een VOD-Asset te behandelen.

Met name als u live streamen wilt uitvoeren in Media Services, moet u een kanaal maken en vervolgens een programma maken onder het kanaal. Als u het programma wilt maken, moet u een Asset maken die het Live Archief voor het programma bevat. Als u CENC met multi-DRM-beveiliging van de live-inhoud wilt bieden, moet u dezelfde instelling/verwerking Toep assen op de Asset alsof het een VOD-activum was voordat u het programma start.

### <a name="what-about-license-servers-outside-media-services"></a>Hoe zit het met licentie servers buiten Media Services?
Klanten die in een licentie server farm zijn belegd, zijn vaak opgenomen in hun eigen data centrum of op een host die wordt gehost door DRM-service providers. Met Media Services Content Protection kunt u in de hybride modus uitvoeren. Inhoud kan worden gehost en dynamisch worden beveiligd in Media Services, terwijl DRM-licenties worden geleverd door servers buiten Media Services. In dit geval moet u rekening houden met de volgende wijzigingen:

* STS moet tokens uitgeven die acceptabel zijn en kunnen worden geverifieerd door de licentie server-farm. De Widevine-licentie servers die door Axinom worden verschaft, vereisen bijvoorbeeld een specifieke JWT die een toeslag bericht bevat. Daarom moet u een STS hebben om een dergelijke JWT te kunnen uitgeven. Voor informatie over dit type implementatie gaat u naar het [Azure-documentatie centrum](https://azure.microsoft.com/documentation/) en raadpleegt u [Axinom gebruiken om Widevine-licenties te leveren aan Azure Media Services](media-services-axinom-integration.md).
* Het is niet meer nodig om de ContentKeyAuthorizationPolicy (License Delivery Service) in Media Services te configureren. U moet de Url's voor de aangeschafte licenties (voor PlayReady, Widevine en FairPlay) opgeven wanneer u AssetDeliveryPolicy configureert om CENC met multi-DRM in te stellen.

### <a name="what-if-i-want-to-use-a-custom-sts"></a>Wat moet ik doen als ik een aangepaste STS wil gebruiken?
Een klant kan ervoor kiezen om een aangepaste STS te gebruiken om JWTs te bieden. Redenen zijn onder andere:

* De IDP die door de klant wordt gebruikt, biedt geen ondersteuning voor STS. In dit geval kan een aangepaste STS een optie zijn.
* De klant heeft mogelijk meer flexibeler of nauw keurigere controle over het integreren van STS met het facturerings systeem voor abonnees van de klant. Een MVPD-operator kan bijvoorbeeld meerdere OTT-abonnee pakketten aanbieden, zoals Premium, Basic en sport. De operator zou mogelijk moeten overeenkomen met de claims in een token met het pakket van een abonnee, zodat alleen de inhoud van een specifiek pakket beschikbaar wordt gemaakt. In dit geval biedt een aangepaste STS de benodigde flexibiliteit en controle.

Wanneer u een aangepaste STS gebruikt, moeten er twee wijzigingen worden aangebracht:

* Wanneer u de service voor het leveren van licenties voor een Asset configureert, moet u de beveiligings sleutel opgeven die wordt gebruikt voor verificatie door de aangepaste STS in plaats van de huidige sleutel van Azure AD. (Meer details volgen.) 
* Wanneer er een JTW-token wordt gegenereerd, wordt een beveiligings sleutel opgegeven in plaats van de persoonlijke sleutel van het huidige x509-certificaat in azure AD.

Er zijn twee soorten beveiligings sleutels:

* Symmetrische sleutel: dezelfde sleutel wordt gebruikt om een JWT te genereren en te verifiëren.
* Asymmetrische sleutel: een combi natie van open bare en persoonlijke sleutels in een x509-certificaat wordt gebruikt met een persoonlijke sleutel voor het versleutelen/genereren van een JWT en met de open bare sleutel om het token te verifiëren.

> [!NOTE]
> Als u .NET Framework/C# als uw ontwikkel platform gebruikt, moet het x509-certificaat dat wordt gebruikt voor een asymmetrische beveiligings sleutel, een sleutel lengte hebben van ten minste 2048. Dit is een vereiste van de klasse System. Identity model. tokens. X509AsymmetricSecurityKey in .NET Framework. Anders wordt de volgende uitzonde ring gegenereerd:
> 
> IDX10630: de ' System. Identity model. tokens. X509AsymmetricSecurityKey voor ondertekening mag niet kleiner zijn dan ' 2048 ' bits.

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

![Scherm afbeelding met de aangepaste aanmeldings pagina van het aangepaste domein van een D-Tenant van Azure.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain1.png)

**Micro soft-domein account met Smart Card**: de aanmeldings pagina die door micro soft wordt aangepast met twee ledige verificatie.

![Scherm opname van de aanmeldings pagina die door micro soft Corporate I met een twee ledige verificatie wordt aangepast.](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain2.png)

**Microsoft-account**: de aanmeldings pagina van de Microsoft-account voor consumenten.

![Aangepast Azure AD-Tenant domein account](./media/media-services-cenc-with-multidrm-access-control/media-services-ad-tenant-domain3.png)

### <a name="use-encrypted-media-extensions-for-playready"></a>Versleutelde Media-extensies gebruiken voor PlayReady
In een moderne browser met versleutelde Media-extensies (EME) voor PlayReady-ondersteuning, zoals Internet Explorer 11 op Windows 8,1 of hoger en micro soft Edge-browser op Windows 10, is PlayReady het onderliggende DRM voor EME.

![EME gebruiken voor PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready1.png)

Het donkere gebied van de speler is omdat PlayReady Protection voor komt dat u een scherm opname van beveiligde video maakt.

De volgende scherm afbeelding toont de invoeg toepassingen van de speler en de ondersteuning van MSE (micro soft Security Essentials)/EME:

![Player-invoeg toepassingen voor PlayReady](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-playready2.png)

EME in micro soft Edge en Internet Explorer 11 op Windows 10 toestaan [PLAYREADY SL3000](https://www.microsoft.com/playready/features/EnhancedContentProtection.aspx/) te worden aangeroepen op Windows 10-apparaten die dit ondersteunen. PlayReady SL3000 ontgrendelt de stroom van uitgebreide Premium-inhoud (4.000, HDR) en nieuwe inhouds leverings modellen (voor uitgebreide inhoud).

PlayReady is het enige DRM-apparaat in de hardware die beschikbaar is op Windows-apparaten (PlayReady SL3000) om zich te richten op de Windows-apparaten. Een streaming-service kan PlayReady gebruiken via EME of via een Universeel Windows-platform toepassing en een hogere video kwaliteit bieden met PlayReady SL3000 dan een ander DRM. Inhoud van Maxi maal 2.000 stromen via Chrome of Firefox, en inhoud tot 4.000 stromen via micro soft Edge/Internet Explorer 11 of een Universeel Windows-platform-toepassing op hetzelfde apparaat. De hoeveelheid is afhankelijk van de service-instellingen en-implementatie.

#### <a name="use-eme-for-widevine"></a>EME gebruiken voor Widevine
In een moderne browser met ondersteuning voor EME/Widevine, zoals Chrome 41 + op Windows 10, Windows 8,1, Mac OSX Yosemite en Chrome op Android 4.4.4, is Google Widevine de DRM achter EME.

![EME gebruiken voor Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine1.png)

Widevine voor komt niet dat u een scherm opname van beveiligde video maakt.

![Player-invoeg toepassingen voor Widevine](./media/media-services-cenc-with-multidrm-access-control/media-services-eme-for-widevine2.png)

### <a name="unentitled-users"></a>Gebruikers onrechtden
Als een gebruiker geen lid is van de groep getiteld gebruikers, wordt de controle niet door gegeven aan de gebruiker. De multi-DRM-licentie service weigert vervolgens de aangevraagde licentie te verlenen, zoals wordt weer gegeven. De gedetailleerde beschrijving is ' licentie ophalen mislukt ', zoals is ontworpen.

![Gebruikers onrechtden](./media/media-services-cenc-with-multidrm-access-control/media-services-unentitledusers.png)

### <a name="run-a-custom-security-token-service"></a>Een aangepaste beveiligings token service uitvoeren
Als u een aangepaste STS uitvoert, wordt de JWT door de aangepaste STS uitgegeven door een symmetrische of asymmetrische sleutel te gebruiken.

De volgende scherm afbeelding toont een scenario dat gebruikmaakt van een symmetrische sleutel (met Chrome):

![Aangepaste STS met een symmetrische sleutel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts1.png)

De volgende scherm afbeelding toont een scenario dat gebruikmaakt van een asymmetrische sleutel via een x509-certificaat (met behulp van een micro soft moderne browser):

![Aangepaste STS met een asymmetrische sleutel](./media/media-services-cenc-with-multidrm-access-control/media-services-running-sts2.png)

In beide gevallen blijft de verificatie van de gebruiker hetzelfde. Het wordt uitgevoerd via Azure AD. Het enige verschil is dat JWTs worden uitgegeven door de aangepaste STS in plaats van met Azure AD. Wanneer u dynamische CENC-beveiliging configureert, wordt door de licentie delivery service-beperking het type JWT opgegeven, ofwel een symmetrische of een asymmetrische sleutel.

## <a name="summary"></a>Samenvatting

In dit document wordt CENC met multi-native DRM-en toegangs beheer via token verificatie, het ontwerp en de implementatie ervan besproken met behulp van Azure, Media Services en Media Player.

* Er is een referentie ontwerp weer gegeven dat alle benodigde onderdelen bevat in een DRM-CENC-subsysteem.
* Er is een referentie-implementatie aangeboden op Azure, Media Services en Media Player.
* Sommige onderwerpen die rechtstreeks betrokken zijn bij het ontwerp en de implementatie, worden ook besproken.

## <a name="additional-notes"></a>Aanvullende opmerkingen

* Widevine is een service van Google Inc. en is onderworpen aan de servicevoorwaarden en het privacybeleid van Google Inc.

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
 
