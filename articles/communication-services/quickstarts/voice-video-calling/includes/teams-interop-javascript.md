---
title: 'Quickstart: deelnemen aan een Teams-vergadering vanuit een web-app'
description: In deze zelfstudie leert u hoe u deel kunt nemen aan een Teams-vergadering met behulp van de Azure Communication Services Calling SDK voor JavaScript
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6747d1d3cfba1c9e2bee7a8a7a48d67d6bed9f8e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564633"
---
In deze quickstart leert u hoe u deel kunt nemen aan een Teams-vergadering met behulp van de Azure Communication Services Calling SDK voor JavaScript.

## <a name="prerequisites"></a>Vereisten

- Een werkende Communication Services [aanroepen van web-app](../getting-started-with-calling.md).
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
        <p><span style="font-weight: bold" id="recording-state"></span></p>
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
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

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
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>Koppeling naar de Teams-vergadering ophalen

De koppeling naar de Teams-vergadering kan worden opgehaald met behulp van Graph API’s. Dit wordt beschreven in [Graph-documentatie](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
The aanroepende SDK voor Communication Services accepteert een volledige koppeling naar een Teams-vergadering. Deze koppeling wordt geretourneerd als onderdeel van de `onlineMeeting`-resource, die toegankelijk is bij de [eigenschap `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). U kunt de vereiste vergaderingsgegevens ook ophalen via de **URL** in de uitnodiging voor de Teams-vergadering zelf.

## <a name="run-the-code"></a>De code uitvoeren

Webpack-gebruikers kunnen de `webpack-dev-server` gebruiken om uw app te bouwen en uit te voeren. Voer de volgende opdracht uit om de toepassingshost op een lokale webserver te bundelen:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Open uw browser en ga naar http://localhost:8080/. U ziet nu het volgende:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Schermopname van de voltooide JavaScript-toepassing.":::

Voeg de context van Teams in het tekstvak in en druk op *Deelnemen aan Teams-vergadering* om deel te nemen aan de Teams-vergadering vanuit uw Communication Services-toepassing.
