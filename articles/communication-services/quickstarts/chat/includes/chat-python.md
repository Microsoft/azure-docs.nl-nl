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
ms.openlocfilehash: 31704e705b828cc0070e3b79f5d527cfa9deb0c3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107386691"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat, moet u het volgende doen:

- Maak een Azure-account met een actief abonnement. Zie [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voor meer informatie.
- [Python](https://www.python.org/downloads/) installeren
- Maak een Azure Communication Services-resource. Zie [Een Azure Communication-resource maken](../../create-communication-resource.md) voor meer informatie. Voor deze snelstart moet u het **eindpunt** van uw resource vastleggen
- Een[Token voor gebruikerstoegang](../../access-tokens.md). Zorg ervoor dat u het bereik instelt op ‘chat’ en noteer de tokenreeks en ook de gebruikersId-reeks.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Open uw terminal of opdrachtvenster, maak een nieuwe map voor uw app en navigeer daar naartoe.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Gebruik een teksteditor om een bestand te maken met de naam **start-chat.py** in de hoofdmap van het project en voeg de structuur voor het programma toe, waaronder de basale verwerking van uitzonderingen. In de volgende secties voegt u alle broncode voor deze snelstart toe aan dit bestand.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>SDK installeren

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services Chat SDK voor Python.

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Deze klasse is vereist voor de chat-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om threads te maken, krijgen en verzenden. |
| ChatThreadClient | Deze klasse is vereist voor de functionaliteit van de chat-thread. U verkrijgt een instantie via de ChatClient en gebruikt deze om berichten te verzenden/ontvangen/bijwerken/verwijderen, gebruikers toe te voegen/te verwijderen of te verzenden, getypte meldingen te verzenden en bevestigingen te lezen. |

## <a name="create-a-chat-client"></a>Een chat-client maken

Als u een chat-client wilt maken, gebruikt u het Communications Service-eindpunt en het `Access Token` dat is gegenereerd als onderdeel van de vereiste stappen. Meer informatie over [tokens voor gebruikerstoegang](../../access-tokens.md).

Deze quickstart gaat niet over het maken van een servicelaag voor het beheren van tokens voor uw chattoepassing, hoewel dit wel wordt aanbevolen. Zie de volgende documentatie voor meer informatie over [chatarchitectuur](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Een chat-thread starten

Gebruik de methode `create_chat_thread` om een chat-thread te maken.

- Gebruik `topic` om een onderwerp te geven aan de thread. Het onderwerp kan worden bijgewerkt nadat de chat-thread is gemaakt met behulp van de functie `update_thread`.
- Gebruik `thread_participants` om een lijst weer te geven met de `ChatParticipant` die moeten worden toegevoegd aan de chat-thread, de `ChatParticipant` neemt `CommunicationUserIdentifier` type als `user`, wat u hebt verkregen nadat u deze heeft aangemaakt via [Een gebruiker maken](../../access-tokens.md#create-an-identity)

`CreateChatThreadResult` is het resultaat dat wordt geretourneerd door het maken van een thread. U kunt deze gebruiken om de op te halen `id` van de chat-thread die is gemaakt. Dit `id` kan vervolgens worden gebruikt om een object op te halen met behulp van de methode `ChatThreadClient` `get_chat_thread_client` . `ChatThreadClient` kan worden gebruikt om andere chatbewerkingen naar deze chat-thread uit te voeren.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen
De methode `get_chat_thread_client` retourneert een thread-client voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: deelnemers toevoegen, bericht verzenden, enzovoort. thread_id is de unieke id van de bestaande chat-thread.

`ChatThreadClient` kan worden gebruikt om andere chatbewerkingen naar deze chat-thread uit te voeren.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Een lijst met alle chatthreads maken
De `list_chat_threads` methode retourneert een iterator van het type `ChatThreadItem` . Deze kan worden gebruikt voor het in een lijst plaatsen van alle chatthreads.

- Gebruik `start_time` om het vroegste tijdstip op te geven om chatthreads tot op te halen.
- Gebruik `results_per_page` om het maximum aantal chatthreads op te geven dat per pagina wordt geretourneerd.

Een iterator van `[ChatThreadItem]` is het antwoord dat wordt geretourneerd door het in een lijst plaatsen van threads

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruik de methode om een bericht te verzenden naar een chatgesprek dat u zojuist hebt `send_message` gemaakt, geïdentificeerd door thread_id.

- Gebruik `content` om de inhoud van het chatbericht op te geven;
- Gebruik `chat_message_type` om het inhoudstype van het bericht op te geven. Mogelijke waarden zijn 'text' en 'html'; als er geen standaardwaarde van 'tekst' is opgegeven, wordt toegewezen.
- Gebruik `sender_display_name` om de weergavenaam van de afzender op te geven.

`SendChatMessageResult` is het antwoord dat wordt geretourneerd door het verzenden van een bericht. Het bevat een id. Dit is de unieke id van het bericht.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>Chatberichten ontvangen van een chat-thread

U kunt chatberichten ophalen door de methode `list_messages` op opgegeven intervallen te pollen.

- Gebruik `results_per_page` om het maximum aantal berichten op te geven dat per pagina moet worden geretourneerd.
- Gebruik `start_time` om het vroegste tijdstip op te geven om berichten op te halen.

Een iterator van `[ChatMessage]` is het antwoord dat wordt geretourneerd door het aanbieden van berichten

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` retourneert de meest recente versie van het bericht, inclusief eventuele bewerkingen of verwijderingen die zijn opgetreden in het bericht met `update_message` en `delete_message`. Voor verwijderde berichten retourneert `ChatMessage.deleted_on` een datum/tijd-waarde die aangeeft wanneer het bericht verwijderd werd. Voor bewerkte berichten retourneert `ChatMessage.edited_on` een datum/tijd die aangeeft wanneer het bericht is bewerkt. Het oorspronkelijke tijdstip waarop het bericht is gemaakt, kan worden geraadpleegd met `ChatMessage.created_on` en kan worden gebruikt om de berichten te ordenen.

`list_messages` retourneert verschillende typen berichten die kunnen worden geïdentificeerd door `ChatMessage.type`. 

Lees hier meer over berichttypen: [Berichttypen.](../../../concepts/chat/concepts.md#message-types)

## <a name="send-read-receipt"></a>Ontvangstbewijs voor lezen verzenden
De methode kan worden gebruikt om namens een gebruiker `send_read_receipt` een leesbevestigingsgebeurtenis in een thread te plaatsen.

- Gebruik `message_id` om de id op te geven van het laatste bericht dat door de huidige gebruiker is gelezen.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Een gebruiker toevoegen als deelnemer aan de chat-thread

Zodra u een chat-thread hebt gemaakt, kunt u gebruikers toevoegen en verwijderen. Door gebruikers toe te voegen, geeft u hen toegang om berichten naar de chat-thread te kunnen verzenden en andere deelnemers toe te voegen/te verwijderen. Voordat u de methode `add_participants` aanroept, moet u ervoor zorgen dat u een nieuw toegangstoken en een nieuwe identiteit hebt verkregen voor die gebruiker. De gebruiker heeft dat toegangstoken nodig om zijn chat-client te initialiseren.

Een of meer gebruikers kunnen worden toegevoegd aan de chat-thread met behulp van de methode . Er is een nieuw toegang token en identificatie `add_participants` beschikbaar voor alle gebruikers.

Er `list(tuple(ChatParticipant, CommunicationError))` wordt een geretourneerd. Wanneer de deelnemer is toegevoegd, wordt een lege lijst verwacht. In het geval van een fout die is opgetreden tijdens het toevoegen van een deelnemer, wordt de lijst gevuld met de mislukte deelnemers, samen met de fout die is opgetreden.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>Threaddeelnemers in een chatgesprek op een lijst zetten

Net als bij het toevoegen van een deelnemer kunt u ook deelnemers uit een thread op een lijst zetten.

Gebruik `list_participants` om de deelnemers van de thread op te halen.
- Gebruik `results_per_page` , optioneel, het maximum aantal deelnemers dat per pagina moet worden geretourneerd.
- Gebruik `skip` , optioneel, om deelnemers over te slaan naar een opgegeven positie in de reactie.

Een iterator van `[ChatParticipant]` is het antwoord dat wordt geretourneerd door de lijst met deelnemers

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `python`.

```console
python start-chat.py
```
