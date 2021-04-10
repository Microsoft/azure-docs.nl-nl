---
title: Microsoft Enterprise SSO-invoegtoepassing voor Apple-apparaten
titleSuffix: Microsoft identity platform | Azure
description: Meer informatie over de Azure Active Directory SSO-invoeg toepassing voor iOS-, iPadOS-en macOS-apparaten.
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
ms.openlocfilehash: 05bfcc86c72d9eb393da919035ce198948b943f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559125"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten (preview-versie)

>[!IMPORTANT]
> Deze functie [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

De *micro soft Enter PRISE SSO-invoeg toepassing voor Apple-apparaten* biedt eenmalige aanmelding (SSO) voor Azure Active Directory (Azure AD)-accounts in MacOS, Ios en iPadOS in alle toepassingen die ondersteuning bieden voor de functie voor [eenmalige aanmelding](https://developer.apple.com/documentation/authenticationservices) van Apple. De-invoeg toepassing biedt SSO voor zelfs oude toepassingen die uw bedrijf mogelijk afhankelijk maakt, maar die nog geen ondersteuning bieden voor de nieuwste identiteits bibliotheken of-protocollen. Micro soft werkte nauw samen met Apple bij het ontwikkelen van deze invoeg toepassing om de gebruiks vriendelijkheid van uw toepassing te verg Roten en de beste beveiliging beschikbaar te stellen.

De Enter prise SSO-invoeg toepassing is momenteel een ingebouwde functie van de volgende apps:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): IOS, iPadOS
* Microsoft Intune [bedrijfsportal](/mem/intune/apps/apps-company-portal-macos): macOS

## <a name="features"></a>Functies

De micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten biedt de volgende voor delen:

- Het biedt eenmalige aanmelding voor Azure AD-accounts voor alle toepassingen die ondersteuning bieden voor de functie van Apple Enter prise SSO.
- Het kan worden ingeschakeld door elke Mobile Device Management (MDM)-oplossing.
- Hiermee wordt SSO uitgebreid naar toepassingen die nog geen gebruikmaken van micro soft-identiteits platform bibliotheken.
- Hiermee wordt SSO uitgebreid naar toepassingen die gebruikmaken van OAuth 2, OpenID Connect Connect en SAML.

## <a name="requirements"></a>Vereisten

Als u de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten wilt gebruiken:

- Het apparaat moet *ondersteuning bieden* voor en beschikken over een geïnstalleerde app die de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten heeft:
  - iOS 13,0 en hoger: [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13,0 en hoger: [Microsoft Authenticator-app](../user-help/user-help-auth-app-overview.md)
  - macOS 10,15 en hoger: [intune-bedrijfsportal-app](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- Het apparaat moet worden *Inge schreven bij MDM*, bijvoorbeeld via Microsoft intune.
- De configuratie moet worden *gepusht naar het apparaat* om de SSO-invoeg toepassing voor Enter prise in te scha kelen. Apple vereist deze beveiligings beperking.

### <a name="ios-requirements"></a>iOS-vereisten:
- iOS 13,0 of hoger moet op het apparaat zijn geïnstalleerd.
- Een micro soft-toepassing die de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten levert, moet op het apparaat zijn geïnstalleerd. Voor een open bare preview zijn deze toepassingen de [app Microsoft Authenticator](/azure/active-directory/user-help/user-help-auth-app-overview).


### <a name="macos-requirements"></a>macOS-vereisten:
- macOS 10,15 of hoger moet op het apparaat zijn geïnstalleerd. 
- Een micro soft-toepassing die de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten levert, moet op het apparaat zijn geïnstalleerd. Voor een open bare preview omvatten deze toepassingen de [app intune-bedrijfsportal](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp).

## <a name="enable-the-sso-plug-in"></a>De SSO-invoeg toepassing inschakelen

Gebruik de volgende informatie om de SSO-invoeg toepassing in te scha kelen met behulp van MDM.
### <a name="microsoft-intune-configuration"></a>Microsoft Intune configuratie

Als u Microsoft Intune gebruikt als uw MDM-service, kunt u de ingebouwde configuratie Profiel instellingen gebruiken om de micro soft Enter prise SSO-invoeg toepassing in te scha kelen:

1. Configureer de [SSO-app-extensie](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) -instellingen van een configuratie profiel. 
1. Als het profiel nog niet is toegewezen, [moet u het profiel toewijzen aan een gebruiker of Apparaatgroep](/mem/intune/configuration/device-profile-assign).

De profiel instellingen die de SSO-invoeg toepassing inschakelen, worden automatisch toegepast op de apparaten van de groep bij de volgende keer dat elk apparaat wordt ingecheckt met intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Hand matige configuratie voor andere MDM-Services

Als u intune niet gebruikt voor MDM, gebruikt u de volgende para meters om de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten te configureren.

iOS-instellingen:

- **Extensie-id**: `com.microsoft.azureauthenticator.ssoextension`
- **Team-ID**: dit veld is niet nodig voor IOS.

macOS-instellingen:

- **Extensie-id**: `com.microsoft.CompanyPortalMac.ssoextension`
- **Team-ID**: `UBF8T346G9`

Algemene instellingen:

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
  
### <a name="more-configuration-options"></a>Meer configuratie opties
U kunt meer configuratie opties toevoegen om SSO-functionaliteit uit te breiden naar andere apps.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Eenmalige aanmelding inschakelen voor apps die geen micro soft Identity platform-bibliotheek gebruiken

De SSO-invoeg toepassing maakt het mogelijk om deel te nemen aan SSO, zelfs als dit niet is ontwikkeld met behulp van een micro soft-SDK zoals micro soft-verificatie bibliotheek (MSAL).

De SSO-invoeg toepassing wordt automatisch geïnstalleerd door apparaten met:
* De verificator-app is gedownload op iOS of iPadOS, of u hebt de Intune-bedrijfsportal-app gedownload in macOS.
* Hun apparaat bij uw organisatie heeft geregistreerd. 

Uw organisatie gebruikt waarschijnlijk de verificator-app voor scenario's zoals multi-factor Authentication (MFA), authenticatie op basis van wacht woorden en voorwaardelijke toegang. U kunt de SSO-invoeg toepassing voor uw toepassingen inschakelen met behulp van een MDM-provider. Micro soft heeft het eenvoudig om de invoeg toepassing in micro soft Endpoint Manager te configureren in intune. Een allowlist wordt gebruikt om deze toepassingen te configureren voor het gebruik van de SSO-invoeg toepassing.

>[!IMPORTANT]
> De invoeg toepassing micro soft Enter prise SSO ondersteunt alleen apps die gebruikmaken van systeem eigen Apple-netwerk technologieën of webweergave. Toepassingen die hun eigen netwerklaag implementatie verzenden, worden niet ondersteund.  

Gebruik de volgende para meters voor het configureren van de micro soft Enter prise SSO-invoeg toepassing voor apps die geen micro soft Identity platform-bibliotheek gebruiken.

Als u een lijst met specifieke apps wilt opgeven, gebruikt u deze para meters:

- **Sleutel**: `AppAllowList`
- **Type**: `String`
- **Waarde**: een door komma's gescheiden lijst met toepassings bundel-id's voor de toepassingen die mogen deel nemen aan SSO.
- **Voor beeld**: `com.contoso.workapp, com.contoso.travelapp`

Als u een lijst met voor voegsels wilt opgeven, gebruikt u deze para meters:
- **Sleutel**: `AppPrefixAllowList`
- **Type**: `String`
- **Waarde**: een door komma's gescheiden lijst met voor voegsels van Application Bundel-id's voor de toepassingen die aan SSO mogen deel nemen. Met deze para meter kunnen alle apps die beginnen met een bepaald voor voegsel, deel nemen aan SSO.
- **Voor beeld**: `com.contoso., com.fabrikam.`

Toegestane [apps](./application-consent-experience.md) die de MDM-beheerder in staat stelt deel te nemen aan SSO, kunnen een token ontvangen voor de eind gebruiker. Voeg dus alleen vertrouwde toepassingen toe aan de allowlist. 

>[!NOTE]
> U hoeft geen toepassingen die gebruikmaken van MSAL of ASWebAuthenticationSession, toe te voegen aan de lijst met apps die kunnen deel nemen aan SSO. Deze toepassingen zijn standaard ingeschakeld. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>App-bundel-id's zoeken op iOS-apparaten

Apple biedt geen eenvoudige manier om bundel-Id's uit de App Store op te halen. De eenvoudigste manier om de bundel-Id's op te halen van de apps die u wilt gebruiken voor eenmalige aanmelding, is door uw leverancier of app-ontwikkelaar te vragen. Als deze optie niet beschikbaar is, kunt u uw MDM-configuratie gebruiken om de bundel-Id's te vinden: 

1. Schakel tijdelijk de volgende vlag in voor uw MDM-configuratie:

    - **Sleutel**: `admin_debug_mode_enabled`
    - **Type**: `Integer`
    - **Waarde**: 1 of 0
1. Als deze vlag is ingeschakeld, meldt u zich aan bij iOS-apps op het apparaat waarvoor u de bundel-ID wilt weten. 
1. Selecteer in de verificator-app **Help**-  >  **Logboeken**  >  **voor** het verzenden van Logboeken. 
1. Zoek in het logboek bestand naar de volgende regel: `[ADMIN MODE] SSO extension has captured following app bundle identifiers` . Op deze regel moeten alle toepassings bundel-Id's worden vastgelegd die zichtbaar zijn voor de SSO-extensie. 

Gebruik de bundel-Id's voor het configureren van SSO voor de apps. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Gebruikers toestaan zich aan te melden bij onbekende toepassingen en de Safari-browser

Standaard biedt de micro soft Enter prise SSO-invoeg toepassing alleen SSO voor geautoriseerde apps wanneer een gebruiker zich heeft aangemeld vanuit een app die gebruikmaakt van een micro soft Identity platform-bibliotheek, zoals MSAL of Azure Active Directory Authentication Library (ADAL). De invoeg toepassing micro soft Enter prise SSO kan ook een gedeelde referentie verkrijgen als deze wordt aangeroepen door een andere app die gebruikmaakt van een micro soft Identity platform-bibliotheek tijdens een nieuwe verwerving van tokens.

Wanneer u de vlag inschakelt `browser_sso_interaction_enabled` , kunnen apps die geen gebruikmaken van een micro soft Identity platform-bibliotheek de eerste Boots trap pingen en een gedeelde referentie ophalen. De Safari-browser kan ook de eerste Boots trap ping uitvoeren en een gedeelde referentie ophalen. 

Als de micro soft Enter prise SSO-invoeg toepassing nog geen gedeelde referentie heeft, wordt er een opgehaald wanneer een aanmelding wordt aangevraagd vanuit een Azure AD-URL in de Safari-browser, ASWebAuthenticationSession, SafariViewController of een andere toegestane systeem eigen toepassing. 

Gebruik deze para meters om de vlag in te scha kelen: 

- **Sleutel**: `browser_sso_interaction_enabled`
- **Type**: `Integer`
- **Waarde**: 1 of 0

macOS vereist deze instelling zodat het een consistente ervaring kan bieden voor alle apps. voor iOS en iPadOS is deze instelling niet vereist omdat de meeste apps de verificator-toepassing gebruiken voor aanmelden. Het wordt echter aangeraden deze instelling in te scha kelen omdat als sommige van uw toepassingen de verificator-app niet op iOS of iPadOS gebruiken, de ervaring wordt verbeterd met deze vlag. De instelling is standaard uitgeschakeld.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>Uitschakelen van de vraag naar MFA tijdens de eerste Boots trap

Standaard vraagt de micro soft Enter prise SSO-invoeg toepassing de gebruiker altijd om MFA tijdens de eerste Boots trap ping en tijdens het ophalen van een gedeelde referentie. De gebruiker wordt gevraagd om MFA, zelfs als deze niet vereist is voor de toepassing die de gebruiker heeft geopend. Dit gedrag zorgt ervoor dat de gedeelde referentie eenvoudig kan worden gebruikt in alle andere toepassingen, zonder de nood zaak om de gebruiker te vragen als MFA later vereist is. Omdat de gebruiker minder vragen krijgt, is deze instelling in het algemeen een goed besluit.

Als u inschakelen inschakelt `browser_sso_disable_mfa` , wordt MFA uitgeschakeld tijdens de eerste Boots trap ping en tijdens het ophalen van de gedeelde referenties. In dit geval wordt de gebruiker alleen gevraagd wanneer MFA vereist is voor een toepassing of bron. 

Als u de vlag wilt inschakelen, gebruikt u deze para meters:

- **Sleutel**: `browser_sso_disable_mfa`
- **Type**: `Integer`
- **Waarde**: 1 of 0

U wordt aangeraden deze vlag uit te scha kelen, omdat hiermee het aantal keren dat de gebruiker wordt gevraagd om zich aan te melden, wordt gereduceerd. Als uw organisatie zelden gebruikmaakt van MFA, is het raadzaam om de vlag in te scha kelen. We raden u echter aan om in plaats hiervan vaker MFA te gebruiken. Daarom is de markering standaard uitgeschakeld.

#### <a name="disable-oauth-2-application-prompts"></a>Vragen om OAuth 2-toepassingen uitschakelen

De micro soft Enter prise SSO-invoeg toepassing biedt SSO door gedeelde referenties toe te voegen aan netwerk aanvragen die afkomstig zijn van toegestane toepassingen. Het is echter mogelijk dat sommige OAuth 2-toepassingen de prompts voor eind gebruikers op de laag van het protocol onjuist afdwingen. Als u dit probleem ziet, ziet u ook dat gedeelde referenties voor die apps worden genegeerd. Uw gebruiker wordt gevraagd zich aan te melden, zelfs als de micro soft Enter prise SSO-invoeg toepassing werkt voor andere toepassingen.  

Het inschakelen `disable_explicit_app_prompt` van de vlag beperkt de mogelijkheid van zowel systeem eigen toepassingen als webtoepassingen om de prompt van een eind gebruiker op de protocol-laag af te dwingen en SSO over te slaan. Als u de vlag wilt inschakelen, gebruikt u deze para meters:

- **Sleutel**: `disable_explicit_app_prompt`
- **Type**: `Integer`
- **Waarde**: 1 of 0

We raden u aan deze vlag in te scha kelen voor een consistente ervaring in alle apps. Het is standaard uitgeschakeld. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Eenmalige aanmelding via cookies inschakelen voor een specifieke toepassing

Enkele apps zijn mogelijk niet compatibel met de SSO-extensie. In het bijzonder kunnen apps met geavanceerde netwerk instellingen onverwachte problemen ondervinden wanneer ze zijn ingeschakeld voor SSO. Zo ziet u mogelijk een fout bericht dat aangeeft dat de netwerk aanvraag is geannuleerd of onderbroken. 

Als u problemen ondervindt met het aanmelden met behulp van de methode die wordt beschreven in de sectie [toepassingen die geen gebruik maken van MSAL](#applications-that-dont-use-msal) , kunt u een alternatieve configuratie proberen. Gebruik deze para meters voor het configureren van de invoeg toepassing:

- **Sleutel**: `AppCookieSSOAllowList`
- **Type**: `String`
- **Waarde**: een door komma's gescheiden lijst met voor voegsels voor toepassings bundel-id's voor de toepassingen die deel kunnen uitmaken van de SSO. Alle apps die beginnen met de vermelde voor voegsels, kunnen deel nemen aan SSO.
- **Voor beeld**: `com.contoso.myapp1, com.fabrikam.myapp2`

Toepassingen die zijn ingeschakeld voor de SSO met behulp van deze installatie, moeten worden toegevoegd aan zowel `AppCookieSSOAllowList` en `AppPrefixAllowList` .

Probeer deze configuratie alleen uit voor toepassingen die onverwachte aanmeldings fouten hebben. 

#### <a name="use-intune-for-simplified-configuration"></a>InTune gebruiken voor vereenvoudigde configuratie

U kunt intune als uw MDM-service gebruiken om de configuratie van de micro soft Enter prise SSO-invoeg toepassing te vereenvoudigen. U kunt bijvoorbeeld intune gebruiken om de invoeg toepassing in te scha kelen en oude apps toe te voegen aan een allowlist, zodat ze SSO ophalen. 

Zie de [intune-configuratie documentatie](/intune/configuration/ios-device-features-settings)voor meer informatie.

## <a name="use-the-sso-plug-in-in-your-application"></a>De SSO-invoeg toepassing in uw toepassing gebruiken

[MSAL voor Apple-apparaten](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versie 1.1.0 en hoger ondersteunt de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten. Het is de aanbevolen manier om ondersteuning toe te voegen voor de micro soft Enter prise SSO-invoeg toepassing. Zo kunt u profiteren van de volledige mogelijkheden van het micro soft Identity-platform.

Als u een toepassing bouwt voor Frontline-werk scenario's, raadpleegt u de [modus voor gedeeld apparaat voor IOS-apparaten](msal-ios-shared-devices.md) voor informatie over de installatie.

## <a name="understand-how-the-sso-plug-in-works"></a>Meer informatie over de werking van de SSO-invoeg toepassing

De micro soft Enter prise SSO-invoeg toepassing is afhankelijk van het [Apple Enter PRISE SSO-Framework](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Id-providers die lid worden van het Framework kunnen netwerk verkeer voor hun domeinen onderscheppen en verbeteren of wijzigen hoe deze aanvragen worden afgehandeld. De SSO-invoeg toepassing kan bijvoorbeeld meer UIs weer geven om referenties voor de eind gebruiker veilig te verzamelen, MFA vereist of op de achtergrond tokens levert aan de toepassing.

Systeem eigen toepassingen kunnen ook aangepaste bewerkingen implementeren en rechtstreeks communiceren met de SSO-invoeg toepassing. Meer informatie vindt u in deze [2019 wereld wijde video over Developer conferentie van Apple](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>Toepassingen die gebruikmaken van MSAL

[MSAL voor Apple-apparaten](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versie 1.1.0 en hoger ondersteunt de micro soft Enter prise SSO-invoeg toepassing voor Apple-apparaten systeem eigen voor werk-en school accounts. 

U hebt geen speciale configuratie nodig als u [alle aanbevolen stappen](./quickstart-v2-ios.md) hebt gevolgd en de standaard- [omleidings-URI-indeling](./redirect-uris-ios.md)hebt gebruikt. Op apparaten met de SSO-invoeg toepassing roept MSAL automatisch aan voor alle interactieve-en Silent-token aanvragen. Er wordt ook een aanroep uitgevoerd voor het inventariseren van accounts en het verwijderen van accounts. Omdat MSAL een systeem eigen SSO-invoeg toepassing implementeert dat afhankelijk is van aangepaste bewerkingen, biedt deze installatie de meest soepele systeem eigen ervaring voor de eind gebruiker. 

Als de SSO-invoeg toepassing niet is ingeschakeld door MDM, maar de Microsoft Authenticator-app aanwezig is op het apparaat, gebruikt MSAL in plaats daarvan de verificator-app voor alle interactieve token aanvragen. De SSO-invoeg toepassing deelt SSO met de verificator-app.

### <a name="applications-that-dont-use-msal"></a>Toepassingen die geen gebruikmaken van MSAL

Toepassingen die geen gebruikmaken van een micro soft Identity platform-bibliotheek, zoals MSAL, kunnen nog steeds SSO ophalen als een beheerder deze toepassingen toevoegt aan de allowlist. 

U hoeft de code in die apps niet te wijzigen, zolang aan de volgende voor waarden wordt voldaan:

- De toepassing maakt gebruik van Apple frameworks om netwerk aanvragen uit te voeren. Deze frameworks bevatten bijvoorbeeld [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) en [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession). 
- De toepassing gebruikt standaard protocollen om te communiceren met Azure AD. Deze protocollen omvatten bijvoorbeeld OAuth 2, SAML en WS-Federation.
- De toepassing verzamelt geen gebruikers namen en wacht woorden met lees bare tekst in de systeem eigen gebruikers interface.

In dit geval wordt SSO gegeven wanneer de toepassing een netwerk aanvraag maakt en een webbrowser opent om de gebruiker in te ondertekenen. Wanneer een gebruiker wordt omgeleid naar een aanmeldings-URL voor Azure AD, valideert de SSO-invoeg toepassing de URL en controleert deze op een SSO-referentie voor die URL. Als de referentie wordt gevonden, wordt deze door de SSO-invoeg toepassing door gegeven aan Azure AD, waardoor de toepassing de netwerk aanvraag kan volt ooien zonder dat de gebruiker wordt gevraagd om referenties in te voeren. Als het apparaat ook bekend is bij Azure AD, stuurt de SSO-invoeg toepassing het certificaat van het apparaat door om te voldoen aan de op apparaten gebaseerde controle van voorwaardelijke toegang. 

Ter ondersteuning van SSO voor niet-MSAL apps, implementeert de SSO-invoeg toepassing een protocol dat lijkt op de Windows-browser-invoeg toepassing die wordt beschreven in [Wat is een primair vernieuwings token?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Vergeleken met MSAL-apps, fungeert de SSO-invoeg toepassing transparant voor niet-MSAL-apps. Het wordt geïntegreerd met de bestaande browser aanmelding die apps bieden. 

De eind gebruiker ziet de bekende ervaring en hoeft zich niet opnieuw aan te melden bij elke toepassing. In plaats van de systeem eigen account kiezer weer te geven, voegt de SSO-invoeg toepassing SSO-sessies toe aan de ervaring op webgebaseerde account kiezer. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [modus voor gedeelde apparaten voor IOS-apparaten](msal-ios-shared-devices.md).
