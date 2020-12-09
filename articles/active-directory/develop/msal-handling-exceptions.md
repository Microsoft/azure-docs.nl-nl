---
title: Fouten en uitzonde ringen (MSAL)
titleSuffix: Microsoft identity platform
description: Meer informatie over het afhandelen van fouten en uitzonde ringen, voorwaardelijke toegang en claim uitdagingen in MSAL-toepassingen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5b4ed1e21c33a952b639009b619db4f497f2cfbf
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860062"
---
# <a name="handle-msal-exceptions-and-errors"></a>MSAL-uitzonde ringen en-fouten verwerken

Dit artikel bevat een overzicht van de verschillende soorten fouten en aanbevelingen voor het verwerken van veelvoorkomende aanmeldings fouten.

## <a name="msal-error-handling-basics"></a>Basis beginselen van MSAL-fout afhandeling

Uitzonde ringen in micro soft Authentication Library (MSAL) zijn bedoeld voor het oplossen van problemen met app-ontwikkel aars, niet voor het weer geven van eind gebruikers. Uitzonderings berichten zijn niet gelokaliseerd.

Bij het verwerken van uitzonde ringen en fouten kunt u het uitzonderings type zelf en de fout code gebruiken om onderscheid te maken tussen uitzonde ringen.  Zie [verificatie-en autorisatie fout codes](reference-aadsts-error-codes.md)voor een lijst met fout codes.

Tijdens de aanmeldings procedure kunnen er fouten optreden met betrekking tot toestemmingen, voorwaardelijke toegang (MFA, Apparaatbeheer, op locatie gebaseerde beperkingen), het uitgeven en inwisselen van tokens en gebruikers eigenschappen.

Raadpleeg de volgende sectie die overeenkomt met de taal die u gebruikt voor meer informatie over het afhandelen van fouten voor uw app.

## <a name="net"></a>[.NET](#tab/dotnet)

Bij het verwerken van .NET-uitzonde ringen kunt u het uitzonderings type zelf en het `ErrorCode` lid gebruiken om onderscheid te maken tussen uitzonde ringen. `ErrorCode` waarden zijn constanten van het type [MsalError](/dotnet/api/microsoft.identity.client.msalerror).

U kunt ook een kijkje geven in de velden [MsalClientException](/dotnet/api/microsoft.identity.client.msalexception), [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception)en [MsalUIRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception).

Als [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) wordt gegenereerd, kunt u de [fout codes voor verificatie en autorisatie](reference-aadsts-error-codes.md) proberen om te zien of de code daar wordt vermeld.

### <a name="common-net-exceptions"></a>Algemene .NET-uitzonde ringen

Hier volgen enkele veelvoorkomende uitzonde ringen die mogelijk worden gegenereerd en enkele mogelijke oplossingen:  

| Uitzondering | Foutcode | Oplossing|
| --- | --- | --- |
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS65001: de gebruiker of beheerder heeft niet ingestemd met het gebruik van de toepassing met de ID {appId} met de naam {appName}. Verzend een interactieve autorisatie aanvraag voor deze gebruiker en resource.| U moet eerst de toestemming van de gebruiker ophalen. Als u geen .NET Core gebruikt (die geen Web-UI heeft), roept u (eenmaal) aan `AcquireTokeninteractive` . Als u .NET Core gebruikt of als u geen gebruik wilt maken van een `AcquireTokenInteractive` , kan de gebruiker naar een URL navigeren om toestemming te geven: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read` . om aan te roepen `AcquireTokenInteractive` : `app.AcquireTokenInteractive(scopes).WithAccount(account).WithClaims(ex.Claims).ExecuteAsync();`|
| [MsalUiRequiredException](/dotnet/api/microsoft.identity.client.msaluirequiredexception) | AADSTS50079: de gebruiker is verplicht [multi-factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md)te gebruiken.| Er is geen beperking. Als MFA is geconfigureerd voor uw Tenant en Azure Active Directory (AAD) besluit het af te dwingen, moet u terugvallen op een interactieve stroom, zoals `AcquireTokenInteractive` of `AcquireTokenByDeviceCode` .|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) |AADSTS90010: het toekennings type wordt niet ondersteund via de */veelvoorkomende* -of */consumers* -eind punten. Gebruik het */organizations* -of Tenant-specifieke eind punt. U hebt */veelvoorkomende* gebruikt.| Zoals uitgelegd in het bericht van Azure AD, moet de instantie een Tenant of anderszins */organizations* hebben.|
| [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) | AADSTS70002: de hoofd tekst van de aanvraag moet de volgende para meter bevatten: `client_secret or client_assertion` .| Deze uitzonde ring kan worden gegenereerd als uw toepassing niet is geregistreerd als een open bare client toepassing in azure AD. Bewerk in het Azure Portal het manifest voor uw toepassing en stel in `allowPublicClient` op `true` . |
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)| `unknown_user Message`: Kan aangemelde gebruiker niet identificeren| De bibliotheek kan geen query uitvoeren op de huidige aangemelde gebruiker van Windows of deze gebruiker is niet opgenomen in AD of AAD. Beperking 1: Controleer op UWP of de toepassing de volgende mogelijkheden heeft: ondernemings verificatie, particuliere netwerken (client en server), informatie over gebruikers accounts. Risico beperking 2: Implementeer uw eigen logica voor het ophalen van de gebruikers naam (bijvoorbeeld john@contoso.com ) en gebruik het `AcquireTokenByIntegratedWindowsAuth` formulier dat de gebruikers naam gebruikt.|
| [MsalClientException](/dotnet/api/microsoft.identity.client.msalclientexception)|integrated_windows_auth_not_supported_managed_user| Deze methode is afhankelijk van een protocol dat wordt weer gegeven door Active Directory (AD). Als een gebruiker is gemaakt in Azure Active Directory zonder een AD-back-up (' beheerde ' gebruiker), mislukt deze methode. Gebruikers die zijn gemaakt in AD en die worden ondersteund door AAD (' federatieve ' gebruikers), kunnen profiteren van deze niet-interactieve verificatie methode. Risico beperking: interactieve verificatie gebruiken.|

### `MsalUiRequiredException`

Een van de algemene status codes die door MSAL.NET worden geretourneerd bij het aanroepen `AcquireTokenSilent()` is `MsalError.InvalidGrantError` . Deze status code betekent dat de toepassing de verificatie bibliotheek opnieuw moet aanroepen, maar in de interactieve modus (AcquireTokenInteractive of AcquireTokenByDeviceCodeFlow voor open bare client toepassingen en een uitdaging in web-apps). Dit komt doordat er aanvullende gebruikers interactie is vereist voordat het verificatie token kan worden uitgegeven.

De meeste tijd wanneer `AcquireTokenSilent` dit mislukt, komt doordat de token cache geen tokens bevat die overeenkomen met uw aanvraag. Toegangs tokens verlopen in één uur en `AcquireTokenSilent` Er wordt geprobeerd een nieuwe op te halen op basis van een vernieuwings token (in OAuth2-voor waarden is dit de stroom voor het vernieuwen van het token). Deze stroom kan om verschillende redenen ook mislukken, bijvoorbeeld als een Tenant beheerder strengere aanmeldings beleidsregels configureert. 

De interactie is gericht op het uitvoeren van een actie door de gebruiker. Sommige van deze voor waarden zijn gemakkelijk te oplossen (bijvoorbeeld het accepteren van gebruiks voorwaarden met één klik), en sommige kunnen niet worden omgezet met de huidige configuratie (de computer in kwestie moet bijvoorbeeld verbinding maken met een specifiek bedrijfs netwerk). Sommige gebruikers helpen bij het instellen van multi-factor Authentication of het installeren van Microsoft Authenticator op het apparaat.

### <a name="msaluirequiredexception-classification-enumeration"></a>`MsalUiRequiredException` inventarisatie van classificatie

In MSAL wordt een `Classification` veld weer gegeven, dat u kunt lezen om een betere gebruikers ervaring te bieden, bijvoorbeeld om de gebruiker te laten weten dat het wacht woord is verlopen of dat ze toestemming moeten geven om bepaalde resources te gebruiken. De ondersteunde waarden maken deel uit van de `UiRequiredExceptionClassification` Enum:

| Classificatie    | Betekenis           | Aanbevolen verwerking |
|-------------------|-------------------|----------------------|
| BasicAction | De voor waarde kan worden omgezet door de interactie van de gebruiker tijdens de interactieve verificatie stroom. | Roep AcquireTokenInteractively () aan. |
| AdditionalAction | Voor waarde kan worden opgelost door extra Remedia-interactie met het systeem, buiten de interactieve verificatie stroom. | Roep AcquireTokenInteractively () aan om een bericht weer te geven waarin de herstel actie wordt uitgelegd. De aanroepende toepassing kan ervoor kiezen om stromen te verbergen waarvoor additional_action nodig is als de gebruiker de herstel actie niet waarschijnlijk kan volt ooien. |
| MessageOnly      | De voor waarde kan op dit moment niet worden opgelost. Bij het starten van een interactieve verificatie stroom wordt een bericht weer gegeven waarin de voor waarde wordt uitgelegd. | Roep AcquireTokenInteractively () aan om een bericht weer te geven waarin de voor waarde wordt uitgelegd. AcquireTokenInteractively () retourneert een UserCanceled-fout nadat de gebruiker het bericht heeft gelezen en het venster sluit. De aanroepende toepassing kan ervoor kiezen om stromen te verbergen die leiden tot message_only als de gebruiker waarschijnlijk niet in aanmerking komt voor het bericht.|
| ConsentRequired  | Toestemming van de gebruiker ontbreekt of is ingetrokken. | Roep AcquireTokenInteractively () aan om de gebruiker toestemming te geven. |
| UserPasswordExpired | Het wacht woord van de gebruiker is verlopen. | Roep AcquireTokenInteractively () aan zodat gebruikers hun wacht woord opnieuw kunnen instellen. |
| PromptNeverFailed| Interactieve verificatie is aangeroepen met de parameter prompt = Never, het afdwingen van MSAL om te vertrouwen op browser cookies en niet om de browser weer te geven. Dit is mislukt. | Roep AcquireTokenInteractively () aan zonder prompt. none |
| AcquireTokenSilentFailed | De SDK voor MSAL heeft niet genoeg informatie om een token uit de cache op te halen. Dit kan zijn omdat er geen tokens in de cache staan of omdat er geen account is gevonden. Het fout bericht bevat meer details.  | Roep AcquireTokenInteractively () aan. |
| Geen    | Er worden geen verdere details gegeven. De voor waarde kan worden opgelost door de gebruikers interactie tijdens de interactieve verificatie stroom. | Roep AcquireTokenInteractively () aan. |

## <a name="net-code-example"></a>Voor beeld van .NET-code

```csharp
AuthenticationResult res;
try
{
 res = await application.AcquireTokenSilent(scopes, account)
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex) when (ex.ErrorCode == MsalError.InvalidGrantError)
{
 switch (ex.Classification)
 {
  case UiRequiredExceptionClassification.None:
   break;
  case UiRequiredExceptionClassification.MessageOnly:
  // You might want to call AcquireTokenInteractive(). Azure AD will show a message
  // that explains the condition. AcquireTokenInteractively() will return UserCanceled error
  // after the user reads the message and closes the window. The calling application may choose
  // to hide features or data that result in message_only if the user is unlikely to benefit 
  // from the message
  try
  {
      res = await application.AcquireTokenInteractive(scopes).ExecuteAsync();
  }
  catch (MsalClientException ex2) when (ex2.ErrorCode == MsalError.AuthenticationCanceledError)
  {
   // Do nothing. The user has seen the message
  }
  break;

  case UiRequiredExceptionClassification.BasicAction:
  // Call AcquireTokenInteractive() so that the user can, for instance accept terms
  // and conditions

  case UiRequiredExceptionClassification.AdditionalAction:
  // You might want to call AcquireTokenInteractive() to show a message that explains the remedial action. 
  // The calling application may choose to hide flows that require additional_action if the user 
  // is unlikely to complete the remedial action (even if this means a degraded experience)

  case UiRequiredExceptionClassification.ConsentRequired:
  // Call AcquireTokenInteractive() for user to give consent.
  
  case UiRequiredExceptionClassification.UserPasswordExpired:
  // Call AcquireTokenInteractive() so that user can reset their password
  
  case UiRequiredExceptionClassification.PromptNeverFailed:
  // You used WithPrompt(Prompt.Never) and this failed
  
  case UiRequiredExceptionClassification.AcquireTokenSilentFailed:
  default:
  // May be resolved by user interaction during the interactive authentication flow.
  res = await application.AcquireTokenInteractive(scopes)
                         .ExecuteAsync(); break;
 }
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

MSAL.js biedt fout objecten die de verschillende soorten veelvoorkomende fouten abstract en geclassificeerd. Het biedt ook een interface voor toegang tot specifieke details van de fouten, zoals fout berichten om deze op de juiste wijze af te handelen.

### <a name="error-object"></a>Fout object

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

Door de fout klasse uit te breiden, hebt u toegang tot de volgende eigenschappen:
- `AuthError.message`: Hetzelfde als de `errorMessage` .
- `AuthError.stack`: Stack tracering voor gegenereerde fouten.

### <a name="error-types"></a>Fouttypen

De volgende fout typen zijn beschikbaar:

- `AuthError`: Basis fout klasse voor de MSAL.js-bibliotheek, die ook wordt gebruikt voor onverwachte fouten.

- `ClientAuthError`: Fout klasse, die een probleem met client verificatie aanduidt. De meeste fouten die afkomstig zijn uit de-bibliotheek, zijn ClientAuthErrors. Deze fouten zijn het gevolg van dingen zoals het aanroepen van een aanmeldings methode wanneer de aanmelding al wordt uitgevoerd, de gebruiker annuleert de aanmelding, enzovoort.

- `ClientConfigurationError`: Fout klasse, wordt `ClientAuthError` gegenereerd voordat aanvragen worden gedaan wanneer de opgegeven gebruikers configuratie parameters ongeldig of ontbrekend zijn.

- `ServerError`: Fout klasse, vertegenwoordigt de fout teken reeksen die door de verificatie server zijn verzonden. Dit kunnen fouten zijn, zoals ongeldige aanvraag indelingen of para meters, of andere fouten die voor komen dat de server de gebruiker verifieert of autoriseert.

- `InteractionRequiredAuthError`: Fout klasse, wordt uitgebreid `ServerError` om Server fouten voor te stellen, waarvoor een interactieve aanroep is vereist. Deze fout treedt op `acquireTokenSilent` als de gebruiker moet communiceren met de server om referenties of toestemming te geven voor verificatie/autorisatie. Fout codes zijn onder andere `"interaction_required"` , `"login_required"` , en `"consent_required"` .

Voor fout afhandeling in verificatie stromen met omleidings methoden ( `loginRedirect` , `acquireTokenRedirect` ) moet u de retour aanroep registreren, die wordt aangeroepen met geslaagd of mislukt na de omleiding met de `handleRedirectCallback()` methode:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

De methoden voor de pop-upervaring ( `loginPopup` , `acquireTokenPopup` ) retour nering, zodat u het Promise-patroon (. then en. Catch) kunt gebruiken om ze te verwerken zoals wordt weer gegeven:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Fouten waarvoor interactie nodig is

Er wordt een fout bericht weer gegeven wanneer u probeert een niet-interactieve methode te gebruiken voor het verkrijgen van een token `acquireTokenSilent` , zoals, maar MSAL kan niet op de achtergrond worden uitgevoerd.

Mogelijke oorzaken zijn:

- u moet zich aanmelden
- u moet toestemming geven
- u moet een multi-factor Authentication-ervaring door lopen.

Het herstel is om een interactieve methode aan te roepen, zoals `acquireTokenPopup` of `acquireTokenRedirect` :

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

## <a name="python"></a>[Python](#tab/python)

In MSAL voor python worden de meeste fouten overgebracht als een retour waarde van de API-aanroep. De fout wordt weer gegeven als een woorden lijst met de JSON-reactie van het micro soft Identity-platform.

* Een geslaagd antwoord bevat de `"access_token"` sleutel. De indeling van het antwoord wordt gedefinieerd door het OAuth2-protocol. Zie [5,1 geslaagde reactie](https://tools.ietf.org/html/rfc6749#section-5.1) voor meer informatie
* Een fout bericht bevat `"error"` en is doorgaans `"error_description"` . De indeling van het antwoord wordt gedefinieerd door het OAuth2-protocol. Zie [5,2-fout](https://tools.ietf.org/html/rfc6749#section-5.2) melding voor meer informatie

Als er een fout wordt geretourneerd, `"error_description"` bevat de sleutel een bericht met een lees bare tekst. Deze bevat meestal een fout code voor micro soft Identity platform. Zie [fout codes voor verificatie en autorisatie](./reference-aadsts-error-codes.md)voor meer informatie over de verschillende fout codes.

In MSAL voor python zijn uitzonde ringen zeldzame, omdat de meeste fouten worden verwerkt door een fout waarde te retour neren. De `ValueError` uitzonde ring wordt alleen gegenereerd als er een probleem is met de manier waarop u de bibliotheek probeert te gebruiken, bijvoorbeeld wanneer de API-para meter (s) ongeldig zijn.

## <a name="java"></a>[Java](#tab/java)

In MSAL voor Java zijn er drie soorten uitzonde ringen: `MsalClientException` , `MsalServiceException` , en `MsalInteractionRequiredException` ; die overnemen van `MsalException` .

- `MsalClientException` wordt gegenereerd wanneer er een fout optreedt die lokaal is voor de tape wisselaar of het apparaat.
- `MsalServiceException` wordt gegenereerd wanneer de Secure token service (STS) een fout bericht retourneert of een andere netwerk fout optreedt.
- `MsalInteractionRequiredException` wordt gegenereerd wanneer de gebruikers interface-interactie vereist is om de verificatie te volt ooien.

### <a name="msalserviceexception"></a>MsalServiceException

`MsalServiceException` beschrijft HTTP-headers die in de aanvragen naar de STS worden geretourneerd. Toegang tot deze via `MsalServiceException.headers()`

### <a name="msalinteractionrequiredexception"></a>MsalInteractionRequiredException

Een van de algemene status codes die worden geretourneerd door MSAL voor Java tijdens het aanroepen `AcquireTokenSilently()` is `InvalidGrantError` . Dit betekent dat aanvullende gebruikers interactie vereist is voordat een verificatie token kan worden uitgegeven. Uw toepassing moet de verificatie bibliotheek opnieuw aanroepen, maar in de interactieve modus door verzen ding `AuthorizationCodeParameters` of `DeviceCodeParameters` voor open bare client toepassingen.

De meeste tijd is `AcquireTokenSilently` mislukt, omdat de token cache geen token heeft die overeenkomt met uw aanvraag. Toegangs tokens verlopen in één uur en `AcquireTokenSilently` Er wordt geprobeerd een nieuwe te verkrijgen op basis van een vernieuwings token. In OAuth2-voor waarden is dit de vernieuwings token stroom. Deze stroom kan ook om verschillende redenen mislukken, bijvoorbeeld wanneer een Tenant beheerder strengere aanmeldings beleid configureert.

Sommige voor waarden die deze fout veroorzaken, kunnen gemakkelijk worden opgelost. Het is bijvoorbeeld mogelijk dat de gebruiksrecht overeenkomst moet worden geaccepteerd. Het kan ook zijn dat de aanvraag niet kan worden afgehandeld met de huidige configuratie, omdat de machine verbinding moet maken met een specifiek bedrijfs netwerk.

In MSAL wordt een `reason` veld weer gegeven, dat u kunt gebruiken om een betere gebruikers ervaring te bieden. Zo `reason` kan het veld ertoe leiden dat u de gebruiker kunt vertellen dat het wacht woord is verlopen of dat ze toestemming nodig hebben om een aantal resources te gebruiken. De ondersteunde waarden maken deel uit van de  `InteractionRequiredExceptionReason` Enum:

| Reden | Betekenis | Aanbevolen verwerking |
|---------|-----------|-----------------------------|
| `BasicAction` | De voor waarde kan worden opgelost door de gebruikers interactie tijdens de interactieve verificatie stroom | Aanroep `acquireToken` met interactieve para meters |
| `AdditionalAction` | De voor waarde kan worden opgelost door extra Remedia-interactie met het systeem buiten de interactieve verificatie stroom. | Bel `acquireToken` met interactieve para meters om een bericht weer te geven waarin wordt uitgelegd hoe de herstel actie moet worden uitgevoerd. De aanroep-app kan ervoor kiezen om stromen te verbergen waarvoor extra actie moet worden ondernomen als de gebruiker de herstel actie niet waarschijnlijk kan volt ooien. |
| `MessageOnly` | De voor waarde kan op dit moment niet worden opgelost. Start interactieve verificatie stroom om een bericht weer te geven waarin de voor waarde wordt uitgelegd. | Aanroep `acquireToken` met interactieve para meters om een bericht weer te geven waarin de voor waarde wordt uitgelegd. `acquireToken` retourneert de `UserCanceled` fout nadat de gebruiker het bericht heeft gelezen en het venster sluit. De app kan ervoor kiezen om stromen te verbergen die resulteren in een bericht als de gebruiker waarschijnlijk niet in aanmerking komt voor het bericht. |
| `ConsentRequired`| Toestemming van de gebruiker ontbreekt of is ingetrokken. |Roep `acquireToken` met interactieve para meters, zodat de gebruiker toestemming kan geven. |
| `UserPasswordExpired` | Het wacht woord van de gebruiker is verlopen. | Aanroep `acquireToken` met interactieve para meter zodat de gebruiker het wacht woord opnieuw kan instellen |
| `None` |  Meer informatie vindt u hier. De voor waarde kan door de gebruikers interactie worden omgezet tijdens de interactieve verificatie stroom. | Aanroep `acquireToken` met interactieve para meters |

### <a name="code-example"></a>Code voorbeeld

```java
        IAuthenticationResult result;
        try {
            PublicClientApplication application = PublicClientApplication
                    .builder("clientId")
                    .b2cAuthority("authority")
                    .build();

            SilentParameters parameters = SilentParameters
                    .builder(Collections.singleton("scope"))
                    .build();

            result = application.acquireTokenSilently(parameters).join();
        }
        catch (Exception ex){
            if(ex instanceof MsalInteractionRequiredException){
                // AcquireToken by either AuthorizationCodeParameters or DeviceCodeParameters
            } else{
                // Log and handle exception accordingly
            }
        }
```

## <a name="iosmacos"></a>[iOS/macOS](#tab/iosmacos)

De volledige lijst met MSAL voor iOS-en macOS-fouten wordt vermeld in [MSALError Enum](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Alle MSAL-gegenereerde fouten worden geretourneerd met het `MSALErrorDomain` domein.

Voor systeem fouten retourneert MSAL het origineel `NSError` van de systeem-API. Als bijvoorbeeld het verkrijgen van tokens mislukt vanwege een gebrek aan netwerk connectiviteit, retourneert MSAL een fout met het `NSURLErrorDomain` domein en de `NSURLErrorNotConnectedToInternet` code.

U wordt aangeraden ten minste de volgende twee MSAL-fouten aan de client zijde te verwerken:

- `MSALErrorInteractionRequired`: De gebruiker moet een interactieve aanvraag uitvoeren. Er zijn veel voor waarden die kunnen leiden tot deze fout, zoals een verlopen verificatie sessie of de nood zaak van aanvullende authenticatie vereisten. Roep de MSAL Interactive token Acquisition-API aan om te herstellen. 

- `MSALErrorServerDeclinedScopes`: Sommige of alle bereiken zijn afgewezen. Bepaal of u wilt door gaan met alleen de toegekende bereiken of stop het aanmeldings proces.

> [!NOTE]
> De `MSALInternalError` Enum mag alleen worden gebruikt voor naslag informatie en fout opsporing. Probeer deze fouten niet automatisch af te handelen tijdens runtime. Als uw app een van de fouten tegen komt die onder vallen `MSALInternalError` , wilt u mogelijk een algemeen bericht van de gebruiker weer geven waarin wordt uitgelegd wat er is gebeurd.

`MSALInternalErrorBrokerResponseNotReceived`Betekent bijvoorbeeld dat de gebruiker de verificatie niet heeft voltooid en hand matig is teruggekeerd naar de app. In dit geval moet uw app een algemeen fout bericht weer geven waarin wordt uitgelegd dat de verificatie is voltooid en er wordt voorgesteld dat ze opnieuw proberen te verifiëren.

De volgende voorbeeld code van doel-C demonstreert de aanbevolen procedures voor het verwerken van enkele veelvoorkomende fout situaties:

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

## <a name="conditional-access-and-claims-challenges"></a>Voorwaardelijke toegang en claim uitdagingen

Wanneer tokens op de achtergrond worden opgehaald, kan uw toepassing fouten ontvangen wanneer een [Challenge voor voorwaardelijke toegang](../azuread-dev/conditional-access-dev-guide.md) , zoals MFA-beleid, is vereist voor een API die u probeert te openen.

Het patroon voor het afhandelen van deze fout is het interactief verkrijgen van een token met behulp van MSAL. Bij het interactief ophalen van een token wordt de gebruiker gevraagd om te voldoen aan het vereiste beleid voor voorwaardelijke toegang.

In bepaalde gevallen bij het aanroepen van een API waarvoor voorwaardelijke toegang is vereist, kunt u een claim Challenge ontvangen in de fout van de API. Als het beleid voor voorwaardelijke toegang een beheerd apparaat (intune) heeft, is de fout bijvoorbeeld [AADSTS53000: uw apparaat moet worden beheerd om toegang te krijgen tot deze bron](reference-aadsts-error-codes.md) of iets dergelijks. In dit geval kunt u de claims door geven in de aanroep van het Acquire-token, zodat de gebruiker wordt gevraagd om te voldoen aan het juiste beleid.

### <a name="net"></a>.NET

Bij het aanroepen van een API waarvoor voorwaardelijke toegang is vereist vanuit MSAL.NET, moet uw toepassing de uitzonde ringen voor claim controle afhandelen. Dit wordt weer gegeven als een [MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) waarbij de eigenschap [claims](/dotnet/api/microsoft.identity.client.msalserviceexception.claims) niet leeg is.

Als u de claim Challenge wilt afhandelen, moet u de `.WithClaim()` methode van de- `PublicClientApplicationBuilder` klasse gebruiken.

### <a name="javascript"></a>Javascript

Bij het op de achtergrond ophalen van tokens (met `acquireTokenSilent` ) met behulp van MSAL.js, kan uw toepassing fouten ontvangen wanneer een [Challenge voor voorwaardelijke toegang](../azuread-dev/conditional-access-dev-guide.md) , zoals MFA-beleid, is vereist voor een API die u probeert te openen.

Het patroon voor het afhandelen van deze fout is het maken van een interactieve aanroep voor het verkrijgen van tokens in MSAL.js zoals `acquireTokenPopup` of `acquireTokenRedirect` zoals in het volgende voor beeld:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

Als u het token interactief aanschaft, wordt de gebruiker gevraagd om te voldoen aan het vereiste beleid voor voorwaardelijke toegang.

Wanneer u een API aanroept waarvoor voorwaardelijke toegang is vereist, kunt u een claim Challenge ontvangen in de fout van de API. In dit geval kunt u de geretourneerde claims in de fout door geven aan het `claimsRequest` veld van de `AuthenticationParameters.ts` klasse om aan het juiste beleid te voldoen. 

Zie [aanvullende claims aanvragen](active-directory-optional-claims.md) voor meer informatie.

### <a name="msal-for-ios-and-macos"></a>MSAL voor iOS en macOS

Met MSAL voor iOS en macOS kunt u specifieke claims aanvragen in zowel interactieve als Silent-verwervings scenario's.

Als u aangepaste claims wilt aanvragen, geeft u `claimsRequest` in `MSALSilentTokenParameters` of `MSALInteractiveTokenParameters` .

Zie [aangepaste claims aanvragen met MSAL voor IOS en macOS](request-custom-claims.md) voor meer informatie.

## <a name="retrying-after-errors-and-exceptions"></a>Opnieuw proberen na fouten en uitzonde ringen

U wordt verwacht dat u het beleid voor opnieuw proberen implementeert bij het aanroepen van MSAL. MSAL maakt HTTP-aanroepen van de AAD-service mogelijk en er kunnen incidentele fouten optreden, bijvoorbeeld het netwerk kan uitvallen of de server overbelast is.  

### <a name="http-error-codes-500-600"></a>HTTP-fout codes 500-600

MSAL.NET implementeert een eenvoudig mechanisme voor opnieuw proberen voor fouten met HTTP-fout codes 500-600.

### <a name="http-429"></a>HTTP 429

Wanneer de service token server (STS) is overbelast met te veel aanvragen, wordt HTTP-fout 429 geretourneerd met een hint over hoe lang totdat u het antwoord veld opnieuw kunt proberen `Retry-After` .

### <a name="net"></a>.NET

[MsalServiceException](/dotnet/api/microsoft.identity.client.msalserviceexception) -Opper vlakken `System.Net.Http.Headers.HttpResponseHeaders` als een-eigenschap `namedHeaders` . U kunt aanvullende informatie uit de fout code gebruiken om de betrouw baarheid van uw toepassingen te verbeteren. In het geval dat wordt beschreven, kunt u het `RetryAfterproperty` (type `RetryConditionHeaderValue` ) gebruiken en berekenen wanneer u het opnieuw wilt proberen.

Hier volgt een voor beeld van een daemon-toepassing met behulp van de client referenties flow. U kunt dit aanpassen aan een van de methoden voor het verkrijgen van een token.

```csharp

bool retry = false;
do
{
    TimeSpan? delay;
    try
    {
         result = await publicClientApplication.AcquireTokenForClient(scopes, account).ExecuteAsync();
    }
    catch (MsalServiceException serviceException)
    {
         if (serviceException.ErrorCode == "temporarily_unavailable")
         {
             RetryConditionHeaderValue retryAfter = serviceException.Headers.RetryAfter;
             if (retryAfter.Delta.HasValue)
             {
                 delay = retryAfter.Delta;
             }
             else if (retryAfter.Date.HasValue)
             {
                 delay = retryAfter.Date.Value.Offset;
             }
         }
    }
    …
    if (delay.HasValue)
    {
        Thread.Sleep((int)delay.Value.TotalMilliseconds); // sleep or other
        retry = true;
    }
} while (retry);
```
