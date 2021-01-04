---
title: Gebeurtenissen verzenden naar of ontvangen van Azure Event Hubs met behulp van Python (meest recente versie)
description: Dit artikel bevat een overzicht van het maken van een Python-toepassing waarmee gebeurtenissen worden verzonden naar/ontvangen van Azure Event Hubs met behulp van het nieuwste pakket azure-eventhub.
ms.topic: quickstart
ms.date: 02/11/2020
ms.openlocfilehash: f05f546f19a7944c049b97ba18065159db6fab67
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605561"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub"></a>Gebeurtenissen verzenden naar of gebeurtenissen ontvangen van Event Hubs met behulp van Python (azure-eventhub)
In deze quickstart ziet u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van het Python-pakket **azure-eventhub**.

## <a name="prerequisites"></a>Vereisten
Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u het [Event Hubs-overzicht](event-hubs-about.md) voordat u deze quickstart uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, met inbegrip van Azure Event Hubs, hebt u een abonnement nodig.  Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen als MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Python 2.7 of 3.5 of hoger, waarbij PIP is geïnstalleerd en bijgewerkt.
- Het Python-pakket voor Event Hubs. 

    Als u het pakket wilt installeren, voert u deze opdracht uit bij een opdrachtprompt met Python in het pad:

    ```cmd
    pip install azure-eventhub
    ```

    Installeer het volgende pakket om de gebeurtenissen te ontvangen met behulp van Azure Blob-opslag als controlepuntopslag:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Een Event Hubs-naamruimte en een Event Hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Haal vervolgens de **verbindingsreeks voor de Event Hubs-naamruimte** op door de instructies in het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingsreeks later in deze quickstart.

## <a name="send-events"></a>Gebeurtenissen verzenden
In deze sectie maakt u een Python-script voor het verzenden van gebeurtenissen naar de Event Hub die u eerder hebt gemaakt.

1. Open uw favoriete Python-editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/).
2. Maak een script met de naam *send.py*. Met dit script wordt een batch van gebeurtenissen verzonden naar de Event Hub die u eerder hebt gemaakt.
3. Plak de volgende code in *send.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
        # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Ga voor de volledige broncode, inclusief informatieve opmerkingen, naar de [pagina GitHub send_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).
    

## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze quickstart wordt Azure Blob Storage gebruikt als controlepuntopslag. De controlepuntopslag wordt gebruikt om controlepunten te behouden (dat wil zeggen, de laatste leesposities).  


> [!WARNING]
> Als u deze code op Azure Stack Hub uitvoert, treden er runtimefouten op tenzij u zich richt op een specifieke versie van de Storage-API. Dat komt doordat de Event Hubs-SDK de meest recente Azure Storage-API gebruikt die beschikbaar is in Azure maar die niet beschikbaar is op uw Azure Stack Hub-platform. Azure Stack Hub biedt mogelijk ondersteuning voor een andere versie van de Storage Blob-SDK dan de versies die doorgaans in Azure beschikbaar zijn. Als u Azure Blob-opslag gebruikt als controlepuntopslag, controleert u de [ondersteunde versie van de Azure Storage-API voor uw build van Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) en stelt u die versie in uw code als doel in. 
>
> Als u bijvoorbeeld op Azure Stack Hub versie 2005 uitvoert, is de hoogste beschikbare versie van de Storage-service versie 2019-02-02. De Event Hubs-SDK-clientbibliotheek maakt standaard gebruik van de hoogste beschikbare versie op Azure (2019-07-07 op het moment van de release van de SDK). In dit geval moet u naast de volgende stappen in deze sectie ook code toevoegen om de API-versie van de Storage-service te richten op 2019-02-02. Zie de [synchrone](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) en [asynchrone](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) voorbeelden in GitHub voor een voorbeeld van het instellen van een specifieke versie van de Storage-API. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Een Azure-opslagaccount en een blobcontainer maken
Maak een Azure-opslagaccount met daarin een blobcontainer door de volgende stappen te volgen:

1. [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Een blobcontainer maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De verbindingsreeks voor het opslagaccount ophalen](../storage/common/storage-configure-connection-string.md)

Zorg ervoor dat u de verbindingsreeks en de containernaam vastlegt voor later gebruik in de receive-code.


### <a name="create-a-python-script-to-receive-events"></a>Een Python-script maken om gebeurtenissen te ontvangen

In deze sectie maakt u een Python-script om gebeurtenissen van uw Event Hub te ontvangen:

1. Open uw favoriete Python-editor, bijvoorbeeld [Visual Studio Code](https://code.visualstudio.com/).
2. Maak een script met de naam *recv.py*.
3. Plak de volgende code in *recv.py*:

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method. Read from the beginning of the partition (starting_position: "-1")
            await client.receive(on_event=on_event,  starting_position="-1")

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Ga voor de volledige broncode, inclusief aanvullende informatieve opmerkingen, naar de [pagina GitHub recv_with_checkpoint_store_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>De ontvangende app uitvoeren

U voert het script uit door een opdrachtprompt te openen met Python in het pad en deze opdracht uit te voeren:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>De verzendende app uitvoeren

U voert het script uit door een opdrachtprompt te openen met Python in het pad en deze opdracht uit te voeren:

```bash
python send.py
```

In het ontvangstvenster worden de berichten weergegeven die naar de Event Hub zijn verzonden.


## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u gebeurtenissen asynchroon verzonden en ontvangen. Ga naar de pagina [GitHub sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples) om te leren hoe u gebeurtenissen synchroon verzendt en ontvangt.

Ga naar de [Azure Event Hubs-clientbibliotheek voor Python-voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples) voor alle voorbeelden (zowel synchrone als asynchrone) in GitHub.
