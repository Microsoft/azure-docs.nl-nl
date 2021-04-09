---
title: Vastgelegde gegevens van Azure Event Hubs lezen vanuit een Python-app (nieuwste versie)
description: In dit artikel leest u hoe u Python-code kunt schrijven om gegevens vast te leggen die naar een Event Hub worden verzonden en de vastgelegde gebeurtenisgegevens van een Azure-opslagaccount te lezen.
ms.topic: quickstart
ms.date: 01/04/2021
ms.openlocfilehash: acc2ce04add5fd837e9edc789e9616a9f04fb4b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97883194"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub"></a>Gegevens van Event Hubs vastleggen in Azure Storage en deze lezen met behulp van Python (azure-eventhub)

U kunt een Event Hub zo configureren dat de gegevens die naar de Event Hub worden verzonden, worden vastgelegd in een Azure-opslagaccount of Azure Data Lake Storage Gen 1 of Gen 2. In dit artikel leest u hoe u Python-code kunt schrijven om gebeurtenissen te verzenden naar een Event Hub en de vastgelegde gegevens te lezen vanuit **Azure Blob Storage**. Zie [Overzicht van de functie Capture van Event Hubs](event-hubs-capture-overview.md) voor meer informatie over deze functie.

In deze handleiding wordt gebruikgemaakt van de [Azure Python-SDK](https://azure.microsoft.com/develop/python/) om de functie Capture te demonstreren. De app *sender.py* verzendt gesimuleerde omgevingstelemetrie naar Event Hubs in JSON-indeling. De Event Hub gebruikt de functie Capture om deze gegevens in batches naar Blob Storage te schrijven. De app *capturereader.py* leest deze blobs en maakt een toe te voegen bestand voor elk apparaat. De app schrijft de gegevens vervolgens in CSV-bestanden.

In deze snelstart, gaat u het volgende doen: 

> [!div class="checklist"]
> * Een Azure Blob-opslagaccount en -container maken in Azure Portal.
> * Een Event Hubs-naamruimte maken behulp van Azure Portal.
> * Een Event Hub maken waarvoor de functie Capture is ingeschakeld en deze koppelen aan uw opslagaccount.
> * Gegevens naar uw Event Hub verzenden met behulp van een Python-script.
> * Bestanden uit Event Hubs Capture lezen en verwerken met behulp van een ander Python-script.

## <a name="prerequisites"></a>Vereisten

- Python met PIP en de volgende pakketten moeten zijn geïnstalleerd. De code in dit artikel is getest voor deze versies. 
    - Python 3.7
    - azure-eventhub 5.2.0
    - azure-storage-blob 12.6.0
    - avro-python3 1.10.1
- Een Azure-abonnement. Als u nog geen abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.  
- Een actieve Azure Event Hubs-naamruimte en Event Hub.
[Maak een Event Hubs-naamruimte en een Event Hub](event-hubs-create.md). Noteer de naam van de Event Hubs-naamruimte, de naam van de Event Hub en de primaire toegangssleutel voor de naamruimte. Zie [Een Event Hubs-verbindingsreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) om een toegangssleutel op te halen. De naam van de standaardsleutel is *RootManageSharedAccessKey*. Voor deze quickstart hebt u alleen de primaire sleutel nodig. De verbindingsreeks hebt u niet nodig.  
- Een Azure-opslagaccount, een blobcontainer in het opslagaccount en een verbindingsreeks naar het opslagaccount. Als u deze items niet hebt, gaat u als volgt te werk:  
    1. [Een Azure-opslagaccount maken](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [Een blobcontainer in het opslagaccount maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [De verbindingsreeks voor het opslagaccount ophalen](../storage/common/storage-configure-connection-string.md)

    Zorg ervoor dat u de verbindingsreeks en de containernaam vastlegt voor later gebruik in deze quickstart.  
- De functie Capture inschakelen voor de Event Hub. Volg hiervoor de instructies in [Event Hubs Capture inschakelen met behulp van Azure Portal](event-hubs-capture-enable-through-portal.md). Selecteer het opslagaccount en de blobcontainer die u in de vorige stap hebt gemaakt. U kunt de functie ook inschakelen wanneer u een Event Hub maakt.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Een Python-script maken om gebeurtenissen naar uw Event Hub te verzenden
In deze sectie maakt u een Python-script waarmee 200 gebeurtenissen (10 apparaten x 20 gebeurtenissen) naar een Event Hub worden verzonden. Deze gebeurtenissen zijn een sample van een omgevingsmeting die wordt verzonden in JSON-indeling. 

1. Open uw favoriete Python-editor, bijvoorbeeld [Visual Studio Code][Visual Studio Code].
2. Maak een script met de naam *sender.py*. 
3. Plak de volgende code in *sender.py*. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Wijzig de volgende waarden in de scripts:  
    * Vervang `EVENT HUBS NAMESPACE CONNECTION STRING` door de verbindingsreeks voor uw Event Hubs-naamruimte.  
    * Vervang `EVENT HUB NAME` door de naam van uw Event Hub.  
5. Voer het script uit om gebeurtenissen naar de Event Hub te verzenden.  
6. In de Azure-portal kunt u controleren of de Event Hub de berichten heeft ontvangen. Schakel over naar de weergave **Berichten** in de sectie **Metrische gegevens**. U moet de pagina vernieuwen om de grafiek bij te werken. Het kan een paar seconden duren voordat op de pagina wordt weergegeven dat de berichten zijn ontvangen. 

    [![Controleren of de Event Hub de berichten heeft ontvangen](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Een Python-script maken om uw Capture-bestanden te lezen
In dit voorbeeld worden de vastgelegde gegevens opgeslagen in Azure Blob-opslag. Met het script in deze sectie worden de vastgelegde gegevensbestanden van uw Azure-opslagaccount gelezen en worden CSV-bestanden gegenereerd die u eenvoudig kunt openen en weergeven. Er worden tien bestanden weergegeven in de huidige werkmap van de toepassing. Deze bestanden bevatten de omgevingsmetingen voor de tien apparaten. 

1. Maak in uw Python-editor een script met de naam *capturereader.py*. Met dit script worden de vastgelegde bestanden gelezen en wordt voor elk apparaat een bestand gemaakt om uitsluitend de gegevens voor dat apparaat in te schrijven.
2. Plak de volgende code in *capturereader.py*. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Vervang `AZURE STORAGE CONNECTION STRING` door de verbindingsreeks voor uw Azure-opslagaccount. De naam van de container die u in deze quickstart hebt gemaakt, is *Capture*. Als u een andere naam voor de container hebt gebruikt, vervangt u *capture* door de naam van de container in het opslagaccount. 

## <a name="run-the-scripts"></a>De scripts uitvoeren
1. Open een opdrachtprompt met Python in het pad en voer vervolgens de volgende opdrachten uit om pakketten te installeren die voor Python vereist zijn:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```

    > [!NOTE]
    > De code in dit artikel is getest voor deze versies. 
    > - Python 3.7
    > - azure-eventhub 5.2.0
    > - azure-storage-blob 12.6.0
    > - avro-python3 1.10.1
2. Wijzig uw map in de map waarin u *sender.py* en *capturereader.py* hebt opgeslagen en voer de volgende opdracht uit:
   
   ```
   python sender.py
   ```
   
   Met deze opdracht wordt een nieuw Python-proces gestart om het verzendprogramma uit te voeren.
3. Wacht enkele minuten tot de Capture is uitgevoerd en voer vervolgens de volgende opdracht uit in uw oorspronkelijke opdrachtvenster:
   
   ```
   python capturereader.py
   ```

   Deze Capture-processor maakt gebruik van de lokale map om alle blobs uit het opslagaccount en de container in te downloaden. Deze verwerkt alles wat niet leeg is en schrijft de resultaten als CSV-bestanden in de lokale map.

## <a name="next-steps"></a>Volgende stappen
Bekijk [Python-voorbeelden in GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
