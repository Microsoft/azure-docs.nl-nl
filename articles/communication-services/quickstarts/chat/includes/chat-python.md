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
ms.openlocfilehash: deec1dabe405d13d6009311c8b2d68a930e7aa29
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661660"
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
from azure.communication.chat import ChatClient, CommunicationTokenCredential, CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Een chat-thread starten

Gebruik de methode `create_chat_thread` om een chat-thread te maken.

- Gebruik `topic` om een onderwerp te geven aan de thread. Het onderwerp kan worden bijgewerkt nadat de chat-thread is gemaakt met behulp van de functie `update_thread`.
- Gebruik `thread_participants` om een lijst weer te geven met de `ChatThreadParticipant` die moeten worden toegevoegd aan de chat-thread, de `ChatThreadParticipant` neemt `CommunicationUserIdentifier` type als `user`, wat u hebt verkregen nadat u deze heeft aangemaakt via [Een gebruiker maken](../../access-tokens.md#create-an-identity)
- Gebruik `repeatability_request_id` om te leiden dat de aanvraag kan worden herhaald. De client kan de aanvraag meerdere keren met dezelfde Herhaal baarheid-aanvraag-ID maken en een juiste reactie terughalen zonder dat de server de aanvraag meerdere keren uitvoert.

Het antwoord `chat_thread_client` wordt gebruikt om bewerkingen uit te voeren op de zojuist gemaakte chat-thread, zoals deel nemers toevoegen aan de chat-thread, een bericht verzenden, een bericht verwijderen enzovoort. Het bevat een `thread_id` eigenschap die de unieke id van de chat thread is.

#### <a name="without-repeatability-request-id"></a>Zonder Herhaal baarheid-aanvraag-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

chat_thread_client = chat_client.create_chat_thread(topic, participants)
```

#### <a name="with-repeatability-request-id"></a>Met herhaal baarheid-aanvraag-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, participants, repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Een chat-thread-client ophalen
De methode `get_chat_thread` retourneert een thread-client voor een thread die al bestaat. Het kan worden gebruikt voor het uitvoeren van bewerkingen op de gemaakte thread: deel nemers toevoegen, bericht verzenden thread_id enzovoort is de unieke ID van de bestaande chat-thread.

```python
thread_id = 'id'
chat_thread = chat_client.get_chat_thread(thread_id)
```

## <a name="list-all-chat-threads"></a>Alle chat-threads weer geven
De `list_chat_threads` methode retourneert een iterator van het type `ChatThreadInfo` . Het kan worden gebruikt voor het weer geven van alle chat-threads.

- Gebruiken `start_time` om het vroegste tijdstip op te geven waarop chat-threads worden ontvangen.
- Gebruik `results_per_page` om het maximum aantal chat threads op te geven dat per pagina wordt geretourneerd.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)
start_time = start_time.replace(tzinfo=pytz.utc)
chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)

for info in chat_thread_infos:
    # Iterate over all chat threads
    print("thread id:", info.id)
```

## <a name="delete-a-chat-thread"></a>Een chatgesprek verwijderen
De `delete_chat_thread` wordt gebruikt om een chat-thread te verwijderen.

- Gebruik `thread_id` om de thread_id op te geven van een bestaande chat-thread die moet worden verwijderd

```python
thread_id='id'
chat_client.delete_chat_thread(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Een bericht verzenden naar een chat-thread

Gebruik `send_message` methode om een bericht te verzenden naar een chat-thread die u zojuist hebt gemaakt, geïdentificeerd door thread_id.

- Gebruik `content` om de inhoud van het chatbericht op te geven;
- Gebruiken `chat_message_type` om het inhouds type van het bericht op te geven. Mogelijke waarden zijn ' text ' en ' HTML '; Als er geen standaard waarde van ' text ' is opgegeven.
- Gebruik `sender_display_name` om de weergavenaam van de afzender op te geven.

Het antwoord is een ' id ' van `str` het type. Dit is de unieke id van het bericht.

#### <a name="message-type-not-specified"></a>Er is geen bericht type opgegeven
```python

content='hello world'
sender_display_name='sender name'

send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)
```

#### <a name="message-type-specified"></a>Opgegeven bericht type
```python
from azure.communication.chat import ChatMessageType

content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>Een specifiek chat bericht ophalen van een chat-thread
De `get_message` functie kan worden gebruikt om een specifiek bericht op te halen dat wordt geïdentificeerd door een message_id

- Gebruiken `message_id` om de bericht-id op te geven

Het antwoord van het type `ChatMessage` bevat alle informatie met betrekking tot het afzonderlijke bericht.

```python
message_id = 'message_id'
chat_message = chat_thread_client.get_message(message_id)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Chatberichten ontvangen van een chat-thread

U kunt chatberichten ophalen door de methode `list_messages` op opgegeven intervallen te pollen.

- Gebruik `results_per_page` om het maximum aantal berichten op te geven dat per pagina moet worden geretourneerd.
- Gebruiken `start_time` om het vroegste tijdstip op te geven waarop berichten moeten worden opgehaald.

```python
chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
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
```

## <a name="update-a-message"></a>Een bericht bijwerken
U kunt de inhoud van een bestaand bericht bijwerken met behulp van de `update_message` -methode, geïdentificeerd door de message_id

- Gebruiken `message_id` om de message_id op te geven
- Gebruiken `content` om de nieuwe inhoud van het bericht in te stellen

```python
message_id='id'
content = 'updated content'
chat_thread_client.update_message(message_id=message_id, content=content)
```

## <a name="send-read-receipt-for-a-message"></a>Lees bevestiging verzenden voor een bericht
De `send_read_receipt` methode kan worden gebruikt om een gebeurtenis voor een lees bevestiging te posten naar een thread, namens een gebruiker.

- Gebruiken `message_id` om de id op te geven van het laatste bericht dat door de huidige gebruiker is gelezen

```python
message_id='id'
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>Lees bevestigingen voor een chat-thread weer geven
De `list_read_receipts` methode kan worden gebruikt om Lees bevestigingen voor een thread te verkrijgen.

- Gebruik `results_per_page` om het maximum aantal lees bevestigingen voor chat berichten op te geven dat per pagina moet worden geretourneerd.
- Gebruiken `skip` om Lees bevestigingen voor chat berichten overs Laan tot een opgegeven positie in antwoord opgeven.

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=2, skip=0)

for page in read_receipts.by_page():
    for item in page:
        print(item)
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
message_id='id'
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Een gebruiker toevoegen als deel nemer aan de chat thread

Zodra u een chat-thread hebt gemaakt, kunt u gebruikers toevoegen en verwijderen. Door gebruikers toe te voegen, kunt u hen toegang geven om berichten te verzenden naar de chat-thread en andere deel nemers toe te voegen of te verwijderen. Voordat u de methode `add_participant` aanroept, moet u ervoor zorgen dat u een nieuw toegangstoken en een nieuwe identiteit hebt verkregen voor die gebruiker. De gebruiker heeft dat toegangstoken nodig om zijn chat-client te initialiseren.

Gebruik `add_participant` methode om thread deelnemers toe te voegen aan de thread die wordt geïdentificeerd door thread_id.

- Gebruiken `thread_participant` om de deel nemer op te geven die moet worden toegevoegd aan de chat-thread;
- `user`, vereist, is de `CommunicationUserIdentifier` die u hebt gemaakt door `CommunicationIdentityClient` op [een gebruiker maken](../../access-tokens.md#create-an-identity)
- `display_name`, optioneel, is de weergave naam voor de deel nemer aan de thread.
- `share_history_time`, optioneel, is de tijd waarop de chat geschiedenis wordt gedeeld met de deel nemer. Als u de geschiedenis wilt delen sinds het begin van de chat-thread, stelt u deze eigenschap in op een willekeurige datum die gelijk is aan of kleiner is dan de aanmaaktijd van de thread. Als u geen geschiedenis wilt delen vóór wanneer de deel nemer is toegevoegd, stelt u deze in op de huidige datum. Om een gedeeltelijke geschiedenis te delen, stelt u deze in op een tussenliggende datum.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())

chat_thread_client.add_participant(new_chat_thread_participant)
```

Er kunnen ook meerdere gebruikers aan de chat thread worden toegevoegd met behulp van de `add_participants` -methode, waarbij het nieuwe toegangs token en de zoek functie beschikbaar zijn voor alle gebruikers.

```python
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
        user=self.new_user,
        display_name='name',
        share_history_time=datetime.utcnow())
thread_participants = [new_chat_thread_participant] # instead of passing a single participant, you can pass a list of participants
chat_thread_client.add_participants(thread_participants)
```


## <a name="remove-user-from-a-chat-thread"></a>Gebruiker verwijderen uit een chat-thread

Net als bij het toevoegen van een deel nemer, kunt u ook deel nemers uit een thread verwijderen. Als u wilt verwijderen, moet u de Id's bijhouden van de deel nemers die u hebt toegevoegd.

Gebruik `remove_participant` methode om de thread deelnemer te verwijderen uit de thread die wordt geïdentificeerd door thread.
- `user` is de `CommunicationUserIdentifier` moet worden verwijderd uit de thread.

```python
chat_thread_client.remove_participant(user)
```

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `python`.

```console
python start-chat.py
```
