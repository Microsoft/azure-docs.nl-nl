---
title: MSAL.js client-apps initialiseren | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het initialiseren van client toepassingen met behulp van de micro soft-verificatie bibliotheek voor Java script (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: fee241ba9f4a340513951c515f1996fc7e2385bf
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063590"
---
# <a name="initialize-client-applications-using-msaljs"></a>Client toepassingen initialiseren met MSAL.js

In dit artikel wordt beschreven hoe u de micro soft-verificatie bibliotheek voor Java script (MSAL.js) initialiseert met een exemplaar van een toepassing van een gebruikers agent.

De toepassing van de gebruikers agent is een vorm van een open bare client toepassing waarin de client code wordt uitgevoerd in een gebruikers agent, zoals een webbrowser. Dergelijke clients slaan geen geheimen op omdat de browser context geopend is.

Zie [open bare en vertrouwelijke client-apps in MSAL](msal-client-applications.md)voor meer informatie over de client toepassings typen en toepassings configuratie opties.

## <a name="prerequisites"></a>Vereisten

Voordat u een toepassing initialiseert, moet u [deze eerst registreren bij de Azure Portal](scenario-spa-app-registration.md), waarbij u een vertrouwens relatie tot stand brengt tussen uw toepassing en het micro soft Identity-platform.

Nadat u uw app hebt geregistreerd, hebt u enkele of alle van de volgende waarden nodig die u kunt vinden in de Azure Portal.

| Waarde | Vereist | Beschrijving |
|:----- | :------: | :---------- |
| (Client-)id van de app | Vereist | Een GUID die uw toepassing op unieke wijze identificeert binnen het micro soft Identity-platform. |
| Instantie | Optioneel | De URL van de identiteits provider (het *exemplaar*) en de *doel groep voor aanmelding* voor uw toepassing. Het exemplaar en de doel groep bij het samen voegen van de *instantie*. |
| (Tenant-)id van de map | Optioneel | Geef dit op als u een line-of-Business-toepassing wilt bouwen voor uw organisatie, ook wel een *toepassing met één Tenant* genoemd. |
| Omleidings-URI | Optioneel | Als u een web-app bouwt, `redirectUri` geeft de opgegeven locatie aan waar de ID-provider (het micro soft Identity-platform) de beveiligings tokens moet retour neren die het heeft uitgegeven. |

## <a name="initialize-msaljs-2x-apps"></a>Initialiseren MSAL.js 2. x-apps

Initialiseer de MSAL-verificatie context door een [PublicClientApplication][msal-js-publicclientapplication] met een [configuratie][msal-js-configuration] object te instantiëren. De mini maal vereiste configuratie-eigenschap is het `clientID` van uw toepassing, weer gegeven als de **toepassings-id (client)** op de pagina **overzicht** van de app-registratie in de Azure Portal.

Hier volgt een voor beeld van een configuratie object en instantiëring van een `PublicClientApplication` :

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

Roep [handleRedirectPromise][msal-js-handleredirectpromise] aan als uw toepassing gebruikmaakt van de omleidings stromen. Bij het gebruik van de omleidings stromen `handleRedirectPromise` moet worden uitgevoerd op elke pagina belasting.

Er zijn drie mogelijke resultaten van de belofte:

- `.then` wordt aangeroepen en `tokenResponse` is een waarheid: de toepassing wordt geretourneerd vanuit een omleidings bewerking die is geslaagd.
- `.then` wordt aangeroepen en `tokenResponse` is False ( `null` ): de toepassing wordt niet geretourneerd vanuit een omleidings bewerking.
- `.catch` wordt aangeroepen: de toepassing wordt geretourneerd vanuit een omleidings bewerking en er is een fout opgetreden.

## <a name="initialize-msaljs-1x-apps"></a>Initialiseren MSAL.js 1. x-apps

Initialiseer de MSAL 1. x-verificatie context door een [UserAgentApplication][msal-js-useragentapplication] met een configuratie object te instantiëren. De mini maal vereiste configuratie-eigenschap is het `clientID` van uw toepassing, weer gegeven als de **toepassings-id (client)** op de pagina **overzicht** van de app-registratie in de Azure Portal.

Voor verificatie methoden met omleidings stromen ([loginRedirect][msal-js-loginredirect] en [acquireTokenRedirect][msal-js-acquiretokenredirect]) in MSAL.js 1.2. x of eerder moet u expliciet een call back voor geslaagde of mislukte registraties door de `handleRedirectCallback()` methode registreren. Het expliciet registreren van de call back is vereist in MSAL.js 1.2. x en lager omdat omleidings stromen geen belofte retour neren zoals de methoden met een pop-upervaring. Het registreren van de call back is *optioneel* in MSAL.js versie 1.3. x of hoger.

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>Eén exemplaar en configuratie

Beide MSAL.js 1. x en 2. x zijn zodanig ontworpen dat er één exemplaar en configuratie van de `UserAgentApplication` or `PublicClientApplication` , respectievelijk, om een enkele verificatie context te vertegenwoordigen.

Meerdere exemplaren van `UserAgentApplication` of `PublicClientApplication` worden niet aanbevolen, omdat deze conflicterende cache vermeldingen en gedrag veroorzaken in de browser.

## <a name="next-steps"></a>Volgende stappen

Dit MSAL.js 2. x-code voorbeeld op GitHub demonstreert de instantiëring van een [PublicClientApplication][msal-js-publicclientapplication] met een [configuratie][msal-js-configuration] object:

[Azure-samples/MS-Identity-java script-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal.html#configuration
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
