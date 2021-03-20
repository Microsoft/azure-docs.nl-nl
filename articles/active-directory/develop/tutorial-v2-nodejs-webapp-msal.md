---
title: 'Zelf studie: aanmeldings gebruikers in een Node.js & Express web-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelf studie voegt u ondersteuning toe voor het aanmelden van gebruikers in een web-app.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 3f1f26acbba0f5830421e760d6a68a11f618fa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648987"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Zelf studie: aanmeldings gebruikers in een Node.js & snelle web-app

In deze zelf studie bouwt u een web-app die gebruikers aanmeldt. De web-app die u bouwt, maakt gebruik [van de micro soft Authentication Library (MSAL) voor het knoop punt](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Volg de stappen in deze zelf studie voor het volgende:

> [!div class="checklist"]
> - De app registreren in de Azure Portal
> - Een Express web-app-project maken
> - De verificatie bibliotheek pakketten installeren
> - App-registratie Details toevoegen
> - Code voor gebruikers aanmelding toevoegen
> - De app testen

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) of een andere code-editor

## <a name="register-the-application"></a>De toepassing registreren

Voer eerst de stappen in [een toepassing registreren met het micro soft Identity-platform](quickstart-register-app.md) uit om uw app te registreren.

Gebruik de volgende instellingen voor de registratie van uw app:

- Naam: `ExpressWebApp` (aanbevolen)
- Ondersteunde account typen: **accounts in elke organisatie Directory (een Azure AD-adres lijst met meerdere tenants) en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox)**
- Platform type: **Web**
- Omleidings-URI: `http://localhost:3000/redirect`
- Client geheim: `*********` (deze waarde vastleggen voor gebruik in een latere stap. dit wordt slechts één keer weer gegeven)

## <a name="create-the-project"></a>Het project maken

Maak een map om uw toepassing te hosten, bijvoorbeeld *ExpressWebApp*.

1. Ga eerst naar de projectmap in uw terminal en voer de volgende `npm`-opdrachten uit:

```console
    npm init -y
    npm install --save express
```

2. Maak vervolgens een bestand met de naam *index.js* en voeg de volgende code toe:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

U hebt nu een eenvoudige webserver die wordt uitgevoerd op poort 3000. De structuur van het bestand en de map van het project moet er ongeveer als volgt uitzien:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>De verificatie bibliotheek installeren

Ga naar de hoofdmap van de projectmap in een Terminal en installeer het MSAL-knooppunt pakket via NPM.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>App-registratie Details toevoegen

Voeg de volgende code toe aan het *index.js* -bestand dat u eerder hebt gemaakt:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
```

Vul deze gegevens in met de waarden die u hebt opgehaald via de Azure app Registration-portal:

- `Enter_the_Tenant_Id_here` een van de volgende waarden zijn:
  - Als uw toepassing ondersteuning biedt voor *accounts in deze organisatiemap*, vervang deze waarde dan door de **Tenant-id** of **Tenantnaam**. Bijvoorbeeld `contoso.microsoft.com`.
  - Als uw toepassing ondersteuning biedt voor *accounts in elke organisatiemap*, vervangt u waarde door `organizations`.
  - Als uw toepassing *accounts in elke organisatiemap en persoonlijke Microsoft-accounts* ondersteunt, vervang deze waarde dan door `common`.
  - Als u de ondersteuning wilt beperken tot *alleen persoonlijke Microsoft-accounts*, vervang deze waarde dan door `consumers`.
- `Enter_the_Application_Id_Here`: De **Id van de toepassing (client)** van de toepassing die u hebt geregistreerd.
- `Enter_the_Cloud_Instance_Id_Here`: Het Azure-cloudexemplaar waarin uw toepassing is geregistreerd.
  - Voer `https://login.microsoftonline.com` in voor de hoofd- (of *globale*) Azure-cloud.
  - Voor **nationale** clouds (bijvoorbeeld China) kunt u de juiste waarden vinden in [Nationale clouds](authentication-national-cloud.md).
- `Enter_the_Client_secret`: Vervang deze waarde door het client geheim dat u eerder hebt gemaakt. Als u een nieuwe sleutel wilt genereren, gebruikt u **certificaten & geheimen** in de instellingen voor app-registratie in de Azure Portal.

> [!WARNING]
> Een geheim met lees bare tekst in de bron code vormt een groter beveiligings risico. In dit artikel wordt een client geheim met lees bare tekst gebruikt voor alleen eenvoud. Gebruik [certificaat referenties](active-directory-certificate-credentials.md) in plaats van client geheimen in uw vertrouwelijke client toepassingen, met name de apps die u wilt implementeren voor productie.

## <a name="add-code-for-user-login"></a>Code voor gebruikers aanmelding toevoegen

Voeg de volgende code toe aan het *index.js* -bestand dat u eerder hebt gemaakt:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Aanmelden bij test

Het maken van de toepassing is voltooid en u bent nu klaar om de functionaliteit van de app te testen.

1. Start de Node.js console-app door de volgende opdracht uit te voeren vanuit de hoofdmap van de projectmap:

```console
   node index.js
```

2. Open een browservenster en ga naar `http://localhost:3000`. Er wordt een aanmeldings scherm weer gegeven:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Aanmeldings scherm van Azure AD weer geven":::

3. Wanneer u uw referenties hebt ingevoerd, wordt een venster met toestemming weer gegeven waarin u wordt gevraagd de machtigingen voor de app goed te keuren.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Het toestemming scherm voor Azure AD wordt weer gegeven":::

## <a name="how-the-application-works"></a>Hoe de toepassing werkt

In deze zelf studie hebt u een [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) -object van het MSAL-knoop punt geïnitialiseerd door het door te geven aan een configuratie object (*msalConfig*) dat para meters bevat die zijn verkregen van uw Azure AD-app-registratie op Azure Portal. De web-app die u hebt gemaakt, maakt gebruik van de [OAuth 2,0-autorisatie code subsidie stroom](./v2-oauth2-auth-code-flow.md) voor het aanmelden van gebruikers en het verkrijgen van id-en toegangs tokens.

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over het ontwikkelen van Node.js & Express-webtoepassing op het micro soft-identiteits platform, raadpleegt u onze scenario reeks met meerdere onderdelen:

> [!div class="nextstepaction"]
> [Scenario: Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md)