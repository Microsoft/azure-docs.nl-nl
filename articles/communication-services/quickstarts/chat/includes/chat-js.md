---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 322f54e4fa2e8096f68d5bbc216032a5b4e53c22
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726698"
---
## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u het volgende doen:

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie.
- Installeer [Node.js](https://nodejs.org/en/download/) actieve LTS en onderhouds LTS-versies.
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../../create-communication-resource.md) voor meer informatie. U moet **uw resource-eind punt vastleggen** voor deze Quick Start.
- Maak *drie* ACS-gebruikers en geef ze een toegangs [token](../../access-tokens.md)voor gebruikers toegangs token. Zorg ervoor dat u het bereik instelt op **Chat** en **Noteer de token teken reeks, evenals de teken reeks GebruikersID**. In de volledige demo wordt een thread met twee eerste deel nemers gemaakt en vervolgens een derde deel nemer aan de thread toegevoegd.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-web-application"></a>Een nieuwe webtoepassing maken

Open eerst uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daarnaartoe.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Voer `npm init -y` uit om een **package.json**-bestand te maken met de standaardinstellingen.

```console
npm init -y
```

### <a name="install-the-packages"></a>De pakketten installeren

Gebruik de `npm install` opdracht om de onderstaande sdk's voor communicatie Services voor Java script te installeren.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

De optie `--save` geeft de bibliotheek weer als afhankelijkheid in het **package.json**-bestand.

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In deze snelstart wordt webpack gebruikt om de toepassingsassets te bundelen. Voer de volgende opdracht uit om de webpack-, webpack-CLI- en webpack-dev-server npm-pakketten te installeren en deze weer te geven als ontwikkelingsafhankelijkheden in uw **package.json**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Maak een `webpack.config.js` bestand in de hoofdmap. Kopieer de volgende configuratie naar dit bestand:

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

Een `start` script toevoegen aan uw `package.json` , we gebruiken dit voor het uitvoeren van de app. In de `scripts` sectie van `package.json` het volgende toevoegen:

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

Maak een **index. html**-bestand in de hoofdmap van uw project. Dit bestand wordt gebruikt als sjabloon voor het toevoegen van chat mogelijkheden met behulp van de Azure Communication chat SDK voor Java script.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Maak een bestand in de hoofdmap van uw project met de naam **client.js** om de toepassingslogica voor deze quickstart te bevatten.

### <a name="create-a-chat-client"></a>Een chat-client maken

Als u een chat-client in uw web-app wilt maken, gebruikt u het **eind punt** van de communicatie service en het **toegangs token** dat is gegenereerd als onderdeel van de vereiste stappen.

Met toegangstokens voor gebruikers kunt u clienttoepassingen maken die zich rechtstreeks verifiëren bij Azure Communication Services. Deze Snelstartgids heeft geen betrekking op het maken van een servicelaag voor het beheren van tokens voor uw chat toepassing. Zie [Chat concepten](../../../concepts/chat/concepts.md) voor meer informatie over de chat architectuur en [gebruikers toegangs tokens](../../access-tokens.md) voor meer informatie over toegangs tokens.

In **client.js** het endpoint-en toegangs token in de onderstaande code gebruiken om chat mogelijkheden toe te voegen met de Azure Communication chat SDK voor Java script.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Vervang **endpointUrl** door het resource-eind punt van de communicatie Services, Zie [een Azure-communicatie resource maken](../../create-communication-resource.md) als u dit nog niet hebt gedaan.
- Vervang **userAccessToken** door het token dat u hebt uitgegeven.


### <a name="run-the-code"></a>De code uitvoeren

Voer de volgende opdracht uit om de toepassingshost op een lokale webserver te bundelen:
```console
npm run start
```
Open uw browser en ga naar http://localhost:8080/.
In de console voor ontwikkelaarshulpprogramma’s in uw browser zou u het volgende moeten zien:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Objectmodel
De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services chat SDK voor Java script.

| Naam                                   | Beschrijving                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Deze klasse is vereist voor de chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, krijgen en verzenden. |
| ChatThreadClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden, te ontvangen, bij te werken of te verwijderen, gebruikers toe te voegen, te verwijderen of op te halen, getypte meldingen te verzenden, bevestigingen te lezen en u op chatgebeurtenissen te abonneren. |


## <a name="start-a-chat-thread"></a>Een chat-thread starten

Gebruik de methode `createThread` om een chat-thread te maken.

`createThreadRequest` wordt gebruikt om de thread-aanvraag te beschrijven:

- Gebruiken `topic` om een onderwerp te geven aan deze chat sessie. Onderwerpen kunnen worden bijgewerkt nadat de chat thread is gemaakt met behulp van de `UpdateThread` functie.
- Gebruik `participants` om de deel nemers weer te geven die moeten worden toegevoegd aan de chat-thread.

Indien opgelost, `createChatThread` retourneert methode een `CreateChatThreadResult` . Dit model bevat een `chatThread` eigenschap waar u toegang hebt tot de `id` van de zojuist gemaakte thread. Vervolgens kunt u de gebruiken `id` om een exemplaar van een te verkrijgen `ChatThreadClient` . De `ChatThreadClient` kan vervolgens worden gebruikt om de bewerking uit te voeren binnen de thread, zoals het verzenden van berichten of het weer geven van deel nemers.

```JavaScript
async function createChatThread() {
  const createChatThreadRequest = {
    topic: "Hello, World!"
  };
  const createChatThreadOptions = {
    participants: [
      {
        id: '<USER_ID>',
        displayName: '<USER_DISPLAY_NAME>'
      }
    ]
  };
  const createChatTtreadResult = await chatClient.createChatThread(
    createChatThreadRequest,
    createChatThreadOptions
  );
  const threadId = createChatThreadResult.chatThread.id;
  return threadId;
}

createChatThread().then(async threadId => {
  console.log(`Thread created:${threadId}`);
  // PLACEHOLDERS
  // <CREATE CHAT THREAD CLIENT>
  // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
  // <SEND MESSAGE TO A CHAT THREAD>
  // <LIST MESSAGES IN A CHAT THREAD>
  // <ADD NEW PARTICIPANT TO THREAD>
  // <LIST PARTICIPANTS IN A THREAD>
  // <REMOVE PARTICIPANT FROM THREAD>
  });
```

Wanneer u het browser tabblad vernieuwt, ziet u het volgende in de-console:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen

De methode `getChatThreadClient` retourneert een `chatThreadClient` voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: deel nemers toevoegen, berichten verzenden, enzovoort thread is de unieke ID van de bestaande chat-thread.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Voeg deze code toe in plaats van de opmerking `<CREATE CHAT THREAD CLIENT>` in **client.js**, vernieuw uw browsertabblad en controleer de console, waar u het volgende zou moeten zien:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="list-all-chat-threads"></a>Alle chat-threads weer geven

De `listChatThreads` methode retourneert een `PagedAsyncIterableIterator` van het type `ChatThreadItem` . Het kan worden gebruikt voor het weer geven van alle chat-threads.
Een iterator van `[ChatThreadItem]` is het antwoord dat wordt geretourneerd door een lijst met threads

```JavaScript
const threads = chatClient.listChatThreads();
for await (const thread of threads) {
   // your code here
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruik `sendMessage` methode om een bericht te verzenden naar een thread die wordt geïdentificeerd door thread.

`sendMessageRequest` wordt gebruikt om de bericht aanvraag te beschrijven:

- Gebruik `content` om de inhoud van het chatbericht op te geven;

`sendMessageOptions` wordt gebruikt voor het beschrijven van de bewerking optionele para meters:

- Gebruik `senderDisplayName` om de weergavenaam van de afzender op te geven.
- Gebruiken `type` om het bericht type op te geven, zoals tekst of HTML;

`SendChatMessageResult` is het antwoord dat wordt geretourneerd door het verzenden van een bericht, bevat het een ID. Dit is de unieke ID van het bericht.

```JavaScript
const sendMessageRequest =
{
  content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
  senderDisplayName : 'Jack',
  type: 'text'
};
const sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
const messageId = sendChatMessageResult.id;
```

Voeg deze code toe in plaats van de opmerking `<SEND MESSAGE TO A CHAT THREAD>` in **client.js**, vernieuw uw browsertabblad en controleer de console.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Chatberichten ontvangen van een chat-thread

Met realtime signalering kunt u zich abonneren om te luisteren naar nieuwe inkomende berichten en de huidige berichten dienovereenkomstig bij te werken in het geheugen. Azure Communication Services ondersteunt een [lijst met gebeurtenissen waarop u zich kunt abonneren](../../../concepts/chat/concepts.md#real-time-notifications).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
  console.log("Notification chatMessageReceived!");
  // your code here
});

```
Voeg deze code toe in plaats van de opmerking `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` in **client.js**.
Wanneer u uw browsertabblad vernieuwt, zou u in de console een bericht `Notification chatMessageReceived` moeten zien.

U kunt chatberichten ook ophalen door de methode `listMessages` op opgegeven intervallen te pollen.

```JavaScript

const messages = chatThreadClient.listMessages();
for await (const message of messages) {
   // your code here
}

```
Voeg deze code toe in plaats van de opmerking `<LIST MESSAGES IN A CHAT THREAD>` in **client.js**.
Vernieuw uw tabblad, in de-console, vindt u de lijst met berichten die in deze chat-thread worden verzonden.

`listMessages` retourneert verschillende typen berichten die kunnen worden geïdentificeerd door `chatMessage.type`. 

Zie [Berichttypen](../../../concepts/chat/concepts.md#message-types)voor meer informatie.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Een gebruiker toevoegen als deel nemer aan de chat thread

Zodra u een chat-thread hebt gemaakt, kunt u gebruikers toevoegen en verwijderen. Door gebruikers toe te voegen, kunt u hen toegang geven tot het verzenden van berichten naar de chat-thread en andere deel nemers toevoegen/verwijderen.

Voordat u de `addParticipants` -methode aanroept, moet u ervoor zorgen dat u een nieuw toegangs token en een nieuwe identiteit hebt verkregen voor die gebruiker. De gebruiker heeft dat toegangstoken nodig om zijn chat-client te initialiseren.

`addParticipantsRequest` Hierin wordt het object aanvraag beschreven `participants` waarin de deel nemers worden toegevoegd aan de chat-thread.
- `id`, vereist, is de communicatie-id die moet worden toegevoegd aan de chat-thread.
- `displayName`, optioneel, is de weergave naam voor de deel nemer aan de thread.
- `shareHistoryTime`, optioneel, is de tijd waarop de chat geschiedenis wordt gedeeld met de deel nemer. Als u de geschiedenis wilt delen sinds het begin van de chat-thread, stelt u deze eigenschap in op een willekeurige datum die gelijk is aan of kleiner is dan de aanmaaktijd van de thread. Als u geen geschiedenis wilt delen vóór wanneer de deel nemer is toegevoegd, stelt u deze in op de huidige datum. Als u een deel van de geschiedenis wilt delen, stelt u de eigenschap in op de gewenste datum.

```JavaScript

const addParticipantsRequest =
{
  participants: [
    {
      id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
      displayName: 'Jane'
    }
  ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
**NEW_PARTICIPANT_USER_ID** vervangen door een [nieuwe gebruikers-id](../../access-tokens.md) Voeg deze code toe in plaats van de `<ADD NEW PARTICIPANT TO THREAD>` Opmerking in **client.js**

## <a name="list-users-in-a-chat-thread"></a>Gebruikers in een chat-thread weergeven
```JavaScript
const participants = chatThreadClient.listParticipants();
for await (const participant of participants) {
   // your code here
}
```
Voeg deze code toe in plaats van de opmerking `<LIST PARTICIPANTS IN A THREAD>` in **client.js**, vernieuw uw browsertabblad en controleer de console, waar u informatie over gebruikers in een thread zou moeten zien.

## <a name="remove-user-from-a-chat-thread"></a>Gebruiker verwijderen uit een chat-thread

Net als bij het toevoegen van een deel nemer, kunt u deel nemers uit een chat-thread verwijderen. Als u wilt verwijderen, moet u de Id's bijhouden van de deel nemers die u hebt toegevoegd.

Gebruik de methode `removeParticipant`, waarbij `participant` de communicatiegebruiker is die uit de thread moet worden verwijderd.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Vervang **PARTICIPANT_ID** door een gebruikers-id die is gebruikt in de vorige stap (<NEW_PARTICIPANT_USER_ID>).
Voeg deze code toe in plaats van de opmerking `<REMOVE PARTICIPANT FROM THREAD>` in **client.js**.
