---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van Java (verouderd)
description: Dit artikel bevat een overzicht van het maken van een Java-toepassing waarmee gebeurtenissen worden verzonden naar/ontvangen van Azure Event Hubs met behulp van het oude pakket azure-eventhubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: e86ca76f3eb661e1407a02b58e60b62b391f5702
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607980"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Java gebruiken om gebeurtenissen te verzenden of gebeurtenissen te ontvangen uit Azure Event Hubs (azure-eventhubs)

In deze quickstart leert u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van het Java-pakket **azure-eventhubs**.

> [!WARNING]
> In deze quickstart wordt gebruikgemaakt van de oude pakketten **azure-eventhubs** en **azure-eventhubs-eph**. Zie [Send and receive events using azure-messaging-eventhubs](event-hubs-java-get-started-send.md) (Gebeurtenissen verzenden en ontvangen met azure-messaging-eventhubs) voor een quickstart met de nieuwste **azure-messaging-eventhubs**. Als u uw toepassing van het oude naar een nieuw pakket wilt verplaatsen, raadpleegt u de [Guide to migrate from azure-eventhubs to azure-messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md) (Handleiding voor het migreren van azure-eventhubs naar azure-messaging-eventhubs). 

## <a name="prerequisites"></a>Vereisten

Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u het [Event Hubs-overzicht](event-hubs-about.md) voordat u deze quickstart uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, met inbegrip van Azure Event Hubs, hebt u een abonnement nodig.  Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen als MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- Een Java-ontwikkelomgeving. In deze quickstart wordt gebruikgemaakt van [Eclipse](https://www.eclipse.org/).
- **Een Event Hubs-naamruimte en een Event Hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Haal vervolgens de waarde van de toegangssleutel voor de Event Hub op door de instructies in het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de toegangssleutel in de code die u verderop in deze quickstart schrijft. De standaardsleutelnaam is: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie wordt beschreven hoe u een Java-toepassing maakt voor het verzenden van gebeurtenissen naar een Event Hub. 

> [!NOTE]
> U kunt deze snelstart als voorbeeld downloaden van de [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), de tekenreeksen `EventHubConnectionString` en `EventHubName` vervangen door uw event hub-waarden en deze uitvoeren. U kunt ook de stappen in deze quickstart volgen om uw eigen oplossing te maken.

### <a name="add-reference-to-azure-event-hubs-library"></a>Verwijzing naar Azure Event Hubs-bibliotheek toevoegen

De Java-clientbibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten in de [centrale opslagplaats voor Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). U kunt naar deze bibliotheek verwijzen met behulp van de volgende afhankelijkheidsdeclaratie in uw Maven-projectbestand:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Voor verschillende soorten build-omgevingen kunt u uitdrukkelijk de meest recente vrijgegeven JAR-bestanden verkrijgen bij de [centrale opslagplaats van Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Importeer als u een eenvoudige gebeurtenisuitgever wilt het pakket *com.microsoft.azure.eventhubs* voor de Event Hubs-clientklassen en het pakket *com.microsoft.azure.servicebus* voor utils-klassen zoals algemene uitzonderingen die worden gedeeld met de Azure Service Bus-berichtenclient. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. Voeg een klasse genaamd `SimpleSend` toe en voeg de volgende code aan de klasse toe:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {

    }
 }
```

### <a name="construct-connection-string"></a>De verbindingsreeks samenstellen

Gebruik de ConnectionStringBuilder-klasse om een verbindingsreekswaarde op te geven die moet worden doorgegeven aan het exemplaar van de Event Hubs-client. Vervang de tijdelijke aanduidingen door de waarden die u hebt verkregen tijdens het maken van de naamruimte en Event Hub:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Code schrijven voor het verzenden van gebeurtenissen

Maak een enkele gebeurtenis door een tekenreeks te transformeren naar de UTF-8 byte-codering. Maak vervolgens een nieuw exemplaar van de Event Hubs-client vanuit de verbindingsreeks en verzend het bericht:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);

        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Compileer het programma en voer het uit, en controleer of er geen fouten zijn.

Gefeliciteerd! U hebt nu berichten verzonden naar een Event Hub.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Bijlage: Hoe berichten worden doorgestuurd naar Event Hub-partities

Voordat berichten door consumenten worden opgehaald, moeten ze eerst worden gepubliceerd op de partities van de uitgevers. Wanneer berichten synchroon naar Event Hub worden gepubliceerd met behulp van de methode sendSync() in het objectcom.microsoft.azure.eventhubs.EventHubClient, kan het bericht naar een specifieke partitie worden verzonden of worden gedistribueerd naar alle beschikbare partities op een round-robin-wijze, afhankelijk van het feit of de partitiesleutel wel of niet is opgegeven.

Wanneer een tekenreeks die staat voor de partitiesleutel wordt opgegeven, wordt de sleutel gehasht om te bepalen aan welke partitie de gebeurtenis moet worden verzonden.

Wanneer de partitiesleutel niet is ingesteld, worden de berichten op een round-robin-wijze naar alle beschikbare partities verzonden

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Gebeurtenissen ontvangen
De code in deze zelfstudie is gebaseerd op de [EventProcessorSample-code op GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), die u kunt bekijken om de volledig werkende toepassing weer te geven.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Berichten ontvangen met EventProcessorHost in Java

**EventProcessorHost** is een Java-klasse die het ontvangen van gebeurtenissen van Event Hubs vereenvoudigt door permanente controlepunten en parallelle ontvangst van deze Event Hubs te beheren. Met EventProcessorHost kunt u gebeurtenissen splitsen over meerdere ontvangers, zelfs als deze worden gehost in verschillende knooppunten. In dit voorbeeld wordt het gebruik van EventProcessorHost gedemonstreerd voor één ontvanger.

### <a name="create-a-storage-account"></a>Create a storage account

U hebt een [Azure Storage-account][Azure Storage-account] nodig om EventProcessorHost te gebruiken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en selecteer **Een resource maken** aan de linkerkant van het scherm.
2. Selecteer **Opslag** en selecteer vervolgens **Opslagaccount**. Typ in het venster **Opslagaccount maken** een naam voor het opslagaccount. Vul de rest van de velden in, selecteer de gewenste regio en selecteer vervolgens **Maken**.

    ![Een opslagaccount maken in de Azure-portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Selecteer het zojuist gemaakte opslagaccount en selecteer vervolgens **Toegangssleutels**:

    ![Uw toegangssleutels ophalen in de Azure-portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Kopieer de key1-waarde naar een tijdelijke locatie. U gaat deze verderop in de zelfstudie gebruiken.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Maak een Java-project met behulp van EventProcessorHost

De Java-clientbibliotheek voor Event Hubs is beschikbaar voor gebruik in Maven-projecten uit de [centrale opslagplaats voor Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22), en hiernaar kan worden verwezen met de volgende afhankelijkheidsverklaring binnen het Maven-projectbestand: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Voor verschillende soorten build-omgevingen kunt u uitdrukkelijk de meest recente vrijgegeven JAR-bestanden verkrijgen bij de [centrale opslagplaats van Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. Maak voor het volgende voorbeeld eerst een nieuw Maven-project voor een console/shell-toepassing in uw favoriete Java-ontwikkelomgeving. De klasse heet `ErrorNotificationHandler`.     

    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Gebruik de volgende code om een nieuwe klasse te maken met de naam `EventProcessorSample`. Vervang de tijdelijke aanduidingen door de waarden die u hebt gebruikt toen u het Event Hub-account en opslagaccount maakte:

   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";

           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);

           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);

           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
                 try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
            {
                return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!

           System.out.println("End of sample");
       }
   }
   ```
3. Maak nog een klasse genaamd `EventProcessor`. Doe dit met de volgende code:

    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }

        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;

                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                               data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

In deze zelfstudie wordt één exemplaar van EventProcessorHost gebruikt. Voor een betere doorvoer raden we u aan om meerdere exemplaren van EventProcessorHost uit te voeren, bij voorkeur op verschillende machines.  Dit biedt ook redundantie. In die gevallen werken de verschillende exemplaren automatisch samen om de ontvangen gebeurtenissen gelijkmatig te verdelen. Als u wilt dat meerdere ontvangers *alle* gebeurtenissen verwerken, gebruik dan het concept **ConsumerGroup**. Wanneer er gebeurtenissen van verschillende computers worden ontvangen, kan het nuttig zijn om namen voor EventProcessorHost-exemplaren op te geven op basis van de computers waarop (of rollen waarin) ze zijn geïmplementeerd.

### <a name="publishing-messages-to-eventhub"></a>Berichten publiceren naar EventHub

Voordat berichten door consumenten worden opgehaald, moeten ze eerst worden gepubliceerd op de partities van de uitgevers. Wanneer berichten synchroon naar Event Hub worden gepubliceerd met behulp van de methode sendSync() in het objectcom.microsoft.azure.eventhubs.EventHubClient, kan het bericht naar een specifieke partitie worden verzonden of worden gedistribueerd naar alle beschikbare partities op een round-robin-wijze, afhankelijk van het feit of de partitiesleutel wel of niet is opgegeven.

Wanneer een tekenreeks die staat voor de partitiesleutel wordt opgegeven, wordt de sleutel gehasht om te bepalen aan welke partitie de gebeurtenis moet worden verzonden.

Wanneer de partitiesleutel niet is ingesteld, worden de berichten op een round-robin-wijze naar alle beschikbare partities verzonden

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Een aangepaste CheckpointManager voor EventProcessorHost (EPH) implementeren

De API biedt een mechanisme voor het implementeren van uw aangepaste controlepuntbeheer voor scenario's waarbij de standaard implementatie niet compatibel is met uw situatie.

Het standaard controlepuntbeheer maakt gebruik van blob-opslag, maar als u het controlepuntbeheer die door EPH wordt gebruikt overschrijft met uw eigen implementatie, kunt u een willekeurige opslagplaats gebruiken als back-up voor de implementatie van het controlepuntbeheer.

Een klasse maken die de interface com.microsoft.azure.eventprocessorhost.ICheckpointManager implementeert

Uw aangepaste implementatie van controlepuntbeheer gebruiken (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Binnen uw implementatie kunt u het standaard mechanisme voor controlepunten overschrijven en uw eigen controlepunten implementeren op basis van uw eigen gegevensarchief (zoals SQL Server, CosmosDB en Azure Cache voor Redis). Het is raadzaam dat de opslagplaats die wordt gebruikt voor back-up van de implementatie van controlepuntbeheer toegankelijk is voor alle EPH-instanties die gebeurtenissen verwerken voor de consumentengroep.

U kunt elke gegevensopslag gebruiken die beschikbaar is in uw omgeving.

De klasse com.microsoft.azure.eventprocessorhost.EventProcessorHost biedt twee constructors waarmee u het controlepuntbeheer voor uw EventProcessorHost kunt overschrijven.

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Functies en terminologie in Azure Event Hubs](event-hubs-features.md)
- [Veelgestelde vragen over Event Hubs](event-hubs-faq.md)
