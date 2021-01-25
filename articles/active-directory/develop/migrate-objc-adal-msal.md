---
title: Migratie handleiding voor ADAL naar MSAL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen MSAL voor iOS/macOS en de Azure AD-verificatie bibliotheek voor ObjectiveC (ADAL. ObjC) en hoe u migreert naar MSAL voor iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 7dc3241198fbc6eeddba059251f28c6dc35c8a29
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754944"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Toepassingen migreren naar MSAL voor iOS en macOS

De Azure Active Directory-verificatie bibliotheek ([ADAL doelstelling-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) is gemaakt om met Azure Active Directory accounts te werken via het eind punt v 1.0.

De micro soft-verificatie bibliotheek voor iOS en macOS (MSAL) is ontworpen om te werken met alle micro soft-identiteiten, zoals Azure Active Directory (Azure AD)-accounts, persoonlijke micro soft-accounts en Azure AD B2C accounts via het micro soft Identity platform (formeel het Azure AD v 2.0-eind punt).

Het micro soft Identity-platform heeft enkele belang rijke verschillen met Azure Active Directory v 1.0. In dit artikel worden deze verschillen beschreven en vindt u richt lijnen voor het migreren van een app van ADAL naar MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Verschillen in ADAL-en MSAL-apps

### <a name="who-can-sign-in"></a>Wie kan zich aanmelden

* ADAL biedt alleen ondersteuning voor werk-en school accounts, ook wel bekend als Azure AD-accounts.
* MSAL biedt ondersteuning voor persoonlijke micro soft-accounts (MSA-accounts) zoals Hotmail.com, Outlook.com en Live.com.
* MSAL ondersteunt werk-en school accounts en Azure AD B2C accounts.

### <a name="standards-compliance"></a>Naleving van standaarden

* Het micro soft Identity-platform is gebaseerd op OAuth 2,0 en OpenID Connect Connect-standaarden.

### <a name="incremental-and-dynamic-consent"></a>Incrementele en dynamische toestemming

* Het Azure Active Directory v 1.0-eind punt vereist dat alle machtigingen vooraf worden gedeclareerd tijdens de registratie van de toepassing. Dit betekent dat deze machtigingen statisch zijn.
* Met het micro soft Identity-platform kunt u dynamisch machtigingen aanvragen. Apps kunnen alleen om machtigingen vragen als dat nodig is en meer aanvragen als de app nodig heeft.

Voor meer informatie over verschillen tussen Azure Active Directory v 1.0 en het micro soft-identiteits platform, Zie [Waarom bijwerken naar micro soft Identity platform?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="adal-and-msal-library-differences"></a>Verschillen in ADAL-en MSAL-bibliotheek

De MSAL open bare API weerspiegelt enkele belang rijke verschillen tussen Azure AD v 1.0 en het micro soft-identiteits platform.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication in plaats van ADAuthenticationContext

`ADAuthenticationContext` is het eerste object dat een ADAL-app maakt. Dit vertegenwoordigt een exemplaar van ADAL. Apps maken een nieuw exemplaar van `ADAuthenticationContext` voor elke combi natie van Azure Active Directory Cloud en Tenant (Authority). Dit `ADAuthenticationContext` kan ook worden gebruikt voor het ophalen van tokens voor meerdere open bare client toepassingen.

In MSAL is de belangrijkste interactie via een `MSALPublicClientApplication` object, dat is gemodelleerd na een [OAuth 2,0 open bare client](https://tools.ietf.org/html/rfc6749#section-2.1). Eén exemplaar van `MSALPublicClientApplication` kan worden gebruikt om te communiceren met meerdere Aad-Clouds en tenants, zonder dat er voor elke instantie een nieuw exemplaar hoeft te worden gemaakt. Voor de meeste apps `MSALPublicClientApplication` is één instantie voldoende.

### <a name="scopes-instead-of-resources"></a>Bereiken in plaats van resources

In ADAL moest een app een *resource* -id opgeven, zoals `https://graph.microsoft.com` het verkrijgen van tokens uit het Azure Active Directory v 1.0-eind punt. Een resource kan een aantal bereiken of oAuth2Permissions in het app-manifest definiëren dat deze begrijpt. Deze toegestane client-apps voor het aanvragen van tokens van die bron voor een bepaalde set bereiken die vooraf zijn gedefinieerd tijdens de app-registratie.

In MSAL, in plaats van één resource-id, bieden apps een set scopes per aanvraag. Een bereik is een resource-id gevolgd door een machtigings naam in de vorm van de bron/machtiging. Bijvoorbeeld: `https://graph.microsoft.com/user.read`

Er zijn twee manieren om bereiken in MSAL te bieden:

* Geef een lijst op met alle machtigingen die nodig zijn voor uw apps. Bijvoorbeeld: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    In dit geval vraagt de app de `directory.read` machtigingen en aan `directory.write` . De gebruiker wordt gevraagd om toestemming te geven voor die machtigingen als ze nog niet vóór deze app hebben ingestemd. De toepassing kan ook aanvullende machtigingen ontvangen die de gebruiker al heeft ingestemd voor de toepassing. De gebruiker wordt alleen gevraagd om toestemming te geven voor nieuwe machtigingen of machtigingen die nog niet zijn toegekend.

* Het `/.default` bereik.

Dit is het ingebouwde bereik voor elke toepassing. Dit verwijst naar de statische lijst met machtigingen die zijn geconfigureerd tijdens het registreren van de toepassing. Het gedrag ervan is vergelijkbaar met dat van `resource` . Dit kan handig zijn wanneer u de migratie uitvoert om ervoor te zorgen dat een vergelijk bare reeks bereiken en gebruikers ervaring wordt behouden.

Als u het `/.default` bereik wilt gebruiken, voegt `/.default` u toe aan de resource-id. Bijvoorbeeld: `https://graph.microsoft.com/.default`. Als uw resource eindigt met een schuine streep ( `/` ), moet u nog steeds toevoegen `/.default` , met inbegrip van de voorloop back slash, wat resulteert in een bereik met een dubbele slash ( `//` ).

U kunt [hier](./v2-permissions-and-consent.md#the-default-scope) meer informatie lezen over het gebruik van het bereik '/.default '

### <a name="supporting-different-webview-types--browsers"></a>Ondersteuning voor verschillende webweergave typen & browsers

ADAL biedt alleen ondersteuning voor UIWebView/WKWebView voor iOS en de webweergave voor macOS. MSAL voor iOS ondersteunt meer opties voor het weer geven van webinhoud wanneer u een autorisatie code aanvraagt en niet langer ondersteunt `UIWebView` ; waarmee de gebruikers ervaring en beveiliging kunnen worden verbeterd.

MSAL op iOS maakt standaard gebruik van [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), het webonderdeel Apple raadt voor verificatie op Ios 12 +-apparaten. Het biedt de voor delen van single Sign-On (SSO) via het delen van cookies tussen apps en de Safari-browser.

U kunt ervoor kiezen om een ander webonderdeel te gebruiken, afhankelijk van de app-vereisten en de ervaring van de eind gebruiker die u wilt. Zie [ondersteunde webweergave typen](customize-webviews.md) voor meer opties.

Bij de migratie van ADAL naar MSAL, `WKWebView` biedt de gebruikers ervaring die het meest overeenkomt met ADAL op Ios en macOS. U wordt aangeraden om `ASWebAuthenticationSession` , indien mogelijk, te migreren naar op Ios. Voor macOS raden we u aan om te gebruiken `WKWebView` .

### <a name="account-management-api-differences"></a>Verschillen in de API voor account beheer

Wanneer u de ADAL-methoden aanroept `acquireToken()` of `acquireTokenSilent()` , ontvangt u een `ADUserInformation` object met een lijst met claims van de `id_token` die het account vertegenwoordigt dat wordt geverifieerd. Retourneert daarnaast `ADUserInformation` een `userId` op basis van de `upn` claim. Na de eerste aanschaf van een interactief token, verwacht ADAL dat ontwikkel aars `userId` in alle Silent-aanroepen kunnen voorzien.

ADAL biedt geen API om bekende gebruikers identiteiten op te halen. Het is afhankelijk van de app voor het opslaan en beheren van deze accounts.

MSAL biedt een set Api's voor het weer geven van alle accounts die bekend zijn bij MSAL, zonder dat u een token hoeft op te halen.

Net als ADAL retourneert MSAL account gegevens die een lijst met claims van de bevatten `id_token` . Het maakt deel uit van het `MSALAccount` object binnen het `MSALResult` object.

MSAL biedt een set Api's voor het verwijderen van accounts, waardoor de verwijderde accounts ontoegankelijk zijn voor de app. Nadat het account is verwijderd, wordt de gebruiker in latere aanvragen voor token overname gevraagd om een interactief token te verkrijgen. Het verwijderen van accounts is alleen van toepassing op de client toepassing die deze heeft gestart en verwijdert het account niet uit de andere apps die worden uitgevoerd op het apparaat of in de systeem browser. Dit zorgt ervoor dat de gebruiker op het apparaat blijft beschikken over een SSO-ervaring, zelfs na het afmelden van een afzonderlijke app.

Daarnaast retourneert MSAL ook een account-id die kan worden gebruikt om een token later op de achtergrond aan te vragen. De account-id (toegankelijk via `identifier` de eigenschap in het `MSALAccount` object) kan echter niet worden uitgesteld en u kunt niet aannemen in welke indeling deze zich bevindt en u wilt deze niet meer interpreteren of parseren.

### <a name="migrating-the-account-cache"></a>De account cache migreren

Bij het migreren van ADAL slaan apps normaal ADAL `userId` , die niet de `identifier` vereiste zijn voor MSAL. Als een eenmalige migratie stap kan een app een query uitvoeren op een MSAL-account met behulp van de gebruikers-id van ADAL met de volgende API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Met deze API wordt de cache van zowel MSAL als ADAL gelezen om het account te vinden via ADAL userId (UPN).

Als het account wordt gevonden, moet de ontwikkelaar het account gebruiken voor het op de achtergrond ophalen van het token. Bij de eerste aanschaf van een Silent-token wordt het account effectief bijgewerkt en de ontwikkelaar krijgt een MSAL-compatibele account-id in het MSAL-resultaat ( `identifier` ). Daarna `identifier` moet u alleen worden gebruikt voor account lookups met behulp van de volgende API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Hoewel het mogelijk is om ADAL te blijven gebruiken `userId` voor alle bewerkingen in MSAL, omdat `userId` deze is gebaseerd op UPN, gelden er meerdere beperkingen die leiden tot een onjuiste gebruikers ervaring. Als de UPN bijvoorbeeld wijzigt, moet de gebruiker zich opnieuw aanmelden. We raden aan dat alle apps het niet- `identifier` bewerkte account gebruiken voor alle bewerkingen.

Meer informatie over de [cache status migratie](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Wijzigingen aan token overname

MSAL introduceert een aantal wijzigingen bij het ophalen van tokens-aanroepen:

* Net als ADAL `acquireTokenSilent` resulteert altijd in een Silent-aanvraag.
* In tegens telling tot ADAL, worden `acquireToken` met de gebruikers interface of de Microsoft Authenticator-app altijd resultaten weer gegeven. Afhankelijk van de SSO-status in webweergave/Microsoft Authenticator, kan de gebruiker worden gevraagd om zijn of haar referenties in te voeren.
* In ADAL wordt `acquireToken` met `AD_PROMPT_AUTO` de eerste poging om het token te verkrijgen, en wordt alleen de gebruikers interface weer gegeven als de Silent-aanvraag is mislukt. In MSAL kan deze logica worden bereikt door eerst te roepen `acquireTokenSilent` en alleen aan te roepen `acquireToken` als Silent Acquisition niet kan worden uitgevoerd. Hierdoor kunnen ontwikkel aars de gebruikers ervaring aanpassen voordat ze de interactieve aanschaf van het token starten.

### <a name="error-handling-differences"></a>Verschillen in fout afhandeling

MSAL biedt meer duidelijkheid over fouten die kunnen worden verwerkt door uw app en die door de gebruiker moeten worden uitgevoerd. Er is een beperkt aantal fouten dat door de ontwikkelaar moet worden afgehandeld:

* `MSALErrorInteractionRequired`: De gebruiker moet een interactieve aanvraag uitvoeren. Dit kan verschillende oorzaken hebben, zoals een verlopen verificatie sessie, het beleid voor voorwaardelijke toegang is gewijzigd, een vernieuwings token is verlopen of ingetrokken, er zijn geen geldige tokens in de cache, enzovoort.
* `MSALErrorServerDeclinedScopes`: De aanvraag is niet volledig voltooid en sommige bereiken hebben geen toegang gekregen. Dit kan worden veroorzaakt door een gebruiker die toestemming weigert voor een of meer scopes.

Het verwerken van alle andere fouten in de [ `MSALError` lijst](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) is optioneel. U kunt de informatie in deze fouten gebruiken om de gebruikers ervaring te verbeteren.

Zie [uitzonde ringen en fouten afhandelen met MSAL](msal-error-handling-ios.md) voor meer informatie over het afhandelen van MSAL-fouten.

### <a name="broker-support"></a>Broker-ondersteuning

MSAL, te beginnen met versie 0.3.0, biedt ondersteuning voor brokered-verificatie met behulp van de app Microsoft Authenticator. Microsoft Authenticator biedt ook ondersteuning voor scenario's voor voorwaardelijke toegang. Voor beelden van scenario's voor voorwaardelijke toegang zijn het nalevings beleid voor apparaten waarvoor de gebruiker het apparaat moet registreren via intune of bij AAD moet registreren om een token op te halen. En Mobile Application Management (MAM) beleids regels voor voorwaardelijke toegang, waarvoor naleving moet worden bewezen voordat uw app een token kan verkrijgen.

Om Broker in te scha kelen voor uw toepassing:

1. Registreer een Broker-indeling die compatibel is met een omleidings-URI voor de toepassing. De indeling van de Broker-compatibele omleidings-URI is `msauth.<app.bundle.id>://auth` . Vervang door `<app.bundle.id>` de bundel-id van uw toepassing. Als u migreert van ADAL en uw toepassing al als Broker geschikt is, hoeft u niets meer te doen. Uw vorige omleidings-URI is volledig compatibel met MSAL. u kunt dus door gaan naar stap 3.

2. Voeg het omleidings-URI-schema van uw toepassing toe aan het bestand info. plist. Voor de standaard MSAL-omleidings-URI is de indeling `msauth.<app.bundle.id>` . Bijvoorbeeld:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Voeg volgende schema's toe aan de info. plist van uw app onder LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Voeg het volgende toe aan het bestand AppDelegate. m voor het afhandelen van retour aanroepen: objectief-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Business to business (B2B)

In ADAL maakt u afzonderlijke exemplaren van `ADAuthenticationContext` voor elke Tenant waarvoor de app tokens aanvraagt. Dit is niet langer een vereiste in MSAL. In MSAL kunt u een enkele instantie van maken `MSALPublicClientApplication` en gebruiken voor elke Aad-Cloud en organisatie door een andere instantie op te geven voor acquireToken-en acquireTokenSilent-aanroepen.

## <a name="sso-in-partnership-with-other-sdks"></a>Eenmalige aanmelding in partnerschap met andere Sdk's

MSAL voor iOS kan eenmalige aanmelding behaalt via een Unified cache met de volgende Sdk's:

- ADAL doelstelling-C 2.7. x +
- MSAL.NET voor Xamarin 2.4. x +
- ADAL.NET voor Xamarin 4.4. x +

SSO wordt bereikt via het delen van iOS-sleutel hanger en is alleen beschikbaar voor apps die zijn gepubliceerd vanuit hetzelfde Apple Developer-account.

SSO via iOS-sleutel hanger delen is het enige type Silent SSO.

MSAL kan op macOS een eenmalige aanmelding uitvoeren met andere MSAL voor iOS-en macOS-toepassingen en ADAL-toepassingen op basis van een doel-C.

MSAL op iOS biedt ook ondersteuning voor twee andere typen SSO:

* Eenmalige aanmelding via de webbrowser. MSAL voor iOS biedt ondersteuning voor `ASWebAuthenticationSession` SSO via cookies die worden gedeeld tussen andere apps op het apparaat en met name de Safari-browser.
* Eenmalige aanmelding via een verificatie Broker. Op een iOS-apparaat Microsoft Authenticator fungeren als de verificatie Broker. Het kan beleid voor voorwaardelijke toegang volgen, zoals het vereisen van een compatibel apparaat, en eenmalige aanmelding bieden voor geregistreerde apparaten. MSAL Sdk's die beginnen met versie 0.3.0 ondersteunen standaard een Broker.

## <a name="intune-mam-sdk"></a>InTune MAM SDK

De [intune mam SDK](/intune/app-sdk-get-started) ondersteunt MSAL voor IOS, te beginnen met versie [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL en ADAL in dezelfde app

ADAL-versie 2.7.0 en hoger kunnen niet naast elkaar worden gebruikt in dezelfde toepassing. De belangrijkste reden hiervoor is door de algemene code van de gedeelde submodule. Omdat objectief-C geen naam ruimten ondersteunt, zijn er twee exemplaren van dezelfde klasse als u zowel ADAL-als MSAL-Frameworks aan uw toepassing toevoegt. Er is geen garantie dat de ene wordt opgenomen in runtime. Als beide Sdk's dezelfde versie van de conflicterende klasse gebruiken, kan uw app nog steeds werken. Als het echter een andere versie is, kan uw app onverwachte crashes ondervinden die lastig zijn om te diagnosticeren.

Het uitvoeren van ADAL en MSAL in dezelfde productie toepassing wordt niet ondersteund. Als u echter alleen de gebruikers van ADAL doelstelling-C naar MSAL voor iOS en macOS test en migreert, kunt u [ADAL doelstelling-c 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10)blijven gebruiken. Het is de enige versie die werkt met MSAL in dezelfde toepassing. Er zijn geen nieuwe functie-updates voor deze ADAL-versie. deze kan daarom alleen worden gebruikt voor migratie-en test doeleinden. Uw app is niet afhankelijk van de ADAL-en MSAL-dekking.

De ADAL-en MSAL-samen werking in dezelfde toepassing wordt niet ondersteund.
De ADAL-en MSAL-samen werking tussen meerdere toepassingen wordt volledig ondersteund.

## <a name="practical-migration-steps"></a>Praktische migratie stappen

### <a name="app-registration-migration"></a>Migratie van app-registratie

U hoeft uw bestaande AAD-toepassing niet te wijzigen om over te scha kelen naar MSAL en AAD-accounts in te scha kelen. Als uw ADAL-toepassing echter geen brokered-verificatie ondersteunt, moet u een nieuwe omleidings-URI voor de toepassing registreren voordat u kunt overschakelen naar MSAL.

De omleidings-URI moet de volgende indeling hebben: `msauth.<app.bundle.id>://auth` . Vervang door `<app.bundle.id>` de bundel-id van uw toepassing. Geef de omleidings-URI op in de [Azure Portal](https://aka.ms/MobileAppReg).

Alleen voor iOS, ter ondersteuning van op certificaten gebaseerde verificatie, moet een extra omleidings-URI worden geregistreerd in uw toepassing en de Azure Portal in de volgende indeling: `msauth://code/<broker-redirect-uri-in-url-encoded-form>` . Bijvoorbeeld: `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

We raden aan dat alle apps beide omleidings-Uri's registreren.

Als u ondersteuning voor incrementele toestemming wilt toevoegen, selecteert u de Api's en de machtigingen die uw app heeft geconfigureerd om toegang aan te vragen bij de app-registratie op het tabblad **API-machtigingen** .

Als u migreert van ADAL en u zowel AAD als MSA-accounts wilt ondersteunen, moet uw bestaande registratie van de toepassing worden bijgewerkt ter ondersteuning van beide. Het is niet raadzaam om uw bestaande productie-app bij te werken voor het direct ondersteunen van AAD en MSA. Maak in plaats daarvan een andere client-ID die zowel AAD als MSA ondersteunt voor het testen en nadat u hebt gecontroleerd of alle scenario's werken, werk de bestaande app bij.

### <a name="add-msal-to-your-app"></a>MSAL toevoegen aan uw app

U kunt de MSAL-SDK toevoegen aan uw app met behulp van uw favoriete pakket beheer programma. Zie [hier voor gedetailleerde instructies](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Het bestand info. plist van uw app bijwerken

Voeg voor alleen iOS het omleidings-URI-schema van uw toepassing toe aan het bestand info. plist. Voor ADAL Broker-compatibele apps moet deze al aanwezig zijn. Het standaard-URI-schema voor MSAL omleiding heeft de volgende indeling: `msauth.<app.bundle.id>` .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Voeg volgende schema's toe aan de info. plist van uw app onder `LSApplicationQueriesSchemes` .

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Uw AppDelegate-code bijwerken

Voor alleen iOS, voegt u het volgende toe aan het bestand AppDelegate. m:

Doel-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Als u Xcode 11 gebruikt**, moet u in plaats daarvan MSAL-call back in het `SceneDelegate` bestand plaatsen.
Als u ondersteuning biedt voor UISceneDelegate en UIApplicationDelegate voor compatibiliteit met oudere iOS-versies, moet MSAL-callback in beide bestanden worden geplaatst.

Doel-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift

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

Hiermee kan MSAL antwoorden van de Broker en het webonderdeel afhandelen.
Dit is niet nodig in ADAL omdat de app ' swizzled ' automatisch de methoden voor het delegeren van apps heeft. Het hand matig toevoegen van het bestand is minder fout gevoelig en geeft de toepassing meer controle.

### <a name="enable-token-caching"></a>Tokencaching inschakelen

De tokens van uw app worden met MSAL standaard in de cache opgeslagen in de sleutelketen in iOS of macOS. 

Tokencaching inschakelen:
1. Zorg ervoor dat de toepassing juist is afgemeld
2. Ga naar de instellingen voor uw Xcode-project > **tabblad Mogelijkheden** > **Delen van sleutelketens inschakelen**
3. Klik op **+** en voer een vermelding voor een **Sleutelketengroep** in: 3.a Voor iOS voert u `com.microsoft.adalcache` in 3.b Voor macOS voert u `com.microsoft.identity.universalstorage` in

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>MSALPublicClientApplication maken en overschakelen naar de acquireToken-en acquireTokeSilent-aanroepen

U kunt maken `MSALPublicClientApplication` met de volgende code:

Doel-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Roep vervolgens de account beheer-API aan om te zien of er accounts in de cache zijn:

Doel-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



of alle accounts lezen:

Doel-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Als er een account wordt gevonden, roept u de MSAL- `acquireTokenSilent` API aan:

Doel-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Volgende stappen

Meer informatie over [verificatie stromen en toepassings scenario's](authentication-flows-app-scenarios.md)