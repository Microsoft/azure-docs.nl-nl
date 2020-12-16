---
title: Azure Queue Storage gebruiken vanuit python-Azure Storage
description: Meer informatie over het gebruik van Azure Queue Storage van python voor het maken en verwijderen van wacht rijen en het invoegen, ophalen en verwijderen van berichten.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 08/25/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: e473bf5c2761010a6aeea94e6430d34ca34989fb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588273"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Azure Queue Storage gebruiken vanuit python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Overzicht

In dit artikel worden algemene scenario's voor het gebruik van de Azure Queue Storage-service beschreven. De scenario's die worden behandeld, zijn het invoegen, inspecteren, ophalen en verwijderen van wachtrij berichten. De code voor het maken en verwijderen van wacht rijen wordt ook behandeld.

De voor beelden in dit artikel zijn geschreven in Python en gebruiken de [Azure Queue Storage-client bibliotheek voor python](https://github.com/Azure/Azure-SDK-for-Python/tree/master/sdk/storage/azure-storage-queue). Zie de sectie [volgende stappen](#next-steps) voor meer informatie over wacht rijen.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage-SDK voor Python downloaden en installeren

De [Azure Storage SDK voor python](https://github.com/azure/azure-storage-python) vereist python v 2.7, v 3.3 of hoger.

### <a name="install-via-pypi"></a>Installeren via PyPI

Als u wilt installeren via de python-pakket index (PyPI), typt u:

# <a name="python-v12"></a>[Python-V12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Als u een upgrade uitvoert van de Azure Storage SDK voor python v 0.36 of eerder, verwijdert u de oudere SDK met `pip uninstall azure-storage` voordat u het meest recente pakket installeert.

Zie voor alternatieve installatie methoden [Azure SDK voor python](https://github.com/Azure/Azure-SDK-for-Python).

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Uw toepassing configureren voor toegang tot Queue Storage

# <a name="python-v12"></a>[Python-V12](#tab/python)

[`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient)Met het object kunt u werken met een wachtrij. Voeg de volgende code toe aan de bovenkant van een python-bestand waarin u via een programma toegang wilt krijgen tot een Azure-wachtrij:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2)Met het object kunt u met wacht rijen werken. Met de volgende code wordt een- `QueueService` object gemaakt. Voeg de volgende code toe aan de bovenkant van een python-bestand waarin u programmatisch toegang wilt krijgen Azure Storage:

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

Het `os` pakket biedt ondersteuning voor het ophalen van een omgevings variabele. Het `uuid` pakket biedt ondersteuning voor het genereren van een unieke id voor een wachtrij naam.

## <a name="create-a-queue"></a>Een wachtrij maken

De connection string wordt opgehaald uit de `AZURE_STORAGE_CONNECTION_STRING` omgevings variabele die u eerder hebt ingesteld.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Met de volgende code wordt een- `QueueClient` object gemaakt met behulp van de opslag Connection String.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Met de volgende code wordt een- `QueueService` object gemaakt met behulp van de opslag Connection String.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

## <a name="insert-a-message-into-a-queue"></a>Een bericht in een wachtrij invoegen

# <a name="python-v12"></a>[Python-V12](#tab/python)

Gebruik de-methode om een bericht in een wachtrij in te voegen [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Als u een bericht in een wachtrij wilt invoegen, gebruikt u de [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) methode om een nieuw bericht te maken en toe te voegen aan de wachtrij.

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

Azure-wachtrij berichten worden opgeslagen als tekst. Als u binaire gegevens wilt opslaan, moet u de functies van base64-code ring en decodering instellen voordat u een bericht in de wachtrij plaatst.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Configureer base64-functies voor coderen en decoderen op het client object van de wachtrij.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Configureer functies voor Base64-code ring en decodering op Queue Storage object.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="peek-at-messages"></a>Berichten bekijken

# <a name="python-v12"></a>[Python-V12](#tab/python)

U kunt berichten bekijken zonder ze uit de wachtrij te verwijderen door de methode aan te roepen [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) . Deze methode bekijkt standaard één bericht.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

U kunt berichten bekijken zonder ze uit de wachtrij te verwijderen door de methode aan te roepen [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#peek-messages-queue-name--num-messages-none--timeout-none-) . Deze methode bekijkt standaard één bericht.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>De inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een taak vertegenwoordigt, kunt u deze functie gebruiken om de status van de taak bij te werken.

# <a name="python-v12"></a>[Python-V12](#tab/python)

De volgende code gebruikt de [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) methode om een bericht bij te werken. De time-out voor de zicht baarheid is ingesteld op 0, wat betekent dat het bericht onmiddellijk wordt weer gegeven en dat de inhoud wordt bijgewerkt.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

De volgende code gebruikt de [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) methode om een bericht bij te werken. De time-out voor de zicht baarheid is ingesteld op 0, wat betekent dat het bericht onmiddellijk wordt weer gegeven en dat de inhoud wordt bijgewerkt.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen

U kunt een schatting ophalen van het aantal berichten in de wachtrij.

# <a name="python-v12"></a>[Python-V12](#tab/python)

De [get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) -methode retourneert wachtrij eigenschappen met inbegrip van de `approximate_message_count` .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

De [`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-queue-metadata-queue-name--timeout-none-) methode retourneert wachtrij-eigenschappen, inclusief `approximate_message_count` .

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

Het resultaat is alleen van benadering omdat berichten kunnen worden toegevoegd of verwijderd nadat de service op uw verzoek reageert.

## <a name="dequeue-messages"></a>Bericht uit een wachtrij verwijderen

Een bericht uit een wachtrij verwijderen in twee stappen. Als uw code een bericht niet kan verwerken, zorgt dit proces met twee stappen ervoor dat u hetzelfde bericht kunt ophalen en het opnieuw probeert. Roep aan `delete_message` nadat het bericht is verwerkt.

# <a name="python-v12"></a>[Python-V12](#tab/python)

Wanneer u [receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)aanroept, wordt standaard het volgende bericht in de wachtrij weer gegeven. Een bericht dat wordt geretourneerd van is niet `receive_messages` zichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-)aanroepen.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Wanneer u [get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)aanroept, wordt standaard het volgende bericht in de wachtrij weer gegeven. Een bericht dat wordt geretourneerd van is niet `get_messages` zichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#delete-message-queue-name--message-id--pop-receipt--timeout-none-)aanroepen.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht.

# <a name="python-v12"></a>[Python-V12](#tab/python)

In het volgende code voorbeeld wordt de [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) methode gebruikt om berichten in batches op te halen. Vervolgens wordt elk bericht binnen elke batch verwerkt met behulp van een geneste `for` lus. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

In het volgende code voorbeeld wordt de [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) methode gebruikt om 16 berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `for` lus verwerkt. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

# <a name="python-v12"></a>[Python-V12](#tab/python)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de- [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) methode aan.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de- [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2#delete-queue-queue-name--fail-not-exist-false--timeout-none-) methode aan.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Queue Storage hebt geleerd, volgt u deze koppelingen voor meer informatie.

- [Naslag informatie voor Azure Queue Storage python API](/python/api/azure-storage-queue)
- [Python-ontwikkelaarscentrum](https://azure.microsoft.com/develop/python/)
- [Naslag informatie over Azure Storage REST API](/rest/api/storageservices/)
