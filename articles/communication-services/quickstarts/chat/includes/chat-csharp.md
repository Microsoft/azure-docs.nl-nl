---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 1db7eeb61bc4ded2d7015baecaacd974d7767812
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653513"
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

Wijzig uw directory in de zojuist gemaakte app-map en gebruik de opdracht `dotnet build` om uw toepassing te compileren.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Het pakket installeren

De Azure Communication chat-clientbibliotheek installeren voor .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.4
``` 

## <a name="object-model"></a>Objectmodel

De volgende klassen verwerken enkele van de belangrijkste functies van de Azure Communication Services chat-clientbibliotheek voor C#.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Deze klasse is vereist voor de chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, op te halen en te verwijderen. |
| ChatThreadClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden/ontvangen/bijwerken, deel nemers toe te voegen/te verwijderen/te verwijderen, te verzenden en berichten te lezen. |

## <a name="create-a-chat-client"></a>Een chat-client maken

Als u een chat-client wilt maken, gebruikt u uw communicatie Services-eind punt en het toegangs token dat is gegenereerd als onderdeel van de vereiste stappen. U moet de klasse `CommunicationIdentityClient` uit de clientbibliotheek `Administration` gebruiken om een gebruiker te maken en een token uit te geven om aan uw chat-client door te geven.

Meer informatie over [toegangstokens voor gebruikers](../../access-tokens.md).

Deze Snelstartgids heeft geen betrekking op het maken van een servicelaag voor het beheren van tokens voor uw chat toepassing, hoewel dit wordt aanbevolen. Meer informatie over de [architectuur van chatten](../../../concepts/chat/concepts.md)

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
```

## <a name="start-a-chat-thread"></a>Een chat-thread starten

Gebruik de- `createChatThread` methode op de chatClient om een chat-thread te maken
- Gebruik `topic` om een onderwerp te geven aan deze chat. Het onderwerp kan worden bijgewerkt nadat de chat-thread is gemaakt met behulp van de functie `UpdateTopic`.
- Gebruik de eigenschap `participants` om een lijst met `ChatParticipant`-objecten door te geven om aan de chat-thread toe te voegen. Het `ChatParticipant`-object wordt geïnitialiseerd met een `CommunicationIdentifier`-object. `CommunicationIdentifier` kan van het type `CommunicationUserIdentifier` `MicrosoftTeamsUserIdentifier` of zijn `PhoneNumberIdentifier` . Als u bijvoorbeeld een object wilt ophalen `CommunicationIdentifier` , moet u een toegangs-id door geven die u hebt gemaakt met behulp van de volgende instructie voor [het maken van een gebruiker](../../access-tokens.md#create-an-identity)

Het antwoord object van de methode createChatThread bevat de chatThread-gegevens. Als u wilt communiceren met de bewerkingen van de chat-thread, zoals het toevoegen van deel nemers, het verzenden van een bericht, het verwijderen van een bericht, enzovoort, moet een chatThreadClient-client exemplaar worden geïnstantieerd met de methode GetChatThreadClient op de ChatClient-client. 

```csharp
var chatParticipant = new ChatParticipant(communicationIdentifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen
De methode `GetChatThreadClient` retourneert een thread-client voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: leden toevoegen, bericht verzenden, enz. threadId is de unieke id van de bestaande chat-thread.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruiken `SendMessage` om een bericht naar een thread te verzenden.

- Gebruik `content` om de inhoud van het bericht op te geven. Dit is verplicht.
- Wordt gebruikt `type` voor het inhouds type van het bericht, zoals ' text ' of ' HTML '. Als u niets opgeeft, wordt ' text ' ingesteld.
- Gebruik `senderDisplayName` om de weergavenaam van de afzender te geven. Als u niets opgeeft, wordt er een lege teken reeks ingesteld.

```csharp
var messageId = await chatThreadClient.SendMessageAsync(content:"hello world", type: );
```
## <a name="get-a-message"></a>Een bericht ontvangen

Gebruiken `GetMessage` om een bericht uit de service op te halen.
`messageId` is de unieke ID van het bericht.

`ChatMessage` is het antwoord dat het resultaat is van het ophalen van een bericht, het bevat een ID, de unieke id van het bericht, onder andere velden. Ga naar Azure. Communication. chat. ChatMessage

```csharp
ChatMessage chatMessage = await chatThreadClient.GetMessageAsync(messageId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Chatberichten ontvangen van een chat-thread

U kunt chatberichten ophalen door de methode `GetMessages` op de chat-thread-client op bepaalde intervallen te pollen.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
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

Zie [Berichttypen](../../../concepts/chat/concepts.md#message-types) voor meer informatie.

## <a name="update-a-message"></a>Een bericht bijwerken

U kunt een bericht dat al is verzonden, bijwerken door `UpdateMessage` aan te roepen op `ChatThreadClient`.

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Een bericht verwijderen

U kunt een bericht verwijderen door `DeleteMessage` aan te roepen op `ChatThreadClient`.

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

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

await chatThreadClient.AddParticipantsAsync(participants);
```
## <a name="remove-user-from-a-chat-thread"></a>Gebruiker verwijderen uit een chat-thread

Net als bij het toevoegen van een gebruiker aan een thread, kunt u gebruikers uit een chat-thread verwijderen. Hiervoor moet u de identiteit bijhouden `CommunicationUser` van de deel nemer die u hebt toegevoegd.

```csharp
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
await chatThreadClient.RemoveParticipantAsync(gloria);
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

## <a name="send-typing-notification"></a>Bericht over verzend type

Gebruik `SendTypingNotification` om aan te geven dat de gebruiker een antwoord in de thread typt.

```csharp
await chatThreadClient.SendTypingNotificationAsync();
```

## <a name="send-read-receipt"></a>Lees bevestiging verzenden

Gebruiken `SendReadReceipt` om andere deel nemers op de hoogte te stellen dat het bericht door de gebruiker is gelezen.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId);
```

## <a name="get-read-receipts"></a>Lees bevestigingen ophalen

Gebruik `GetReadReceipts` om de status van berichten te controleren om te zien welke items door andere deel nemers van een chat thread worden gelezen.

```csharp
AsyncPageable<ChatMessageReadReceipt> allReadReceipts = chatThreadClient.GetReadReceiptsAsync();
await foreach (ChatMessageReadReceipt readReceipt in allReadReceipts)
{
    Console.WriteLine($"{readReceipt.ChatMessageId}:{((CommunicationUserIdentifier)readReceipt.Sender).Id}:{readReceipt.ReadOn}");
}
```
## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```console
dotnet run
```
