---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 1032c450ab53ed77771a2233f9cff5677f658123
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612883"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (8.11.1 en 10.14.1 aanbevolen).
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../create-communication-resource.md).

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
```

Voer `npm init -y` uit om een **package.json**-bestand te maken met de standaardinstellingen.

```console
npm init -y
```

### <a name="install-the-package"></a>Het pakket installeren

Gebruik de `npm install` opdracht voor het installeren van de Azure Communication Services Identity client-bibliotheek voor Java script.

```console

npm install @azure/communication-identity --save

```

De optie `--save` geeft de bibliotheek weer als afhankelijkheid in het **package.json**-bestand.

## <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open een nieuw tekstbestand in uw code-editor
1. Voeg een `require` oproep toe om de `CommunicationIdentityClient` te laden
1. Maak de structuur voor het programma, waaronder eenvoudige afhandeling van uitzonderingen

Gebruik de volgende code om te beginnen:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');

const main = async () => {
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered an error");
  console.log(error);
})
```

1. Sla het nieuwe bestand op als **issue-access-token.js** in de map *access-tokens-quickstart*.

## <a name="authenticate-the-client"></a>De client verifiëren

Breng een `CommunicationIdentityClient` tot stand met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code aan de `main` methode toe:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

U kunt ook het eind punt en de toegangs sleutel afzonderlijk scheiden.
```javascript
// This code demonstrates how to fetch your endpoint and access key
// from an environment variable.
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const accessKey = process.env["COMMUNICATION_SERVICES_ACCESSKEY"];
const tokenCredential = new AzureKeyCredential(accessKey);
// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(endpoint, tokenCredential)
```

Als u beheerde identiteit hebt ingesteld, raadpleegt u beheerde identiteiten [gebruiken](../managed-identity.md). u kunt ook verifiëren met beheerde identiteit.
```javascript
const endpoint = process.env["COMMUNICATION_SERVICES_ENDPOINT"];
const tokenCredential = new DefaultAzureCredential();
var client = new CommunicationIdentityClient(endpoint, tokenCredential);
```

## <a name="create-an-identity"></a>Een identiteit maken

Azure Communication Services onderhoudt een lichte identiteitsmap. Gebruik de methode `createUser` om een nieuwe vermelding in de map te maken met een unieke `Id`. Sla de ontvangen identiteit op met een toewijzing aan gebruikers van uw toepassing. U kunt dit bijvoorbeeld doen door ze op te slaan in de database van uw toepassingsserver. De identiteit is later vereist voor het uitgeven van toegangstokens.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="issue-access-tokens"></a>Toegangstokens uitgeven

Gebruik de methode `getToken` om een toegangstoken voor een al bestaande Communication Services-identiteit uit te geven. Met de parameter `scopes` wordt een set primitieven gedefinieerd, waarmee dit toegangstoken wordt geautoriseerd. Raadpleeg de [lijst met ondersteunde acties](../../concepts/authentication.md). Er kan een nieuw exemplaar van de parameter `communicationUser` worden samengesteld op basis van de tekenreeksweergave van de Azure Communication Service-identiteit.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

Toegangstokens zijn kortdurende referenties die opnieuw moeten worden uitgegeven. Als u dit niet doet, kan dit leiden tot onderbrekingen van de gebruikerservaring van uw toepassing. De antwoordeigenschap `expiresOn` geeft de levensduur van het toegangstoken aan.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Een identiteit maken en binnen dezelfde aanvraag een toegangs token uitgeven

Gebruik de `createUserAndToken` methode voor het maken van een communicatie Services-identiteit en het uitgeven van een toegangs token. Met de parameter `scopes` wordt een set primitieven gedefinieerd, waarmee dit toegangstoken wordt geautoriseerd. Raadpleeg de [lijst met ondersteunde acties](../../concepts/authentication.md).

```javascript
// Issue an identity and an access token with the "voip" scope for the new identity
let identityTokenResponse = await this.client.createUserAndToken(["voip"]);
const { token, expiresOn, user } = identityTokenResponse;
console.log(`\nCreated an identity with ID: ${user.communicationUserId}`);
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

## <a name="refresh-access-tokens"></a>Toegangstokens vernieuwen

Toegangstokens vernieuwen komt neer op `getToken` aanroepen met dezelfde identiteit die is gebruikt om de tokens uit te geven. U moet ook de `scopes` voor de vernieuwde tokens opgeven.

```javascript
// // Value of identityResponse represents the Azure Communication Services identity stored during identity creation and then used to issue the tokens being refreshed
let refreshedTokenResponse = await identityClient.getToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>Toegangstokens intrekken

In sommige gevallen kunt u toegangstokens expliciet intrekken. Wanneer de gebruiker van een toepassing bijvoorbeeld het wachtwoord wijzigt dat wordt gebruikt voor verificatie bij uw service. Met de methode `revokeTokens` worden alle actieve toegangstokens die zijn verleend aan de identiteit ongeldig gemaakt.

```javascript
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>Een identiteit verwijderen

Als u een identiteit verwijdert, worden alle actieve toegangstokens ingetrokken en wordt voorkomen dat er toegangstokens voor de identiteit worden uitgegeven. Ook wordt alle persistente inhoud verwijderd die aan de identiteit is gekoppeld.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>De code uitvoeren

Navigeer vanuit een consoleprompt naar de map die het bestand *issue-access-token.js* bevat en voer vervolgens de volgende `node`-opdracht uit om de app uit te voeren.

```console
node ./issue-access-token.js
```
