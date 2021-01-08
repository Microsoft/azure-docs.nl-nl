---
title: SSO van meerdere apps inschakelen op Android met behulp van MSAL | Azure
titleSuffix: Microsoft identity platform
description: Micro soft Authentication Library (MSAL) voor Android gebruiken om eenmalige aanmelding in te scha kelen voor uw toepassingen.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: c4c98ad377100c35b0c364607bfd3803d07a95a7
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015926"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Procedure: cross-app SSO inschakelen op Android met behulp van MSAL

Eenmalige aanmelding (SSO) stelt gebruikers in staat om hun referenties één keer in te voeren en deze referenties automatisch te laten werken in toepassingen.

Met het [micro soft Identity-platform](./index.yml) en de micro soft Authentication Library (MSAL) kunt u SSO inschakelen in uw eigen app-pakket. Met de functies van de Broker en de verificator kunt u SSO uitbreiden over het hele apparaat.

In deze procedure leert u hoe u de Sdk's kunt configureren die door uw toepassing worden gebruikt om eenmalige aanmelding te bieden aan uw klanten.

## <a name="prerequisites"></a>Vereisten

In deze procedure wordt ervan uitgegaan dat u weet hoe u:

- Richt uw app in met behulp van de Azure Portal. Zie de instructies voor het maken van een app in [de Android-zelf studie](./tutorial-v2-android.md#create-a-project) voor meer informatie over dit onderwerp.
- Integreer uw toepassing met de [micro soft-verificatie bibliotheek voor Android](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Methoden voor eenmalige aanmelding

Er zijn twee manieren voor toepassingen die gebruikmaken van MSAL voor Android om SSO te garanderen:

* Via een [Broker-toepassing](#sso-through-brokered-authentication)
* Via de [systeem browser](#sso-through-system-browser)


   Het is raadzaam om een Broker-toepassing te gebruiken voor voor delen, zoals SSO van het apparaat, account beheer en voorwaardelijke toegang. Het vereist echter dat uw gebruikers extra toepassingen downloaden.

## <a name="sso-through-brokered-authentication"></a>Eenmalige aanmelding via brokered-verificatie

U kunt het beste een van de micro soft-verificatie-Brokers gebruiken om deel te nemen aan SSO (single sign-on) voor het hele apparaat en om te voldoen aan het beleid voor voorwaardelijke toegang van de organisatie. Integreren met een Broker biedt de volgende voor delen:

- Eenmalige aanmelding voor het apparaat
- Voorwaardelijke toegang voor:
  - Intune-app-beveiliging
  - Apparaatregistratie (Workplace Join)
  - Beheer van mobiele apparaten
- Account beheer op het hele apparaat
  -  via Android AccountManager &-account instellingen
  - "Werk account"-aangepast account type

Op Android is de micro soft Authentication Broker een onderdeel dat is opgenomen in de [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) -en [intune-bedrijfsportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) -apps.

In het volgende diagram ziet u de relatie tussen uw app, de micro soft Authentication Library (MSAL) en de verificatie-Brokers van micro soft.

![Diagram waarin wordt getoond hoe een toepassing is gekoppeld aan MSAL, Broker-apps en de Android-account beheerder.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Apps installeren die een Broker hosten

Apps met Broker-hosting kunnen op elk gewenst moment worden geïnstalleerd door de eigenaar van het apparaat vanuit hun App Store (meestal Google Play Store). Sommige Api's (bronnen) worden echter beschermd door beleids regels voor voorwaardelijke toegang waarvoor apparaten zijn vereist:

- Geregistreerd (toegevoegd aan werk plek) en/of
- Geregistreerd in Apparaatbeheer of
- Inge schreven bij Intune-app-beveiliging

Als op een apparaat nog geen Broker-app is geïnstalleerd, geeft MSAL de gebruiker de opdracht om er een te installeren zodra de app interactief een token probeert op te halen. De app moet de gebruiker vervolgens door geven aan de hand van de stappen om het apparaat compatibel te maken met het vereiste beleid.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Gevolgen van het installeren en verwijderen van een Broker

#### <a name="when-a-broker-is-installed"></a>Wanneer een Broker is geïnstalleerd

Wanneer een Broker op een apparaat is geïnstalleerd, worden alle volgende interactieve token aanvragen (aanroepen naar `acquireToken()` ) afgehandeld door de broker in plaats van lokaal door MSAL. Elke SSO-status die eerder beschikbaar is voor MSAL, is niet beschikbaar voor de Broker. Als gevolg hiervan moet de gebruiker zich opnieuw verifiëren of een account selecteren uit de bestaande lijst met accounts die bekend zijn bij het apparaat.

Als u een Broker installeert, hoeft de gebruiker zich niet opnieuw aan te melden. Alleen als de gebruiker een moet omzetten `MsalUiRequiredException` , gaat de volgende aanvraag naar de Broker. `MsalUiRequiredException` kan om verschillende redenen worden gegenereerd en moet interactief worden opgelost. Bijvoorbeeld:

- De gebruiker heeft het wacht woord gewijzigd dat is gekoppeld aan het account.
- Het account van de gebruiker voldoet niet meer aan het beleid voor voorwaardelijke toegang.
- De gebruiker heeft de toestemming ingetrokken om de app te koppelen aan hun account.

**Meerdere Brokers** : als er meerdere brokers zijn geïnstalleerd op een apparaat, is de broker die als eerste is geïnstalleerd altijd de actieve Broker. Er kan slechts één Broker actief zijn op een apparaat.

#### <a name="when-a-broker-is-uninstalled"></a>Wanneer een Broker wordt verwijderd

Als er slechts één app voor het hosten van Broker is geïnstalleerd en deze is verwijderd, moet de gebruiker zich opnieuw aanmelden. Als u de actieve Broker verwijdert, worden het account en de bijbehorende tokens van het apparaat verwijderd.

Als Intune-bedrijfsportal is geïnstalleerd en als de actieve Broker fungeert, en Microsoft Authenticator ook is geïnstalleerd, moet de gebruiker zich opnieuw aanmelden als de Intune-bedrijfsportal (actieve Broker) is verwijderd. Zodra de gebruiker zich opnieuw aanmeldt, wordt de app Microsoft Authenticator de actieve Broker.

### <a name="integrating-with-a-broker"></a>Integreren met een Broker

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Een omleidings-URI voor een Broker genereren

U moet een omleidings-URI registreren die compatibel is met de Broker. De omleidings-URI voor de Broker moet de pakket naam van uw app en de door base64 gecodeerde weer gave van de hand tekening van uw app bevatten.

De indeling van de omleidings-URI is: `msauth://<yourpackagename>/<base64urlencodedsignature>`

U kunt met het [hulp programma](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) Keys een met base64 gecodeerde hand tekening-hash genereren met behulp van de handtekening sleutels van uw app en vervolgens de Azure Portal gebruiken om de omleidings-URI te genereren met behulp van die hash.

Linux en macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Nadat u een hand tekening-hash hebt gegenereerd met het *hulp programma*, gebruikt u de Azure Portal om de omleidings-URI te genereren:

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">de <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a> en selecteer uw Android-app in **app-registraties**.
1. Selecteer **verificatie**  >  **een platform**  >  **Android** toevoegen.
1. In het deel venster **uw Android-app configureren** dat wordt geopend, voert u de **hand tekening-hash** in die u eerder hebt gegenereerd en een **pakket naam**.
1. Selecteer de knop **configureren** .

De Azure Portal genereert de omleidings-URI voor u en wordt weer gegeven in het veld **omleidings-URI** van het configuratie deel venster van **Android** .

Zie [uw app ondertekenen](https://developer.android.com/studio/publish/app-signing) in de gebruikers handleiding van Android Studio voor meer informatie over het ondertekenen van uw app.

> [!IMPORTANT]
> Gebruik de handtekening sleutel van uw productie voor de productie versie van uw app.

#### <a name="configure-msal-to-use-a-broker"></a>MSAL configureren voor het gebruik van een Broker

Als u een Broker in uw app wilt gebruiken, moet u bevestigen dat u de omleiding van de Broker hebt geconfigureerd. Neem bijvoorbeeld zowel de door Broker ingeschakelde omleidings-URI op, en geef aan dat u deze hebt geregistreerd, door de volgende instellingen op te nemen in uw MSAL-configuratie bestand:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Uitzonde ringen die betrekking hebben op Broker

MSAL communiceert met de Broker op twee manieren:

- Broker gebonden service
- Android-AccountManager

MSAL maakt eerst gebruik van de Broker-gebonden service omdat het aanroepen van deze service geen Android-machtigingen vereist. Als binding met de gebonden service mislukt, gebruikt MSAL de Android AccountManager-API. MSAL doet dit alleen als de machtiging al is toegekend aan uw app `"READ_CONTACTS"` .

Als er een met de fout code wordt weer geven `MsalClientException` `"BROKER_BIND_FAILURE"` , zijn er twee opties:

- Vraag de gebruiker om energie optimalisatie uit te scha kelen voor de Microsoft Authenticator-app en de Intune-bedrijfsportal.
- De gebruiker vragen om de machtiging te verlenen `"READ_CONTACTS"`

### <a name="verify-broker-integration"></a>Broker-integratie controleren

Het is mogelijk niet onmiddellijk duidelijk dat de integratie van de Broker werkt, maar u kunt de volgende stappen gebruiken om te controleren:

1. Voltooi op uw Android-apparaat een aanvraag met behulp van de Broker.
1. Zoek in de instellingen op uw Android-apparaat naar een nieuw gemaakt account dat overeenkomt met het account waarmee u bent geverifieerd. Het account moet van het type *work-account* zijn.

U kunt het account uit instellingen verwijderen als u de test wilt herhalen.

## <a name="sso-through-system-browser"></a>Eenmalige aanmelding via de systeem browser

Android-toepassingen hebben de mogelijkheid om de aangepaste tabbladen webweergave, systeem browser of Chrome te gebruiken voor de gebruikers ervaring voor authenticatie. Als de toepassing geen brokered-verificatie gebruikt, moet de systeem browser worden gebruikt in plaats van de native webweergave om SSO te kunnen gebruiken.

### <a name="authorization-agents"></a>Machtigingsagenten

Het kiezen van een specifieke strategie voor autorisatie agenten is optioneel en vertegenwoordigt extra functionaliteits-apps die kunnen worden aangepast. De meeste apps gebruiken de standaard instellingen voor MSAL (Zie [het configuratie bestand van de ANDROID MSAL begrijpen](msal-configuration.md) voor een overzicht van de verschillende standaard waarden).

MSAL ondersteunt autorisatie met behulp van een `WebView` -of de systeem browser.  In de onderstaande afbeelding ziet u hoe deze eruitziet met de `WebView` of de systeem browser met CustomTabs of zonder CustomTabs:

![Voor beelden van MSAL-aanmelding](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Implicaties van eenmalige aanmelding

Toepassingen die zijn geïntegreerd met MSAL gebruiken standaard de aangepaste tabbladen van de systeem browser om te autoriseren. In tegens telling tot webweergaven delen aangepaste tabbladen een cookie jar met de standaard systeem browser om minder aanmeldingen mogelijk te maken met web of andere systeem eigen apps die zijn geïntegreerd met aangepaste tabbladen.

Als de toepassing gebruikmaakt van een `WebView` strategie zonder Microsoft Authenticator of bedrijfsportal ondersteuning in de app te integreren, hebben gebruikers geen eenmalige aanmelding op het apparaat of tussen systeem eigen apps en web-apps.

Als de toepassing MSAL gebruikt met een Broker als Microsoft Authenticator of Intune-bedrijfsportal, kunnen gebruikers een SSO-ervaring hebben voor toepassingen als ze een actieve aanmelding hebben met een van de apps.

### <a name="webview"></a>WebView

Als u de webweergave in-app wilt gebruiken, plaatst u de volgende regel in de JSON van de app-configuratie die wordt door gegeven aan MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Wanneer u de in-app gebruikt `WebView` , wordt de gebruiker rechtstreeks aangemeld bij de app. De tokens worden bewaard in de sandbox van de app en zijn niet beschikbaar buiten het cookie jar van de app. Als gevolg hiervan kan de gebruiker geen SSO-ervaring hebben voor toepassingen, tenzij de apps worden geïntegreerd met de verificator of Bedrijfsportal.

Biedt echter `WebView` de mogelijkheid om het uiterlijk aan te passen aan de gebruikers interface voor aanmelden. Zie [Android-webviewers](https://developer.android.com/reference/android/webkit/WebView) voor meer informatie over hoe u deze aanpassing kunt uitvoeren.

### <a name="default-browser-plus-custom-tabs"></a>Standaard browser plus aangepaste tabbladen

MSAL maakt standaard gebruik van de browser en een strategie voor [aangepaste tabbladen](https://developer.chrome.com/multidevice/android/customtabs) . U kunt deze strategie expliciet aanduiden om te voor komen dat toekomstige releases worden gewijzigd met `DEFAULT` behulp van de volgende JSON-configuratie in het aangepaste configuratie bestand:

```json
"authorization_user_agent" : "BROWSER"
```

Gebruik deze methode om een SSO-ervaring te bieden via de browser van het apparaat. MSAL maakt gebruik van een gedeeld cookie jar, waarmee andere systeem eigen apps of web-apps SSO kunnen bezorgen op het apparaat met behulp van de permanente sessie cookie die door MSAL is ingesteld.

### <a name="browser-selection-heuristic"></a>Selectie heuristiek van browser

Omdat het niet mogelijk is om het exacte browser pakket op te geven dat moet worden gebruikt voor elk van de brede matrix van Android-telefoons, wordt door MSAL een MSAL geïmplementeerd die de beste SSO van meerdere apparaten probeert te bieden.

MSAL haalt de standaard browser voornamelijk uit de package manager en controleert of deze zich in een geteste lijst met veilige browsers bevindt. Als dat niet het geval is, gaat MSAL terug naar het gebruik van de webweergave in plaats van een andere niet-standaard browser vanuit de veilige lijst te starten. De standaard browser wordt gekozen, ongeacht of deze aangepaste tabbladen ondersteunt. Als de browser aangepaste tabbladen ondersteunt, wordt het aangepaste tabblad in MSAL geopend. aangepaste tabbladen hebben een uiterlijke en `WebView` eenvoudige aanpassing van de gebruikers interface. Zie [aangepaste tabbladen in Android](https://developer.chrome.com/multidevice/android/customtabs) voor meer informatie.

Als er geen browser pakketten aanwezig zijn op het apparaat, maakt MSAL gebruik van de in-app `WebView` . Als de standaard instelling van het apparaat niet wordt gewijzigd, moet dezelfde browser worden gestart voor elke aanmelding om te zorgen voor een SSO-ervaring.

#### <a name="tested-browsers"></a>Geteste browsers

De volgende browsers zijn getest om te zien of ze correct worden omgeleid naar de `"redirect_uri"` opgegeven in het configuratie bestand:

| Apparaat | Ingebouwde browser | Chrome | Opera  | Microsoft Edge | UC-browser | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | door | door |niet van toepassing |niet van toepassing |niet van toepassing |niet van toepassing |
| Samsung S7 (API 25) | fase<sup>1</sup> | door | door | door | daarvan |door |
| Huawei (API 26) |fase<sup>2</sup> | door | daarvan | door | door |door |
| Vivo (API 26) |door|door|door|door|door|daarvan|
| Pixel 2 (API 26) |door | door | door | door | daarvan |door |
| Oppo | door | niet van toepassing<sup>3</sup>|niet van toepassing  |niet van toepassing |niet van toepassing | niet van toepassing|
| OnePlus (API 25) |door | door | door | door | daarvan |door |
| Nexus (API 28) |door | door | door | door | daarvan |door |
|MI | door | door | door | door | daarvan |door |

<sup>1</sup> De ingebouwde browser van Samsung is Samsung internet.<br/>
<sup>2</sup> De ingebouwde browser van Huawei is de Huawei-browser.<br/>
<sup>3</sup> De standaard browser kan niet worden gewijzigd in de oppo.

## <a name="next-steps"></a>Volgende stappen

Met de [modus gedeeld apparaat voor Android-apparaten](msal-android-shared-devices.md) kunt u een Android-apparaat configureren zodat het eenvoudig kan worden gedeeld door meerdere werk nemers.