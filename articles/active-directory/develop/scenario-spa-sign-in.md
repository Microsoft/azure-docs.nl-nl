---
title: Aanmeldings & voor een app met één pagina
titleSuffix: Microsoft identity platform
description: Meer informatie over het maken van een toepassing met één pagina (aanmelden)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 60ecb60d2fe90f190963255adff7a0bb1df15da4
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756373"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Toepassing met één pagina: aanmelden en afmelden

Meer informatie over het toevoegen van een aanmelding aan de code voor uw toepassing met één pagina.

Voordat u tokens kunt ophalen voor toegang tot Api's in uw toepassing, hebt u een geverifieerde gebruikers context nodig. U kunt gebruikers op twee manieren aanmelden bij uw toepassing in MSAL.js:

* [Pop-upvenster](#sign-in-with-a-pop-up-window), met behulp van de- `loginPopup` methode
* [Omleiden](#sign-in-with-redirect), met behulp van de- `loginRedirect` methode

U kunt eventueel ook de scopes door geven van de Api's waarvoor u de gebruiker om toestemming moet vragen op het moment dat u zich aanmeldt.

> [!NOTE]
> Als uw toepassing al toegang heeft tot een geverifieerde gebruikers context of ID-token, kunt u de aanmeldings stap overs Laan en de tokens rechtstreeks ophalen. Zie [SSO zonder MSAL.js login](msal-js-sso.md#sso-without-msaljs-login)voor meer informatie.

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Kiezen tussen een pop-up-of omleidings ervaring

U kunt niet zowel de pop-up-als omleidings methoden in uw toepassing gebruiken. De keuze tussen een pop-up-of omleidings ervaring is afhankelijk van uw toepassings stroom:

* Als u niet wilt dat gebruikers tijdens de verificatie naar de hoofd pagina van de toepassing gaan, wordt de pop-upmethode aangeraden. Omdat de verificatie omleiding plaatsvindt in een pop-upvenster, blijft de status van de hoofd toepassing behouden.

* Als gebruikers browser beperkingen of-beleid hebben waarin pop-upvensters zijn uitgeschakeld, kunt u de omleidings methode gebruiken. Gebruik de omleidings methode met de Internet Explorer-browser, omdat er [bekende problemen zijn met pop-upvensters in Internet Explorer](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Aanmelden met een pop-upvenster


# <a name="javascript-msaljs-2x"></a>[Java script (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success

        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();
    
        if (currentAccounts === null) {
            // no accounts detected
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
        }
    
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-1x"></a>[Java script (MSAL.js 1. x)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

Met de MSAL-hoek wrapper kunt u specifieke routes in uw toepassing beveiligen door deze toe te voegen `MsalGuard` aan de definitie van de route. Deze Guard roept de-methode aan om u aan te melden wanneer de route wordt geopend.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Schakel de configuratie optie in voor een pop-upvenster `popUp` . U kunt ook de volgende bereiken door geven die toestemming nodig heeft:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Aanmelden met omleiding

# <a name="javascript-msaljs-2x"></a>[Java script (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    //handle redirect response

    // In case multiple accounts exist, you can select
    const currentAccounts = myMsal.getAllAccounts();

    if (currentAccounts === null) {
        // no accounts detected
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
    }
}

myMsal.handleRedirectPromise(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[Java script (MSAL.js 1. x)](#tab/javascript1)

De omleidings methoden retour neren geen belofte als gevolg van de verplaatsing van de hoofd toepassing. Als u de geretourneerde tokens wilt verwerken en openen, moet u geslaagde en fout-Call backs registreren voordat u de omleidings methoden aanroept.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

De code is hetzelfde als hierboven beschreven in het gedeelte over aanmelden met een pop-upvenster. De standaard stroom wordt omgeleid.

---

## <a name="sign-out"></a>Afmelden

De MSAL-bibliotheek biedt een `logout` methode waarmee de cache in browser opslag wordt gewist en waarmee een afmeldings aanvraag wordt verzonden naar Azure Active Directory (Azure AD). Nadat u zich hebt afgemeld, wordt de tape wisselaar standaard weer doorgestuurd naar de start pagina van de toepassing.

U kunt de URI configureren waarnaar de omleiding moet worden omgeleid na het instellen van de registratie `postLogoutRedirectUri` . Deze URI moet ook worden geregistreerd als de afmeldings-URI in de registratie van uw toepassing.

# <a name="javascript-msaljs-2x"></a>[Java script (MSAL.js 2. x)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByUsername(username)
}

myMsal.logout(logoutRequest);
```

# <a name="javascript-msaljs-1x"></a>[Java script (MSAL.js 1. x)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel in dit scenario en [Verwerf een token voor de app](scenario-spa-acquire-token.md).