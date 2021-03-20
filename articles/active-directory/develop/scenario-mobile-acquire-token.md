---
title: Een token verkrijgen om een web-API (Mobile apps) aan te roepen | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een mobiele app die web-Api's aanroept. (Een Token ophalen voor de app.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6e6eda3d711710ea7450165ab02d7a260067bfcb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99582550"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Een Token ophalen voor een mobiele app die web-Api's aanroept

Voordat uw app beveiligde web-Api's kan aanroepen, heeft deze een toegangs token nodig. Dit artikel begeleidt u bij het proces voor het ophalen van een token met behulp van de micro soft Authentication Library (MSAL).

## <a name="define-a-scope"></a>Een bereik definiëren

Wanneer u een token aanvraagt, definieert u een bereik. Het bereik bepaalt welke gegevens uw app kan gebruiken.

De eenvoudigste manier om een bereik te definiëren is het combi neren van de gewenste Web-API `App ID URI` met het bereik `.default` . Deze definitie vertelt het micro soft-identiteits platform dat uw app alle scopes vereist die in de portal zijn ingesteld.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Tokens ophalen

### <a name="acquire-tokens-via-msal"></a>Tokens verkrijgen via MSAL

MSAL staat apps toe om tokens op de achtergrond en interactief te verkrijgen. Wanneer u belt `AcquireTokenSilent()` of `AcquireTokenInteractive()` , retourneert MSAL een toegangs token voor de aangevraagde bereiken. Het juiste patroon is het maken van een Silent-aanvraag en terugvallen op een interactieve aanvraag.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

Probeer eerst een token op de achtergrond te verkrijgen:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

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

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Als MSAL wordt geretourneerd `MSALErrorInteractionRequired` , kunt u tokens interactief ophalen:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
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

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL voor iOS en macOS ondersteunt diverse aanpassingen om een token interactief of op de achtergrond te verkrijgen:
* [Algemene para meters voor het ophalen van een token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Para meters voor het ophalen van een interactief token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Para meters voor het ophalen van een Silent token](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

In het volgende voor beeld wordt de minimale code weer gegeven om een token interactief op te halen. In het voor beeld wordt Microsoft Graph gebruikt voor het lezen van het profiel van de gebruiker.

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

#### <a name="mandatory-parameters-in-msalnet"></a>Verplichte para meters in MSAL.NET

`AcquireTokenInteractive` heeft slechts één verplichte para meter: `scopes` . `scopes`Met de para meter worden teken reeksen opgesomd die de bereiken definiëren waarvoor een token is vereist. Als het token voor Microsoft Graph is, kunt u de vereiste bereiken vinden in de API-verwijzing van elke Microsoft Graph-API. Ga in de verwijzing naar de sectie ' machtigingen '.

Als u bijvoorbeeld [de contact personen van de gebruiker wilt weer geven](/graph/api/user-list-contacts), gebruikt u de scope ' gebruiker. read ', ' Contacts. read '. Raadpleeg [Microsoft Graph-machtigingen](/graph/permissions-reference) voor meer informatie.

Op Android kunt u de bovenliggende activiteit opgeven wanneer u de app maakt met behulp van `PublicClientApplicationBuilder` . Als u de bovenliggende activiteit op dat moment niet opgeeft, kunt u deze later opgeven met behulp `.WithParentActivityOrWindow` van zoals in de volgende sectie. Als u bovenliggende activiteit opgeeft, wordt het token na de interactie teruggestuurd naar die bovenliggende activiteit. Als u deze niet opgeeft, genereert de `.ExecuteAsync()` aanroep een uitzonde ring.

#### <a name="specific-optional-parameters-in-msalnet"></a>Specifieke optionele para meters in MSAL.NET

In de volgende secties worden de optionele para meters in MSAL.NET uitgelegd.

##### <a name="withprompt"></a>WithPrompt

De `WithPrompt()` para meter bepaalt de interactiviteit met de gebruiker door een prompt op te geven.

![Afbeelding van de velden in de structuur van de prompt. Deze constante waarden bepalen de interactiviteit met de gebruiker door het type prompt te definiëren dat wordt weer gegeven door de para meter WithPrompt ().](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

De klasse definieert de volgende constanten:

- `SelectAccount` Hiermee wordt de Security Token Service (STS) gedwongen om het dialoog venster voor het selecteren van accounts weer te geven. Het dialoog venster bevat de accounts waarvoor de gebruiker een sessie heeft. U kunt deze optie gebruiken als u wilt dat de gebruiker uit verschillende identiteiten kan kiezen. Deze optie verstuurt MSAL om `prompt=select_account` naar de ID-provider te verzenden.

    De `SelectAccount` constante is de standaard waarde en levert de best mogelijke ervaring op basis van de beschik bare informatie. De beschik bare informatie kan bestaan uit account, aanwezigheid van een sessie voor de gebruiker, enzovoort. Wijzig deze standaard instelling alleen als u er een goede reden voor hebt.
- `Consent` Hiermee kunt u de gebruiker om toestemming vragen, zelfs als toestemming is verleend. In dit geval verzendt MSAL `prompt=consent` naar de ID-provider.

    U kunt de `Consent` constante gebruiken in toepassingen met een eigen beveiliging waarbij het beheer van de organisatie vereist dat gebruikers het dialoog venster voor toestemming zien telkens wanneer ze de toepassing gebruiken.
- `ForceLogin` Hiermee kan de service de gebruiker om referenties vragen, zelfs als de prompt niet nodig is.

    Deze optie kan nuttig zijn als het ophalen van het token mislukt en u de gebruiker opnieuw wilt aanmelden. In dit geval verzendt MSAL `prompt=login` naar de ID-provider. U kunt deze optie gebruiken in toepassingen met een eigen beveiliging waarbij de gebruiker zich bij het beheer van de organisatie moet aanmelden elke keer dat ze toegang hebben tot specifieke delen van de toepassing.
- `Never` is alleen voor .NET 4,5 en Windows Runtime (WinRT). Met deze constante wordt de gebruiker niet gevraagd, maar wordt geprobeerd de cookie te gebruiken die is opgeslagen in de verborgen Inge sloten webweergave. Zie [Using Web browsers with MSAL.net](./msal-net-web-browsers.md)(Engelstalig) voor meer informatie.

    Als deze optie mislukt, wordt `AcquireTokenInteractive` een uitzonde ring gegenereerd om u te waarschuwen dat er een UI-interactie nodig is. Gebruik vervolgens een andere `Prompt` para meter.
- `NoPrompt` Er wordt geen prompt verzonden naar de ID-provider.

    Deze optie is alleen nuttig voor beleid voor het bewerken van profielen in Azure Active Directory B2C. Zie [B2C-specifiek](https://aka.ms/msal-net-b2c-specificities)voor meer informatie.

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Gebruik de `WithExtraScopeToConsent` wijzigings functie in een geavanceerd scenario waar de gebruiker vooraf toestemming voor verschillende resources moet bieden. U kunt deze optie gebruiken als u geen incrementele toestemming wilt gebruiken, die normaal gesp roken wordt gebruikt met MSAL.NET of het micro soft Identity-platform. Zie voor meer informatie [de gebruiker toestemming geven vóór verschillende bronnen](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Hier volgt een code voorbeeld:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Andere optionele para meters

`AcquireTokenInteractive`Zie de [referentie documentatie voor AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods)voor meer informatie over de andere optionele para meters voor.

### <a name="acquire-tokens-via-the-protocol"></a>Tokens verkrijgen via het Protocol

Het is niet raadzaam om het protocol direct te gebruiken voor het ophalen van tokens. Als u dat wel doet, worden in de app geen scenario's ondersteund die betrekking hebben op eenmalige aanmelding (SSO), Apparaatbeheer en voorwaardelijke toegang.

Wanneer u het protocol gebruikt om tokens voor mobiele apps op te halen, moet u twee aanvragen doen:

* Een autorisatie code ophalen.
* De code voor een token uitwisselen.

#### <a name="get-an-authorization-code"></a>Een autorisatie code ophalen

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Toegang krijgen en het token vernieuwen

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario om [een web-API](scenario-mobile-call-api.md)aan te roepen.