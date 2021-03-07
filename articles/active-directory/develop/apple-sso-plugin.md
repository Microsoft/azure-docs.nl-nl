---
title: Microsoft Enterprise SSO-invoegtoepassing voor Apple-apparaten
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over de Azure Active Directory SSO-invoeg toepassing van micro soft voor iOS-, iPadOS-en macOS-apparaten.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427093"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten (preview-versie)

>[!IMPORTANT]
> Deze functie [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

De *micro soft Enter PRISE SSO-invoeg toepassing voor Apple-apparaten* biedt eenmalige aanmelding (SSO) voor Azure Active Directory (Azure AD)-accounts in MacOS, Ios en iPadOS in alle toepassingen die ondersteuning bieden voor de functie voor [eenmalige aanmelding](https://developer.apple.com/documentation/authenticationservices) van Apple. Dit omvat oudere toepassingen die uw bedrijf mogelijk afhankelijk maakt, maar die nog geen ondersteuning bieden voor de nieuwste identiteits bibliotheken of-protocollen. Micro soft werkte nauw samen met Apple voor het ontwikkelen van deze invoeg toepassing om de bruikbaarheid van uw toepassingen te verhogen en de beste beveiliging te bieden die door Apple en micro soft kan worden geboden.

De Enter prise SSO-invoeg toepassing is momenteel beschikbaar als ingebouwde functie van de volgende apps:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) -IOS, iPadOS
* Microsoft Intune [bedrijfsportal](/mem/intune/apps/apps-company-portal-macos) -macOS

## <a name="features"></a>Functies

De micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten biedt de volgende voor delen:

- Voorziet in SSO voor Azure AD-accounts voor alle toepassingen die ondersteuning bieden voor de functie voor één Sign-On van Apple.
- Kan worden ingeschakeld door elke Mobile Device Management (MDM)-oplossing.
- Breidt SSO uit naar toepassingen die nog geen micro soft-identiteits platform bibliotheken gebruiken.
- Breidt SSO uit voor toepassingen die gebruikmaken van OAuth2, OpenID Connect Connect en SAML.

## <a name="requirements"></a>Vereisten

Micro soft Enter prise SSO-invoeg toepassing gebruiken voor Apple-apparaten:

- Het apparaat moet **ondersteuning bieden** voor een app die de micro soft Enter prise SSO-invoeg toepassing bevat voor Apple-apparaten die zijn **geïnstalleerd**:
  - iOS 13.0 +: [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 +: [app Microsoft Authenticator](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 +: [intune-bedrijfsportal-app](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Apparaat moet zijn **Inge schreven bij MDM** (bijvoorbeeld met Microsoft intune).
- De configuratie moet worden **gepusht naar het apparaat** om de Enter prise SSO-invoeg toepassing op het apparaat in te scha kelen. Deze beveiligings beperking wordt vereist door Apple.

### <a name="ios-requirements"></a>iOS-vereisten:
- iOS 13,0 of hoger moet op het apparaat zijn geïnstalleerd.
- Een micro soft-toepassing die de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten levert, moet op het apparaat zijn geïnstalleerd. Voor een open bare preview zijn deze toepassingen de [app Microsoft Authenticator](/intune/user-help/user-help-auth-app-overview.md).


### <a name="macos-requirements"></a>macOS-vereisten:
- macOS 10,15 of hoger moet op het apparaat zijn geïnstalleerd. 
- Een micro soft-toepassing die de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten levert, moet op het apparaat zijn geïnstalleerd. Voor een open bare preview omvatten deze toepassingen de [app intune-bedrijfsportal](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>De SSO-invoeg toepassing inschakelen met Mobile Device Management (MDM)

### <a name="microsoft-intune-configuration"></a>Microsoft Intune configuratie

Als u Microsoft Intune gebruikt als uw MDM-service, kunt u de ingebouwde configuratie Profiel instellingen gebruiken om de micro soft Enter prise SSO-invoeg toepassing in te scha kelen.

Configureer eerst de [app-extensie-instellingen voor eenmalige aanmelding](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) van een configuratie profiel en [Wijs het profiel toe aan een gebruikers-of Apparaatgroep](/mem/intune/configuration/device-profile-assign) (als dit nog niet is toegewezen).

De profiel instellingen die de SSO-invoeg toepassing inschakelen, worden automatisch toegepast op de apparaten van de groep bij de volgende keer dat elk apparaat wordt ingecheckt met intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Hand matige configuratie voor andere MDM-Services

Als u Microsoft Intune voor Mobile Device Management niet gebruikt, gebruikt u de volgende para meters om de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten te configureren.

#### <a name="ios-settings"></a>iOS-instellingen:

- **Extensie-id**: `com.microsoft.azureauthenticator.ssoextension`
- **Team-ID**: (dit veld is niet nodig voor IOS)

#### <a name="macos-settings"></a>macOS-instellingen:

- **Extensie-id**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Team-ID**: `UBF8T346G9`

#### <a name="common-settings"></a>Algemene instellingen:

- **Type**: omleiden
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Extra configuratieopties
Er kunnen aanvullende configuratie opties worden toegevoegd om SSO-functionaliteit uit te breiden naar extra apps.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Eenmalige aanmelding inschakelen voor apps die geen micro soft Identity platform-bibliotheek gebruiken

De SSO-invoeg toepassing maakt het mogelijk deel te nemen aan eenmalige aanmelding, zelfs als deze niet is ontwikkeld met een micro soft-SDK zoals micro soft Authentication Library (MSAL).

De SSO-invoeg toepassing wordt automatisch geïnstalleerd door apparaten die de Microsoft Authenticator-app hebben gedownload op iOS-en iPadOS-of Intune-bedrijfsportal-app op macOS en hun apparaat bij uw organisatie hebben geregistreerd. Uw organisatie gebruikt de verificator-app waarschijnlijk vandaag voor scenario's zoals multi-factor Authentication, wacht woord-less-verificatie en voorwaardelijke toegang. Het kan worden ingeschakeld voor uw toepassingen met een MDM-provider, maar micro soft heeft het eenvoudig geconfigureerd in micro soft Endpoint Manager van intune. Een acceptatie lijst wordt gebruikt om deze toepassingen te configureren voor het gebruik van de SSO-invoeg toepassing.

>[!IMPORTANT]
> Alleen apps die systeem eigen Apple-netwerk technologieën of webweergave gebruiken, worden ondersteund. Als een toepassing een eigen implementatie van de netwerklaag levert, wordt de micro soft Enter prise SSO-invoeg toepassing niet ondersteund.  

Gebruik de volgende para meters voor het configureren van de micro soft Enter prise SSO-invoeg toepassing voor apps die geen micro soft Identity platform-bibliotheek gebruiken:

Als u een lijst met specifieke apps wilt opgeven:

- **Sleutel**: `AppAllowList`
- **Type**: `String`
- **Waarde**: een door komma's gescheiden lijst met toepassings bundel-id's voor de toepassingen die aan de SSO mogen deel nemen
- **Voor beeld**: `com.contoso.workapp, com.contoso.travelapp`

Of als u een lijst met voor voegsels wilt opgeven:
- **Sleutel**: `AppPrefixAllowList`
- **Type**: `String`
- **Waarde**: een door komma's gescheiden lijst met voor voegsels voor toepassings bundel-id's voor de toepassingen die deel kunnen uitmaken van de SSO. Houd er rekening mee dat hiermee alle apps worden gestart die beginnen met een bepaald voor voegsel om deel te nemen aan de SSO
- **Voor beeld**: `com.contoso., com.fabrikam.`

Toegestane [apps](./application-consent-experience.md) die door de MDM-beheerder zijn toegestaan om deel te nemen aan de SSO, kunnen een token ontvangen voor de eind gebruiker. Daarom is het belang rijk om alleen vertrouwde toepassingen toe te voegen aan de acceptatie lijst. 

>[!NOTE]
> U hoeft geen toepassingen toe te voegen die gebruikmaken van MSAL of ASWebAuthenticationSession aan deze lijst. Deze toepassingen zijn standaard ingeschakeld. 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>App-bundel-id's op iOS-apparaten detecteren

Apple biedt geen gemakkelijke manier om bundel-Id's uit de App Store te detecteren. De eenvoudigste manier om de bundel-Id's te detecteren van de apps die u wilt gebruiken voor eenmalige aanmelding, is door uw leverancier of app-ontwikkelaar te vragen. Als deze optie niet beschikbaar is, kunt u uw MDM-configuratie gebruiken om de bundel-Id's te ontdekken. 

Schakel de volgende vlag in uw MDM-configuratie tijdelijk in:

- **Sleutel**: `admin_debug_mode_enabled`
- **Type**: `Integer`
- **Waarde**: 1 of 0

Als deze vlag is ingeschakeld voor iOS-apps op het apparaat waarvoor u de bundel-ID wilt weten. Open vervolgens Microsoft Authenticator app-> Help-> logboeken verzenden-> logboeken weer geven. 

Zoek in het logboek bestand naar de volgende regel:

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

Hiermee worden alle toepassingen bundel-id's vastgelegd die zichtbaar zijn voor de SSO-extensie. U kunt deze id's vervolgens gebruiken voor het configureren van de SSO voor die apps. 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Gebruikers toestaan zich aan te melden bij onbekende toepassingen en de Safari-browser.

Standaard biedt de micro soft Enter prise SSO-invoeg toepassing alleen SSO voor geautoriseerde apps wanneer een gebruiker zich heeft aangemeld vanuit een app die gebruikmaakt van een micro soft Identity platform-bibliotheek, zoals ADAL of MSAL. De invoeg toepassing micro soft Enter prise SSO kan ook een gedeelde referentie verkrijgen als deze wordt aangeroepen door een andere app die gebruikmaakt van een micro soft Identity platform-bibliotheek tijdens het verkrijgen van een nieuwe token.

Als u een vlag inschakelt `browser_sso_interaction_enabled` , wordt een app die geen micro soft Identity platform-bibliotheek gebruikt om de eerste Boots trap ping uit te voeren en een gedeelde referentie ophalen. Daarnaast kunt u met de Safari-browser de eerste Boots trap uitvoeren en een gedeelde referentie ophalen. Als de micro soft Enter prise SSO-invoeg toepassing nog geen gedeelde referentie heeft, wordt er een opgehaald wanneer een aanmelding wordt aangevraagd vanuit een Azure AD-URL in de Safari-browser, ASWebAuthenticationSession, SafariViewController of een andere toegestane systeem eigen toepassing.  

- **Sleutel**: `browser_sso_interaction_enabled`
- **Type**: `Integer`
- **Waarde**: 1 of 0

Voor macOS is deze instelling vereist om een consistenter ervaring te krijgen in alle apps. Voor iOS en iPadOS is deze instelling niet vereist omdat de meeste apps gebruikmaken van de Microsoft Authenticator toepassing voor aanmelden. Als u echter een aantal toepassingen hebt die niet gebruikmaken van de Microsoft Authenticator op iOS of iPadOS, wordt de ervaring door deze vlag verbeterd. u wordt aangeraden om de instelling in te scha kelen. Deze optie is standaard uitgeschakeld.

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>Voor komen dat er MFA wordt gevraagd voor de eerste Boots trap ping

Standaard vraagt de micro soft Enter prise SSO-invoeg toepassing altijd de gebruiker om multi-factor Authentication (MFA) bij het uitvoeren van het eerste Boots trappen en ophalen van een gedeelde referentie, zelfs als deze niet vereist is voor de huidige toepassing die de gebruiker heeft gestart. Dit betekent dat de gedeelde referentie eenvoudig kan worden gebruikt in alle extra toepassingen zonder de gebruiker te vragen als MFA later nodig wordt. Dit reduceert de tijden dat de gebruiker op het apparaat moet worden gevraagd en is doorgaans een goed besluit.

Als u deze functie inschakelt, `browser_sso_disable_mfa` wordt de gebruiker alleen gevraagd wanneer MFA vereist is voor een toepassing of bron. 

- **Sleutel**: `browser_sso_disable_mfa`
- **Type**: `Integer`
- **Waarde**: 1 of 0

Houd er rekening mee dat deze vlag is uitgeschakeld, omdat hiermee de tijd die de gebruiker op het apparaat moet worden gevraagd, wordt gereduceerd. Als uw organisatie zelden MFA gebruikt, is het raadzaam om de vlag in te scha kelen, maar u wordt aangeraden MFA vaker te gebruiken. Daarom is het standaard uitgeschakeld.

#### <a name="disable-oauth2-application-prompts"></a>OAuth2-toepassings prompts uitschakelen

De micro soft Enter prise SSO-invoeg toepassing biedt SSO door gedeelde referenties toe te voegen aan netwerk aanvragen die afkomstig zijn van toegestane toepassingen. Het is echter mogelijk dat sommige OAuth2-toepassingen de prompts voor eind gebruikers onjuist afdwingen bij de laag van het protocol. Als dit het geval is, ziet u dat gedeelde referenties voor die apps worden genegeerd en dat uw gebruiker wordt gevraagd zich aan te melden, zelfs als de micro soft Enter prise SSO-invoeg toepassing werkt voor andere toepassingen.  

`disable_explicit_app_prompt`Als u deze optie inschakelt, wordt de mogelijkheid van zowel systeem eigen als webtoepassingen beperkt om de prompt van een eind gebruiker op de protocol-laag af te dwingen en SSO over te slaan.

- **Sleutel**: `disable_explicit_app_prompt`
- **Type**: `Integer`
- **Waarde**: 1 of 0

We raden u aan deze vlag in te scha kelen om consistenter te zijn voor alle apps. Deze optie is standaard uitgeschakeld. 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>Eenmalige aanmelding via cookies inschakelen voor een specifieke toepassing

Een klein aantal apps is mogelijk incompatibel met de SSO-extensie. In het bijzonder kunnen apps met geavanceerde netwerk instellingen onverwachte problemen ondervinden wanneer ze zijn ingeschakeld voor de SSO (bijvoorbeeld als er een fout wordt weer geven dat de netwerk aanvraag is geannuleerd of onderbroken). 

Als u problemen ondervindt bij het aanmelden met behulp van de methode die in de sectie wordt beschreven `Enable SSO for apps that don't use MSAL` , kunt u een alternatieve configuratie voor die apps proberen. 

Gebruik de volgende para meters voor het configureren van de micro soft Enter prise SSO-invoeg toepassing voor die specifieke apps:

- **Sleutel**: `AppCookieSSOAllowList`
- **Type**: `String`
- **Waarde**: een door komma's gescheiden lijst met voor voegsels voor toepassings bundel-id's voor de toepassingen die deel kunnen uitmaken van de SSO. Houd er rekening mee dat hiermee alle apps worden gestart die beginnen met een bepaald voor voegsel om deel te nemen aan de SSO
- **Voor beeld**: `com.contoso.myapp1, com.fabrikam.myapp2`

Houd er rekening mee dat toepassingen die zijn ingeschakeld voor de SSO met dit mechanisme, moeten worden toegevoegd aan de `AppCookieSSOAllowList` en `AppPrefixAllowList` .

U kunt deze optie het beste alleen proberen voor toepassingen die onverwachte aanmeldings fouten ondervinden. 

#### <a name="use-intune-for-simplified-configuration"></a>InTune gebruiken voor vereenvoudigde configuratie

Zoals eerder vermeld, kunt u Microsoft Intune als uw MDM-service gebruiken om de configuratie van de micro soft Enter prise SSO-invoeg toepassing te vereenvoudigen, inclusief het inschakelen van de invoeg toepassing en het toevoegen van uw oudere apps aan een lijst met toegestane apparaten, zodat ze SSO kunnen ophalen. Zie de [intune-configuratie documentatie](/intune/configuration/ios-device-features-settings)voor meer informatie.

## <a name="using-the-sso-plug-in-in-your-application"></a>De SSO-invoeg toepassing in uw toepassing gebruiken

De [micro soft Authentication Library (MSAL) voor Apple-apparaten](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versie 1.1.0 en hoger ondersteunt de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten. Het is de aanbevolen manier om ondersteuning toe te voegen voor de micro soft Enter prise SSO-invoeg toepassing en ervoor te zorgen dat u beschikt over de volledige mogelijkheden van het micro soft Identity-platform.

Als u een toepassing bouwt voor Frontline-werk scenario's, raadpleegt u de [modus voor gedeelde apparaten voor IOS-apparaten](msal-ios-shared-devices.md) voor meer informatie over het instellen van de functie.

## <a name="how-the-sso-plug-in-works"></a>Hoe de SSO-invoeg toepassing werkt

De micro soft Enter prise SSO-invoeg toepassing is gebaseerd op het [Single Sign-On Framework van Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Id-providers die de onboarding van het Framework mogelijk maken, kunnen netwerk verkeer voor hun domeinen onderscheppen en verbeteren of wijzigen hoe deze aanvragen worden afgehandeld. De SSO-invoeg toepassing kan bijvoorbeeld een extra gebruikers interface weer geven om referenties voor de eind gebruiker veilig te verzamelen, MFA vereist of op de achtergrond tokens voor de toepassing bieden.

Systeem eigen toepassingen kunnen ook aangepaste bewerkingen implementeren en rechtstreeks communiceren met de SSO-invoeg toepassing.
Meer informatie over het Framework voor eenmalige aanmelding vindt u in deze [2019 WWDC-video van Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Toepassingen die gebruikmaken van een micro soft Identity platform-bibliotheek

De [micro soft Authentication Library (MSAL) voor Apple-apparaten](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versie 1.1.0 en hoger ondersteunt de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten systeem eigen voor werk-en school accounts. 

Er is geen speciale configuratie nodig als u [alle aanbevolen stappen](./quickstart-v2-ios.md) hebt gevolgd en de standaard- [omleidings-URI-indeling](./redirect-uris-ios.md)hebt gebruikt. Wanneer u uitvoert op een apparaat waarop de SSO-invoeg toepassing is geïnstalleerd, wordt deze automatisch door MSAL aangeroepen voor alle interactieve-en Silent-token aanvragen, evenals account inventarisatie en het verwijderen van accounts. Omdat MSAL een systeem eigen SSO-invoeg toepassing implementeert dat afhankelijk is van aangepaste bewerkingen, biedt deze installatie de meest soepele systeem eigen ervaring voor de eind gebruiker. 

Als de SSO-invoeg toepassing niet is ingeschakeld op MDM, maar de Microsoft Authenticator-app aanwezig is op het apparaat, gebruikt MSAL in plaats daarvan de Microsoft Authenticator-app voor alle interactieve token aanvragen. Met de SSO-invoeg toepassing wordt SSO met de Microsoft Authenticator-app gedeeld.

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Toepassingen die geen micro soft Identity platform-bibliotheek gebruiken

Toepassingen die geen gebruikmaken van een micro soft Identity platform-bibliotheek, zoals MSAL, kunnen eenmalige aanmelding ophalen als een beheerder deze expliciet toevoegt aan de acceptatie lijst. 

Er zijn geen code wijzigingen nodig in deze apps zolang aan de volgende voor waarden wordt voldaan:

- Toepassing maakt gebruik van Apple frameworks om netwerk aanvragen uit te voeren (bijvoorbeeld [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- De toepassing gebruikt standaard protocollen om te communiceren met Azure AD (bijvoorbeeld OAuth2, SAML, WS-Federation)
- De gebruikers naam en het wacht woord van de Lees bare tekst worden niet in de systeem eigen gebruikers interface

In dit geval wordt SSO gegeven wanneer de toepassing een netwerk aanvraag maakt en een webbrowser opent om de gebruiker in te ondertekenen. Wanneer een gebruiker wordt omgeleid naar een aanmeldings-URL voor Azure AD, valideert de SSO-invoeg toepassing de URL en controleert deze of er een SSO-referentie beschikbaar is voor die URL. Als dat het geval is, geeft de SSO-invoeg toepassing de SSO-referentie door aan Azure AD, waarmee de toepassing wordt geautoriseerd om de netwerk aanvraag te volt ooien zonder de gebruiker te vragen hun referenties in te voeren. Als het apparaat wordt herkend door Azure AD, wordt door de SSO-invoeg toepassing ook het certificaat van het apparaat door gegeven om te voldoen aan de op apparaten gebaseerde controle van voorwaardelijke toegang. 

Ter ondersteuning van SSO voor niet-MSAL apps, implementeert de SSO-invoeg toepassing een protocol dat lijkt op de Windows-browser-invoeg toepassing die wordt beschreven in [Wat is een primair vernieuwings token?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Vergeleken met MSAL-apps, voert de SSO-invoeg toepassing op transparante wijze uit voor niet-MSAL-apps door te integreren met de bestaande browser aanmeldings ervaring die apps bieden. De eind gebruiker ziet hun vertrouwde ervaring, met het voor deel dat er geen aanvullende aanmeldingen hoeven te worden uitgevoerd in elk van de toepassingen. In plaats van de systeem eigen account kiezer weer te geven, voegt de SSO-invoeg toepassing SSO-sessies toe aan de ervaring op webgebaseerde account kiezer. 

## <a name="next-steps"></a>Volgende stappen

Zie [modus voor gedeeld apparaat voor IOS-apparaten](msal-ios-shared-devices.md)voor meer informatie over de modus gedeeld apparaat op Ios.
