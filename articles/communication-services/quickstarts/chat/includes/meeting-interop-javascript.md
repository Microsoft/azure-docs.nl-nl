---
title: Quickstart - Deelnemen aan een vergadering in Teams
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 780ef2bbb7851d8bef5fc52a51421a7938043ecb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932305"
---
## <a name="join-the-meeting-chat"></a>Deelnemen aan de vergaderchat 

Zodra Teams-interoperabiliteit is ingeschakeld, kan een Communication Services-gebruiker als gastgebruiker deelnemen aan de Teams-oproep met behulp van de aanroepende clientbibliotheek. Als een gebruiker wordt toegevoegd aan de oproep, wordt deze ook deelnemer aan de vergaderchat. Hier kan de gebruiker vervolgens berichten verzenden naar en ontvangen van andere gebruikers in de oproep. De gebruiker heeft geen toegang tot de chatberichten die zijn verzonden vóór deelname aan de oproep. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Een chatgesprek in een Teams-vergadering ophalen voor een Communication Services-gebruiker

Instantieer eerst een `ChatThreadClient` voor het vergaderchatgesprek. Parseer de koppeling naar de vergadering of gebruik de Graph API's met de vergaderings-id om de gespreks-id op te halen. 

- De koppeling naar een Teams-vergadering ziet er ongeveer zo uit: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. De gespreks-id bevindt zich in deze koppeling op de plek van `meeting_chat_thread_id`. 
- Als u de vergaderings-id hebt, kunt u [Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) gebruiken om de gespreks-id op te halen. Het [GET API](/graph/api/onlinemeeting-get?tabs=http%22+%5c&view=graph-rest-beta)-antwoord heeft een `chatInfo`-object dat de `threadID` bevat. 

Zodra u beschikt over de chatgespreks-id, kunt u de chatgesprekclient ophalen met behulp van de JavaScript-chatclientbibliotheek: 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
De `chatThreadClient` kan worden gebruikt om leden in het chatgesprek te vermelden, de chatgeschiedenis op te halen, en berichten te verzenden.  

## <a name="send-and-receive-messages"></a>Berichten verzenden en ontvangen  

Gebruik `SendMessage` om een bericht te verzenden naar de vergaderchat. De mogelijkheid om te abonneren op gebeurtenissen zoals `chatMessageReceived` wordt niet ondersteund voor het ontvangen van binnenkomende berichten, omdat signalering in realtime nog niet is ingeschakeld voor dit scenario. Als u de meest recente berichten wilt ophalen, kunt u de `ListMessages` API pollen. Voor het interoperabiliteitsscenario biedt de API `ListMessages` nu ondersteuning voor het retourneren van drie nieuwe berichttypen:
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

Kijk [hier](../../../concepts/chat/concepts.md) voor meer informatie over berichttypen. 

**Opmerking**: momenteel wordt het verzenden en ontvangen van berichten alleen ondersteund voor interoperabiliteitsscenario's met Teams. Andere functies zoals het typen van indicatoren en Communication Services-gebruikers die andere gebruikers toevoegen aan of verwijderen uit de Teams-vergadering, worden nog niet ondersteund.  

