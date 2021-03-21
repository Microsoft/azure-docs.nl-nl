---
title: Fouten en uitzonderingen verwerken in MSAL.js
titleSuffix: Microsoft identity platform
description: Meer informatie over het afhandelen van fouten en uitzonde ringen, claim problemen met voorwaardelijke toegang en nieuwe pogingen in MSAL.js toepassingen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761335"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>Fouten en uitzonderingen verwerken in MSAL.js

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>Fout afhandeling in MSAL.js

MSAL.js biedt fout objecten die de verschillende soorten veelvoorkomende fouten abstract en geclassificeerd. Het biedt ook een interface voor toegang tot specifieke details van de fouten, zoals fout berichten om ze op de juiste manier af te handelen.

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

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

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


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Volgende stappen

Schakel [logboek registratie in MSAL.jsin ](msal-logging-js.md) om u te helpen bij het vaststellen en oplossen van problemen.
