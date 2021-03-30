---
title: Best practices voor het afhandelen van ADAL-client toepassingen | Azure
description: Biedt richt lijnen voor het afhandelen van fouten en aanbevolen procedures voor ADAL-client toepassingen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 02/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: ad5595f7eebc8feca2f00a6f95e10c547ded9529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383731"
---
# <a name="error-handling-best-practices-for-azure-active-directory-authentication-library-adal-clients"></a>Aanbevolen procedures voor het afhandelen van de Azure Active Directory Authentication Library (ADAL)-clients

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Dit artikel bevat richt lijnen voor het type fouten dat ontwikkel aars kunnen tegen komen, wanneer u ADAL gebruikt om gebruikers te verifiëren. Wanneer u ADAL gebruikt, zijn er verschillende gevallen waarin een ontwikkelaar mogelijk fouten moet oplossen en afhandelen. De juiste fout afhandeling zorgt voor een goede ervaring van de eind gebruiker en beperkt het aantal keren dat de eind gebruiker zich moet aanmelden.

In dit artikel verkennen we de specifieke gevallen voor elk platform dat wordt ondersteund door ADAL en hoe uw toepassing elke case op de juiste manier kan afhandelen. De richt lijnen voor fouten zijn onderverdeeld in twee bredere categorieën, op basis van de token verwervings patronen van ADAL-Api's:

- **AcquireTokenSilent**: client probeert een token op de achtergrond te verkrijgen (geen gebruikers interface) en kan MISLUKKEN als ADAL niet is gelukt. 
- **AcquireToken**: de client kan proberen op de achtergrond te worden gekocht, maar kan ook interactieve aanvragen uitvoeren waarvoor aanmelding is vereist.

> [!TIP]
> Het is een goed idee om alle fouten en uitzonde ringen te registreren bij gebruik van ADAL en Azure AD. Logboeken zijn niet alleen nuttig voor het leren van de algehele status van uw toepassing, maar zijn ook belang rijk bij het opsporen van fouten in bredere problemen. Hoewel uw toepassing van bepaalde fouten kan worden hersteld, is het mogelijk dat er meer ontwerp problemen optreden waarvoor code wijzigingen nodig zijn om het probleem op te lossen. 
> 
> Wanneer u de fout voorwaarden implementeert die in dit document worden behandeld, moet u de fout code en beschrijving registreren om de redenen die eerder zijn besproken. Zie de [Naslag informatie voor fouten en logboek registratie](#error-and-logging-reference) voor voor beelden van logboek registratie code. 
>

## <a name="acquiretokensilent"></a>AcquireTokenSilent

AcquireTokenSilent probeert een token op te halen met de garantie dat de eind gebruiker geen gebruikers interface (UI) ziet. Er zijn verschillende gevallen waarin de Silent-aanschaf kan mislukken en moet worden afgehandeld via interactieve aanvragen of door een standaard-handler. We vinden het de specifieke voor delen van wanneer en hoe ze elk geval in de volgende secties gebruiken.

Er is een set fouten die door het besturings systeem wordt gegenereerd. hiervoor is mogelijk specifieke fout afhandeling vereist voor de toepassing. Zie ' Operating System ' Errors (Engelstalig) in Naslag informatie voor fouten [en logboek registratie](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Toepassingsscenario's

- [Systeem eigen client](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#native-client) toepassingen (Ios, Android, .net desktop of Xamarin)
- [Webclient](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#web-client) -toepassingen die een [bron](../develop/developer-glossary.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json#resource-server) aanroepen (.net)

### <a name="error-cases-and-actionable-steps"></a>Fout-en actie bare stappen

Er zijn in het algemeen twee situaties met AcquireTokenSilent-fouten:

| Case | Beschrijving |
|------|-------------|
| Voor **Beeld 1**: fout kan worden omgezet met een interactieve aanmelding | Voor fouten die zijn veroorzaakt door een gebrek aan geldige tokens is een interactieve aanvraag nood zakelijk. Voor het opzoeken van de cache en een ongeldig/verlopen vernieuwings token is een AcquireToken-aanroep vereist om het probleem op te lossen.<br><br>In deze gevallen moet de eind gebruiker worden gevraagd zich aan te melden. De toepassing kan ervoor kiezen om direct een interactieve aanvraag te doen, nadat de interactie tussen de eind gebruiker (bijvoorbeeld een aanmeldings knop) of hoger is. De keuze is afhankelijk van het gewenste gedrag van de toepassing.<br><br>Zie de code in de volgende sectie voor dit specifieke geval en de fouten die het probleem vaststellen.|
| **Case 2**: fout kan niet worden omgezet met een interactieve aanmelding | Het probleem wordt niet opgelost met het uitvoeren van een interactieve AcquireToken-aanvraag voor netwerk-en tijdelijke/tijdelijke fouten of andere storingen. Onnodige prompts voor interactieve aanmelding kunnen ook de voor uitzichten van de eind gebruikers. ADAL probeert automatisch één nieuwe poging uit te voeren voor de meeste fouten in AcquireTokenSilent-fouten.<br><br>De client toepassing kan ook op een later tijdstip proberen een nieuwe poging uit te voeren, maar wanneer en hoe afhankelijk is van het gedrag van de toepassing en de gewenste ervaring van de eind gebruiker. De toepassing kan bijvoorbeeld een AcquireTokenSilent opnieuw proberen na een paar minuten of als reactie op de actie van de eind gebruiker. Een onmiddellijke nieuwe poging leidt ertoe dat de toepassing wordt beperkt en niet wordt geprobeerd.<br><br>Een volgende poging mislukt met dezelfde fout betekent niet dat de client een interactieve aanvraag moet doen met behulp van AcquireToken, omdat de fout niet wordt opgelost.<br><br>Zie de code in de volgende sectie voor dit specifieke geval en de fouten die het probleem vaststellen. |

### <a name="net"></a>.NET

De volgende richt lijnen bevatten voor beelden voor het afhandelen van fouten in combi natie met ADAL-methoden: 

- acquireTokenSilentAsync(...)
- acquireTokenSilentSync(...) 
- [afgeschaft] acquireTokenSilent (...)
- [afgeschaft] acquireTokenByRefreshToken (...) 

De code wordt als volgt geïmplementeerd:

```csharp
try{
    AcquireTokenSilentAsync(…);
} 

catch (AdalSilentTokenAcquisitionException e) {
    // Exception: AdalSilentTokenAcquisitionException
    // Caused when there are no tokens in the cache or a required refresh failed. 

    // Action: Case 1, resolvable with an interactive request. 
} 

catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
} 
    
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET. 
    // e.ErrorCode contains the error code. 

    // Action: Case 2, not resolvable with an interactive request.
    // Attempt retry after a timed interval or user action.
    // Example Error: network_not_available, default case.
}
```

### <a name="android"></a>Android

De volgende richt lijnen bevatten voor beelden voor het afhandelen van fouten in combi natie met ADAL-methoden: 

- acquireTokenSilentSync(...)
- acquireTokenSilentAsync(...)
- [afgeschaft] acquireTokenSilent (...)

De code wordt als volgt geïmplementeerd:

```java
// *Inside callback*
public void onError(Exception e) {

    if (e instanceof AuthenticationException) {
        // Exception: AdalException
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode().

        // Errors: ADALError.ERROR_SILENT_REQUEST,
        // ADALError.AUTH_REFRESH_FAILED_PROMPT_NOT_ALLOWED,
        // ADALError.INVALID_TOKEN_CACHE_ITEM
        // Description: Request failed due to no tokens in
        // cache or failed a required refresh. 

        // Action: Case 1, resolvable with an interactive request. 

        // Action: Case 2, not resolvable with an interactive request.
        // Attempt retry after a timed interval or user action.
        // Example Errors: default case,
        // DEVICE_CONNECTION_IS_NOT_AVAILABLE, 
        // BROKER_AUTHENTICATOR_ERROR_GETAUTHTOKEN,
    }
}
```

### <a name="ios"></a>iOS

De volgende richt lijnen bevatten voor beelden voor het afhandelen van fouten in combi natie met ADAL-methoden: 

- acquireTokenSilentWithResource(...)

De code wordt als volgt geïmplementeerd:

```objc
[context acquireTokenSilentWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL Objective-C.
            // Error Code: result.error.code

            // Errors: AD_ERROR_SERVER_REFRESH_TOKEN_REJECTED, AD_ERROR_CACHE_NO_REFRESH_TOKEN
            // Description: No tokens in cache or failed a required token refresh failed. 
            // Action: Case 1, resolvable with an interactive request. 

            // Error: AD_ERROR_CACHE_MULTIPLE_USERS
            // Description: There was ambiguity in the silent request resulting in multiple cache items.
            // Action: Special Case, application should perform another silent request and specify the user using ADUserIdentifier. 
            // Can be caused in cases of a multi-user application. 

            // Action: Case 2, not resolvable with an interactive request.
            // Attempt retry after some time or user action.
            // Example Errors: default case,
            // AD_ERROR_CACHE_BAD_FORMAT
        }
    }
}]
```

## <a name="acquiretoken"></a>AcquireToken

AcquireToken is de standaard methode ADAL die wordt gebruikt om tokens op te halen. In gevallen waarin de gebruikers identiteit is vereist, probeert AcquireToken eerst een token op te halen. vervolgens wordt de gebruikers interface, indien nodig, weer gegeven (tenzij PromptBehavior. Never is door gegeven). In gevallen waarin de toepassings-id is vereist, probeert AcquireToken een token op te halen, maar wordt de gebruikers interface niet weer gegeven als er geen eind gebruiker is. 

Bij het afhandelen van AcquireToken-fouten is de fout afhandeling afhankelijk van het platform en het scenario dat de toepassing probeert te verkrijgen. 

Het besturings systeem kan ook een set fouten genereren, waarvoor fout afhandeling is vereist die afhankelijk is van de specifieke toepassing. Zie ' besturingssysteem fouten ' in Naslag informatie voor fouten [en logboek registratie](#error-and-logging-reference). 

### <a name="application-scenarios"></a>Toepassingsscenario's

- Systeem eigen client toepassingen (iOS, Android, .NET desktop of Xamarin)
- Webtoepassingen die een resource-API aanroepen (.NET)
- Toepassingen met één pagina (Java script)
- Service-naar-service-toepassingen (.NET, Java)
  - Alle scenario's, waaronder namens-van
  - Namens specifieke scenario's

### <a name="error-cases-and-actionable-steps-native-client-applications"></a>Fout gevallen en stappen die u actie kan ondernemen: systeem eigen client toepassingen

Als u een systeem eigen client toepassing bouwt, zijn er enkele cases voor het afhandelen van fouten waarmee u rekening moet houden met betrekking tot netwerk problemen, tijdelijke storingen en andere platformspecifieke fouten. In de meeste gevallen mag een toepassing niet onmiddellijk nieuwe pogingen uitvoeren, maar moet u in plaats daarvan wachten op interactie van de eind gebruiker die een aanmelding aanvraagt. 

Er zijn enkele speciale gevallen waarin één nieuwe poging het probleem kan oplossen. Bijvoorbeeld wanneer een gebruiker gegevens moet inschakelen op een apparaat of als de Azure AD Broker-down load is voltooid nadat de eerste fout is opgetreden. 

Als er een fout optreedt, kan de gebruikers interface door een toepassing worden weer gegeven, zodat de eind gebruiker enige interactie kan uitvoeren waarbij een nieuwe poging wordt gevraagd. Als er bijvoorbeeld een offline fout is opgetreden in het apparaat, moet u zich opnieuw aanmelden om een AcquireToken opnieuw te proberen, in plaats van de fout onmiddellijk opnieuw te proberen. 

Fout afhandeling in systeem eigen toepassingen kan in twee gevallen worden gedefinieerd:

|  |  |
|------|-------------|
| Voor **Beeld 1**:<br>Niet-herstel bare fout (de meeste gevallen) | 1. Probeer niet direct opnieuw te proberen. Presenteer de gebruikers interface van de eind gebruiker op basis van de specifieke fout die een nieuwe poging aanroept (bijvoorbeeld ' Probeer u opnieuw aan te melden ' of ' Azure AD Broker-toepassing downloaden '). |
| Voor **Beeld 2**:<br>Herstel bare fout | 1. Voer een enkele nieuwe poging uit als de eind gebruiker een status heeft ingevoerd die het resultaat van een geslaagde bewerking is.<br><br>2. als de nieuwe poging mislukt, presenteert u de gebruikers interface van de eind gebruiker op basis van de specifieke fout die een nieuwe poging aanroept (' Probeer u opnieuw aan te melden ', ' Azure AD Broker-app downloaden ', etc.). |

> [!IMPORTANT]
> Als een gebruikers account wordt door gegeven aan ADAL in een stille aanroep en mislukt, kan de eind gebruiker zich met de volgende interactieve aanvraag aanmelden met een ander account. Na een geslaagde AcquireToken met een gebruikers account moet de toepassing controleren of de aangemelde gebruiker overeenkomt met het lokale gebruikers object van de toepassing. Een niet-overeenkomend rapport genereert geen uitzonde ring (behalve bij doel C), maar moet worden overwogen in gevallen waarin een gebruiker lokaal bekend is vóór de verificatie aanvragen (zoals een mislukte Silent-aanroep).
>

#### <a name="net"></a>.NET

De volgende richt lijnen bevatten voor beelden van fout afhandeling in combi natie met alle niet-Silent AcquireToken (...) ADAL-methoden, *met uitzonde ring* van: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(..., ClientCredential, ...)
- AcquireTokenAsync(..., ClientAssertion, ...)
- AcquireTokenAsync (..., UserAssertion,...)   

De code wordt als volgt geïmplementeerd:

```csharp
try {
    AcquireTokenAsync(…);
} 
    
catch(AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.
    
    // Design time consideration: Certain errors may be caused at development and exposed through this exception. 
    // Looking inside the description will give more guidance on resolving the specific issue. 

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

    } 

catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // e.ErrorCode contains the error code

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

> [!NOTE]
> ADAL .NET heeft een extra overweging omdat het PromptBehavior. Never ondersteunt. Dit heeft gedrag als AcquireTokenSilent.
>

De volgende richt lijnen bevatten voor beelden voor het afhandelen van fouten in combi natie met ADAL-methoden: 

- acquireToken (..., PromptBehavior. Never)

De code wordt als volgt geïmplementeerd:

```csharp
    try {acquireToken(…, PromptBehavior.Never);
    } 

catch(AdalServiceException e) {
    // Exception: AdalServiceException represents 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Action: Case 1: Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: default case

} catch (AdalException e) {
    // Error Code: e.ErrorCode == "user_interaction_required"
    // Description: user_interaction_required indicates the silent request failed 
    // in a way that's resolvable with an interactive request.
    // Action: Resolvable with an interactive request. 

    // Action: Case 1, Non-Retryable 
    // Do not perform an immediate retry. Only retry after user action. 
    // Example Errors: network_not_available, default case
}
```

#### <a name="android"></a>Android

De volgende richt lijnen bevatten voor beelden van fout afhandeling in combi natie met alle niet-Silent AcquireToken (...) ADAL-methoden. 

De code wordt als volgt geïmplementeerd:

```java
AcquireTokenAsync(…);

// *Inside callback*
public void onError(Exception e) {
    if (e instanceof AuthenticationException) {
        // Exception: AdalException 
        // Represents a library exception generated by ADAL Android.
        // Error Code: e.getCode();

        // Error: ADALError.BROKER_APP_INSTALLATION_STARTED
        // Description: Broker app not installed, user will be prompted to download the app. 

        // Action: Case 2, Retriable Error 
        // Perform a single retry. If that fails, only try again after user action. 

        // Action: Case 1, Non-Retriable 
        // Do not perform an immediate retry. Only retry after user action. 
        // Example Errors: default case, DEVICE_CONNECTION_IS_NOT_AVAILABLE
    }
}
```

#### <a name="ios"></a>iOS

De volgende richt lijnen bevatten voor beelden van fout afhandeling in combi natie met alle niet-Silent AcquireToken (...) ADAL-methoden. 

De code wordt als volgt geïmplementeerd:

```objc
[context acquireTokenWithResource:[ARGS], completionBlock:^(ADAuthenticationResult *result) {
    if (result.status == AD_FAILED) {
        if ([error.domain isEqualToString:ADAuthenticationErrorDomain]){
            // Exception: AD_FAILED 
            // Represents a library error generated by ADAL ObjC.
            // Error Code: result.error.code 

            // Error: AD_ERROR_SERVER_WRONG_USER
            // Description: App passed a user into ADAL and the end user signed in with a different account. 
            // Action: Case 1, Non-retriable (as is) and up to the application on how to handle this case. 
            // It can attempt a new request without specifying the user, or use UI to clarify the user account to sign in. 

            // Action: Case 1, Non-Retriable 
            // Do not perform an immediate retry. Only retry after user action. 
            // Example Errors: default case
        }
    }
}]
```

### <a name="error-cases-and-actionable-steps-web-applications-that-call-a-resource-api-net"></a>Fout cases en handelingen die u actie kan ondernemen: webtoepassingen die een resource-API aanroepen (.NET)

Als u een .NET-Web-app bouwt die aanroepen een token met een autorisatie code voor een resource krijgt, is de enige vereiste code een standaard-handler voor de algemene case. 

De volgende richt lijnen bevatten voor beelden voor het afhandelen van fouten in combi natie met ADAL-methoden: 

- AcquireTokenByAuthorizationCodeAsync(...)

De code wordt als volgt geïmplementeerd:

```csharp
try {
    AcquireTokenByAuthorizationCodeAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action or wait until much later. 
    // Example Errors: default case
}
```

### <a name="error-cases-and-actionable-steps-single-page-applications-adaljs"></a>Fout-en actie bare stappen: toepassingen met één pagina (adal.js)

Als u een toepassing met één pagina bouwt met behulp van adal.js met AcquireToken, is de code voor fout afhandeling vergelijkbaar met die van een typische Silent-oproep. Met name in adal.js, AcquireToken geen gebruikers interface weer gegeven. 

Een mislukte AcquireToken heeft de volgende gevallen:

|  |  |
|------|-------------|
| Voor **Beeld 1**:<br>Kan worden omgezet met een interactieve aanvraag | 1. als het aanmelden () mislukt, moet u niet onmiddellijk een nieuwe poging uitvoeren. Alleen opnieuw proberen na een gebruikers actie vraagt een nieuwe poging.|
| Voor **Beeld 2**:<br>Kan niet worden omgezet met een interactieve aanvraag. Fout is een nieuwe poging. | 1. Voer een enkele nieuwe poging uit als de primaire gebruiker een status heeft die een geslaagd resultaat heeft.<br><br>2. als het opnieuw proberen mislukt, presenteert u de eind gebruiker een actie op basis van de specifieke fout die een nieuwe poging kan doen ("Meld u opnieuw aan"). |
| Voor **Beeld 3**:<br>Kan niet worden omgezet met een interactieve aanvraag. Fout kan niet opnieuw worden uitgevoerd. | 1. Probeer niet direct opnieuw te proberen. Presenteer de eind gebruiker met een actie op basis van de specifieke fout waarmee een nieuwe poging wordt gedaan ("Probeer zich opnieuw aan te melden"). |

De code wordt als volgt geïmplementeerd:

```javascript
AuthContext.acquireToken(…, function(error, errorDesc, token) {
    if (error || errorDesc) {
        // Represents any token acquisition failure that occurred. 
        // Error Code: error.indexOf("<ERROR_STRING>")

        // Errors: if (error.indexOf("interaction_required"))
        //         if (error.indexOf("login required"))
        // Description: ADAL wasn't able to silently acquire a token because of expire or fresh session. 
        // Action: Case 1, Resolvable with an interactive login() request. 

        // Error: if (error.indexOf("Token Renewal Failed")) 
        // Description: Timeout when refreshing the token.
        // Action: Case 2, Not resolvable interactively, error is retriable.
        // Perform a single retry. Only try again after user action.

        // Action: Case 3, Not resolvable interactively, error is not retriable. 
        // Do not perform an immediate retry. Only retry after user action.
        // Example Errors: default case
    }
}
```

### <a name="error-cases-and-actionable-steps-service-to-service-applications-net-only"></a>Fout gevallen en stappen die kunnen worden uitgevoerd: service-naar-service toepassingen (alleen .NET)

Als u een service-naar-service-toepassing bouwt die gebruikmaakt van AcquireToken, zijn er enkele belang rijke fouten die de code moet verwerken. De enige keer dat er een fout optreedt, is het retour neren van de fout terug naar de aanroepende app (voor namens de cases) of een strategie voor opnieuw proberen toe te passen. 

#### <a name="all-scenarios"></a>Alle scenario 's

Voor *alle* scenario's voor service-naar-service-toepassingen, met inbegrip van namens-van:

- Probeer niet onmiddellijk opnieuw te proberen. ADAL probeert een enkele nieuwe poging voor bepaalde mislukte aanvragen. 
- Alleen opnieuw proberen nadat een gebruiker of een app-actie wordt gevraagd een nieuwe poging. Een daemon-toepassing die werkt met een bepaald interval, moet bijvoorbeeld wachten tot het volgende interval opnieuw wordt opgegeven.

De volgende richt lijnen bevatten voor beelden voor het afhandelen van fouten in combi natie met ADAL-methoden: 

- AcquireTokenAsync(..., IClientAssertionCertification, ...)
- AcquireTokenAsync(...,ClientCredential, ...)
- AcquireTokenAsync(...,ClientAssertion, ...)
- AcquireTokenAsync(...,UserAssertion, ...)

De code wordt als volgt geïmplementeerd:

```csharp
try {
    AcquireTokenAsync(…);
} 

catch (AdalException e) {
    // Exception: AdalException
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Errors: default case
}  
```

#### <a name="on-behalf-of-scenarios"></a>Namens scenario's

Voor scenario's met *betrekking* tot service-naar-service-toepassingen.

De volgende richt lijnen bevatten voor beelden voor het afhandelen van fouten in combi natie met ADAL-methoden: 

- AcquireTokenAsync(..., UserAssertion, ...)

De code wordt als volgt geïmplementeerd:

```csharp
try {
AcquireTokenAsync(…);
} 

catch (AdalServiceException e) {
    // Exception: AdalServiceException 
    // Represents an error produced by the STS. 
    // e.ErrorCode contains the error code and description, which can be used for debugging. 
    // NOTE: Do not code a dependency on the contents of the error description, as it can change over time.

    // Error: On-Behalf-Of Error Handler
    if (e.ErrorCode == "interaction_required") {
        // Description: The client needs to perform some action due to a config from admin. 
        // Action: Capture `claims` parameter inside ex.InnerException.InnerException.
        // Generate HTTP error 403 with claims, throw back HTTP error to client.
        // Wait for client to retry. 
    }
} 
        
catch (AdalException e) {
    // Exception: AdalException 
    // Represents a library exception generated by ADAL .NET.
    // Error Code: e.ErrorCode

    // Action: Do not perform an immediate retry. Only try again after user action (if applicable) or wait until much later. 
    // Example Error: default case
}
```

We hebben een [volledig voor beeld](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) gemaakt waarin dit scenario wordt gedemonstreerd.

## <a name="error-and-logging-reference"></a>Naslag informatie voor fouten en logboek registratie

### <a name="logging-personal-identifiable-information--organizational-identifiable-information"></a>Logboek registratie van persoonlijke gegevens & organisatie herken bare informatie 
ADAL-logboek registratie legt geen persoonlijke Identificeer bare informatie vast of registreert geen gegevens die door de organisatie kunnen worden geïdentificeerd. Met de bibliotheek kunnen app-ontwikkel aars dit inschakelen via een setter in de klasse logger. Door persoonlijke Identificeer bare gegevens of gegevens van de organisatie te identificeren, wordt de app verantwoordelijk voor het veilig verwerken van uiterst gevoelige gegevens en het voldoen aan wettelijke vereisten.

### <a name="net"></a>.NET

#### <a name="adal-library-errors"></a>ADAL-bibliotheek fouten

Als u specifieke ADAL-fouten wilt verkennen, is de bron code in de [opslag plaats Azure-ActiveDirectory-Library-voor-dotnet](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/blob/8f6d560fbede2247ec0e217a21f6929d4375dcaa/src/ADAL.PCL/Utilities/Constants.cs#L58) de beste fout referentie.

#### <a name="guidance-for-error-logging-code"></a>Richt lijnen voor het vastleggen van fouten in code

ADAL .NET-logboek registratie is afhankelijk van het platform waaraan wordt gewerkt. Raadpleeg de [wiki logboeken](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Logging-in-ADAL.Net) voor code over het inschakelen van logboek registratie.

### <a name="android"></a>Android

#### <a name="adal-library-errors"></a>ADAL-bibliotheek fouten

Als u specifieke ADAL-fouten wilt verkennen, is de bron code in de [opslag plaats Azure-ActiveDirectory-library-for-Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/blob/dev/adal/src/main/java/com/microsoft/aad/adal/ADALError.java#L33) de beste fout referentie.

#### <a name="operating-system-errors"></a>Fouten van besturings systeem

Android-besturingssysteem fouten worden weer gegeven via verificatie in ADAL, zijn herkenbaar als ' SERVER_INVALID_REQUEST ' en kunnen verder worden uitgebreid door de fout beschrijvingen. 

Raadpleeg de [ADAL Android-wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki)voor een volledige lijst met veelvoorkomende fouten en de stappen die u moet uitvoeren wanneer uw app of eind gebruikers deze problemen ondervinden. 

#### <a name="guidance-for-error-logging-code"></a>Richt lijnen voor het vastleggen van fouten in code

```java
// 1. Configure Logger
Logger.getInstance().setExternalLogger(new ILogger() {    
    @Override   
    public void Log(String tag, String message, String additionalMessage, LogLevel level, ADALError errorCode) { 
    // …
    // You can write this to logfile depending on level or errorcode. 
    writeToLogFile(getApplicationContext(), tag +":" + message + "-" + additionalMessage);    
    }
}

// 2. Set the log level
Logger.getInstance().setLogLevel(Logger.LogLevel.Verbose);

// By default, the `Logger` does not capture any PII or OII

// PII or OII will be logged
Logger.getInstance().setEnablePII(true);

// To STOP logging PII or OII, use the following setter
Logger.getInstance().setEnablePII(false);


// 3. Send logs to logcat.
adb logcat > "C:\logmsg\logfile.txt";
```

### <a name="ios"></a>iOS

#### <a name="adal-library-errors"></a>ADAL-bibliotheek fouten

Als u specifieke ADAL-fouten wilt verkennen, is de bron code in de [opslag plaats Azure-ActiveDirectory-library-for-objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc/blob/dev/ADAL/src/ADAuthenticationError.m#L295) de beste fout referentie.

#### <a name="operating-system-errors"></a>Fouten van besturings systeem

Er kunnen iOS-fouten optreden tijdens het aanmelden wanneer gebruikers webweergaves en de aard van de verificatie gebruiken. Dit kan worden veroorzaakt door omstandigheden zoals TLS-fouten, time-outs of netwerk fouten:

- Voor het delen van rechten zijn aanmeldingen niet permanent en de cache is leeg. U kunt oplossen door de volgende regel code toe te voegen aan de sleutel hanger: `[[ADAuthenticationSettings sharedInstance] setSharedCacheKeychainGroup:nil];`
- Voor de NsUrlDomainset fouten verandert de actie afhankelijk van de app-logica. Zie de [NSURLErrorDomain-referentie documentatie](https://developer.apple.com/documentation/foundation/nsurlerrordomain#declarations) voor specifieke instanties die kunnen worden verwerkt.
- Zie [ADAL obj-C common issues (Engelstalig)](https://github.com/AzureAD/azure-activedirectory-library-for-objc#adauthenticationerror) voor een lijst met veelvoorkomende fouten die worden onderhouden door het ADAL-doel-C-team.

#### <a name="guidance-for-error-logging-code"></a>Richt lijnen voor het vastleggen van fouten in code

```objc
// 1. Enable NSLogging
[ADLogger setNSLogging:YES];

// 2. Set the log level (if you want verbose)
[ADLogger setLevel:ADAL_LOG_LEVEL_VERBOSE];

// 3. Set up a callback block to simply print out
[ADLogger setLogCallBack:^(ADAL_LOG_LEVEL logLevel, NSString *message, NSString *additionalInformation, NSInteger errorCode, NSDictionary *userInfo) {
     NSString* log = [NSString stringWithFormat:@"%@ %@", message, additionalInformation];    
     NSLog(@"%@", log);
}];
```

### <a name="guidance-for-error-logging-code---javascript"></a>Richt lijnen voor het vastleggen van fouten in code-java script 

```javascript
0: Error1: Warning2: Info3: Verbose
window.Logging = {
    level: 3,
    log: function (message) {
        console.log(message);
    }
};
```

## <a name="related-content"></a>Gerelateerde inhoud

* [Azure AD-verificatie bibliotheken][AAD-Auth-Libraries]
* [Scenario's voor Azure AD-verificatie][AAD-Auth-Scenarios]
* [Toepassingen integreren met Azure Active Directory][AAD-Integrating-Apps]

Gebruik de volgende opmerkingen om feedback te geven en ons te helpen bij het verfijnen en vormen van onze inhoud.

[![Hiermee wordt de knop ' Aanmelden met Microsoft ' weer gegeven][AAD-Sign-In]][AAD-Sign-In]
<!--Reference style links -->

[AAD-Auth-Libraries]: ./active-directory-authentication-libraries.md
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Integrating-Apps]:../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[AZURE-portal]: https://portal.azure.com

<!--Image references-->
[AAD-Sign-In]:./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png

