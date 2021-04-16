---
title: Quickstart - Deelnemen aan een vergadering in Teams
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 49f9bac40ae803f980a22c19fd5d44d85fa99e9e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564493"
---
## <a name="joining-the-meeting-chat"></a>Deelnemen aan de vergaderingschat 

Zodra De interoperabiliteit van Teams is ingeschakeld, kan Communication Services lid worden van de Teams-aanroep als een externe gebruiker met behulp van de aanroepen van de SDK. Als een gebruiker wordt toegevoegd aan de oproep, wordt deze ook deelnemer aan de vergaderchat. Hier kan de gebruiker vervolgens berichten verzenden naar en ontvangen van andere gebruikers in de oproep. De gebruiker heeft geen toegang tot de chatberichten die zijn verzonden vóór deelname aan de oproep. Als u wilt deelnemen aan de vergadering en wilt beginnen met chatten, kunt u de volgende stappen volgen.

## <a name="install-the-chat-packages"></a>De chatpakketten installeren

Gebruik de `npm install` opdracht om de benodigde SDK Communication Services voor JavaScript te installeren.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

Met de optie `--save` wordt de bibliotheek als een afhankelijkheid in bestand **package.json** vermeld.

## <a name="add-the-teams-ui-controls"></a>De besturingselementen voor de gebruikersinterface van Teams toevoegen

Vervang de code in index.html door het volgende codefragment.
De tekstvakken boven aan de pagina worden gebruikt om de context van de Teams-vergadering en de thread-id van de vergadering in te voeren. De knop Deelnemen aan teamsvergadering wordt gebruikt om deel te nemen aan de opgegeven vergadering.
Onder aan de pagina wordt een chatpop-up weergegeven. Het kan worden gebruikt om berichten te verzenden op de vergaderingsthread en alle berichten die op de thread worden verzonden terwijl de ACS-gebruiker lid is, worden in realtime weergegeven.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>De besturingselementen voor de gebruikersinterface van Teams inschakelen

Vervang de inhoud van het client.js bestand door het volgende codefragment.

Vervang in het codefragment 
- `SECRET CONNECTION STRING` met de connection string van uw Communication Service 
- `ENDPOINT URL` door de eindpunt-URL van uw Communication Service

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Een chatgesprek in een Teams-vergadering ophalen voor een Communication Services-gebruiker

De koppeling en chat van de Teams-vergadering kunnen worden opgehaald met behulp van Graph API's, zoals beschreven in [graph-documentatie.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) The aanroepende SDK voor Communication Services accepteert een volledige koppeling naar een Teams-vergadering. Deze koppeling wordt geretourneerd als onderdeel van de resource, toegankelijk onder de eigenschap Met de `onlineMeeting` [Graph API's](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)kunt u ook de [ `joinWebUrl` ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) `threadId` verkrijgen. Het antwoord heeft een `chatInfo` -object dat de `threadID` bevat. 

U kunt ook de vereiste informatie over de vergadering en de thread-id verkrijgen via de URL van **de join-vergadering** in de teamsvergadering uitnodiging zelf.
De koppeling naar een Teams-vergadering ziet er ongeveer zo uit: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. De `threadId` is waar zich in de `meeting_chat_thread_id` koppeling. Zorg ervoor dat `meeting_chat_thread_id` de niet is opgeslagen voordat u deze gebruikt. Deze moet de volgende indeling hebben: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>De code uitvoeren

Webpack-gebruikers kunnen de `webpack-dev-server` gebruiken om uw app te bouwen en uit te voeren. Voer de volgende opdracht uit om de toepassingshost op een lokale webserver te bundelen:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Open uw browser en ga naar http://localhost:8080/. U ziet nu het volgende:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Schermopname van de voltooide JavaScript-toepassing.":::

Voeg de koppeling Teams meeting en thread-ID in de tekstvakken in. Druk *op Deelnemen aan Teams-vergadering om* deel te nemen aan de Teams-vergadering. Nadat de ACS-gebruiker is opgenomen in de vergadering, kunt u chatten vanuit uw Communication Services toepassing. Navigeer naar het vak onder aan de pagina om de chat te starten.

> [!NOTE] 
> Momenteel wordt alleen het verzenden, ontvangen en bewerken van berichten ondersteund voor interoperabiliteitsscenario's met Teams. Andere functies zoals het typen van indicatoren en Communication Services-gebruikers die andere gebruikers toevoegen aan of verwijderen uit de Teams-vergadering, worden nog niet ondersteund.  
