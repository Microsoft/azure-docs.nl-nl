---
title: Trusted Service JavaScript
description: Dit is de Java script-versie van het maken van een vertrouwde service voor Communication Services.
author: dademath
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/28/2020
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: a16846b8859f93a2d376691115e4b2dd0a7163b6
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633348"
---
## <a name="download-code"></a>Download code

De voltooide code voor deze Quick Start vinden op [github](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/Trusted%20Authentication%20Service)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie.
- [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).
- [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (10.14.1 aanbevolen). Gebruik de opdracht `node --version` om uw versie te controleren. 
- De [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio Code. 
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../../quickstarts/create-communication-resource.md).

## <a name="overview"></a>Overzicht

:::image type="content" source="../media/trusted-service-architecture.png" alt-text="Diagram voor architectuur van een vertrouwde service":::

Voor deze zelfstudie gaan we een Azure-functie maken die als een service voor het inrichten van een vertrouwde token zal fungeren. U kunt deze zelfstudie gebruiken om uw eigen service voor het inrichten van een token te bootstrappen.

Met deze service worden gebruikers bij Azure Communication Services geverifieerd. Voor gebruikers van uw Communication Services-toepassingen is een `Access Token` vereist om deel te kunnen nemen aan chatthreads en VoIP-oproepen. De Azure-functie werkt als een vertrouwde schakel tussen de gebruiker en Communication Services. Zo kunt u toegangstokens inrichten zonder dat uw gebruikers de verbindingsreeks van uw resource krijgen te zien.

Zie de documentatie over de concepten [client-serverarchitectuur](../../concepts/client-and-server-architecture.md) en [verificatie en autorisatie](../../concepts/authentication.md) voor meer informatie.

## <a name="setting-up"></a>Instellen

### <a name="azure-functions-set-up"></a>Azure Functions instellen

Eerst gaat u de basisstructuur voor uw Azure-functie instellen. Stapsgewijze instructies voor deze instellingen vindt u hier: [Een functie maken met behulp van Visual Studio Code](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript)

Voor de Azure-functie is de volgende configuratie vereist:

- Taal: Javascript
- Sjabloon: HTTP-trigger
- Autorisatieniveau: Anoniem (dit kan later worden gewijzigd als u de voorkeur geeft aan een ander autorisatiemodel)
- Functienaam: Door de gebruiker gedefinieerd

Na het volgen van de [instructies voor Azure Functions](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript) met de bovenstaande configuratie, moet u een project in Visual Studio Code voor de Azure-functie hebben dat een bestand `index.js` heeft dat de functie zelf bevat. De code in dit bestand moet er als volgt uitzien:

```javascript

module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}

```

We gaan nu verder met het installeren van Azure Communication Services-bibliotheken.

### <a name="install-communication-services-libraries"></a>Communicatie Services-bibliotheken installeren

We gaan bibliotheek gebruiken `Administration` om `User Access Tokens` te genereren.

Gebruik de opdracht `npm install` om de Azure Communication Services Administration-clientbibliotheek voor JavaScript te installeren.

```console

npm install @azure/communication-administration --save

```

Met de optie `--save` wordt de bibliotheek als een afhankelijkheid in bestand **package.json** vermeld.

Importeer de interface voor de `CommunicationIdentityClient` boven in het bestand `index.js`.

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-administration');
```

## <a name="access-token-generation"></a>Toegangstoken genereren

Als u wilt dat uw Azure-functie `User Access Tokens` kan genereren, moet u eerst de verbindingsreeks voor de Communication Services-resource gebruiken.

Ga naar de [quickstart over het inrichten van resources](../../quickstarts/create-communication-resource.md) voor meer informatie over het ophalen van uw verbindingsreeks.

``` javascript
const connectionString = 'INSERT YOUR RESOURCE CONNECTION STRING'
```

U gaat nu uw oorspronkelijke functie aanpassen om `User Access Tokens` te genereren. 

`User Access Tokens` worden gegenereerd door een gebruiker te maken met behulp van de methode `createUser`. Zodra de gebruiker is gemaakt, kan de methode `issueToken` worden gebruikt om een token te genereren voor de gebruiker die door de Azure-functie wordt geretourneerd.

Voor dit voorbeeld wordt het tokenbereik geconfigureerd voor `voip`. Er kunnen andere bereiken nodig zijn voor uw toepassing. Meer informatie over [bereiken](../../quickstarts/access-tokens.md)

```javascript
module.exports = async function (context, req) {
    let tokenClient = new CommunicationIdentityClient(connectionString);

    const user = await tokenClient.createUser();

    const userToken = await tokenClient.issueToken(user, ["voip"]);

    context.res = {
        body: userToken
    };
}
```

Voor bestaande `CommunicationUser` van Communication Services kunt u de stap voor het maken van een gebruiker overslaan en alleen een toegangstoken genereren. Meer informatie vindt u in de [quickstart voor het maken van tokens voor gebruikerstoegang](../../quickstarts/access-tokens.md).

## <a name="test-the-azure-function"></a>De Azure-functie testen

Test de Azure-functie lokaal met behulp van `F5`. Hiermee initialiseert u de Azure-functie lokaal en maakt u deze toegankelijk via: `http://localhost:7071/api/FUNCTION_NAME`. Bekijk aanvullende documentatie over [lokaal uitvoeren](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-locally)

Open de URL in uw browser en controleer of er een antwoordtekst wordt weergeven met de gebruikers-id voor Communication, het token en de vervaltijd van het token.

:::image type="content" source="../media/trusted-service-sample-response.png" alt-text="Schermopname met een voorbeeldantwoord voor de gemaakte Azure-functie.":::

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

Als u uw Azure-functie wilt implementeren, kunt u daarvoor [stapsgewijze instructies volgen](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#sign-in-to-azure)

Doorgaans moet u dan het volgende doen:
1. Meld u vanuit Visual Studio aan bij Azure
2. Publiceer uw project naar uw Azure-account. Hier moet u een bestaand abonnement kiezen.
3. Maak een nieuwe Azure-functieresource met behulp van de Visual Studio-wizard of gebruik een bestaande resource. Als het een nieuwe resource betreft, moet u deze configureren voor de gewenste regio, runtime en uw unieke id.
4. Wacht tot de implementatie is voltooid.
5. Voer de functie 🎉 uit.

## <a name="run-azure-function"></a>Azure-functie uitvoeren

Voer de Azure-functie uit via de URL `http://<function-appn-ame>.azurewebsites.net/api/<function-name>`.

U kunt de URL vinden door in Visual Studio Code met de rechtermuisknop op de functie te klikken en de functie-URL te kopiëren.

Meer informatie over het [uitvoeren van uw Azure-functie](../../../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-javascript#run-the-function-in-azure)
