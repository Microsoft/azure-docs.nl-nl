---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van Java (meest recente versie)
description: Dit artikel bevat een overzicht van het maken van een Java-toepassing die gebeurtenissen verzend/ontvangt naar/van Azure Event Hubs met behulp van het meest recente azure-messaging-eventhubs-pakket.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 640f6c4dcb223e55e10f7cb5d7daaa44dbd41578
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172020"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Gebruik Java om gebeurtenissen te verzenden naar of te ontvangen van Azure Event Hubs (azure-messaging-eventhubs)
In deze quickstart ziet u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van het **azure-messaging-eventHubs** Java-pakket.

> [!IMPORTANT]
> In deze snelstartgids wordt gebruik gemaakt van het nieuwe **azure-messaging-eventhubs**-pakket. Zie [verzenden en ontvangen van gebeurtenissen met azure-eventhubs en azure-eventhubs-eph](event-hubs-java-get-started-send-legacy.md) voor een quickstart die gebruikmaakt van de oude **azure-eventhubs** en **azure-eventhubs-eph**-pakketten. 


## <a name="prerequisites"></a>Vereisten
Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u het [Event Hubs-overzicht](event-hubs-about.md) voordat u deze quickstart uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, met inbegrip van Azure Event Hubs, hebt u een abonnement nodig.  Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen als MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Een Java-ontwikkelomgeving. In deze quickstart wordt gebruikgemaakt van [Eclipse](https://www.eclipse.org/). Java Development Kit (JDK) met versie 8 of hoger is vereist. 
- **Een Event Hubs-naamruimte en een Event Hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Haal vervolgens de **verbindingsreeks voor de Event Hubs-naamruimte** op door de instructies in het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingsreeks later in deze quickstart.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie wordt beschreven hoe u een Java-toepassing maakt voor het verzenden van gebeurtenissen naar een Event Hub. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Verwijzing naar Azure Event Hubs-bibliotheek toevoegen

De Java-clientbibliotheek voor Event Hubs is beschikbaar in de [Centrale opslagplaats voor Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). U kunt naar deze bibliotheek verwijzen met behulp van de volgende afhankelijkheidsdeclaratie in uw Maven-projectbestand:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. Voeg een klasse genaamd `Sender` toe en voeg de volgende code aan de klasse toe:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Verbindingsreeks en Event Hub
Deze code gebruikt de verbindingsreeks voor de Event Hubs-naamruimte en de naam van de Event Hub om een Event Hubs-client samen te stellen. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Een Event Hubs-producentclient maken 
Met deze code wordt een producentclientobject gemaakt dat wordt gebruikt voor het produceren/verzenden van gebeurtenissen naar de Event Hub. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Een batch gebeurtenissen voorbereiden
Met deze code wordt een batch gebeurtenissen voorbereid. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>De batch met gebeurtenissen naar de Event Hub verzenden
Met deze code wordt de batch gebeurtenissen die u in de vorige stap hebt voorbereid, verzonden naar de Event Hub. De volgende codeblokken op de verzendbewerking. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Sluiten en opschonen
Met deze code wordt de producent afgesloten. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Volledige code voor het verzenden van gebeurtenissen
Dit is de volledige code voor het verzenden van gebeurtenissen naar de Event Hub. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Bouw het programma en controleer of er geen fouten zijn. U voert dit programma uit nadat u het ontvangersprogramma hebt uitgevoerd. 

## <a name="receive-events"></a>Gebeurtenissen ontvangen
De code in deze zelfstudie is gebaseerd op het [EventProcessorClient-voorbeeld op GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java), dat u kunt bekijken om de volledig werkende toepassing weer te geven.

> [!WARNING]
> Als u deze code op Azure Stack Hub uitvoert, treden er runtimefouten op tenzij u zich richt op een specifieke versie van de Storage-API. Dat komt doordat de Event Hubs-SDK de meest recente Azure Storage-API gebruikt die beschikbaar is in Azure maar die niet beschikbaar is op uw Azure Stack Hub-platform. Azure Stack Hub biedt mogelijk ondersteuning voor een andere versie van de Storage Blob-SDK dan de versies die doorgaans in Azure beschikbaar zijn. Als u Azure Blob-opslag gebruikt als controlepuntopslag, controleert u de [ondersteunde versie van de Azure Storage-API voor uw build van Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) en stelt u die versie in uw code als doel in. 
>
> Als u bijvoorbeeld op Azure Stack Hub versie 2005 uitvoert, is de hoogste beschikbare versie van de Storage-service versie 2019-02-02. De Event Hubs-SDK-clientbibliotheek maakt standaard gebruik van de hoogste beschikbare versie op Azure (2019-07-07 op het moment van de release van de SDK). In dit geval moet u naast de volgende stappen in deze sectie ook code toevoegen om de API-versie van de Storage-service te richten op 2019-02-02. Zie [dit voorbeeld op GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java) voor een voorbeeld van hoe u een specifieke versie van Storage-API instelt. 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Een Azure Storage en een blobcontainer maken
In deze quickstart gebruikt u Azure Storage (bijvoorbeeld Blob Storage) als controlepuntopslag. Controlepunten is een proces waarbij een gebeurtenisprocessor de positie van de laatste uitgevoerde gebeurtenis binnen een partitie markeert of doorvoert. Het markeren van een controlepunt wordt doorgaans uitgevoerd binnen de functie waarmee de gebeurtenissen worden verwerkt. Zie [Gebeurtenisprocessor](event-processor-balance-partition-load.md) voor meer informatie over controle punten.

Volg deze stappen om een Azure Storage-account te maken. 

1. [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Een blobcontainer maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De verbindingsreeks voor het opslagaccount ophalen](../storage/common/storage-configure-connection-string.md)

    Noteer de **verbindingsreeks** en de **naam van de container**. U gebruikt deze in de receive-code. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Event Hubs-bibliotheken toevoegen aan uw Java-project
Voeg de volgende afhankelijkheden toe in het pom.xml-bestand. 

- [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.1.1</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.0</version>
    </dependency>
</dependencies>
```

1. Voeg de volgende instructie **importeren** in boven aan het Java-bestand. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    import java.util.concurrent.TimeUnit;
    ```
2. Maak een klasse genaamd `Receiver` toe en voeg de reeks variabelen aan de klasse toe. Vervang de plaatsaanduidingen door de correcte waarden. 
    ```java
    private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
    private static final String eventHubName = "<EVENT HUB NAME>";
    private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
    private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    ```
3. Voeg de volgende methode `main` toe aan de klasse. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(STORAGE_CONNECTION_STRING) 
            .containerName(STORAGE_CONTAINER_NAME) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. Voeg de twee helper-methoden (`PARTITION_PROCESSOR` en `ERROR_HANDLER`) toe waarmee gebeurtenissen en fouten naar de klasse `Receiver` worden verwerkt. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. De volledige code moet er als volgt uitzien: 

    ```java

    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    import java.util.concurrent.TimeUnit;
    
    public class Receiver {
        
        private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
        private static final String eventHubName = "<EVENT HUB NAME>";
        private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
        private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    
        public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

            if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
                eventContext.updateCheckpoint();
            }
        };
        
        public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
            System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
                errorContext.getPartitionContext().getPartitionId(),
                errorContext.getThrowable());
        };
        
        public static void main(String[] args) throws Exception {
            BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
                .connectionString(STORAGE_CONNECTION_STRING)
                .containerName(STORAGE_CONTAINER_NAME)
                .buildAsyncClient();
    
            EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
                .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName)
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .processEvent(PARTITION_PROCESSOR)
                .processError(ERROR_HANDLER)
                .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient));
    
            EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

            System.out.println("Starting event processor");
            eventProcessorClient.start();
        
            System.out.println("Press enter to stop.");
            System.in.read();
        
            System.out.println("Stopping event processor");
            eventProcessorClient.stop();
            System.out.println("Event processor stopped.");
        
            System.out.println("Exiting process");
        }
        
    }
    ```
3. Bouw het programma en controleer of er geen fouten zijn. 

## <a name="run-the-applications"></a>De toepassingen uitvoeren
1. Voer de toepassing **ontvanger** uit.
1. Voer vervolgens de toepassing **afzender** uit. 
1. Controleer in het toepassingsvenster **ontvanger** of de gebeurtenissen worden weergeven die zijn gepubliceerd door de afzenderstoepassing.
1. Druk op **ENTER** in het ontvangerstoepassingsvenster om de toepassing te stoppen. 

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende voorbeelden op GitHub:

- [azure-messaging-eventhubs voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
