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
ms.openlocfilehash: 7ffb656613a5401ac37f1e606b6d70dea9934b2f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021445"
---
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
# Add required client library components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
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

Deze Snelstartgids heeft geen betrekking op het maken van een servicelaag voor het beheren van tokens voor uw chat toepassing, hoewel dit wordt aanbevolen. Raadpleeg de volgende documentatie voor meer informatie over de [architectuur van chatten](../../../concepts/chat/concepts.md)

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient
from azure.communication.identity._shared.user_credential import CommunicationTokenCredential
from azure.communication.chat._shared.user_token_refresh_options import CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Een chat-thread starten

Gebruik de methode `create_chat_thread` om een chat-thread te maken.

- Gebruik `topic` om een onderwerp te geven aan de thread. Het onderwerp kan worden bijgewerkt nadat de chat-thread is gemaakt met behulp van de functie `update_thread`.
- Gebruik `thread_participants` om een lijst weer te geven met de `ChatThreadParticipant` die moeten worden toegevoegd aan de chat-thread, de `ChatThreadParticipant` neemt `CommunicationUserIdentifier` type als `user`, wat u hebt verkregen nadat u deze heeft aangemaakt via [Een gebruiker maken](../../access-tokens.md#create-an-identity)
- Gebruik `repeatability_request_id` om te leiden dat de aanvraag kan worden herhaald. De client kan de aanvraag meerdere keren met dezelfde Herhaal baarheid-aanvraag-ID maken en een juiste reactie terughalen zonder dat de server de aanvraag meerdere keren uitvoert.

`CreateChatThreadResult` is het resultaat van het maken van een thread, kunt u deze gebruiken om de chat-thread op te halen `id` die u hebt gemaakt. Dit `id` kan vervolgens worden gebruikt om een object op te halen `ChatThreadClient` met behulp van de- `get_chat_thread_client` methode. `ChatThreadClient` kan worden gebruikt om andere chat bewerkingen uit te voeren op deze chat-thread.

#### <a name="without-repeatability-request-id"></a>Zonder Herhaal baarheid-aanvraag-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

#### <a name="with-repeatability-request-id"></a>Met herhaal baarheid-aanvraag-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, 
                                                    thread_participants=participants, 
                                                    repeatability_request_id=repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen
De methode `get_chat_thread_client` retourneert een thread-client voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: deel nemers toevoegen, bericht verzenden thread_id enzovoort is de unieke ID van de bestaande chat-thread.

`ChatThreadClient` kan worden gebruikt om andere chat bewerkingen uit te voeren op deze chat-thread.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="get-a-chat-thread"></a>Een chat-thread ophalen

De `get_chat_thread` methode use haalt een `ChatThread` van de-service op; `thread_id` is de unieke id van de thread.
- Gebruik `thread_id` , vereist, om de unieke id van de thread op te geven. 
```python
chat_thread = chat_client.get_chat_thread(thread_id=thread_id)
```

## <a name="list-all-chat-threads"></a>Alle chat-threads weer geven
De `list_chat_threads` methode retourneert een iterator van het type `ChatThreadInfo` . Het kan worden gebruikt voor het weer geven van alle chat-threads.

- Gebruiken `start_time` om het vroegste tijdstip op te geven waarop chat-threads worden ontvangen.
- Gebruik `results_per_page` om het maximum aantal chat threads op te geven dat per pagina wordt geretourneerd.

Een iterator van `[ChatThreadInfo]` is het antwoord dat wordt geretourneerd door een lijst met threads

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        print(chat_thread_info)
```

## <a name="delete-a-chat-thread"></a>Een chatgesprek verwijderen
De `delete_chat_thread` wordt gebruikt om een chat-thread te verwijderen.

- Gebruik `thread_id` om de thread_id op te geven van een bestaande chat-thread die moet worden verwijderd

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_client.delete_chat_thread(thread_id=thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruik `send_message` methode om een bericht te verzenden naar een chat-thread die u zojuist hebt gemaakt, geïdentificeerd door thread_id.

- Gebruik `content` om de inhoud van het chatbericht op te geven;
- Gebruiken `chat_message_type` om het inhouds type van het bericht op te geven. Mogelijke waarden zijn ' text ' en ' HTML '; Als er geen standaard waarde van ' text ' is opgegeven.
- Gebruik `sender_display_name` om de weergavenaam van de afzender op te geven.

Het antwoord is een ' id ' van `str` het type. Dit is de unieke id van het bericht.

#### <a name="message-type-not-specified"></a>Er is geen bericht type opgegeven
```python
topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)

content='hello world'

send_message_result_id = chat_thread_client.send_message(content)
```

#### <a name="message-type-specified"></a>Opgegeven bericht type
```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_id_w_enum)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
print("Message sent: id: ", send_message_result_id_w_str)
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>Een specifiek chat bericht ophalen van een chat-thread
De `get_message` functie kan worden gebruikt om een specifiek bericht op te halen dat wordt geïdentificeerd door een message_id

- Gebruiken `message_id` om de bericht-id op te geven.

Het antwoord van het type `ChatMessage` bevat alle informatie met betrekking tot het afzonderlijke bericht.

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Chatberichten ontvangen van een chat-thread

U kunt chatberichten ophalen door de methode `list_messages` op opgegeven intervallen te pollen.

- Gebruik `results_per_page` om het maximum aantal berichten op te geven dat per pagina moet worden geretourneerd.
- Gebruiken `start_time` om het vroegste tijdstip op te geven waarop berichten moeten worden opgehaald.

Een iterator van `[ChatMessage]` is het antwoord dat wordt geretourneerd door het weer geven van berichten

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` retourneert de meest recente versie van het bericht, inclusief eventuele bewerkingen of verwijderingen die zijn opgetreden in het bericht met `update_message` en `delete_message`. Voor verwijderde berichten retourneert `ChatMessage.deleted_on` een datum/tijd-waarde die aangeeft wanneer het bericht verwijderd werd. Voor bewerkte berichten retourneert `ChatMessage.edited_on` een datum/tijd die aangeeft wanneer het bericht is bewerkt. Het oorspronkelijke tijdstip waarop het bericht is gemaakt, kan worden geraadpleegd met `ChatMessage.created_on` en kan worden gebruikt om de berichten te ordenen.

`list_messages` retourneert verschillende typen berichten die kunnen worden geïdentificeerd door `ChatMessage.type`. Deze typen zijn:

- `ChatMessageType.TEXT`: Het normale chat bericht dat door een thread deelnemer wordt verzonden.

- `ChatMessageType.HTML`: HTML-chat bericht verzonden door een thread deelnemer.

- `ChatMessageType.TOPIC_UPDATED`: Systeembericht dat aangeeft dat het onderwerp is bijgewerkt.

- `ChatMessageType.PARTICIPANT_ADDED`: Systeem bericht dat aangeeft dat een of meer deel nemers zijn toegevoegd aan de chat thread.

- `ChatMessageType.PARTICIPANT_REMOVED`: Systeem bericht dat aangeeft dat een deel nemer is verwijderd uit de chat thread.

Zie [Berichttypen](../../../concepts/chat/concepts.md#message-types)voor meer informatie.

## <a name="update-topic-of-a-chat-thread"></a>Onderwerp van een chat-thread bijwerken
U kunt het onderwerp van een chat thread bijwerken met behulp van de- `update_topic` methode

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)

chat_thread = chat_client.get_chat_thread(chat_thread_client.thread_id)
updated_topic = chat_thread.topic

print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>Een bericht bijwerken
U kunt de inhoud van een bestaand bericht bijwerken met behulp van de `update_message` -methode, geïdentificeerd door de message_id

- Gebruik `message_id` , vereist, is de unieke id van het bericht.
- Gebruik `content` , optioneel, is de inhoud van het bericht dat moet worden bijgewerkt. als deze niet is opgegeven, is deze leeg

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>Lees bevestiging verzenden voor een bericht
De `send_read_receipt` methode kan worden gebruikt om een gebeurtenis voor een lees bevestiging te posten naar een thread, namens een gebruiker.

- Gebruiken `message_id` om de id op te geven van het laatste bericht dat door de huidige gebruiker is gelezen.

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>Lees bevestigingen voor een chat-thread weer geven
De `list_read_receipts` methode kan worden gebruikt om Lees bevestigingen voor een thread te verkrijgen.

- Gebruik `results_per_page` om het maximum aantal lees bevestigingen voor chat berichten op te geven dat per pagina moet worden geretourneerd.
- Gebruiken `skip` om Lees bevestigingen voor chat berichten overs Laan tot een opgegeven positie in antwoord opgeven.

Een iterator van `[ChatMessageReadReceipt]` is het antwoord dat wordt geretourneerd door Lees bevestigingen van vermeldingen

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=5, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
        print('Sender', read_receipt.sender)
        print('Message Id', read_receipt.chat_message_id)
        print('Read On Timestamp', read_receipt.read_on)
```

## <a name="send-typing-notification"></a>Bericht over verzend type
De `send_typing_notification` methode kan worden gebruikt om namens een gebruiker een type gebeurtenis te plaatsen in een thread.

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>Bericht verwijderen
De `delete_message` methode kan worden gebruikt om een bericht te verwijderen, geïdentificeerd door een message_id

- Gebruiken `message_id` om de message_id op te geven

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Een gebruiker toevoegen als deel nemer aan de chat thread

Zodra u een chat-thread hebt gemaakt, kunt u gebruikers toevoegen en verwijderen. Door gebruikers toe te voegen, kunt u hen toegang geven om berichten te verzenden naar de chat-thread en andere deel nemers toe te voegen of te verwijderen. Voordat u de methode `add_participant` aanroept, moet u ervoor zorgen dat u een nieuw toegangstoken en een nieuwe identiteit hebt verkregen voor die gebruiker. De gebruiker heeft dat toegangstoken nodig om zijn chat-client te initialiseren.

Gebruik `add_participant` methode om thread deelnemers toe te voegen aan de thread die wordt geïdentificeerd door thread_id.

- Gebruiken `thread_participant` om de deel nemer op te geven die moet worden toegevoegd aan de chat-thread;
- `user`, vereist, is de `CommunicationUserIdentifier` die u hebt gemaakt door `CommunicationIdentityClient` op [een gebruiker maken](../../access-tokens.md#create-an-identity)
- `display_name`, optioneel, is de weergave naam voor de deel nemer aan de thread.
- `share_history_time`, optioneel, is de tijd waarop de chat geschiedenis wordt gedeeld met de deel nemer. Als u de geschiedenis wilt delen sinds het begin van de chat-thread, stelt u deze eigenschap in op een willekeurige datum die gelijk is aan of kleiner is dan de aanmaaktijd van de thread. Als u geen geschiedenis wilt delen vóór wanneer de deel nemer is toegevoegd, stelt u deze in op de huidige datum. Om een gedeeltelijke geschiedenis te delen, stelt u deze in op een tussenliggende datum.

Als de deel nemer is toegevoegd, wordt er geen fout gegenereerd. Als er een fout is opgetreden bij het toevoegen van een deel nemer, wordt er een `RuntimeError` gegenereerd

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create an user
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_user = identity_client.create_user()

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

participant = ChatThreadParticipant(
    user=new_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow())

try:
    chat_thread_client.add_participant(thread_participant=participant)
except RuntimeError as e:
    if e is not None and decide_to_retry(error=e):
        chat_thread_client.add_participant(thread_participant=participant)
```

Er kunnen ook meerdere gebruikers aan de chat thread worden toegevoegd met behulp van de `add_participants` -methode, waarbij het nieuwe toegangs token en de zoek functie beschikbaar zijn voor alle gebruikers.

Er `list(tuple(ChatThreadParticipant, CommunicationError))` wordt een geretourneerd. Wanneer de deel nemer is toegevoegd, wordt er een lege lijst verwacht. Als er een fout is opgetreden bij het toevoegen van een deel nemer, wordt de lijst gevuld met de mislukte deel nemers, samen met de fout die is aangetroffen.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
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
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatThreadParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(thread_participants=participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if len(retry) > 0:
    chat_thread_client.add_participants(retry)
```


## <a name="remove-user-from-a-chat-thread"></a>Gebruiker verwijderen uit een chat-thread

Net als bij het toevoegen van een deel nemer, kunt u ook deel nemers uit een thread verwijderen. Als u wilt verwijderen, moet u de Id's bijhouden van de deel nemers die u hebt toegevoegd.

Gebruik `remove_participant` methode om de thread deelnemer te verwijderen uit de thread die wordt geïdentificeerd door thread.
- `user` is de `CommunicationUserIdentifier` moet worden verwijderd uit de thread.

```python
chat_thread_client.remove_participant(user=new_user)

# # converesely you can also do the following; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
# 
# user_id = 'some user id'
# chat_thread_client.remove_participant(user=CommunincationUserIdentfier(new_user))
```

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `python`.

```console
python start-chat.py
```
