---
title: 'Zelfstudie: Een iOS- of macOS-app maken die gebruikmaakt van het Microsoft identiteitsplatform voor verificatie | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie bouwt u een iOS- of macOS-app die gebruikmaakt van het Microsoft-identiteitsplatform voor het aanmelden van gebruikers. U krijgt een toegangstoken waarmee u de Microsoft Graph API namens hen kunt aanroepen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 09/18/2020
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f04a8aa96b51ac9330e4302c3afcc48f7d305b39
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507707"
---
# <a name="tutorial-sign-in-users-and-call-microsoft-graph-from-an-ios-or-macos-app"></a>Zelfstudie: Gebruikers aanmelden en Microsoft Graph aanroepen vanuit een iOS- of macOS-app

In deze zelfstudie bouwt u een iOS- of macOS-app die integreert met het Microsoft-identiteitsplatform voor het aanmelden van gebruikers. U krijgt een toegangstoken waarmee u de Microsoft Graph API kunt aanroepen.

Wanneer u de handleiding hebt voltooid, accepteert uw toepassing aanmeldingen van persoonlijke Microsoft-accounts (waaronder outlook.com, live.com en overige accounts), en werk- of schoolaccounts van elk bedrijf of elke organisatie waar Azure Active Directory wordt gebruikt. Deze zelfstudie is van toepassing op iOS- en macOS-apps. Sommige stappen verschillen tussen de twee platforms.

In deze zelfstudie:

> [!div class="checklist"]
> * Een iOS- of macOS-app-project maken in *Xcode*
> * De app registreren in de Azure-portal
> * Code toevoegen voor de ondersteuning van het aan- en afmelden van gebruikers
> * Code toevoegen om de Microsoft Graph API aan te roepen
> * De app testen

## <a name="prerequisites"></a>Vereisten

- [Xcode 11.x+](https://developer.apple.com/xcode/)

## <a name="how-tutorial-app-works"></a>Hoe de zelfstudie-app werkt

![Er wordt getoond hoe de voorbeeld-app werkt die wordt gegenereerd in deze zelfstudie](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Met de app in deze zelfstudie kunnen gebruikers zich aanmelden en gegevens ophalen vanuit Microsoft Graph. Deze gegevens worden geopend via een beveiligde API (Microsoft Graph API in dit geval), waarvoor autorisatie is vereist en die is beveiligd met het Microsoft-identiteitsplatform.

Met name:

* Met uw app wordt de gebruiker aangemeld via een browser of via Microsoft Authenticator.
* De eindgebruiker accepteert de machtigingen die met de toepassing zijn aangevraagd.
* Aan de app wordt een toegangstoken verleend voor de Microsoft Graph API.
* Het toegangstoken is opgenomen in de HTTP-aanvraag voor de web-API.
* Verwerk het Microsoft Graph-antwoord.

In dit voorbeeld wordt de MSAL (Microsoft Authentication Library) gebruikt om Authentication te implementeren. In MSAL worden tokens automatisch vernieuwd, eenmalige aanmelding (SSO) geboden tussen andere apps op het apparaat, en de accounts beheerd.

Als u een voltooide versie wilt downloaden van de app die u in deze zelfstudie hebt gemaakt, kunt u beide versies vinden op GitHub:

- [Voorbeeld van iOS-code](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/) (Github)
- [Voorbeeld van macOS-code](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/) (GitHub)

## <a name="create-a-new-project"></a>Een nieuw project maken

1. Open Xcode en selecteer **Een nieuw Xcode-project maken**.
2. Selecteer voor iOS-apps **iOS** > **App met één weergave**, en selecteer **Volgende**.
3. Selecteer voor macOS-apps **macOS** > **Cocoa -app** ,en selecteer **Volgende**.
4. Geef een projectnaam op.
5. Stel de **Taal** in op **Swift**, en selecteer **Volgende**.
6. Selecteer een map om daarin de app te maken en selecteer **Maken**.

## <a name="register-your-application"></a>Uw toepassing registreren

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatiemap (alle Azure AD-mappen - meerdere tenants) en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox).**
1. Selecteer **Registreren**.
1. Selecteer onder **Beheren** achtereenvolgens **Verificatie** > **Een platform toevoegen** > **iOS/macOS**.
1. Voer de bundel-id van het project in. Als u de code hebt gedownload, is de id `com.microsoft.identitysample.MSALiOS`. Als u een eigen project wilt maken, selecteert u uw project in Xcode en opent u het tabblad **Algemeen**. De bundel-id wordt weergegeven in de sectie **Identiteit**.
1. Selecteer **Configureren** en sla de **MSAL-configuratie** op die wordt weergegeven op de pagina **MSAL-configuratie**, zodat u deze kunt invoeren wanneer u de app later configureert. 
1. Selecteer **Gereed**.

## <a name="add-msal"></a>MSAL toevoegen

Kies een van de volgende manieren om de MSAL in uw app te installeren:

### <a name="cocoapods"></a>CocoaPods

1. Als u [CocoaPods](https://cocoapods.org/) gebruikt, installeert u `MSAL` door eerst een leeg bestand te maken met de naam `podfile`, in dezelfde map als het bestand `.xcodeproj` van uw project. Voeg het volgende toe in `podfile`:

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Vervang `<your-target-here>` door de naam van uw project.
3. Navigeer in een terminalvenster naar de map die het `podfile` bevat dat u hebt gemaakt, en voer `pod install` uit om de MSAL te installeren.
4. Sluit Xcode en open `<your project name>.xcworkspace` om het project opnieuw te laden in Xcode.

### <a name="carthage"></a>Carthage

Als u [Carthage](https://github.com/Carthage/Carthage)gebruikt, installeert u `MSAL` door het toe te voegen aan uw `Cartfile`:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Voer vanuit een terminalvenster, in dezelfde map als het bijgewerkte `Cartfile`, de volgende opdracht uit om de afhankelijkheden in uw project bij te werken met Carthage.

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Handmatig

U kunt ook een Git-submodule gebruiken of de meest recente release bekijken om te gebruiken als framework in uw toepassing.

## <a name="add-your-app-registration"></a>Uw app-registratie toevoegen

Vervolgens voegen we de app-registratie toe aan uw code.

Voeg eerst de volgende importinstructie toe aan de bovenkant van de `ViewController.swift`, en ook `AppDelegate.swift`- of `SceneDelegate.swift`-bestanden:

```swift
import MSAL
```

Voeg vervolgens de volgende code toe aan `ViewController.swift` voor `viewDidLoad()`:

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization's Azure AD tenant to sign in

let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user

var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

De enige waarde die u hierboven moet wijzigen, is de waarde die is toegewezen `kClientID`als uw [Toepassings-id](./developer-glossary.md#application-id-client-id). Deze waarde maakt deel uit van de MSAL-configuratiegegevens die u hebt opgeslagen tijdens de stap aan het begin van deze zelfstudie om de toepassing te registreren in de Azure-portal.

## <a name="configure-xcode-project-settings"></a>Xcode-projectinstellingen configureren

Voeg een nieuwe sleutelketengroep toe aan uw project **Ondertekening en mogelijkheden**. De sleutelketengroep moet zijn: `com.microsoft.adalcache` in iOS en `com.microsoft.identity.universalstorage` in macOS.

![Xcode-gebruikersinterface waarin wordt weergegeven hoe de sleutelketen moet zijn ingesteld](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>Alleen voor iOS: URL-schema's configureren

In deze stap registreert u `CFBundleURLSchemes`, zodat de gebruiker na aanmelding kan worden omgeleid naar de app. Met `LSApplicationQueriesSchemes` kan uw app bovendien ook gebruikmaken van Microsoft Authenticator.

Open `Info.plist` in Xcode als een broncodebestand, en voeg het volgende toe in de sectie `<dict>`. Vervang `[BUNDLE_ID]` door de waarde die u in de Azure Portal hebt gebruikt. Als u de code hebt gedownload, is de bundel-id `com.microsoft.identitysample.MSALiOS`. Als u een eigen project wilt maken, selecteert u uw project in Xcode en opent u het tabblad **Algemeen**. De bundel-id wordt weergegeven in de sectie **Identiteit**.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Alleen voor macOS: App-sandbox configureren

1. Ga naar de instellingen voor uw Xcode-project > **tabblad Mogelijkheden** > **App-sandbox**
2. Selecteer het selectie vakje **Uitgaande verbindingen (client)** .

## <a name="create-your-apps-ui"></a>De gebruikersinterface van uw app maken

Maak nu een gebruikersinterface die een knop voor het aanroepen van de Microsoft Graph API, een knop voor afmelden, en een tekstweergave voor uitvoer bevat, door de volgende code toe te voegen aan de klasse `ViewController`:

### <a name="ios-ui"></a>iOS-gebruikersinterface

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {

    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right

    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)

    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {

    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)

}

@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>macOS-gebruikersinterface

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

var usernameLabel: NSTextField!

func initUI() {

    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true

    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true

    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false

    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

Vervang vervolgens in de klasse `ViewController` de methode `viewDidLoad()` door:

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()

        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }

        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>MSAL gebruiken

### <a name="initialize-msal"></a>MSAL initialiseren

Voeg de volgende methode `initMSAL` toe aan de klasse `ViewController`:

```swift
    func initMSAL() throws {

        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }

        let authority = try MSALAADAuthority(url: authorityURL)

        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Voeg na methode `initMSAL` het volgende toe aan de klasse `ViewController`.

### <a name="ios-code"></a>iOS-code:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>macOS-code:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Alleen voor iOS: callback na aanmelding afhandelen

Open het `AppDelegate.swift`-bestand. Als u de callback na aanmelding wilt afhandelen, voegt u `MSALPublicClientApplication.handleMSALResponse` toe aan de klasse `appDelegate`, zoals:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Als u Xcode 11 gebruikt**, moet u MSAL-callback in plaats hiervan in de `SceneDelegate.swift` plaatsen.
Als u ondersteuning biedt voor UISceneDelegate en UIApplicationDelegate voor compatibiliteit met oudere iOS-versies, moet MSAL-callback in beide bestanden worden geplaatst.

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

#### <a name="acquire-tokens"></a>Tokens verkrijgen

Nu kunnen we verwerkingslogica voor de gebruikersinterface van de toepassing implementeren en interactief tokens ophalen via MSAL.

MSAL biedt twee primaire methoden voor het ophalen van tokens: `acquireTokenSilently()` en `acquireTokenInteractively()`:

- Met `acquireTokenSilently()` wordt geprobeerd om een gebruiker aan te melden en tokens op te halen zonder enige gebruikersinteractie, mits een account aanwezig is. Voor `acquireTokenSilently()` moet een geldig `MSALAccount` worden geboden, wat kan worden opgehaald met behulp van een van de accountopsommings-API’s van MSAL. In dit voorbeeld wordt `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` gebruikt om het huidige account op te halen.

- Met `acquireTokenInteractively()` wordt altijd de gebruikersinterface weergegeven bij een poging om de gebruiker aan te melden. Voor een interactieve ervaring met eenmalige aanmelding kunnen sessiecookies in de browser worden gebruikt of een account in Microsoft Authenticator.

Voeg de volgende code toe aan de klasse `ViewController`:

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {

        self.loadCurrentAccount { (account) in

            guard let currentAccount = account else {

                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }

            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {

        guard let applicationContext = self.applicationContext else { return }

        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main

        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }

            if let currentAccount = currentAccount {

                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")

                self.updateCurrentAccount(account: currentAccount)

                if let completion = completion {
                    completion(self.currentAccount)
                }

                return
            }

            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)

            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>Interactief een token ophalen

Met het volgende codefragment wordt de eerste keer een token opgehaald door een object `MSALInteractiveTokenParameters` te maken en `acquireToken` aan te roepen. Vervolgens voegt u code toe waarmee de volgende acties worden uitgevoerd:

1. `MSALInteractiveTokenParameters` maken met bereiken.
2. `acquireToken()` aanroepen met de gemaakte parameters.
3. Fouten afhandelen. Raadpleeg [Handleiding voor foutafhandeling in MSAL voor iOS en macOS](msal-error-handling-ios.md) voor meer details.
4. De geslaagde aanvraag afhandelen.

Voeg de volgende code toe aan de klasse `ViewController`.

```swift
func acquireTokenInteractively() {

    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }

    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount

    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in

        // #3
        if let error = error {

            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }

        guard let result = result else {

            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }

        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}
```

Met de eigenschap `promptType` van `MSALInteractiveTokenParameters` configureert u het gedrag voor verificatie en voor de instemmingsprompt. De volgende waarden worden ondersteund:

- `.promptIfNecessary` (standaard): de gebruiker krijgt alleen een prompt te zien als dat nodig is. De SSO-ervaring wordt bepaald door de aanwezigheid van cookies in de webweergave en door het accounttype. Als er meerdere gebruikers zijn aangemeld, wordt de accountselectie weergegeven. *Dit is het standaardgedrag*.
- `.selectAccount`: als er geen gebruiker is opgegeven, wordt in de webweergave voor verificatie een lijst weergegeven met de op dat moment aangemelde accounts waaruit de gebruiker een selectie kan maken.
- `.login`: vereist dat de gebruiker zich verifieert in de webweergave. Er kan slechts één account tegelijk zijn aangemeld als u deze waarde opgeeft.
- `.consent`: de gebruiker moet toestemming geven voor de huidige set scopes voor de aanvraag.

#### <a name="get-a-token-silently"></a>Een token op de achtergrond ophalen

Als u een bijgewerkt token op de achtergrond wilt ophalen, voegt u de volgende code toe aan de klasse `ViewController`. Hiermee maakt u een object `MSALSilentTokenParameters` en roept u `acquireTokenSilent()` aan:

```swift

    func acquireTokenSilently(_ account : MSALAccount!) {

        guard let applicationContext = self.applicationContext else { return }

        /**

         Acquire a token for an existing account silently

         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */

        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)

        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.

                if (nsError.domain == MSALErrorDomain) {

                    if (nsError.code == MSALError.interactionRequired.rawValue) {

                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }

                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>De Microsoft Graph API aanroepen

Zodra u een token hebt, kan de app deze gebruiken in de HTTP-koptekst om een geautoriseerde aanvraag te doen bij Microsoft Graph:

| sleutel voor koptekst    | waarde                 |
| ------------- | --------------------- |
| Autorisatie | Bearer \<access-token> |

Voeg de volgende code toe aan de klasse `ViewController`:

```swift
    func getContentWithToken() {

        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)

        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

            }.resume()
    }
```

Zie [Microsoft Graph API](https://graph.microsoft.com) voor meer informatie over de Microsoft Graph API.

### <a name="use-msal-for-sign-out"></a>MSAL gebruiken voor afmelden

Voeg vervolgens ondersteuning voor afmelden toe.

> [!Important]
> Bij afmelden met MSAL wordt alle bekende informatie over een gebruiker verwijderd uit de toepassing. Ook wordt een actieve sessie verwijderd op het apparaat van de gebruiker wanneer dit is toegestaan op basis van de apparaatconfiguratie. U kunt de gebruiker ook afmelden vanuit de browser.

Als u de mogelijkheid voor afmelden wilt toevoegen, voegt u de volgende code toe binnen de klasse `ViewController`.

```swift
@objc func signOut(_ sender: AnyObject) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account

             - account:    The account to remove from the cache
             */

            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview

            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }

                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })

        }
    }
```

### <a name="enable-token-caching"></a>Tokencaching inschakelen

De tokens van uw app worden met MSAL standaard in de cache opgeslagen in de sleutelketen in iOS of macOS.

Tokencaching inschakelen:

1. Zorg ervoor dat de toepassing juist is afgemeld
1. Ga naar de instellingen voor uw Xcode-project > **tabblad Mogelijkheden** > **Delen van sleutelketens inschakelen**
1. Selecteer **+** en voer een van de volgende **Sleutelketengroepen** in:
    - iOS: `com.microsoft.adalcache`
    - macOS: `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Help-methoden toevoegen
Voeg de volgende Help-methode toe aan de klasse `ViewController` om het voorbeeld te voltooien.

### <a name="ios-ui"></a>iOS-gebruikersinterface:

``` swift

    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

    func updateAccountLabel() {

        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }

        self.usernameLabel.text = currentAccount.username
    }

    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>macOS-gebruikersinterface:

```swift
    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Alleen voor iOS: aanvullende informatie over het apparaat ophalen

Gebruik de volgende code om de huidige apparaatconfiguratie te lezen, inclusief of het apparaat is geconfigureerd als gedeeld:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {

        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }

                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Toepassingen met meerdere accounts

Deze app is gebouwd voor een enkel accountscenario. MSAL biedt ook ondersteuning voor scenario's met meerdere accounts, maar hiervoor is extra werk van apps vereist. U moet een gebruikersinterface maken om gebruikers te helpen bij het selecteren van het account dat ze willen gebruiken voor elke actie waarvoor tokens zijn vereist. Uw app kan ook een heuristiek implementeren om te selecteren welk account moet worden gebruikt, door alle account van MSAL te doorzoeken. Zie bijvoorbeeld `accountsFromDeviceForParameters:completionBlock:` [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Uw app testen

Bouw en implementeer de app op een testapparaat of simulator. U moet zich kunnen aanmelden en tokens ontvangen voor Azure AD of persoonlijke Microsoft-accounts.

De eerste keer dat een gebruiker zich aanmeldt bij uw app, wordt hun door Microsoft gevraagd toestemming te geven voor de aangevraagde machtigingen. Hoewel de meeste gebruikers toestemming kunnen geven, is voor sommige Azure AD-tenants gebruikerstoestemming uitgeschakeld. In dit geval moeten beheerders toestemming geven namens alle gebruikers. Registreer de bereiken van de app in de Azure-portal om dit scenario te ondersteunen.

Nadat u zich hebt aangemeld, verschijnen in de app de gegevens die zijn geretourneerd via het Microsoft Graph `/me`-eindpunt.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bouwen van mobiele apps die beveiligde web-API's aanroepen in onze reeks met meerdere scenario's.

> [!div class="nextstepaction"]
> [Scenario: Een mobiele app die web-API's aanroept](scenario-mobile-overview.md)
