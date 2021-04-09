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
ms.openlocfilehash: 4c8bd66dde54ff90ea2191fba58f10c87c45cf68
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958367"
---
## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u het volgende doen:
- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie.
- [Visual Studio](https://visualstudio.microsoft.com/downloads/) installeren
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../../create-communication-resource.md) voor meer informatie. Voor deze quickstart moet u het **eindpunt** van uw resource vastleggen.
- Een [toegangstoken voor gebruikers](../../access-tokens.md). Zorg ervoor dat u het bereik instelt op ‘chat’ en noteer de tokenreeks en ook de gebruikersId-reeks.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `ChatQuickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: **Program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Wijzig uw map in de zojuist gemaakte app-map en gebruik de opdracht `dotnet build` om uw toepassing te compileren.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Het pakket installeren

De Azure Communication chat SDK voor .NET installeren

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0
```

## <a name="object-model"></a>Objectmodel

De volgende klassen behandelen enkele van de belangrijkste functies van de Azure Communication Services chat SDK voor C#.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Deze klasse is vereist voor de chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, krijgen en verzenden. |
| ChatThreadClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden/ontvangen/bijwerken, deel nemers toe te voegen/te verwijderen/te verwijderen, te verzenden en berichten te lezen. |

## <a name="create-a-chat-client"></a>Een chat-client maken

Als u een chat-client wilt maken, gebruikt u uw communicatie Services-eind punt en het toegangs token dat is gegenereerd als onderdeel van de vereiste stappen. U moet de `CommunicationIdentityClient` klasse van de identiteits-SDK gebruiken om een gebruiker te maken en een token uit te geven om door te geven aan uw chat-client.

Meer informatie over [tokens voor gebruikerstoegang](../../access-tokens.md).

Deze Snelstartgids heeft geen betrekking op het maken van een servicelaag voor het beheren van tokens voor uw chat toepassing, hoewel dit wordt aanbevolen. Meer informatie over de [architectuur van chatten](../../../concepts/chat/concepts.md)

Kopieer de volgende code fragmenten en plak deze in het bron bestand: **Program. cs**
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Een chat-thread starten

Gebruik de- `createChatThread` methode op de chatClient om een chat-thread te maken
- Gebruik `topic` om een onderwerp te geven aan deze chat. Het onderwerp kan worden bijgewerkt nadat de chat-thread is gemaakt met behulp van de functie `UpdateTopic`.
- Gebruik de eigenschap `participants` om een lijst met `ChatParticipant`-objecten door te geven om aan de chat-thread toe te voegen. Het `ChatParticipant`-object wordt geïnitialiseerd met een `CommunicationIdentifier`-object. `CommunicationIdentifier` kan van het type `CommunicationUserIdentifier` `MicrosoftTeamsUserIdentifier` of zijn `PhoneNumberIdentifier` . Als u bijvoorbeeld een object wilt ophalen `CommunicationIdentifier` , moet u een toegangs-id door geven die u hebt gemaakt met behulp van de volgende instructie voor [het maken van een gebruiker](../../access-tokens.md#create-an-identity)

Het antwoord object van de `createChatThread` methode bevat de `chatThread` Details. Voor interactie met de bewerkingen van de chat-thread, zoals het toevoegen van deel nemers, het verzenden van een bericht, het verwijderen van een bericht, enzovoort, `chatThreadClient` moet een client exemplaar worden geïnstantieerd met behulp `GetChatThreadClient` van de methode op de `ChatClient` client.

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen
De methode `GetChatThreadClient` retourneert een thread-client voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: leden toevoegen, bericht verzenden, enz. threadId is de unieke id van de bestaande chat-thread.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>Alle chat-threads weer geven
Gebruik `GetChatThreads` om alle chat-threads op te halen waarvan de gebruiker deel uitmaakt.

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruiken `SendMessage` om een bericht naar een thread te verzenden.

- Gebruik `content` om de inhoud van het bericht op te geven. Dit is verplicht.
- Wordt gebruikt `type` voor het inhouds type van het bericht, zoals ' text ' of ' HTML '. Als u niets opgeeft, wordt ' text ' ingesteld.
- Gebruik `senderDisplayName` om de weergavenaam van de afzender te geven. Als u niets opgeeft, wordt er een lege teken reeks ingesteld.

```csharp
SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Chatberichten ontvangen van een chat-thread

U kunt chatberichten ophalen door de methode `GetMessages` op de chat-thread-client op bepaalde intervallen te pollen.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` maakt gebruik van een optionele `DateTimeOffset`-parameter. Als die offset wordt opgegeven, ontvangt u berichten die daarna zijn ontvangen, bijgewerkt of verwijderd. Berichten die vóór de offset-tijd zijn ontvangen maar daarna zijn bewerkt of verwijderd, worden ook geretourneerd.

`GetMessages` retourneert de meest recente versie van het bericht, inclusief eventuele bewerkingen of verwijderingen die zijn opgetreden in het bericht met `UpdateMessage` en `DeleteMessage`. Voor verwijderde berichten retourneert `chatMessage.DeletedOn` een datum/tijd-waarde die aangeeft wanneer dat bericht is verwijderd. Voor bewerkte berichten retourneert `chatMessage.EditedOn` een datum/tijd die aangeeft wanneer het bericht is bewerkt. De oorspronkelijke tijd van het maken van het bericht kan worden geopend met `chatMessage.CreatedOn` en kan worden gebruikt voor het bestellen van de berichten.

`GetMessages` retourneert verschillende typen berichten die kunnen worden geïdentificeerd door `chatMessage.Type`. Deze typen zijn:

- `Text`: Normaal chatbericht verzonden door een thread-lid.

- `Html`: Een opgemaakt tekst bericht. Houd er rekening mee dat Communication Services-gebruikers momenteel geen RTF-berichten kunnen verzenden. Dit berichttype wordt ondersteund voor berichten die Teams-gebruikers verzenden naar Communication Services-gebruikers in Teams Interop-scenario's.

- `TopicUpdated`: Systeembericht dat aangeeft dat het onderwerp is bijgewerkt. kenmerk

- `ParticipantAdded`: Systeem bericht dat aangeeft dat een of meer deel nemers zijn toegevoegd aan de chat thread. kenmerk

- `ParticipantRemoved`: Systeem bericht dat aangeeft dat een deel nemer is verwijderd uit de chat thread.

Zie [Berichttypen](../../../concepts/chat/concepts.md#message-types)voor meer informatie.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Een gebruiker toevoegen als deel nemer aan de chat thread

Zodra u een thread hebt gemaakt, kunt u gebruikers toevoegen en verwijderen. Door gebruikers toe te voegen, geeft u hen toegang tot het verzenden van berichten naar de thread en toevoegen/verwijderen van andere deel nemers. Voordat u `AddParticipants` aanroept, moet u ervoor zorgen dat u een nieuw toegangstoken en een nieuwe identiteit hebt verkregen voor die gebruiker. De gebruiker heeft dat toegangstoken nodig om zijn chat-client te initialiseren.

Gebruik `AddParticipants` om een of meer deel nemers aan de chat-thread toe te voegen. Hieronder vindt u de ondersteunde kenmerken voor elke thread deelnemer (s):
- `communicationUser`, vereist, is de identiteit van de deel nemer aan de thread.
- `displayName`, optioneel, is de weergave naam voor de deel nemer aan de thread.
- `shareHistoryTime`, optioneel, de tijd waarop de chat geschiedenis wordt gedeeld met de deel nemer.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>Thread deelnemers ophalen

Gebruiken `GetParticipants` om de deel nemers van de chat-thread op te halen.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>Lees bevestiging verzenden

Gebruiken `SendReadReceipt` om andere deel nemers op de hoogte te stellen dat het bericht door de gebruiker is gelezen.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```console
dotnet run
```
