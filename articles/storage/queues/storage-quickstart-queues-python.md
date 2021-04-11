---
title: 'Quickstart: Azure Queue Storage-clientbibliotheek v12 - Python'
description: Leer hoe u de Azure Queue Storage-clientbibliotheek v12 voor Python kunt gebruiken om een wachtrij te maken en er berichten aan toe te voegen. Leer vervolgens hoe u berichten uit de wachtrij kunt lezen en verwijderen. U leert ook hoe u een wachtrij kunt verwijderen.
author: twooley
ms.author: twooley
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-python
ms.openlocfilehash: 1881af372c1f4e1c5cbb4ea7be0ede4c96bbf4ee
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276141"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Quickstart: Azure Queue Storage-clientbibliotheek v12 voor Python

Ga aan de slag met de Azure Queue Storage-clientbibliotheek v12 voor Python. Azure Queue Storage is een service waarmee u grote aantallen berichten kunt opslaan om later op te halen en te verwerken. Volg deze stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Gebruik de Azure Queue Storage-clientbibliotheek v12 voor Python voor het volgende:

- Een wachtrij maken
- Berichten aan een wachtrij toevoegen
- Berichten in een wachtrij bekijken
- Een bericht in een wachtrij bijwerken
- Berichten van een wachtrij ontvangen
- Berichten uit een wachtrij verwijderen
- Een wachtrij verwijderen

Aanvullende bronnen:

- [API-referentiedocumentatie](/python/api/azure-storage-queue/index)
- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Pakket (Python-pakketindex)](https://pypi.org/project/azure-storage-queue/)
- [Voorbeelden](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- Azure Storage-account: [maak een opslagaccount](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) voor uw besturings systeem-2,7 of 3.6 +

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Queue Storage-clientbibliotheek v12 voor Python.

### <a name="create-the-project"></a>Het project maken

Maak een Python-toepassing met de naam `queues-quickstart-v12`.

1. Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor het project.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Schakel over naar de zojuist gemaakte map `queues-quickstart-v12`.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Het pakket installeren

Installeer het pakket Azure Blob Storage-clientbibliotheek voor Python met behulp van de opdracht `pip install`.

```console
pip install azure-storage-queue
```

Met deze opdracht wordt het pakket Azure Queue Storage-clientbibliotheek voor Python geïnstalleerd, evenals alle bibliotheken waarvan het afhankelijk is. In dit geval is dat alleen de Azure-kernbibliotheek voor Python.

### <a name="set-up-the-app-framework"></a>Stel het app-framework in

1. Open een nieuw tekstbestand in uw code-editor
1. Voeg `import`-instructies toe
1. Maak de structuur voor het programma, waaronder zeer eenvoudige afhandeling van uitzonderingen

    Hier volgt de code:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Sla het nieuwe bestand op als `queues-quickstart-v12.py` in de map `queues-quickstart-v12`.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot aan de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een voorraad werk te maken dat asynchroon moet worden verwerkt. Queue Storage biedt drie typen resources:

- Het opslagaccount
- Een wachtrij in het opslagaccount
- Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de Queue Storage-architectuur](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende Python-klassen om te communiceren met deze resources:

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): Met de `QueueServiceClient` kunt u alle wachtrijen in uw opslagaccount beheren.
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): Met de `QueueClient`-klasse kunt u een afzonderlijke wachtrij en de bijbehorende berichten beheren en bewerken.
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): De `QueueMessage`-klasse vertegenwoordigt de afzonderlijke objecten die worden geretourneerd wanneer [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) wordt aangeroepen voor een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

Deze voorbeeldcodefragmenten laten zien hoe u de volgende acties kunt uitvoeren met de Azure Queue Storage-clientbibliotheek voor Python:

- [De verbindingsreeks ophalen](#get-the-connection-string)
- [Een wachtrij maken](#create-a-queue)
- [Berichten aan een wachtrij toevoegen](#add-messages-to-a-queue)
- [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
- [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
- [Berichten van een wachtrij ontvangen](#receive-messages-from-a-queue)
- [Berichten uit een wachtrij verwijderen](#delete-messages-from-a-queue)
- [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

Met de volgende code wordt de verbindingsreeks voor het opslagaccount opgehaald. De verbindingsreeks is opgeslagen in de omgevingsvariabele die is gemaakt in de sectie [De opslagverbindingsreeks configureren](#configure-your-storage-connection-string).

Voeg deze code toe in het `try`-blok:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Een wachtrij maken

Verzin een naam voor de nieuwe wachtrij. Met de volgende code wordt een UUID-waarde aan de wachtrijnaam toegevoegd om te verzekeren dat deze uniek is.

> [!IMPORTANT]
> Wachtrijnamen mogen alleen kleine letters, cijfers en afbreekstreepjes bevatten en moeten beginnen met een letter of cijfer. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet bovendien tussen 3 en 63 tekens lang zijn. Zie [Naamgeving van wachtrijen en metagegevens](/rest/api/storageservices/naming-queues-and-metadata) voor meer informatie.

Maak een exemplaar van de [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient)-klasse. Roep vervolgens de methode [`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) aan om de wachtrij in uw opslagaccount te maken.

Voeg deze code toe aan het einde van het `try`-blok:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Berichten aan een wachtrij toevoegen

Met het volgende codefragment worden berichten aan de wachtrij toegevoegd door de methode [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) aan te roepen. Ook wordt de [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) opgeslagen die met de derde `send_message`-aanroep wordt geretourneerd. De `saved_message` wordt gebruikt om de inhoud van een bericht later in het programma bij te werken.

Voeg deze code toe aan het einde van het `try`-blok:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de methode [`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) aan te roepen. Met deze methode worden één of meer berichten vooraan in de wachtrij opgehaald, maar wordt de zichtbaarheid van het bericht niet gewijzigd.

Voeg deze code toe aan het einde van het `try`-blok:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

Werk de inhoud van een bericht bij door de methode [`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) aan te roepen. Deze methode kan de time-out voor zichtbaarheid en de inhoud van een bericht wijzigen. De inhoud van het bericht moet een UTF-8-gecodeerde tekenreeks zijn die maximaal 64 KB groot is. Geef behalve de nieuwe inhoud ook waarden uit het bericht door die eerder in de code waren opgeslagen. De `saved_message`-waarden identificeren welk bericht moet worden bijgewerkt.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

Download eerder toegevoegde berichten door de methode [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) aan te roepen.

Voeg deze code toe aan het einde van het `try`-blok:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Berichten uit een wachtrij verwijderen

Verwijder berichten uit de wachtrij nadat ze zijn ontvangen en verwerkt. In dit geval betekent ‘verwerken’ gewoon dat het bericht wordt weergegeven in de console.

De app pauzeert voor gebruikersinvoer door `input` aan te roepen voordat deze de berichten verwerkt en verwijdert. Verifieer in uw [Azure-portal](https://portal.azure.com) dat de resources correct zijn gemaakt, voordat ze worden verwijderd. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij, zodat u nog een kans hebt om ze te verwerken.

Voeg deze code toe aan het einde van het `try`-blok:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Een wachtrij verwijderen

Met de volgende code worden de resources opgeschoond die met de app zijn gemaakt, door de wachtrij te verwijderen met de methode [`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-).

Voeg deze code toe aan het einde van het `try`-blok en sla het bestand op:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app worden drie berichten gemaakt en aan een Azure-wachtrij toegevoegd. De code toont de berichten in de wachtrij, haalt ze op en verwijdert ze, en verwijdert uiteindelijk de wachtrij.

Navigeer in het consolevenster naar de map die het bestand `queues-quickstart-v12.py` bevat, en voer de volgende opdracht `python` uit om de app uit te voeren.

```console
python queues-quickstart-v12.py
```

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Queue Storage client library v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Wanneer de app pauzeert voordat deze berichten ontvangt, controleert u uw opslagaccount in de [Azure-portal](https://portal.azure.com). Verifieer dat de berichten in de wachtrij staan.

Druk op de `Enter`-toets om de berichten te ontvangen en verwijderen. Druk nogmaals op de `Enter`-toets wanneer u daarom wordt gevraagd, om de wachtrij te verwijderen en de demo te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een wachtrij maakt en berichten eraan toevoegt met behulp van Python-code. Vervolgens leerde u hoe u berichten kunt bekijken, ophalen en verwijderen. Tot slot leerde u hoe u een berichtenwachtrij verwijdert.

Voor zelfstudies, voorbeelden, quickstarts en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Azure voor Python-ontwikkelaars](/azure/python/)

- Zie de [Azure Storage-bibliotheken voor Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage) voor meer informatie.
- Raadpleeg [Azure Queue Storage-clientbibliotheek v12 voor Python - voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples) voor meer Azure Queue Storage-voorbeeld-apps.
