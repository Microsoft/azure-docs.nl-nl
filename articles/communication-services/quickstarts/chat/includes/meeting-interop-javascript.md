---
title: Quickstart - Deelnemen aan een vergadering in Teams
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 773bca81694534346019e30e9d55190af6f51e74
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106787"
---
## <a name="joining-the-meeting-chat"></a>Deel nemen aan de chat functie van de vergadering 

Zodra teams interoperabiliteit is ingeschakeld, kan een communicatie Services-gebruiker worden toegevoegd als een externe gebruiker via de aanroepende SDK. Als een gebruiker wordt toegevoegd aan de oproep, wordt deze ook deelnemer aan de vergaderchat. Hier kan de gebruiker vervolgens berichten verzenden naar en ontvangen van andere gebruikers in de oproep. De gebruiker heeft geen toegang tot de chatberichten die zijn verzonden vóór deelname aan de oproep. U kunt de volgende stappen volgen om deel te nemen aan de vergadering en te beginnen met chatten.

## <a name="install-the-chat-packages"></a>Chat-pakketten installeren

Gebruik de `npm install` opdracht om de benodigde communicatie Services-sdk's voor Java script te installeren.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

Met de optie `--save` wordt de bibliotheek als een afhankelijkheid in bestand **package.json** vermeld.

## <a name="add-the-teams-ui-controls"></a>De besturingselementen voor de gebruikersinterface van Teams toevoegen

Vervang de code in index.html door het volgende fragment.
De tekst vakken boven aan de pagina worden gebruikt voor het invoeren van de context van teams en de thread-ID van de vergadering. De knop deel nemen aan de vergadering wordt gebruikt om deel te nemen aan de opgegeven vergadering.
Er wordt een pop-upvenster met chatten onder aan de pagina weer gegeven. Het kan worden gebruikt om berichten te verzenden naar de thread van de vergadering en wordt weer gegeven in real-time berichten die worden verzonden via de thread terwijl de ACS-gebruiker lid is.

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

Vervang de inhoud van het client.js bestand door het volgende code fragment.

Vervang in het code fragment 
- `SECRET CONNECTION STRING` met de connection string van de communicatie service 
- `ENDPOINT URL` met de eind punt-URL van uw communicatie service

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

De koppelingen en chatten van teams kunnen worden opgehaald met behulp van Graph-Api's, die worden beschreven in [Graph-documentatie](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta). The aanroepende SDK voor Communication Services accepteert een volledige koppeling naar een Teams-vergadering. Deze koppeling wordt geretourneerd als onderdeel van de `onlineMeeting` resource, toegankelijk via de [ `joinWebUrl` eigenschap](/graph/api/resources/onlinemeeting?view=graph-rest-beta) met de [Graph api's](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta), u kunt ook de `threadId` . Het antwoord heeft een `chatInfo` object dat de bevat `threadID` . 

U kunt ook de vereiste gegevens voor de vergadering en de thread-ID ophalen van de URL voor **deelname aan** de vergadering zelf uitnodigen.
De koppeling naar een Teams-vergadering ziet er ongeveer zo uit: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. De `threadId` gaat `meeting_chat_thread_id` in de koppeling. Zorg ervoor dat de `meeting_chat_thread_id` is ontsnapt vóór gebruik. Deze moet de volgende indeling hebben: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>De code uitvoeren

Webpack-gebruikers kunnen de `webpack-dev-server` gebruiken om uw app te bouwen en uit te voeren. Voer de volgende opdracht uit om de toepassingshost op een lokale webserver te bundelen:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Open uw browser en ga naar http://localhost:8080/. U ziet nu het volgende:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Schermopname van de voltooide JavaScript-toepassing.":::

Plaats de koppeling teams en thread-ID in de tekst vakken. Klik op *deel nemen teams* om deel te nemen aan de vergadering van teams. Nadat de ACS-gebruiker in de vergadering is toegelaten, kunt u chatten vanuit uw communicatie Services-toepassing. Ga naar het vak onder aan de pagina om te beginnen met chatten.

> [!NOTE] 
> Momenteel worden alleen berichten verzonden, ontvangen en bewerkt die worden ondersteund voor interoperabiliteits scenario's met teams. Andere functies zoals het typen van indicatoren en Communication Services-gebruikers die andere gebruikers toevoegen aan of verwijderen uit de Teams-vergadering, worden nog niet ondersteund.  
