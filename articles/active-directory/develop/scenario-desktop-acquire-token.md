---
title: Een token verkrijgen om een web-API (desktop-app) aan te roepen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een bureau blad-app die web-Api's aanroept voor het verkrijgen van een token voor de app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/06/2021
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: c63ee686ae218a696069465bb8d2d1d7413a998e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104799085"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Bureau blad-app voor het aanroepen van web-Api's: een Token ophalen

Nadat u een exemplaar van de open bare client toepassing hebt gemaakt, gebruikt u dit om een token op te halen dat u vervolgens gebruikt om een web-API aan te roepen.

## <a name="recommended-pattern"></a>Aanbevolen patroon

De Web-API wordt gedefinieerd door de `scopes` . Ongeacht de ervaring die u in uw toepassing opgeeft, is het patroon dat u moet gebruiken:

- Er wordt systematisch geprobeerd een token op te halen uit de token cache door aan te roepen `AcquireTokenSilent` .
- Als deze aanroep mislukt, gebruikt `AcquireToken` u de stroom die u wilt gebruiken, die hier wordt weer gegeven `AcquireTokenXX` .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="macos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>In MSAL voor iOS en macOS

Doel-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

In het knoop punt MSAL schaft u tokens aan via de autorisatie code stroom met de bewijs sleutel voor code Exchange (PKCE). MSAL-knoop punt gebruikt een token cache in het geheugen om te zien of er gebruikers accounts in de cache zijn. Als dat het geval is, kan het account object worden door gegeven aan de- `acquireTokenSilent()` methode om een toegangs token in de cache op te halen.

```JavaScript

const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const msalTokenCache = pca.getTokenCache();

let accounts = await msalTokenCache.getAllAccounts();

    if (accounts.length > 0) {

        const silentRequest = {
            account: accounts[0], // Index must match the account that is trying to acquire token silently
            scopes: ["user.read"],
        };
    
        pca.acquireTokenSilent(silentRequest).then((response) => {
            console.log("\nSuccessful silent token acquisition");
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => console.log(error));
    } else {
        const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

        const authCodeUrlParameters = {
            scopes: ["User.Read"],
            redirectUri: "your_redirect_uri",
            codeChallenge: challenge, // PKCE Code Challenge
            codeChallengeMethod: "S256" // PKCE Code Challenge Method 
        };
        
        // get url to sign user in and consent to scopes needed for application
        pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            console.log(response);
        
            const tokenRequest = {
                code: response["authorization_code"],
                codeVerifier: verifier // PKCE Code Verifier 
                redirectUri: "your_redirect_uri",
                scopes: ["User.Read"],
            };
            
            // acquire a token by exchanging the code
            pca.acquireTokenByCode(tokenRequest).then((response) => {
                console.log("\nResponse: \n:", response);
            }).catch((error) => {
                console.log(error);
            });
        }).catch((error) => console.log(JSON.stringify(error)));
    }
```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```
---

Hier volgen de verschillende manieren om tokens te verkrijgen in een bureaublad toepassing.

## <a name="acquire-a-token-interactively"></a>Een token interactief aanschaffen

In het volgende voor beeld wordt de minimale code weer gegeven voor het interactief ophalen van een token voor het lezen van het profiel van de gebruiker met Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Verplichte para meters

`AcquireTokenInteractive` heeft slechts één verplichte para meter, ``scopes`` die een opsomming bevat van teken reeksen die de bereiken definiëren waarvoor een token is vereist. Als het token voor Microsoft Graph is, kunnen de vereiste bereiken worden gevonden in de API-verwijzing van elke Microsoft Graph-API in de sectie met de naam ' permissions '. Als u bijvoorbeeld [de contact personen van de gebruiker wilt weer geven](/graph/api/user-list-contacts), moet de scope ' gebruiker. read ', ' Contacts. read ' worden gebruikt. Raadpleeg [Microsoft Graph-machtigingen](/graph/permissions-reference) voor meer informatie.

Op Android moet u ook de bovenliggende activiteit opgeven met behulp van `.WithParentActivityOrWindow` , zoals wordt weer gegeven, zodat het token wordt teruggestuurd naar de bovenliggende activiteit na de interactie. Als u deze niet opgeeft, wordt er een uitzonde ring gegenereerd bij het aanroepen van `.ExecuteAsync()` .

### <a name="specific-optional-parameters-in-msalnet"></a>Specifieke optionele para meters in MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

De gebruikers interface is belang rijk omdat deze interactief is. `AcquireTokenInteractive` heeft één specifieke optionele para meter die kan worden opgegeven voor platforms die deze kunnen ondersteunen, de bovenliggende gebruikers interface. Bij gebruik in een bureaublad toepassing `.WithParentActivityOrWindow` heeft een ander type, dat afhankelijk is van het platform. U kunt ook de optionele para meter voor het bovenliggende venster weglaten om een venster te maken, als u niet wilt bepalen waar het dialoog venster Aanmelden wordt weer gegeven op het scherm. Dit is van toepassing op toepassingen op basis van de opdracht regel, die wordt gebruikt om aanroepen door te geven aan een andere back-end-service en geen Windows voor gebruikers interactie nodig hebt.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Opmerkingen

- Op .NET Standard geldt de verwachte `object` `Activity` voor Android, `UIViewController` op Ios, `NSWindow` op Mac en `IWin32Window` of `IntPr` in Windows.
- In Windows moet u aanroepen `AcquireTokenInteractive` vanuit de UI-thread zodat de Inge sloten browser de juiste context voor de synchronisatie van gebruikers interface krijgt. Het aanroepen van de UI-thread kan ertoe leiden dat berichten niet goed worden gepompt en dat er deadlock-scenario's zijn met de gebruikers interface. Eén manier om micro soft-verificatie bibliotheken (MSALs) aan te roepen vanuit de UI-thread als u zich al in de UI-thread bevindt, is het gebruik `Dispatcher` van de op WPF.
- Als u gebruikmaakt van WPF, kunt u de klasse gebruiken om een venster van een WPF-besturings element te verkrijgen `WindowInteropHelper.Handle` . De aanroep is vervolgens afkomstig van een WPF-besturings element ( `this` ):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` wordt gebruikt om de interactiviteit met de gebruiker te beheren door een prompt op te geven.

![Afbeelding van de velden in de structuur van de prompt. Deze constante waarden bepalen de interactiviteit met de gebruiker door het type prompt te definiëren dat wordt weer gegeven door de methode WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

De klasse definieert de volgende constanten:

- ``SelectAccount`` Hiermee wordt de STS gedwongen het dialoog venster voor account selectie te presen teren met accounts waarvoor de gebruiker een sessie heeft. Deze optie is handig wanneer ontwikkel aars van toepassingen willen laten kiezen uit verschillende identiteiten. Deze optie verstuurt MSAL om ``prompt=select_account`` naar de ID-provider te verzenden. Dit is de standaardoptie. Het biedt een goede taak om de best mogelijke ervaring te bieden op basis van de beschik bare informatie, zoals het account en de aanwezigheid van een sessie voor de gebruiker. Wijzig deze alleen als u er geen goede reden voor hebt.
- ``Consent`` Hiermee kan de ontwikkelaar van de toepassing ervoor zorgen dat de gebruiker om toestemming wordt gevraagd, zelfs als toestemming is verleend. In dit geval verzendt MSAL `prompt=consent` naar de ID-provider. Deze optie kan worden gebruikt in sommige toepassingen met een eigen beveiliging waarbij het beheer van de organisatie vereist dat de gebruiker het dialoog venster voor toestemming krijgt telkens wanneer de toepassing wordt gebruikt.
- ``ForceLogin`` Hiermee kan de ontwikkelaar van de toepassing toestaan dat de gebruiker om referenties wordt gevraagd door de service, zelfs als deze vraag van de gebruiker mogelijk niet nodig is. Deze optie kan handig zijn om de gebruiker opnieuw aan te melden als het verkrijgen van een token mislukt. In dit geval verzendt MSAL `prompt=login` naar de ID-provider. Soms wordt het gebruikt in toepassingen met een eigen beveiliging waarbij het beheer van de organisatie vereist dat de gebruiker zich opnieuw aanmeldt, telkens wanneer ze toegang hebben tot specifieke delen van een toepassing.
- ``Never`` (alleen voor .NET 4,5 en WinRT) wordt de gebruiker niet gevraagd, maar in plaats daarvan wordt geprobeerd de cookie te gebruiken die is opgeslagen in de verborgen Inge sloten webweergave. Zie webweergaves in MSAL.NET voor meer informatie. Het gebruik van deze optie kan mislukken. In dat geval wordt `AcquireTokenInteractive` een uitzonde ring gegenereerd om te melden dat er een UI-interactie nodig is. U moet een andere `Prompt` para meter gebruiken.
- ``NoPrompt`` Er wordt geen prompt verzonden naar de ID-provider. Deze optie is alleen nuttig voor Azure Active Directory (Azure AD) B2C profiel beleid bewerken. Zie [Azure AD B2C-specifiek](https://aka.ms/msal-net-b2c-specificities)voor meer informatie.

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Deze optie wordt gebruikt in een geavanceerd scenario waarin u de gebruiker vooraf toestemming wilt geven voor verschillende resources, en u geen incrementele toestemming wilt gebruiken, die normaal gesp roken wordt gebruikt met MSAL.NET/the micro soft Identity platform. Zie voor meer informatie [de gebruiker toestemming geven vóór verschillende bronnen](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Een web-UI is een mechanisme voor het aanroepen van een browser. Dit mechanisme kan een specifiek UI-webbrowser-besturings element zijn of een manier om het openen van de browser te delegeren.
MSAL biedt web-UI-implementaties voor de meeste platformen, maar er zijn gevallen waarin u de browser zelf wilt hosten:

- Platforms die niet expliciet worden gedekt door MSAL, bijvoorbeeld razendsnelle, eenheid en mono op Desk tops.
- U wilt uw toepassing testen door de gebruikers interface en een geautomatiseerde browser gebruiken die kan worden gebruikt met selenium.
- De browser en de app die MSAL uitvoeren, bevinden zich in afzonderlijke processen.

##### <a name="at-a-glance"></a>In een oogopslag

Hiervoor geeft u MSAL op `start Url` die moeten worden weer gegeven in een browser van de keuze, zodat de eind gebruiker items zoals hun gebruikers naam kan invoeren.
Nadat de verificatie is voltooid, moet uw app teruggaan naar MSAL `end Url` . Deze bevat een code van Azure AD.
De host van `end Url` is altijd `redirectUri` . Voer een van de volgende handelingen uit om een onderscheping uit te `end Url` voeren:

- Browser omleidingen bewaken totdat `redirect Url` Er wordt bereikt.
- De browser omleiden naar een URL die u bewaken.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi is een uitbreidings punt

`WithCustomWebUi` is een uitbreidbaar punt dat u kunt gebruiken om uw eigen gebruikers interface op te geven in open bare client toepassingen. U kunt de gebruiker ook het/authorize-eind punt van de ID-provider laten door lopen en hen laten aanmelden en toestemming geven. MSAL.NET kan vervolgens de verificatie code inwisselen en een Token ophalen. Het wordt bijvoorbeeld gebruikt in Visual Studio om electrons-toepassingen (bijvoorbeeld de feedback van Visual Studio) te bieden voor de webinteractie, maar laat MSAL.NET het meeste werk doen. U kunt deze ook gebruiken als u UI-automatisering wilt bieden. In open bare client toepassingen gebruikt MSAL.NET de bewijs sleutel voor code Exchange (PKCE)-standaard om ervoor te zorgen dat de beveiliging wordt geëerbiedigd. Alleen MSAL.NET kunnen de code inwisselen. Zie [RFC 7636-proof Key voor code Exchange by OAuth Public clients](https://tools.ietf.org/html/rfc7636)(Engelstalig) voor meer informatie.

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>WithCustomWebUi gebruiken

`.WithCustomWebUI`Volg deze stappen om te gebruiken.

  1. Implementeer de `ICustomWebUi`-interface. Zie [deze website](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70)voor meer informatie. Implementeer één `AcquireAuthorizationCodeAsync` methode en accepteer de door MSAL.net berekende verificatie code-URL. Laat de gebruiker vervolgens de interactie door lopen met de ID-provider en retour neer de URL waarmee de identiteits provider uw implementatie weer samen met de autorisatie code zou noemen. Als u problemen ondervindt, moet uw implementatie een `MsalExtensionException` uitzonde ring genereren voor de samen werking met MSAL.
  2. Gebruik in uw `AcquireTokenInteractive` oproep de `.WithCustomUI()` wijzigings functie waarmee het exemplaar van uw aangepaste webgebruikersinterface wordt door gegeven.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Voor beelden van implementatie van ICustomWebUi in test Automation: SeleniumWebUI

Het MSAL.NET-team heeft de UI-tests herschreven om dit uitbreidings mechanisme te gebruiken. Als u geïnteresseerd bent, bekijkt u de klasse [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) in de bron code MSAL.net.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Een fantastische ervaring bieden met SystemWebViewOptions

Vanuit MSAL.NET 4,1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) kunt u het volgende opgeven:

- De URI naar ( `BrowserRedirectError` ) of het HTML-fragment dat moet worden weer gegeven ( `HtmlMessageError` ) in het geval van aanmeld-of toestemmings fouten in de webbrowser van het systeem.
- De URI naar ( `BrowserRedirectSuccess` ) of het HTML-fragment dat moet worden weer gegeven ( `HtmlMessageSuccess` ) in het geval van een geslaagde aanmelding of toestemming.
- De actie die moet worden uitgevoerd om de systeem browser te starten. U kunt uw eigen implementatie opgeven door de gemachtigde in te stellen `OpenBrowserAsync` . De klasse biedt ook een standaard implementatie voor twee browsers: `OpenWithEdgeBrowserAsync` en `OpenWithChromeEdgeBrowserAsync` voor respectievelijk micro soft Edge en [micro soft Edge in chroom](https://www.windowscentral.com/faq-edge-chromium).

Als u deze structuur wilt gebruiken, schrijft u iets zoals in het volgende voor beeld:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Andere optionele para meters

Zie AcquireTokenInteractiveParameterBuilder voor meer informatie over de andere optionele para meters voor `AcquireTokenInteractive` . [](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods)

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[MacOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>In MSAL voor iOS en macOS

Doel-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

In het knoop punt MSAL schaft u tokens aan via de autorisatie code stroom met de bewijs sleutel voor code Exchange (PKCE). Het proces bestaat uit twee stappen: eerst haalt de toepassing een URL op die kan worden gebruikt om een autorisatie code te genereren. Deze URL kan worden geopend in een browser van Choice, waar de gebruiker hun referenties kan invoeren en wordt teruggeleid naar de `redirectUri` (geregistreerd tijdens de registratie van de app) met een autorisatie code. Ten tweede geeft de toepassing de autorisatie code door die is ontvangen voor de `acquireTokenByCode()` methode waarmee deze wordt uitgewisseld voor een toegangs token.

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };
    
    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

MSAL python biedt rechtstreeks geen directe methode voor het ophalen van tokens. In plaats daarvan moet de toepassing een autorisatie aanvraag verzenden in de implementatie van de gebruikers interactie stroom om een autorisatie code te verkrijgen. Deze code kan vervolgens worden door gegeven aan de- `acquire_token_by_authorization_code` methode om het token op te halen.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```
---

## <a name="integrated-windows-authentication"></a>Geïntegreerde Windows-authenticatie

Als u zich wilt aanmelden voor een domein gebruiker op een domein of Azure AD-computer, gebruikt u geïntegreerde Windows-verificatie (IWA).

### <a name="constraints"></a>Beperkingen

- Geïntegreerde Windows-verificatie is alleen bruikbaar voor *federatieve en* gebruikers, dat wil zeggen, gebruikers die zijn gemaakt in Active Directory en worden ondersteund door Azure AD. Gebruikers die rechtstreeks in azure AD zijn gemaakt zonder Active Directory back-ups, ook wel *Managed* users genoemd, kunnen deze verificatie stroom niet gebruiken. Deze beperking heeft geen invloed op de gebruikers naam en het wacht woord.
- IWA is bedoeld voor apps die zijn geschreven voor .NET Framework-, .NET core-en Universeel Windows-platform-platforms (UWP).
- Met IWA wordt [multi-factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md)niet omzeild. Als MFA is geconfigureerd, kan IWA mislukken als een MFA-Challenge vereist is, omdat MFA gebruikers interactie vereist.
  
    IWA is niet-interactief, maar MFA vereist een interactiviteit van de gebruiker. U kunt niet bepalen wanneer de identiteits provider MFA moet aanvragen om te worden uitgevoerd, de Tenant beheerder. Vanuit onze waarnemingen is MFA vereist wanneer u zich aanmeldt vanuit een ander land of een andere regio, wanneer u zich niet via VPN hebt verbonden met een bedrijfs netwerk, en soms zelfs wanneer er verbinding is via VPN. Er wordt geen deterministische set regels verwacht. Azure AD maakt gebruik van AI om voortdurend te leren of MFA vereist is. Terugvallen op een gebruikers prompt, zoals interactieve verificatie of IWA, als er een storing optreedt in de service.

- De door gegeven instantie `PublicClientApplicationBuilder` moet zijn:
  - De Tenant van het formulier `https://login.microsoftonline.com/{tenant}/` , waarbij de `tenant` GUID is die de Tenant-id vertegenwoordigt of een domein dat is gekoppeld aan de Tenant.
  - Voor werk-en school accounts: `https://login.microsoftonline.com/organizations/` .
  - Persoonlijke micro soft-accounts worden niet ondersteund. U kunt geen/veelvoorkomende-of/consumers-tenants gebruiken.

- Geïntegreerde Windows-verificatie is een stille stroom:
  - De gebruiker van uw toepassing moet eerder hebben ingestemd om de toepassing te gebruiken.
  - Of de Tenant beheerder moet eerder toestemming hebben gegeven voor alle gebruikers in de Tenant om de toepassing te gebruiken.
  - Met andere woorden:
    - U bent als ontwikkelaar geselecteerd voor de **toekennings** knop in de Azure Portal.
    - Of een Tenant beheerder heeft de knop **toestemming geven/ingetrokken beheerder voor {Tenant domein}** geselecteerd op het tabblad **API-machtigingen** van de registratie voor de toepassing. Zie [machtigingen toevoegen voor toegang tot uw web-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)voor meer informatie.
    - Of u hebt een manier gegeven waarop gebruikers toestemming kunnen geven voor de toepassing. Zie [aanvragen van individuele gebruikers toestemming](./v2-permissions-and-consent.md#requesting-individual-user-consent)voor meer informatie.
    - Of u hebt een manier gegeven waarop de Tenant beheerder toestemming kan geven voor de toepassing. Zie toestemming van de [beheerder](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)voor meer informatie.

- Deze stroom is ingeschakeld voor .NET-Desktop-, .NET core-en UWP-apps.

Zie [machtigingen en toestemming van micro soft-identiteits platform](./v2-permissions-and-consent.md)voor meer informatie over toestemming.

### <a name="learn-how-to-use-it"></a>Meer informatie over hoe u deze kunt gebruiken

# <a name="net"></a>[.NET](#tab/dotnet)

Gebruik in MSAL.NET:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Normaal gesp roken hebt u slechts één para meter ( `scopes` ) nodig. Afhankelijk van de manier waarop de Windows-beheerder het beleid heeft ingesteld, is het mogelijk dat toepassingen op uw Windows-computer de aangemelde gebruiker niet kunnen opzoeken. In dat geval gebruikt u een tweede methode, `.WithUsername()` en geeft u de gebruikers naam van de aangemelde gebruiker door als UPN-indeling, bijvoorbeeld `joe@contoso.com` .

In het volgende voor beeld wordt het meest recente geval weer gegeven, met uitleg over het soort uitzonde ringen dat u kunt krijgen en de bijbehorende oplossingen.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Zie [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods)voor de lijst met mogelijke para meters op AcquireTokenByIntegratedWindowsAuthentication.

# <a name="java"></a>[Java](#tab/java)

Deze extractie is afkomstig uit de [MSAL java dev](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)-voor beelden.

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[MacOS](#tab/macOS)

Deze stroom is niet van toepassing op macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Deze stroom wordt nog niet ondersteund in het knoop punt MSAL.

# <a name="python"></a>[Python](#tab/python)

Deze stroom wordt nog niet ondersteund in MSAL python.

---

## <a name="username-and-password"></a>Gebruikersnaam en wachtwoord

U kunt ook een token verkrijgen door de gebruikers naam en het wacht woord op te geven. Deze stroom is beperkt en niet aanbevolen, maar er zijn nog steeds gebruik van gevallen waarin het nodig is.

### <a name="this-flow-isnt-recommended"></a>Deze stroom wordt niet aanbevolen

De gebruikers naam en het wacht woord worden *niet aanbevolen* omdat uw toepassing een gebruiker vraagt voor het wacht woord niet veilig is. Zie [Wat is de oplossing voor het groeiende probleem van wacht woorden?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) voor meer informatie. De voorkeurs stroom voor het op de achtergrond verkrijgen van een token op computers die lid zijn van een Windows-domein is [geïntegreerde Windows-verificatie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). U kunt ook de [code stroom](https://aka.ms/msal-net-device-code-flow)van het apparaat gebruiken.

Het gebruik van een gebruikers naam en wacht woord is handig in sommige gevallen, zoals DevOps-scenario's. Maar als u een gebruikers naam en wacht woord wilt gebruiken in interactieve scenario's waarin u uw eigen gebruikers interface opgeeft, moet u nadenken hoe u deze kunt verlaten. Door een gebruikers naam en wacht woord op te geven, krijgt u een aantal dingen:

- Kern principes van moderne identiteit. Een wacht woord kan worden gephishd en opnieuw afgespeeld omdat een gedeeld geheim kan worden onderschept. Het is niet compatibel met een wacht woord.
- Gebruikers die MFA nodig hebben, kunnen zich niet aanmelden omdat er geen interactie is.
- Gebruikers kunnen eenmalige aanmelding (SSO) niet uitvoeren.

### <a name="constraints"></a>Beperkingen

De volgende beperkingen zijn ook van toepassing:

- De gebruikers naam en het wacht woord stroom zijn niet compatibel met voorwaardelijke toegang en multi-factor Authentication. Als uw app wordt uitgevoerd in een Azure AD-Tenant waarvoor de Tenant beheerder multi-factor Authentication vereist, kunt u deze stroom niet gebruiken. Veel organisaties doen dat.
- Het werkt alleen voor werk-en school accounts (niet MSA).
- De stroom is beschikbaar op .NET Desktop en .NET core, maar niet op UWP.

### <a name="b2c-specifics"></a>B2C-specifiek

Zie voor meer informatie [wacht woord voor de resource-eigenaar (ROPC) met B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Gebruiken

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`bevat de-methode `AcquireTokenByUsernamePassword` .

In het volgende voor beeld wordt een vereenvoudigd geval weer gegeven.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

In het volgende voor beeld wordt het meest recente geval weer gegeven, met uitleg over het soort uitzonde ringen dat u kunt krijgen en de bijbehorende oplossingen.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Zie AcquireTokenByUsernamePasswordParameterBuilder voor meer informatie over alle wijzigingen die kunnen worden toegepast op `AcquireTokenByUsernamePassword` . [](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder#methods)

# <a name="java"></a>[Java](#tab/java)

De volgende extractie is afkomstig uit de [MSAL java dev](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)-voor beelden.

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[MacOS](#tab/macOS)

Deze stroom wordt niet ondersteund op MSAL voor macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Deze extractie is afkomstig uit de [MSAL-knooppunt dev](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/username-password)-voor beelden. In het onderstaande code fragment worden de gebruikers naam en het wacht woord alleen voor illustratie doeleinden hardcoded. Dit moet worden vermeden in de productie omgeving. In plaats daarvan wordt de gebruiker door een eenvoudige gebruikers interface gevraagd om zijn gebruikers naam en wacht woord in te voeren. 

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

// For testing, enter your username and password below.
// In production, replace this with a UI prompt instead.
const usernamePasswordRequest = {
    scopes: ["user.read"],
    username: "", // Add your username here
    password: "", // Add your password here
};

pca.acquireTokenByUsernamePassword(usernamePasswordRequest).then((response) => {
    console.log("acquired token by password grant");
}).catch((error) => {
    console.log(error);
});
```

# <a name="python"></a>[Python](#tab/python)

Deze extractie is afkomstig uit de [MSAL python dev](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)-voor beelden.

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

---

## <a name="command-line-tool-without-a-web-browser"></a>Opdracht regel programma zonder webbrowser

### <a name="device-code-flow"></a>Stroom voor apparaatcode

Als u een opdracht regel programma schrijft dat geen Webbe sturings elementen heeft en u de voor gaande stromen niet of wilt gebruiken, gebruikt u de code stroom van het apparaat.

Interactieve verificatie met Azure AD vereist een webbrowser. Zie het [gebruik van webbrowsers](https://aka.ms/msal-net-uses-web-browser)voor meer informatie. Als u gebruikers wilt verifiëren op apparaten of besturings systemen die geen webbrowser hebben, kunt u met de code stroom van het apparaat een ander apparaat gebruiken, zoals een computer of een mobiele telefoon om zich interactief aan te melden. Door gebruik te maken van de code stroom van het apparaat, haalt de toepassing tokens op via een proces met twee stappen dat is ontworpen voor deze apparaten of besturings systemen. Voor beelden van dergelijke toepassingen zijn toepassingen die worden uitgevoerd op iOT of opdracht regel programma's (CLI). Het idee is dat:

1. Wanneer gebruikers verificatie is vereist, biedt de app een code voor de gebruiker. De gebruiker wordt gevraagd een ander apparaat te gebruiken, zoals een smartphone met Internet verbinding, om naar een URL te gaan, bijvoorbeeld `https://microsoft.com/devicelogin` . Vervolgens wordt de gebruiker gevraagd de code in te voeren. Op die manier wordt de gebruiker door de webpagina geleid via een normale verificatie-ervaring, inclusief toestemming prompts en multi-factor Authentication, indien nodig.

2. Wanneer de verificatie is geslaagd, ontvangt de opdracht regel-app de vereiste tokens via een back-upkanaal en worden deze gebruikt om de Web-API te kunnen uitvoeren.

### <a name="use-it"></a>Gebruiken

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`bevat een methode met de naam `AcquireTokenWithDeviceCode` .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Deze methode neemt de volgende para meters:

- De `scopes` om een toegangs token voor op te vragen.
- Een call back die de ontvangt `DeviceCodeResult` .

  ![DeviceCodeResult-eigenschappen](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

De volgende voorbeeld code geeft de samen vatting van de meeste huidige gevallen, met uitleg over het soort uitzonde ringen dat u kunt krijgen en de mogelijke oplossingen. Zie voor een volledig functionele code-voor beeld [Active-Directory-dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) op github.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Deze extractie is afkomstig uit de [MSAL java dev](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)-voor beelden.

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[MacOS](#tab/macOS)

Deze stroom is niet van toepassing op macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Deze extractie is afkomstig uit de [MSAL-knooppunt dev](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/standalone-samples/device-code)-voor beelden.

```JavaScript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const deviceCodeRequest = {
    deviceCodeCallback: (response) => (console.log(response.message)),
    scopes: ["user.read"],
    timeout: 20,
};

pca.acquireTokenByDeviceCode(deviceCodeRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

# <a name="python"></a>[Python](#tab/python)

Deze extractie is afkomstig uit de [MSAL python dev](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)-voor beelden.

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

---

## <a name="file-based-token-cache"></a>Token cache op basis van bestanden

In MSAL.NET wordt standaard een token cache in het geheugen beschikbaar gesteld.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serialisatie is aanpasbaar in Windows-bureau blad-apps en web-apps of Web-Api's

In het geval van .NET Framework en .NET Core kunt u, als u dit niet doet, de token cache in het geheugen voor de duur van de toepassing. Houd er rekening mee dat de MSAL .NET desktop-of .NET core-toepassingen van toepassing zijn op console-of Windows-toepassingen (die toegang tot het bestands systeem zouden hebben), *maar ook* webtoepassingen of Web-api's. Deze web-apps en Web-Api's gebruiken mogelijk enkele specifieke cache mechanismen zoals data bases, gedistribueerde caches en redis-caches. Als u een permanente token cache-toepassing in .NET desktop of .NET core wilt hebben, moet u de serialisatie aanpassen.

Klassen en interfaces die betrokken zijn bij de serialisatie van token-cache zijn de volgende typen:

- ``ITokenCache``, waarmee gebeurtenissen worden gedefinieerd om te worden geabonneerd op aanvragen van de token cache-serialisatie en methoden voor het serialiseren of deserialiseren van de cache in verschillende indelingen (ADAL v 3.0, MSAL 2. x en MSAL 3. x = ADAL v 5.0).
- ``TokenCacheCallback`` is een call back aan de gebeurtenissen door gegeven, zodat u de serialisatie kunt afhandelen. Ze worden aangeroepen met argumenten van het type ``TokenCacheNotificationArgs`` .
- ``TokenCacheNotificationArgs`` biedt alleen de toepassing ``ClientId`` en een verwijzing naar de gebruiker waarvoor het token beschikbaar is.

  ![Schema voor serialisatie van token cache](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET maakt token caches voor u en biedt u de `IToken` cache wanneer u een toepassing `UserTokenCache` en eigenschappen aanroept `AppTokenCache` . U zou de interface niet zelf moeten implementeren. Uw verantwoordelijkheid, wanneer u een aangepaste token cache-serialisatie implementeert, is:
>
> - Reageren op `BeforeAccess` en `AfterAccess` gebeurtenissen, of hun *async* -equivalent. De `BeforeAccess` gemachtigde is verantwoordelijk voor het deserialiseren van de cache. De `AfterAccess` gemachtigde is verantwoordelijk voor het serialiseren van de cache.
> - Begrijp dat deel van deze gebeurtenissen Store of laad-blobs, die worden door gegeven via het gebeurtenis argument voor elke gewenste opslag.

De strategieën verschillen, afhankelijk van of u een token cache-serialisatie schrijft voor een open bare client toepassing, zoals een bureau blad of een vertrouwelijke client toepassing, zoals een web-app of Web-API of een daemon-app.

Sinds MSAL v2. x hebt u verschillende opties. Uw keuze is afhankelijk van of u de cache alleen wilt serialiseren naar de MSAL.NET-indeling, een Unified Format-cache die gebruikelijk is met MSAL, maar ook op de platformen. Het is ook mogelijk dat u de [verouderde](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) token cache-serialisatie van ADAL v3 wilt ondersteunen.

De aanpassing van de token cache-serialisatie voor het delen van de SSO-status tussen ADAL.NET 3. x, ADAL.NET 5. x en MSAL.NET wordt uitgelegd in een deel van het voor beeld van [Active Directory-DotNet-v1-naar-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

### <a name="simple-token-cache-serialization-msal-only"></a>Eenvoudige token cache-serialisatie (alleen MSAL)

Het volgende voor beeld is een Naïve-implementatie van aangepaste serialisatie van een token cache voor bureaublad toepassingen. Hier bevindt de token cache van de gebruiker zich in een bestand in dezelfde map als de toepassing of, in een per gebruiker per app-map, in het geval waarin de app een [verpakte bureaublad toepassing](/windows/msix/desktop/desktop-to-uwp-behind-the-scenes)is. Voor de volledige code raadpleegt u het volgende voor beeld: [Active-Directory-DotNet-Desktop-MSGraph-v2](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2).

Nadat u de toepassing hebt gemaakt, schakelt u de serialisatie in door ``TokenCacheHelper.EnableSerialization()`` de toepassing aan te roepen en door te geven `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Deze helperklasse ziet eruit als het volgende code fragment:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
   try
   {
    // For packaged desktop apps (MSIX packages) the executing assembly folder is read-only. 
    // In that case we need to use Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path + "\msalcache.bin" 
    // which is a per-app read/write folder for packaged apps.
    // See https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-behind-the-scenes
    CacheFilePath = System.IO.Path.Combine(Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path, "msalcache.bin3");
   }
   catch (System.InvalidOperationException)
   {
    // Fall back for an un-packaged desktop app
    CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin";
   }
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static string CacheFilePath { get; private set; }

  private static readonly object FileLock = new object();

  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Een preview van een productkwaliteits token op basis van een op bestanden gebaseerde serialisatiefunctie voor open bare client toepassingen voor bureaublad toepassingen die worden uitgevoerd op Windows, Mac en Linux, is beschikbaar via de open-source bibliotheek [micro soft. Identity. client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) . U kunt deze in uw toepassingen insluiten vanuit het volgende NuGet-pakket: [micro soft. Identity. client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Disclaimer: de bibliotheek micro soft. Identity. client. Extensions. Msal is een uitbrei ding van MSAL.NET. Klassen in deze bibliotheken kunnen in de toekomst worden MSAL.NET, zoals of met breuk wijzigingen.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Dubbele token cache-serialisatie (MSAL Unified cache + ADAL v3)

Mogelijk wilt u de token cache-serialisatie implementeren met de indeling Unified cache. Deze indeling is gebruikelijk voor ADAL.NET 4. x en MSAL.NET 2. x, en met andere MSALs van dezelfde generatie of ouder, op hetzelfde platform. Krijg inspiratie met de volgende code:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Deze keer ziet de helper class er als volgt uit:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Gevanceerde Toegang krijgen tot de tokens in de cache van de gebruiker in de achtergrond-apps en-services

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [roep een web-API aan vanuit de bureau blad-app](scenario-desktop-call-api.md).
