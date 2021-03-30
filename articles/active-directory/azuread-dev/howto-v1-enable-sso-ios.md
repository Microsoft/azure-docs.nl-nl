---
title: SSO van meerdere apps inschakelen op iOS met behulp van ADAL | Microsoft Docs
description: De functies van de ADAL SDK gebruiken om eenmalige aanmelding in te scha kelen voor uw toepassingen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 08b018082c753b9524cb12a72d637fe5458d9114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383696"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Procedure: cross-app SSO inschakelen op iOS met ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Eenmalige aanmelding (SSO) stelt gebruikers in staat om hun referenties één keer in te voeren en deze referenties automatisch te laten werken in toepassingen en op verschillende platforms die andere toepassingen mogelijk gebruiken (zoals micro soft-accounts of een werk account van Microsoft 365), ongeacht de uitgever.

Het identiteits platform van micro soft, samen met de Sdk's, maakt het eenvoudig om eenmalige aanmelding in te scha kelen in uw eigen suite van apps of met de Broker-en verificator-toepassingen, op het hele apparaat.

In deze procedure leert u hoe u de SDK in uw toepassing kunt configureren om eenmalige aanmelding te bieden voor uw klanten.

Deze procedure is van toepassing op:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Voorwaardelijke toegang van Azure Active Directory

## <a name="prerequisites"></a>Vereisten

In deze procedure wordt ervan uitgegaan dat u weet hoe u:

* Richt uw app in met behulp van de verouderde portal voor Azure AD. Zie [een app registreren](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) voor meer informatie
* Integreer uw toepassing met de [Azure AD IOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Concepten van eenmalige aanmelding

### <a name="identity-brokers"></a>Identiteits Brokers

Micro soft biedt toepassingen voor elk mobiel platform dat de bridging van referenties voor toepassingen van verschillende leveranciers mogelijk maakt, en voor verbeterde functies waarvoor een enkele veilige locatie is vereist voor het valideren van referenties. Deze worden **makelaars** genoemd.

Op iOS en Android worden makelaars geleverd via download bare toepassingen die door klanten afzonderlijk of naar het apparaat worden gepusht door een bedrijf dat sommige of alle apparaten beheert voor hun werk nemers. Makelaars ondersteunen het beheer van beveiliging alleen voor sommige toepassingen of het hele apparaat op basis van de IT-beheerders configuratie. In Windows wordt deze functionaliteit geleverd door een account kiezer die is ingebouwd in het besturings systeem, technisch aangeduid als de Web authentication Broker.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Patronen voor het aanmelden op mobiele apparaten

Toegang tot referenties op apparaten volgen twee basis patronen:

* Niet-Broker ondersteunde aanmeldingen
* Door Broker gesteunde aanmeldingen

#### <a name="non-broker-assisted-logins"></a>Niet-Broker ondersteunde aanmeldingen

Aanmeldingen die niet worden ondersteund door Broker, zijn aanmeldings ervaringen die worden inline met de toepassing en de lokale opslag op het apparaat gebruiken voor die toepassing. Deze opslag kan worden gedeeld door toepassingen, maar de referenties zijn nauw verbonden met de app of suite van apps met behulp van deze referentie. U hebt dit waarschijnlijk in veel mobiele toepassingen ondervonden wanneer u een gebruikers naam en wacht woord in de toepassing zelf opgeeft.

Deze aanmeldingen hebben de volgende voor delen:

* Gebruikers ervaring bestaat volledig in de toepassing.
* Referenties kunnen worden gedeeld tussen toepassingen die zijn ondertekend door hetzelfde certificaat, waardoor eenmalige aanmelding mogelijk is voor uw pakket toepassingen.
* Controle over de ervaring van het aanmelden van de toepassing wordt vóór en na het aanmelden aan de app gegeven.

Deze aanmeldingen hebben de volgende nadelen:

* Gebruikers kunnen zich niet aanmelden met eenmalige aanmelding voor alle apps die gebruikmaken van een micro soft-identiteit, alleen voor de micro soft-identiteiten die uw toepassing heeft geconfigureerd.
* Uw toepassing kan niet worden gebruikt met meer geavanceerde zakelijke functies, zoals voorwaardelijke toegang of het gebruik van de intune-product suite.
* Uw toepassing biedt geen ondersteuning voor verificatie op basis van certificaten voor zakelijke gebruikers.

Hier volgt een weer gave van de manier waarop de Sdk's samen werken met de gedeelde opslag van uw toepassingen om SSO in te scha kelen:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Door Broker gesteunde aanmeldingen

Aanmeldingen met Broker ondersteuning zijn aanmeldings ervaringen die zich binnen de Broker-toepassing voordoen en gebruiken de opslag en beveiliging van de Broker om referenties te delen in alle toepassingen op het apparaat waarop het identiteits platform wordt toegepast. Dit betekent dat uw toepassingen afhankelijk zijn van de Broker voor het ondertekenen van gebruikers in. Op iOS en Android worden deze makelaars geleverd via download bare toepassingen die klanten onafhankelijk van elkaar installeren of naar het apparaat worden gepusht door een bedrijf dat het apparaat beheert voor hun gebruiker. Een voor beeld van dit type toepassing is de Microsoft Authenticator toepassing op iOS. In Windows wordt deze functionaliteit geleverd door een account kiezer die is ingebouwd in het besturings systeem, wat technisch bekend is als de Web authentication Broker.

De ervaring is afhankelijk van het platform en kan soms storend zijn voor gebruikers als ze niet correct worden beheerd. U bent waarschijnlijk het meest vertrouwd met dit patroon als u de Facebook-toepassing hebt geïnstalleerd en Facebook Connect van een andere toepassing gebruikt. Het identiteits platform gebruikt hetzelfde patroon.

Voor iOS leidt dit tot een overgangs animatie waarbij uw toepassing naar de achtergrond wordt verzonden terwijl de Microsoft Authenticator-toepassingen de voor grond vormen van de gebruiker om te selecteren met welk account ze zich willen aanmelden.

Voor Android en Windows wordt de account kiezer weer gegeven boven op de toepassing, die minder storend is voor de gebruiker.

#### <a name="how-the-broker-gets-invoked"></a>Hoe de Broker wordt aangeroepen

Als er een compatibele Broker op het apparaat is geïnstalleerd, zoals de toepassing Microsoft Authenticator, wordt het werk van de Broker automatisch door de Sdk's gestart wanneer een gebruiker aangeeft dat hij of zij zich wil aanmelden met een account van het identiteits platform. Dit account kan een persoonlijk micro soft-account, een werk-of school account of een account dat u biedt en hosten in azure met behulp van onze B2C-en B2B-producten.

#### <a name="how-we-ensure-the-application-is-valid"></a>Hoe we ervoor zorgen dat de toepassing geldig is

De nood zaak om ervoor te zorgen dat de identiteit van een toepassing die de Broker aanroept, cruciaal is voor de beveiliging die wij bieden in door Broker geassisteerde aanmeldingen. IOS en Android afdwingt geen unieke id's die alleen geldig zijn voor een bepaalde toepassing, waardoor kwaadwillende toepassingen een geldige toepassings-id kunnen ' spoofen ' en de tokens ontvangen die voor de legitieme toepassing bedoeld zijn. Om ervoor te zorgen dat we altijd communiceren met de juiste toepassing tijdens runtime, vragen we de ontwikkelaar om een aangepaste redirectURI te bieden wanneer ze hun toepassing registreren bij micro soft. Hoe ontwikkel aars deze omleidings-URI moeten bebouwen, worden hieronder gedetailleerd beschreven. Deze aangepaste redirectURI bevat de bundel-ID van de toepassing en wordt gegarandeerd uniek voor de toepassing door de Apple App Store. Wanneer een toepassing de Broker aanroept, vraagt de Broker het iOS-besturings systeem aan de bundel-ID die de Broker heeft genoemd. De Broker levert deze bundel-ID aan micro soft in de aanroep van ons identiteits systeem. Als de bundel-ID van de toepassing niet overeenkomt met de bundel-ID die door de ontwikkelaar tijdens de registratie is opgegeven, wordt de toegang geweigerd tot de tokens voor de resource die de toepassing aanvraagt. Met deze controle wordt gegarandeerd dat alleen de toepassing die door de ontwikkelaar is geregistreerd, tokens ontvangt.

**De ontwikkelaar heeft de keuze of de SDK de Broker aanroept of gebruikmaakt van de niet-Broker ondersteunde stroom.** Als de ontwikkelaar ervoor kiest geen gebruik te maken van de stroom die door de Broker wordt ondersteund, verliezen ze het voor deel van het gebruik van SSO-referenties die de gebruiker mogelijk al heeft toegevoegd op het apparaat en voor komt dat hun toepassing kan worden gebruikt met zakelijke functies micro soft biedt zijn klanten, zoals voorwaardelijke toegang, intune-beheer mogelijkheden en verificatie op basis van certificaten.

Deze aanmeldingen hebben de volgende voor delen:

* Gebruikers ervaringen SSO voor al hun toepassingen, ongeacht de leverancier.
* Uw toepassing kan meer geavanceerde zakelijke functies gebruiken, zoals voorwaardelijke toegang of het gebruik van de intune-product suite.
* Uw toepassing kan verificatie op basis van certificaten ondersteunen voor zakelijke gebruikers.
* Veel veiligere aanmeldings ervaring als de identiteit van de toepassing en de gebruiker worden door de Broker toepassing gecontroleerd met aanvullende beveiligings algoritmen en versleuteling.

Deze aanmeldingen hebben de volgende nadelen:

* In iOS wordt de gebruiker door de gebruikers ervaring overgezet terwijl er referenties worden gekozen.
* Verlies van de mogelijkheid om de aanmeldings ervaring voor uw klanten binnen uw toepassing te beheren.

Hier volgt een weer gave van de manier waarop de Sdk's samen werken met de Broker-toepassingen om SSO in te scha kelen:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

## <a name="enabling-cross-app-sso-using-adal"></a>SSO van meerdere apps inschakelen met behulp van ADAL

Hier gebruiken we de ADAL iOS SDK voor het volgende:

* Niet-Broker-ondersteunings-SSO inschakelen voor uw app-pakket
* Ondersteuning voor eenmalige aanmelding via Broker inschakelen

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>EENMALIGe aanmelding inschakelen voor SSO met niet-Broker-ondersteuning

Voor niet-Broker-ondersteunings-SSO tussen toepassingen beheert de Sdk's veel van de complexiteit van SSO voor u. Dit omvat het vinden van de juiste gebruiker in de cache en het onderhouden van een lijst met aangemelde gebruikers die u wilt doorzoeken.

Ga als volgt te werk om SSO in te scha kelen voor alle toepassingen die u nodig hebt:

1. Zorg ervoor dat al uw toepassingen dezelfde client-ID of toepassings-ID gebruiken.
2. Zorg ervoor dat alle toepassingen hetzelfde handtekening certificaat van Apple delen, zodat u de sleutel ketens kunt delen.
3. Vraag hetzelfde sleutel hanger aan voor elk van uw toepassingen.
4. Vertel de Sdk's over de gedeelde sleutel hanger die u door ons wilt laten gebruiken.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Dezelfde client-ID/toepassings-ID gebruiken voor alle toepassingen in uw suite van apps

Om het identiteits platform te laten weten dat het is toegestaan om tokens in uw toepassingen te delen, moeten al uw toepassingen dezelfde client-ID of toepassings-ID delen. Dit is de unieke id die aan u is gegeven tijdens het registreren van uw eerste toepassing in de portal.

Met omleidings-Uri's kunt u verschillende apps identificeren voor de micro soft Identity-service als deze dezelfde toepassings-ID gebruikt. Voor elke toepassing kunnen meerdere omleidings-Uri's zijn geregistreerd in de onboarding-Portal. Elke app in uw suite krijgt een andere omleidings-URI. Hieronder ziet u een voor beeld van hoe dit wordt weer gegeven:

App1 omleidings-URI: `x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 omleidings-URI: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 omleidings-URI: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Deze zijn genest onder dezelfde client-ID/toepassings-ID en worden opgezocht op basis van de omleidings-URI die u naar ons terugstuurt in uw SDK-configuratie.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

De indeling van deze omleidings-Uri's wordt hieronder uitgelegd. U kunt een omleidings-URI gebruiken tenzij u de Broker wilt ondersteunen. in dat geval moeten ze er ongeveer als volgt uitzien:

#### <a name="create-keychain-sharing-between-applications"></a>Sleutel hanger delen tussen toepassingen maken

Het inschakelen van sleutel hanger delen valt buiten het bereik van dit document en wordt gedekt door Apple in hun document waarin de [mogelijkheden worden toegevoegd](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Het is belang rijk dat u bepaalt wat u wilt dat uw sleutel keten wordt aangeroepen en voeg die mogelijkheid toe aan al uw toepassingen.

Wanneer u de rechten hebt ingesteld op de juiste wijze, ziet u een bestand in de projectmap `entitlements.plist` die het recht bevat op het volgende:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Zodra u de sleutel hanger hebt ingeschakeld in elk van uw toepassingen en u klaar bent om SSO te gebruiken, geeft u de identiteits-SDK over uw sleutel hanger door de volgende instelling te gebruiken in uw `ADAuthenticationSettings` met de volgende instelling:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Wanneer u een sleutel hanger in uw toepassingen deelt, kan elke toepassing gebruikers verwijderen of erger alle tokens in uw toepassing te verwijderen. Dit is met name disastrous als u toepassingen hebt die afhankelijk zijn van de tokens om achtergrond taken uit te voeren. Het delen van een sleutel hanger houdt in dat u zorgvuldig moet zijn in alle en alle Verwijder bewerkingen via de identiteits-Sdk's.

Dat is alles. De SDK zal nu referenties delen in al uw toepassingen. De gebruikers lijst wordt ook gedeeld tussen toepassings exemplaren.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>EENMALIGe aanmelding inschakelen voor SSO met Broker-ondersteuning

De mogelijkheid voor een toepassing voor het gebruik van elke Broker die op het apparaat is geïnstalleerd, is **standaard** uitgeschakeld. Als u uw toepassing met de Broker wilt gebruiken, moet u aanvullende configuratie stappen uitvoeren en code toevoegen aan uw toepassing.

De stappen die u moet volgen:

1. Schakel de Broker modus in de aanroep van de toepassings code naar de MS SDK in.
2. Stel een nieuwe omleidings-URI in en geef aan de app en de registratie van uw app aan.
3. Een URL-schema registreren.
4. Voeg een machtiging toe aan het bestand info. plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Stap 1: de Broker modus inschakelen in uw toepassing

De mogelijkheid om uw toepassing te gebruiken voor het gebruik van de Broker is ingeschakeld wanneer u de ' context ' of de eerste installatie van uw verificatie object maakt. U doet dit door het type referenties in uw code in te stellen:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Met deze instelling kan de SDK de Broker proberen aan te `AD_CREDENTIALS_AUTO` roepen, `AD_CREDENTIALS_EMBEDDED` waardoor de SDK niet aan de broker kan worden aangeroepen.

#### <a name="step-2-registering-a-url-scheme"></a>Stap 2: een URL-schema registreren

Het identiteits platform gebruikt Url's om de Broker aan te roepen en vervolgens terug te keren naar uw toepassing. Als u wilt volt ooien, hebt u een URL-schema voor uw toepassing nodig dat het identiteits platform kent. Dit kan naast alle andere app-schema's die u eerder hebt geregistreerd bij uw toepassing.

> [!WARNING]
> We raden u aan het URL-schema tamelijk uniek te maken om de kans op een andere app te minimaliseren met behulp van hetzelfde URL-schema. Apple dwingt de uniekheid van URL-schema's die in de App Store zijn geregistreerd, niet af.

Hieronder ziet u een voor beeld van hoe dit in uw project configuratie wordt weer gegeven. U kunt dit ook doen in XCode:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Stap 3: een nieuwe omleidings-URI tot stand brengen met uw URL-schema

Om ervoor te zorgen dat we de referentie tokens altijd retour neren naar de juiste toepassing, moeten we ervoor zorgen dat we terugbellen naar uw toepassing op een manier die het iOS-besturings systeem kan controleren. Het iOS-besturings systeem rapporteert aan de micro soft Broker-toepassingen de bundel-ID van de toepassing die deze aanroept. Dit kan niet worden vervalst met een Rogue-toepassing. Daarom gebruiken we dit samen met de URI van onze Broker-toepassing om ervoor te zorgen dat de tokens naar de juiste toepassing worden geretourneerd. U moet deze unieke omleidings-URI in uw toepassing tot stand brengen en instellen als omleidings-URI in onze ontwikkelaars Portal.

De omleidings-URI moet de volgende vorm hebben:

`<app-scheme>://<your.bundle.id>`

bijvoorbeeld: *x-msauth-mytestiosapp://com.MyApp.mytestapp*

Deze omleidings-URI moet worden opgegeven in de registratie van uw app met behulp van de [Azure Portal](https://portal.azure.com/). Zie [integreren met Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)voor meer informatie over Azure AD-App-registratie.

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Stap 3a: een omleidings-URI toevoegen in uw app en ontwikkelaars Portal ter ondersteuning van verificatie op basis van certificaten

Ter ondersteuning van verificatie op basis van certificaten moet u een tweede ' msauth ' registreren in uw toepassing en de [Azure Portal](https://portal.azure.com/) voor het afhandelen van certificaat verificatie als u die ondersteuning wilt toevoegen in uw toepassing.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

bijvoorbeeld: *msauth://code/x-msauth-mytestiosapp%3a%2F%2Fcom.MyApp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Stap 4: een configuratie parameter toevoegen aan uw app

ADAL maakt gebruik van – canOpenURL: om te controleren of de Broker op het apparaat is geïnstalleerd. In iOS 9 op heeft Apple vergrendeld welke schema's een toepassing kan opvragen. U moet ' msauth ' toevoegen aan de sectie LSApplicationQueriesSchemes van uw `info.plist file` .

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>U hebt SSO geconfigureerd.

Nu zal de identiteits-SDK automatisch referenties delen in uw toepassingen en de Broker aanroepen als deze aanwezig is op het apparaat.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [SAML-protocol voor eenmalige aanmelding](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
