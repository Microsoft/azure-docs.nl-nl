---
title: Een token verkrijgen om een web-API aan te roepen (apps met één pagina) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een toepassing met één pagina (een Token ophalen om een API aan te roepen)
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: 83896b2599f03961b2dcaf34ea9b55fe16c13b9e
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756437"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Toepassing met één pagina: een Token ophalen om een API aan te roepen

Het patroon voor het verkrijgen van tokens voor Api's met MSAL.js is om eerst een Silent token aanvraag te proberen met behulp van de- `acquireTokenSilent` methode. Wanneer deze methode wordt aangeroepen, controleert de bibliotheek eerst de cache in browser opslag om te zien of er een geldig token bestaat en retourneert het. Als er geen geldig token in de cache staat, verzendt het een Silent-token aanvraag naar Azure Active Directory (Azure AD) van een verborgen iframe. Met deze methode kan de bibliotheek ook tokens vernieuwen. Zie [levens duur van tokens](active-directory-configurable-token-lifetimes.md)voor meer informatie over de waarden van de sessie voor eenmalige aanmelding en levens duur van tokens in azure AD.

De aanvragen voor het Silent-token voor Azure AD kunnen mislukken vanwege een verlopen Azure AD-sessie of een wijziging in een wacht woord. In dat geval kunt u een van de interactieve methoden (waarmee de gebruiker wordt gevraagd) aanroepen om tokens te verkrijgen:

* [Pop-upvenster](#acquire-a-token-with-a-pop-up-window), met behulp van `acquireTokenPopup`
* [Omleiden](#acquire-a-token-with-a-redirect)met `acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Kiezen tussen een pop-up-of omleidings ervaring

 U kunt niet zowel de pop-up-als omleidings methoden in uw toepassing gebruiken. De keuze tussen een pop-up-of omleidings ervaring is afhankelijk van uw toepassings stroom:

* Als u niet wilt dat gebruikers tijdens de verificatie naar de hoofd pagina van de toepassing gaan, wordt de pop-upmethode aanbevolen. Omdat de verificatie omleiding plaatsvindt in een pop-upvenster, blijft de status van de hoofd toepassing behouden.

* Als gebruikers browser beperkingen of-beleid hebben waarin pop-ups Windows zijn uitgeschakeld, kunt u de omleidings methode gebruiken. Gebruik de omleidings methode met de Internet Explorer-browser, omdat er [bekende problemen zijn met pop-upvensters in Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

U kunt de API-bereiken instellen waarvan u wilt dat het toegangs token moet worden opgenomen wanneer het de toegangs token aanvraag bouwt. Houd er rekening mee dat alle aangevraagde bereiken mogelijk niet worden verleend in het toegangs token. Dat is afhankelijk van de toestemming van de gebruiker.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Een token verkrijgen met een pop-upvenster

# <a name="javascript"></a>[JavaScript](#tab/javascript)

In de volgende code wordt het eerder beschreven patroon gecombineerd met de methoden voor een pop-upervaring:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

# <a name="angular"></a>[Angular](#tab/angular)

De MSAL-hoek wrapper biedt de HTTP-Interceptor, waarmee automatisch toegangs tokens op de achtergrond worden opgehaald en worden gekoppeld aan de HTTP-aanvragen voor Api's.

U kunt de scopes voor Api's opgeven in de `protectedResourceMap` configuratie optie. `MsalInterceptor` vraagt deze bereiken bij het automatisch ophalen van tokens.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Voor het slagen en mislukken van de aanschaf van de Silent-tokens, MSAL hoek biedt retour aanroepen waarop u zich kunt abonneren. Het is ook belang rijk om te onthouden dat u zich wilt afmelden.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

U kunt ook expliciet tokens verkrijgen met behulp van de methoden Acquire-token, zoals beschreven in de basis MSAL.js-bibliotheek.

---

## <a name="acquire-a-token-with-a-redirect"></a>Een token verkrijgen met een omleiding

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Het volgende patroon is eerder beschreven, maar wordt weer gegeven met een omleidings methode om tokens interactief te verkrijgen. U moet de retour aanroep van de omleiding registreren zoals eerder is beschreven.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Optionele claims aanvragen

U kunt optionele claims gebruiken voor de volgende doel einden:

- Neem aanvullende claims op in tokens voor uw toepassing.
- Wijzig het gedrag van bepaalde claims die Azure AD retourneert in tokens.
- Aangepaste claims toevoegen en openen voor uw toepassing.

Als u optionele claims in wilt aanvragen `IdToken` , kunt u een stringified-claim object verzenden naar het `claimsRequest` veld van de `AuthenticationParameters.ts` klasse.

```javascript
"optionalClaims":
   {
      "idToken": [
            {
                  "name": "auth_time",
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```

Zie voor meer informatie [optionele claims](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Deze code is hetzelfde als eerder beschreven.

---

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario om [een web-API](scenario-spa-call-api.md)aan te roepen.