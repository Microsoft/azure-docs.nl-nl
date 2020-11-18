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
ms.openlocfilehash: 2b7d00335253772683b867acf0765b77fc493e79
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523890"
---
## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u het volgende doen:

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie. 
- [Python](https://www.python.org/downloads/) installeren
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../../create-communication-resource.md) voor meer informatie. Voor deze snelstart moet u het **eindpunt** van uw resource vastleggen
- Een[Token voor gebruikerstoegang](../../access-tokens.md). Zorg ervoor dat u het bereik instelt op “chat“ en noteer de tokenreeks en ook de gebruikersId-reeks.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Gebruik een teksteditor om een bestand te maken met de naam **start-chat.py** in de hoofdmap van het project en voeg de structuur voor het programma toe, waaronder de basale verwerking van uitzonderingen. In de volgende secties voegt u alle broncode voor deze snelstart toe aan dit bestand.

```python
import os
# Add required client library components from quickstart here

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Clientbibliotheek installeren

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services chat-clientbibliotheek voor Python.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Deze klasse is vereist voor de chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, krijgen en verzenden. |
| ChatThreadClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden/ontvangen/bijwerken/verwijderen, gebruikers toe te voegen/te verwijderen of te verzenden, getypte meldingen te verzenden en bevestigingen te lezen. |

## <a name="create-a-chat-client"></a>Een chat-client maken

Als u een chat-client wilt maken, gebruikt u het Communications Service-eindpunt en het `Access Token` dat is gegenereerd als onderdeel van de vereiste stappen. Meer informatie over [tokens voor gebruikerstoegang](../../access-tokens.md).

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Een chat-thread starten

Gebruik de methode `create_chat_thread` om een chat-thread te maken.

- Gebruik `topic` om een onderwerp te geven aan de thread. Het onderwerp kan worden bijgewerkt nadat de chat-thread is gemaakt met behulp van de functie `update_thread`.
- Gebruik `members` om een lijst weer te geven met de `ChatThreadMember` die moeten worden toegevoegd aan de chat-thread, de `ChatThreadMember` neemt `CommunicationUser` type als `user`, wat u hebt verkregen nadat u deze heeft aangemaakt via [Een gebruiker maken](../../access-tokens.md#create-an-identity)

Het antwoord `chat_thread_client` wordt gebruikt om bewerkingen uit te voeren op de zojuist gemaakte chat-thread, zoals het toevoegen van leden aan de chat-thread, het verzenden of verwijderen van een bericht enzovoort. Het bevat een `thread_id`-eigenschap die de unieke ID van de chatthread is.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Een chat thread-client ophalen
De methode get_chat_thread_client retourneert een thread-client voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: leden toevoegen, bericht verzenden, enz. thread_id is de unieke ID van de bestaande chat-thread.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruik de methode `send_message` om een bericht te verzenden naar een chat-thread die u zojuist hebt gemaakt, geïdentificeerd door threadId.

- Gebruik `content` om de inhoud van het chat bericht te geven;
- Gebruik `priority` om het prioriteitsniveau van het bericht op te geven, zoals 'Normaal' of 'Hoog'; deze eigenschap kan worden gebruikt om een UI-indicator voor de ontvangende gebruiker in uw app te specificeren om de aandacht op het bericht te vestigen of om aangepaste bedrijfslogica uit te voeren.
- Gebruik `senderDisplayName` om de weergavenaam van de afzender op te geven;

Het antwoord `SendChatMessageResult` bevat een "id". Dit is de unieke ID van het bericht.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Chatberichten ontvangen van een chat-thread

U kunt chatberichten ophalen door de methode `list_messages` op opgegeven intervallen te pollen.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` retourneert de meest recente versie van het bericht, inclusief eventuele bewerkingen of verwijderingen die zijn opgetreden in het bericht met `update_message` en `delete_message`. Voor verwijderde berichten retourneert `ChatMessage.deleted_on` een datum/tijd-waarde die aangeeft wanneer het bericht verwijderd werd. Voor bewerkte berichten retourneert `ChatMessage.edited_on` een datum/tijd die aangeeft wanneer het bericht bewerkt werd. Het oorspronkelijke tijdstip waarop het bericht is gemaakt, kan worden geraadpleegd met `ChatMessage.created_on` en kan worden gebruikt om de berichten te ordenen.

`list_messages` retourneert verschillende typen berichten die kunnen worden geïdentificeerd door `ChatMessage.type`. Deze typen zijn:

- `Text`: Normaal chatbericht verzonden door een thread-lid.

- `ThreadActivity/TopicUpdate`: Het systeembericht dat aangeeft dat het onderwerp is bijgewerkt.

- `ThreadActivity/AddMember`: Systeembericht dat aangeeft dat een of meer leden zijn toegevoegd aan de chat-thread.

- `ThreadActivity/DeleteMember`: Een systeembericht dat aangeeft dat een lid is verwijderd uit de chat-thread.

Zie [Berichttypen](../../../concepts/chat/concepts.md#message-types) voor meer informatie.

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Een gebruiker toevoegen als lid van de chat-thread

Zodra u een chat-thread hebt gemaakt, kunt u gebruikers toevoegen en verwijderen. Door gebruikers toe te voegen, kunt u ze toegang geven tot het verzenden van berichten naar de chat-thread, en andere leden toevoegen/verwijderen. Voordat u de methode `add_members` gebruikt, moet u ervoor zorgen dat u een nieuw toegangstoken en een nieuwe identiteit hebt verkregen voor die gebruiker. De gebruiker heeft het toegangstoken nodig om hun chat-client te initialiseren.

Gebruik methode `add_members` om thread-leden toe te voegen aan de thread die wordt geïdentificeerd door threadId.

- Gebruik `members` om de leden weer te geven die moeten worden toegevoegd aan de chat-thread;
- `user`, vereist, is de `CommunicationUser` die u hebt gemaakt door `CommunicationIdentityClient` op [een gebruiker maken](../../access-tokens.md#create-an-identity)
- `display_name`, optioneel, is de weergavenaam voor het thread-lid.
- `share_history_time`, optioneel, is de tijd van waaruit de chat-geschiedenis wordt gedeeld met het lid. Als u de geschiedenis wilt delen sinds het begin van de chat-thread, stelt u deze eigenschap in op een willekeurige datum die gelijk is aan of kleiner is dan de aanmaaktijd van de thread. Als u geen geschiedenis wilt delen voordat het lid is toegevoegd, stel het dan op de huidige datum in. Om een gedeeltelijke geschiedenis te delen, stelt u deze in op een tussenliggende datum.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Gebruiker verwijderen uit een chat-thread

Net als bij het toevoegen van een lid, kunt u ook leden uit een thread verwijderen. Als u wilt verwijderen, moet u de ID's bijhouden van de leden die u hebt toegevoegd.

Gebruik methode `remove_member` om thread-leden te verwijderen van de thread die wordt geïdentificeerd door threadId.
- `user` is de CommunicationUser die uit de thread moet worden verwijderd.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `python`.

```console
python start-chat.py
```
