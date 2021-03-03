---
title: 'Snelstartgids: Microsoft Graph aanroepen vanuit een Node.js bureau blad-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze Quick Start leert u hoe een Node.js Elektroon-bureaublad toepassing gebruikers kan aanmelden en een toegangs token kan ophalen om een API aan te roepen die wordt beveiligd door een micro soft Identity platform-eind punt
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653266"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Quick Start: een toegangs token verkrijgen en de Microsoft Graph-API aanroepen vanuit een Elektror bureau blad-app

In deze Snelstartgids downloadt en voert u een code voorbeeld uit waarin wordt getoond hoe een Elektroon-bureaublad toepassing gebruikers kan aanmelden en toegangs tokens kan verkrijgen om de Microsoft Graph-API aan te roepen.

In deze Quick Start wordt de [micro soft-verificatie bibliotheek voor Node.js (MSAL-knoop punt)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) gebruikt met de [autorisatie code stroom met PKCE](v2-oauth2-auth-code-flow.md).

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) of een andere code-editor

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>De voorbeeld toepassing registreren en downloaden
>
> Volg de onderstaande stappen om aan de slag te gaan.
>
> #### <a name="step-1-register-the-application"></a>Stap 1: Registreer de toepassing
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer **Registreren** om de toepassing te maken.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Selecteer **Een platform toevoegen** > **Mobiele en desktoptoepassingen**.
> 1. Voer in de sectie **Omleidings-URI's** `msal://redirect` in.
> 1. Selecteer **Configureren**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Stap 1: de toepassing configureren in Azure Portal
> Het code voorbeeld voor deze Quick Start werkt alleen als u een antwoord-URL als **msal://Redirect** toevoegt.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-windows-desktop/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-electron-sample-project"></a>Stap 2: het voorbeeld project van elektron downloaden

> [!div renderon="docs"]
> [Het codevoorbeeld downloaden](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>Stap 3: het voorbeeld project elektron configureren
>
> 1. Pak het zip-bestand uit naar een lokale map dicht bij de hoofdmap van de schijf, bijvoorbeeld *C:/Azure-samples*.
> 1. Bewerk *. env* en vervang de waarden van de velden `TENANT_ID` en `CLIENT_ID` met het volgende code fragment:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Waar:
>    - `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
>    - `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)** en **Map-id (tenant-id)** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Stap 4: De toepassing uitvoeren

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>Stap 4: De toepassing uitvoeren

U moet de afhankelijkheden van dit voor beeld eenmaal installeren:

```console
npm install
```

Voer dan de toepassing uit via de opdrachtprompt of console:

```console
npm start
```

U ziet de gebruikers interface van de toepassing met een knop **Aanmelden** .

## <a name="about-the-code"></a>Over de code

Hieronder worden enkele belang rijke aspecten van de voorbeeld toepassing besproken.

### <a name="msal-node"></a>MSAL Node

[MSAL knoop punt](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt om toegang te krijgen tot een API die wordt beveiligd door micro soft Identity platform. Zie [dit artikel](scenario-desktop-overview.md)voor meer informatie over het gebruik van het MSAL-knoop punt met desktop-apps.

U kunt MSAL-knoop punt installeren door de volgende NPM-opdracht uit te voeren.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor het knoop punt MSAL toevoegen door de volgende code toe te voegen:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Vervolgens initialiseert u MSAL met de volgende code:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Waar: |Beschrijving |
> |---------|---------|
> | `clientId` | Is de **Toepassings-id (client-id)** voor de toepassing die is geregistreerd in de Azure-portal. U vindt deze waarde op de pagina **Overzicht** in de Azure-portal. |
> | `authority`    | Het STS-eindpunt voor de gebruiker voor verificatie. Dat is meestal `https://login.microsoftonline.com/{tenant}` voor de openbare cloud, waarbij {tenant} de naam van uw tenant of uw tenant-id is.|

### <a name="requesting-tokens"></a>Tokens aanvragen

In het eerste gedeelte van de autorisatie code stroom met PKCE, voor bereiding en verzend een aanvraag voor een autorisatie code met de juiste para meters. Luister vervolgens in het tweede gedeelte van de stroom naar de reactie van de autorisatie code. Zodra de code is verkregen, moet u deze door geven om een token te verkrijgen.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Waar:| Beschrijving |
> |---------|---------|
> | `authWindow` | Huidig elektro werk venster in verwerking. |
> | `tokenRequest` | Bevat de bereiken die worden aangevraagd, bijvoorbeeld `"User.Read"` voor Microsoft Graph of `"api://<Application ID>/access_as_user"` voor aangepaste web-API's. |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de zelf studie voor meer informatie over het ontwikkelen van MSAL-desktop-apps met het knoop punt:

> [!div class="nextstepaction"]
> [Zelf studie: gebruikers aanmelden en de Microsoft Graph-API aanroepen in een Elektroon-desktop-app](tutorial-v2-nodejs-desktop.md)