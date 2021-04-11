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
ms.openlocfilehash: 7fe50a6236cf67f1048dddecbf46fea836ec05c5
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106125746"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) versie 8 of hoger.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../../create-communication-resource.md).
- Een[Toegangstoken voor gebruikers](../../access-tokens.md). Zorg ervoor dat u het bereik instelt op ‘chat’ en noteer de tokenreeks en ook de gebruikersId-reeks.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-java-application"></a>Een nieuwe Java-toepassing maken

Open uw terminal- of opdrachtvenster en navigeer naar de map waarin u uw Java-toepassing wilt maken. Voer de onderstaande opdracht uit om het Java-project te genereren op basis van de maven-archetype-snelstart-sjabloon.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

U ziet dat het doel 'genereren' een map heeft gemaakt met dezelfde naam als de artifactId. De `src/main/java directory` in deze map bevat de broncode van het project, de map `src/test/java` bevat de testbron en het bestand pom.xml is het Project Object Model van het project of [POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html).

Het POM-bestand van uw toepassing bijwerken voor het gebruik van Java 8 of hoger:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>De pakket verwijzingen voor de chat-SDK toevoegen

In uw POM-bestand verwijzen we naar het pakket `azure-communication-chat` met de chat-API's:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

Voor verificatie moet uw client het pakket `azure-communication-common` verwijzen :

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services chat SDK voor Java.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Deze klasse is vereist voor de chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, krijgen en verzenden. |
| ChatAsyncClient | Deze klasse is vereist voor de asynchrone chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, krijgen en verzenden. |
| ChatThreadClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden/ontvangen/bijwerken/verwijderen, gebruikers toe te voegen/te verwijderen of te verzenden, getypte meldingen te verzenden en bevestigingen te lezen. |
| ChatThreadAsyncClient | Deze klasse is vereist voor de functionaliteit van de asynchrone chat-thread. U verkrijgt een instantie via de ChatAsyncClient en gebruikt deze om berichten te verzenden/ontvangen/bijwerken/verwijderen, gebruikers toe te voegen/te verwijderen of te verzenden, getypte meldingen te verzenden en bevestigingen te lezen. |

## <a name="create-a-chat-client"></a>Een chat-client maken
Als u een chat-client wilt maken, gebruikt u het Communications Service-eindpunt en het toegangstoken dat is gegenereerd als onderdeel van de vereiste stappen. Met toegangstokens voor gebruikers kunt u clienttoepassingen maken die zich rechtstreeks verifiëren bij Azure Communication Services. Zodra u deze tokens op uw server hebt gegenereerd, geeft u ze terug op een clientapparaat. U moet de CommunicationTokenCredential-klasse van de gemeen schappelijke SDK gebruiken om het token door te geven aan uw chat-client.

Meer informatie over de [architectuur van chatten](../../../concepts/chat/concepts.md)

Bij het toevoegen van de importinstructies, moet u ervoor zorgen dat u alleen importbewerkingen toevoegt vanuit de com.azure.communication.chat en com.azure.communication.chat.models naamspaties en niet vanuit de com.azure.communication.chat.implementation naamspatie. In het bestand App.java dat is gegenereerd via Maven, kunt u de volgende code gebruiken om te beginnen met:

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Een chat-thread starten

De methode `createChatThread` gebruiken om een chat-thread te maken.
`createChatThreadOptions` wordt gebruikt om de thread-aanvraag te beschrijven.

- Gebruik de `topic` para meter van de constructor om een onderwerp voor deze chat te geven. Het onderwerp kan worden bijgewerkt nadat de chat thread is gemaakt met behulp van de `UpdateThread` functie.
- Gebruik `participants` om de thread deelnemers weer te geven die moeten worden toegevoegd aan de thread. `ChatParticipant` neemt u de gebruiker die u hebt gemaakt in de Snelstart [Toegangstoken voor gebruikers](../../access-tokens.md).

`CreateChatThreadResult` is het antwoord dat wordt geretourneerd door het maken van een chat thread.
Het bevat een `getChatThread()` methode die het `ChatThread` object retourneert dat kan worden gebruikt voor het ophalen van de thread-client van waaruit u de `ChatThreadClient` bewerkingen voor het uitvoeren van de gemaakte thread kunt verkrijgen: deel nemers toevoegen, bericht verzenden enzovoort. Het `ChatThread` object bevat ook de `getId()` methode waarmee de unieke id van de thread wordt opgehaald.

```Java
ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(firstUser)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(secondUser)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Chat-threads weer geven

Gebruik de- `listChatThreads` methode om een lijst met bestaande chat-threads op te halen.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen

De methode `getChatThreadClient` retourneert een thread-client voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: deel nemers toevoegen, berichten verzenden, enzovoort. `chatThreadId` is de unieke ID van de bestaande chat-thread.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruik de methode `sendMessage` om een bericht te verzenden naar de thread die u zojuist hebt gemaakt, geïdentificeerd door chatThreadId.
`sendChatMessageOptions` wordt gebruikt om de aanvraag voor het chatbericht te beschrijven.

- Gebruik `content` om de inhoud van het chatbericht te geven.
- Gebruiken `type` om het inhouds type van het chat bericht, tekst of HTML op te geven.
- Gebruik `senderDisplayName` om de weergavenaam van de afzender te geven.

Het antwoord `sendChatMessageResult` bevat een `id`, dit is de unieke ID van het bericht.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Chatberichten ontvangen van een chat-thread

U kunt chatberichten ophalen door de methode `listMessages` op de chat-thread-client op bepaalde intervallen te pollen.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` retourneert de meest recente versie van het bericht, inclusief eventuele bewerkingen of verwijderingen die zijn opgetreden in het bericht met behulp van .editMessage() en .deleteMessage(). Voor verwijderde berichten retourneert `chatMessage.getDeletedOn()` een datum/tijd-waarde die aangeeft wanneer dat bericht is verwijderd. Voor bewerkte berichten retourneert `chatMessage.getEditedOn()` een datum/tijd die aangeeft wanneer het bericht is bewerkt. De oorspronkelijke tijd van het maken van het bericht kan worden geopend met `chatMessage.getCreatedOn()` en kan worden gebruikt voor het bestellen van de berichten.

Lees hier meer over bericht typen: [bericht typen](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Lees bevestiging verzenden

Gebruik de `sendReadReceipt` methode om een gebeurtenis voor lees bevestiging te plaatsen in een chat-thread, namens een gebruiker.
`chatMessageId` is de unieke ID van het chat bericht dat is gelezen.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Chat deelnemers weer geven

Gebruiken `listParticipants` voor het ophalen van een verzameling pagina's met de deel nemers van de chat-thread geïdentificeerd door chatThreadId.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Een gebruiker toevoegen als deel nemer aan de chat thread

Zodra u een chat-thread hebt gemaakt, kunt u gebruikers toevoegen en verwijderen. Door gebruikers toe te voegen, kunt u hen toegang geven tot het verzenden van berichten naar de chat-thread en andere deel nemers toevoegen/verwijderen. U moet beginnen met het ophalen van een nieuw toegangstoken en een nieuwe identiteit voor die gebruiker. Voordat u de methode addParticipants aanroept, moet u ervoor zorgen dat u een nieuw toegangs token en een nieuwe identiteit hebt verkregen voor die gebruiker. De gebruiker heeft dat toegangstoken nodig om zijn chat-client te initialiseren.

Gebruik de- `addParticipants` methode om deel nemers aan de thread toe te voegen.

- `communicationIdentifier`, vereist, is het CommunicationIdentifier dat u hebt gemaakt door de CommunicationIdentityClient in de Snelstartgids voor [gebruikers toegangs tokens](../../access-tokens.md) .
- `displayName`, optioneel, is de weergave naam voor de deel nemer aan de thread.
- `shareHistoryTime`, optioneel, is de tijd waarop de chat geschiedenis wordt gedeeld met de deel nemer. Als u de geschiedenis wilt delen sinds het begin van de chat-thread, stelt u deze eigenschap in op een willekeurige datum die gelijk is aan of kleiner is dan de aanmaaktijd van de thread. Als u geen geschiedenis wilt delen vóór wanneer de deel nemer is toegevoegd, stelt u deze in op de huidige datum. Als u gedeeltelijke geschiedenis wilt delen, stelt u deze in op de vereiste datum.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(user4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>De code uitvoeren

Navigeer naar de map die het bestand *pom.xml* bevat en compileer het project met behulp van de volgende `mvn`-opdracht.

```console
mvn compile
```

Bouw vervolgens het pakket.

```console
mvn package
```

Voer de volgende `mvn`-opdracht uit om de app uit te voeren.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
