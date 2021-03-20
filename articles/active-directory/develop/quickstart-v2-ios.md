---
title: 'Snelstart: Aanmelden met Microsoft toevoegen aan een iOS- of macOS-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze snelstart leert u hoe een iOS- of macOS-app gebruikers kan aanmelden, een toegangstoken kan ophalen uit het Microsoft-identiteitsplatform en de Microsoft Graph-API aanroept.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: d2c5af6aeccfbae0851513ff575bde3c39e3ca5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103784"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Quickstart: Gebruikers aanmelden en de Microsoft Graph API aanroepen vanuit een iOS- of macOS-app

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers kunnen worden aangemeld met een systeemeigen iOS- of macOS-toepassing, en een toegangstoken kunnen krijgen om de Microsoft Graph API aan te roepen.

De quickstart is van toepassing op iOS- en macOS-apps. Sommige stappen zijn alleen nodig voor iOS-apps en worden als zodanig aangeduid.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* XCode 10+
* iOS 10+
* macOS 10.12+

## <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt

![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze quickstart](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Optie 1: registreer de app en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Registreer uw app als volgt.
> 1. Ga naar de quickstart-ervaring <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs" target="_blank">Azure-portal - App-registraties</a>.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.    
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer **Registreren**.
> 1. Selecteer onder **Beheren** achtereenvolgens **Verificatie** > **Platform toevoegen** > **iOS**
> 1. Voer de **bundel-id** voor uw toepassing in. De bundel-id is een unieke tekenreeks en een unieke identificatie voor uw toepassing, bijvoorbeeld `com.<yourname>.identitysample.MSALMacOS`. Noteer de waarde die u gebruikt. De iOS-configuratie is ook van toepassing op macOS-toepassingen.
> 1. Selecteer **Configureren** en sla de details van de **MSAL-configuratie** op voor later gebruik verderop in deze quickstart.
> 1. Selecteer **Gereed**.

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Stap 1: Uw toepassing configureren
> Voor het code voorbeeld voor deze Quick Start, voegt u een **omleidings-URI** toe die compatibel is met de auth Broker.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-ios/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken
>
> #### <a name="step-2-download-the-sample-project"></a>Stap 2: Het voorbeeldproject downloaden
> > [!div id="autoupdate_ios" class="nextstepaction"]
> > [Het codevoorbeeld voor iOS downloaden]()
>
> > [!div id="autoupdate_macos" class="nextstepaction"]
> > [Het codevoorbeeld voor macOS downloaden]()
> [!div renderon="docs"]
> #### <a name="step-2-download-the-sample-project"></a>Stap 2: Het voorbeeldproject downloaden
>
> - [Het codevoorbeeld voor iOS downloaden](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
> - [Het codevoorbeeld voor macOS downloaden](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Stap 3: Afhankelijkheden installeren

Navigeer in een terminalvenster naar de map met het gedownloade codevoorbeeld en voer `pod install` uit om de MSAL-bibliotheek te installeren.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>Stap 4: Uw app is geconfigureerd en klaar om te worden uitgevoerd
> Uw project is geconfigureerd met waarden van de eigenschappen van uw app en is klaar om te worden uitgevoerd.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
>
> [!div renderon="docs"]
>#### <a name="step-4-configure-your-project"></a>Stap 4: Het project configureren
> Als u hierboven optie 1 hebt geselecteerd, kunt u deze stappen overslaan.
> 1. Pak het zip-bestand uit en open het project in XCode.
> 1. Bewerk **ViewController.swift** en vervang de regel die begint met 'let kClientID' door het volgende codefragment. Vergeet niet om de waarde voor `kClientID` bij te werken met de client-id die u eerder in deze quickstart hebt opgeslagen tijdens het registreren van uw app in de portal:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Als u een app bouwt voor [nationale Azure AD-clouds](/graph/deployments#app-registration-and-token-service-root-endpoints), vervangt u de regel die begint met 'let kGraphEndpoint' en 'let kAuthority ' door de juiste eindpunten. Gebruik de standaardwaarden voor globale toegang:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Andere eindpunten worden [hier](/graph/deployments#app-registration-and-token-service-root-endpoints) beschreven. Als u bijvoorbeeld de quickstart wilt uitvoeren met Azure AD Duitsland, gaat u als volgt te werk:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Open de projectinstellingen. Voer in de sectie **Identiteit** de **bundel-id** in die u hebt ingevoerd in de portal.
> 1. Klik met de rechtermuisknop op **Info.plist** en selecteer **Openen als** > **Broncode**.
> 1. Vervang onder het dict-hoofdknooppunt `Enter_the_bundle_Id_Here` door de ***bundel-id*** die u hebt gebruikt in de portal.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Bouw de app en voer deze uit.

## <a name="more-information"></a>Meer informatie

Lees deze secties voor meer informatie over deze snelstart.

### <a name="get-msal"></a>MSAL ophalen

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en aanvragen van tokens die worden gebruikt voor toegang tot een API die wordt beveiligd door Microsoft identity platform. U kunt MSAL toevoegen aan uw toepassing met behulp van het volgende proces:

```
$ vi Podfile

```
Voeg het volgende toe aan deze podfile (met het doel van het project):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Voer de CocoaPods-installatieopdracht uit:

```pod install```

### <a name="initialize-msal"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```swift
import MSAL
```

Vervolgens initialiseert u MSAL met de volgende code:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Waar: | Beschrijving |
> |---------|---------|
> | `clientId` | De toepassings-id van de toepassing die is geregistreerd in *portal.azure.com* |
> | `authority` | Het micro soft Identity-platform. In de meeste gevallen is dit `https://login.microsoftonline.com/common` |
> | `redirectUri` | De omleidings-URI van de toepassing. U kunt 'nil' doorgeven om de standaardwaarde te gebruiken, of uw aangepaste omleidings-URI gebruiken. |

### <a name="for-ios-only-additional-app-requirements"></a>Alleen voor iOS: extra app-vereisten

Uw app moet ook het volgende bevatten in uw `AppDelegate`. Hiermee kan de tokenrespons van de verificatiebroker-app worden verwerkt door de MSAL SDK tijdens het verificatieproces.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> Als u in iOS 13+ `UISceneDelegate` gebruikt in plaats van `UIApplicationDelegate`, plaatst u deze code in de `scene:openURLContexts:` callback (zie de [documentatie van Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Als u ondersteuning biedt voor UISceneDelegate en UIApplicationDelegate voor compatibiliteit met oudere iOS-versies, moet MSAL-callback in beide locaties worden geplaatst.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Ten slotte moet uw app een `LSApplicationQueriesSchemes`-vermelding bevatten in ***info.plist*** naast de `CFBundleURLTypes`. In het voorbeeld is deze vermelding al opgenomen.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Gebruikers aanmelden en tokens aanvragen

MSAL biedt twee methoden voor het verkrijgen van tokens: `acquireToken` en `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: Interactief een token ophalen

In sommige situaties moeten gebruikers communiceren met Microsoft identity platform. In dergelijke gevallen kunnen eindgebruikers verplicht zijn om hun account te selecteren, hun referenties in te voeren of toestemming te geven voor de machtigingen van uw app. Bijvoorbeeld:

* De eerste keer dat gebruikers zich aanmelden bij de toepassing
* Als gebruiker hun wachtwoord opnieuw instellen, moeten ze hun referenties invoeren
* Wanneer via de toepassing voor het eerst toegang wordt aangevraagd tot een resource
* Wanneer MFA of ander beleid voor voorwaardelijke toegang is vereist

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Waar:| Beschrijving |
> |---------|---------|
> | `scopes` | Bevat de bereiken die worden aangevraagd (dat wil zeggen `[ "user.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-API's (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Een toegangstoken op de achtergrond verkrijgen

Gebruikers zouden zich niet telkens moeten aanmelden wanneer apps een token aanvragen. Als de gebruiker al is aangemeld, kunnen apps via deze methode tokens op de achtergrond aanvragen.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Waar: | Beschrijving |
> |---------|---------|
> | `scopes` | Bevat de bereiken die worden aangevraagd (dat wil zeggen `[ "user.read" ]` voor Microsoft Graph of `[ "<Application ID URL>/scope" ]` voor aangepaste web-API's (`api://<Application ID>/access_as_user`) |
> | `account` | Het account waarvoor een token wordt aangevraagd. Deze quickstart betreft een toepassing met één account. Als u een app met meerdere accounts wilt maken, moet u logica definiëren om te bepalen welk account moet worden gebruikt voor tokenaanvragen met behulp van `accountsFromDeviceForParameters:completionBlock:` en doorgifte van de juiste `accountIdentifier` |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar de stapsgewijze zelfstudie waarin u een iOS- of macOS-app bouwt die een toegangstoken van het Microsoft-identiteitsplatform ontvangt en dit gebruikt om de Microsoft Graph-API aan te roepen.

> [!div class="nextstepaction"]
> [Zelfstudie: Gebruikers aanmelden en Microsoft Graph aanroepen vanuit een iOS- of macOS-app](tutorial-v2-ios.md)
