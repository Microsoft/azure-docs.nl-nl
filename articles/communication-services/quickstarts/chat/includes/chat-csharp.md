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
ms.openlocfilehash: a76c6467dac69fd3d21aa659c52227046c166938
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94816658"
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
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.3
``` 

## <a name="object-model"></a>Objectmodel

De volgende klassen verwerken enkele van de belangrijkste functies van de Azure Communication Services chat-clientbibliotheek voor C#.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Deze klasse is vereist voor de chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, op te halen en te verwijderen. |
| ChatThreadClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden, te ontvangen, bij te werken of te verwijderen, gebruikers toe te voegen, te verwijderen of op te halen, getypte meldingen te verzenden en bevestigingen te lezen. |

## <a name="create-a-chat-client"></a>Een chat-client maken

Als u een chat-client wilt maken, gebruikt u uw Communication Services-eindpunt en het toegangstoken dat is gegenereerd als onderdeel van de vereiste stappen. U moet de klasse `CommunicationIdentityClient` uit de clientbibliotheek `Administration` gebruiken om een gebruiker te maken en een token uit te geven om aan uw chat-client door te geven. Meer informatie over [toegangstokens voor gebruikers](../../access-tokens.md).

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Een chat-thread starten

Gebruik de methode `createChatThread` om een chat-thread te maken.
- Gebruik `topic` om een onderwerp te geven aan deze chat. Het onderwerp kan worden bijgewerkt nadat de chat-thread is gemaakt met behulp van de functie `UpdateThread`.
- Gebruik de eigenschap `members` om een lijst met `ChatThreadMember`-objecten door te geven om aan de chat-thread toe te voegen. Het `ChatThreadMember`-object wordt geïnitialiseerd met een `CommunicationUser`-object. Om een `CommunicationUser`-object op te halen, moet u een toegangs-id doorgeven die u hebt gemaakt door de instructies in [Een gebruiker maken](../../access-tokens.md#create-an-identity) te volgen.

Het antwoord `chatThreadClient` wordt gebruikt om bewerkingen uit te voeren op de gemaakte chat-thread: leden toevoegen aan de chat-thread, een bericht verzenden, een bericht verwijderen, enz. Het bevat het kenmerk `Id` dat de unieke id van de chat-thread is. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen
De methode `GetChatThreadClient` retourneert een thread-client voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: leden toevoegen, bericht verzenden, enz. threadId is de unieke id van de bestaande chat-thread.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruik de methode `SendMessage` om een bericht te verzenden naar een thread die wordt geïdentificeerd door threadId.

- Gebruik `content` om de inhoud van het chatbericht te geven. Deze inhoud is vereist.
- Gebruik `priority` om het prioriteitsniveau van het bericht op te geven, zoals 'Normaal' of 'Hoog'. Als dit niet wordt opgegeven, wordt 'Normaal' gebruikt.
- Gebruik `senderDisplayName` om de weergavenaam van de afzender op te geven. Als deze niet wordt opgegeven, wordt er een lege naam gebruikt.

`SendChatMessageResult` is het antwoord dat wordt geretourneerd door het verzenden van een bericht. Het bevat een id, die de unieke id van het bericht is.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
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

`GetMessages` retourneert de meest recente versie van het bericht, inclusief eventuele bewerkingen of verwijderingen die zijn opgetreden in het bericht met `UpdateMessage` en `DeleteMessage`. Voor verwijderde berichten retourneert `chatMessage.DeletedOn` een datum/tijd-waarde die aangeeft wanneer dat bericht is verwijderd. Voor bewerkte berichten retourneert `chatMessage.EditedOn` een datum/tijd die aangeeft wanneer het bericht is bewerkt. De oorspronkelijke tijd van het maken van het bericht kan worden geopend met `chatMessage.CreatedOn` en kan worden gebruikt voor het ordenen van de berichten.

`GetMessages` retourneert verschillende typen berichten die kunnen worden geïdentificeerd door `chatMessage.Type`. Deze typen zijn:

- `Text`: Normaal chatbericht verzonden door een thread-lid.

- `ThreadActivity/TopicUpdate`: Systeembericht dat aangeeft dat het onderwerp is bijgewerkt.

- `ThreadActivity/AddMember`: Systeembericht dat aangeeft dat een of meer leden zijn toegevoegd aan de chat-thread.

- `ThreadActivity/DeleteMember`: Systeembericht dat aangeeft dat een lid is verwijderd uit de chat-thread.

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

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Een gebruiker toevoegen als lid van de chat-thread

Zodra u een thread hebt gemaakt, kunt u gebruikers toevoegen en verwijderen. Door gebruikers toe te voegen, geeft u ze toegang voor het verzenden van berichten naar de thread en het toevoegen/verwijderen van andere leden. Voordat u `AddMembers` aanroept, moet u ervoor zorgen dat u een nieuw toegangstoken en een nieuwe identiteit hebt verkregen voor die gebruiker. De gebruiker heeft dat toegangstoken nodig om zijn chat-client te initialiseren.

Gebruik de methode `AddMembers` om thread-leden toe te voegen aan de thread die wordt geïdentificeerd door threadId.

 - Gebruik `members` om de leden weer te geven die moeten worden toegevoegd aan de chat-thread;
 - `User`, vereist, is de identiteit die u voor deze nieuwe gebruiker krijgt.
 - `DisplayName`, optioneel, is de weergavenaam voor het thread-lid.
 - `ShareHistoryTime`, optioneel, is de tijd vanaf wanneer de chat-geschiedenis wordt gedeeld met het lid. Om de geschiedenis vanaf het begin van de chat-thread te delen, stelt u deze in op DateTime.MinValue. Als u geen geschiedenis wilt delen voordat het lid is toegevoegd, stel het dan op de huidige tijd in. Als u een deel van de geschiedenis wilt delen, stelt u het in op een tijdstip tussen het maken van de thread en de huidige tijd.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Gebruiker verwijderen uit een chat-thread

Net als bij het toevoegen van een gebruiker aan een thread, kunt u gebruikers uit een chat-thread verwijderen. Hiervoor moet u de identiteit (CommunicationUser) bijhouden van de leden die u hebt toegevoegd.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```console
dotnet run
```
