---
title: 'Snelstart: VOIP-oproepen toevoegen aan een web-app met behulp van Azure Communication Services'
description: In deze zelfstudie leert u hoe u de Azure Communication Services Calling SDK voor JavaScript gebruikt
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: bd9a607b46c87ca590bcefef310f822d6f23483c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386856"
---
In deze quickstart leert u hoe u een aanroep start met behulp van de Azure Communication Services Calling SDK voor JavaScript.

> [!NOTE]
> In dit document wordt versie 1.0.0-beta.10 van de SDK voor oproepen gebruikt.


## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/), Active LTS en Maintenance LTS-versies (8.11.1 en 10.14.1 aanbevolen).
- Een actieve Communication Services-resource. [Een Communication Services maken](../../create-communication-resource.md).
- Een token voor gebruikerstoegang voor het instantiëren van de client voor oproepen. Meer informatie over [tokens voor gebruikerstoegang maken en beheren](../../access-tokens.md).


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

Hier volgt de code:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Maak een bestand in de hoofdmap van uw project met de naam **client.js** om de toepassingslogica voor deze snelstart te bevatten. Voeg de volgende code toe om de oproepclient te importeren en verwijzingen naar de DOM-elementen op te halen, zodat we onze bedrijfslogica kunnen koppelen. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
let userTokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services Calling SDK:

| Naam                             | Beschrijving                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | De CallClient is het belangrijkste toegangspunt voor de aanroepende SDK.                                                                       |
| CallAgent                        | De CallAgent wordt gebruikt om oproepen te starten en te beheren.                                                                                            |
| AzureCommunicationTokenCredential | De klasse AzureCommunicationTokenCredential implementeert de CommunicationTokenCredential-interface die wordt gebruikt om de CallAgent te instanteren. |


## <a name="authenticate-the-client"></a>De client verifiëren

U moet een geldig token voor gebruikerstoegang voor uw resource invoeren in het tekstveld en op Verzenden klikken. Raadpleeg de documentatie inzake [Token voor gebruikerstoegang](../../access-tokens.md) als u nog geen token hebt. Met behulp van de `CallClient`initialiseert u een `CallAgent`-instantie met een `CommunicationTokenCredential` waarmee we oproepen kunnen doen en ontvangen. Voeg de volgende code toe aan **client.js**:

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>Een oproep starten

Voeg een gebeurtenis-handler toe om een oproep te initiëren wanneer op de `callButton` wordt geklikt:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Een oproep beëindigen

Voeg een gebeurtenis-listener toe om de huidige oproep te beëindigen wanneer op de `hangUpButton` wordt geklikt:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
  submitToken.disabled = false;
});
```

De eigenschap `forEveryone` beëindigt de oproep voor alle deelnemers aan de oproep.

## <a name="run-the-code"></a>De code uitvoeren

Gebruik de `webpack-dev-server` om uw app te bouwen en uit te voeren. Voer de volgende opdracht uit om de toepassingshost op een lokale webserver te bundelen:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Open uw browser en ga naar http://localhost:8080/. U ziet nu het volgende:

:::image type="content" source="../media/javascript/calling-javascript-app-2.png" alt-text="Schermopname van de voltooide JavaScript-toepassing.":::

U kunt een uitgaande VOIP-aanroep maken door een geldig token voor gebruikerstoegang en gebruikers-id op te geven in de bijbehorende tekstvelden en op de **knop Oproep** starten te klikken. Door `8:echo123` te bellen, wordt u verbonden met een echo-bot. Dit is handig om aan de slag te gaan en te controleren of uw audio-apparaten werken.

## <a name="sample-code"></a>Voorbeeldcode

U kunt de voorbeeld-app downloaden van [Github](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-voice-calling).
