---
title: Quickstart - Deelnemen aan een vergadering in Teams
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: fa7f279e13e5dbb59af7eb3a7939be1edf0f7d5d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552221"
---
## <a name="prerequisites"></a>Vereisten

- Een werkende [App voor bellen met Communication Services](../getting-started-with-calling.md).
- Een [Teams implementatie](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>De besturingselementen voor de gebruikersinterface van Teams toevoegen

Vervang code in index. html door het volgende fragment.
Het tekstvak wordt gebruikt voor het invoeren van de context van de Teams-vergadering en de knop wordt gebruikt om deel te nemen aan de opgegeven vergadering:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>De besturingselementen voor de gebruikersinterface van Teams inschakelen

Vervang inhoud in het bestand client.js door het volgende fragment.

```javascript
import { CallClient } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Koppeling naar de Teams-vergadering ophalen

De koppeling naar de Teams-vergadering kan worden opgehaald met behulp van Graph API’s. Dit wordt beschreven in [Graph-documentatie](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
The aanroepende SDK voor Communication Services accepteert een volledige koppeling naar een Teams-vergadering. Deze koppeling wordt geretourneerd als onderdeel van de `onlineMeeting`-resource, die toegankelijk is bij de [eigenschap `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta). U kunt de vereiste vergaderingsgegevens ook ophalen via de **URL** in de uitnodiging voor de Teams-vergadering zelf.

## <a name="run-the-code"></a>De code uitvoeren

Webpack-gebruikers kunnen de `webpack-dev-server` gebruiken om uw app te bouwen en uit te voeren. Voer de volgende opdracht uit om de toepassingshost op een lokale webserver te bundelen:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Open uw browser en ga naar http://localhost:8080/. U ziet nu het volgende:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Schermopname van de voltooide JavaScript-toepassing.":::

Voeg de context van Teams in het tekstvak in en druk op *Deelnemen aan Teams-vergadering* om deel te nemen aan de Teams-vergadering vanuit uw Communication Services-toepassing.
