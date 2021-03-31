---
title: 'Zelf studie: Microsoft Graph aanroepen in een Node.js-console-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelf studie bouwt u een console-app voor het aanroepen van Microsoft Graph aan een Node.js-console-app.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 33d3712e25a06419e0ccc5914cdddfae7d85a371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645786"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Zelf studie: de Microsoft Graph-API aanroepen in een Node.js-console-app

In deze zelf studie bouwt u een console-app die Microsoft Graph-API aanroept met behulp van een eigen identiteit. De console-app die u bouwt [, maakt gebruik van de micro soft Authentication Library (MSAL) voor Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Volg de stappen in deze zelf studie voor het volgende:

> [!div class="checklist"]
> - De app registreren in de Azure Portal
> - Een Node.js console-app-project maken
> - Verificatie logica toevoegen aan uw app
> - App-registratie Details toevoegen
> - Een methode toevoegen om een web-API aan te roepen
> - De app testen

## <a name="prerequisites"></a>Vereisten

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) of een andere code-editor

## <a name="register-the-application"></a>De toepassing registreren

Voer eerst de stappen in [een toepassing registreren met het micro soft Identity-platform](quickstart-register-app.md) uit om uw app te registreren.

Gebruik de volgende instellingen voor de registratie van uw app:

- Naam: `NodeConsoleApp` (aanbevolen)
- Ondersteunde account typen: **accounts in deze organisatie-Directory alleen**
- API-machtigingen: **micro soft-api's**  >  **Microsoft Graph**  >  **toepassings machtigingen** > `User.Read.All`
- Client geheim: `*********` (deze waarde vastleggen voor gebruik in een latere stap. dit wordt slechts één keer weer gegeven)

## <a name="create-the-project"></a>Het project maken

Maak een map om uw toepassing te hosten, bijvoorbeeld *NodeConsoleApp*.

1. Ga eerst naar de projectmap in uw Terminal en voer de volgende NPM-opdrachten uit:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Maak vervolgens een map met de naam *bin*. Vervolgens maakt u in deze map het bestand met de naam *index.js* en voegt u de volgende code toe:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Dit bestand verwijst naar twee andere knooppunt modules: *auth.js* die een implementatie van het MSAL-knoop punt bevat voor het verkrijgen van toegangs tokens en *fetch.js* die een methode bevat voor het maken van een HTTP-aanvraag voor Microsoft Graph API met een toegangs token. Nadat u de rest van de zelfstudie hebt voltooid, moeten de bestands- en mapstructuur van het project er ongeveer als volgt uitzien:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Verificatie logica toevoegen

Maak in de map *bin* een ander bestand met de naam *auth.js* en voeg de volgende code toe om een toegangs token op te halen dat wordt weer gegeven bij het aanroepen van de Microsoft Graph-API.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

In het bovenstaande code fragment maken we eerst een configuratie object (*msalConfig*) en geven ze een door om een MSAL- [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)te initialiseren. Vervolgens maken we een methode voor het verkrijgen van tokens via **client referenties** en wordt deze module beschikbaar gesteld voor toegang door *main.js*. De configuratie parameters in deze module worden getekend vanuit een omgevings bestand, dat we in de volgende stap gaan maken.

## <a name="add-app-registration-details"></a>App-registratie Details toevoegen

Maak een omgevings bestand om de details van de app-registratie op te slaan die worden gebruikt bij het ophalen van tokens. Als u dit wilt doen, maakt u een bestand met de naam *. env* in de hoofdmap van het voor beeld (*NodeConsoleApp*) en voegt u de volgende code toe:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
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
- `Enter_the_Graph_Endpoint_Here` is het exemplaar van de Microsoft Graph API waarmee de toepassing moet communiceren.
  - Vervang beide exemplaren van deze teken reeks door `https://graph.microsoft.com` voor het **wereldwijde** Microsoft Graph API-eindpunt.
  - Zie [Nationale cloudimplementaties](/graph/deployments) in de Microsoft Graph-documentatie voor eindpunten in **nationale** cloudimplementaties.

## <a name="add-a-method-to-call-a-web-api"></a>Een methode toevoegen om een web-API aan te roepen

Maak in de map *bin* een ander bestand met de naam *fetch.js* en voeg de volgende code toe om rest-aanroepen naar de Microsoft Graph-API te brengen:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

Hier wordt de `callApi` methode gebruikt om een HTTP-aanvraag te doen `GET` tegen een beveiligde bron waarvoor een toegangs token is vereist. De aanvraag retourneert vervolgens de inhoud naar de aanroeper. Met deze methode wordt het verkregen token toegevoegd in de *HTTP-autorisatie-header*. De beveiligde bron hier is het [eind punt](/graph/api/user-list) van de Microsoft Graph API-gebruikers. Hiermee worden de gebruikers weer gegeven in de Tenant waarin deze app is geregistreerd.

## <a name="test-the-app"></a>De app testen

Het maken van de toepassing is voltooid en u bent nu klaar om de functionaliteit van de app te testen.

Start de Node.js console-app door de volgende opdracht uit te voeren vanuit de hoofdmap van de projectmap:

```console
node . --op getUsers
```

Dit moet resulteren in een enkele JSON-reactie van Microsoft Graph-API en u ziet een matrix met gebruikers objecten in de-console:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Opdracht regel interface die de reactie van een grafiek weergeeft":::

## <a name="how-the-application-works"></a>Hoe de toepassing werkt

Deze toepassing maakt gebruik van [OAuth 2,0-client referenties toewijzen](./v2-oauth2-client-creds-grant-flow.md). Dit type toekenning wordt meestal gebruikt voor server-naar-server-interacties die op de achtergrond moeten worden uitgevoerd, zonder directe interactie met een gebruiker. Met de referenties overdracht stroom kan een webservice (vertrouwelijke client) eigen referenties gebruiken, in plaats van een gebruiker te imiteren, om te verifiëren wanneer een andere webservice wordt aangeroepen. Het type toepassingen dat met dit verificatie model wordt ondersteund, zijn meestal **daemons** of **service accounts**.

Het bereik dat moet worden aangevraagd voor een client referentie stroom is de naam van de resource gevolgd door `/.default` . Deze notatie vertelt Azure Active Directory (Azure AD) voor het gebruik van de machtigingen op toepassings niveau die statisch zijn gedeclareerd tijdens de registratie van de toepassing. Daarnaast moeten deze API-machtigingen worden verleend door een **Tenant beheerder**.

## <a name="next-steps"></a>Volgende stappen

Als u meer wilt weten over de ontwikkeling van Node.js-console toepassing op het micro soft-identiteits platform, raadpleegt u onze scenario reeks met meerdere onderdelen:

> [!div class="nextstepaction"]
> [Scenario: daemon-toepassing](scenario-daemon-overview.md)