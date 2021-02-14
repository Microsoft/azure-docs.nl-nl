---
title: Azure Service Bus-wachtrijen gebruiken met Java (azure-messaging-servicebus)
description: In deze zelfstudie leert u hoe u Java gebruikt om berichten te verzenden naar en te ontvangen van een Azure Service Bus-wachtrij. U gebruikt het nieuwe pakket azure-messaging-servicebus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: bfe7835bea4415085279fb77eb85d67ed3f5f0f3
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518603"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Berichten verzenden naar en berichten ontvangen van Azure Service Bus-wachtrijen (Java)
In deze quickstart maakt u een Java-app om berichten te verzenden naar en te ontvangen van een Azure Service Bus-wachtrij. 

> [!IMPORTANT]
> Deze quickstart maakt gebruik van het nieuwe pakket azure.messaging-servicebus. Raadpleeg [Berichten verzenden en ontvangen met azure-servicebus](service-bus-java-how-to-use-queues-legacy.md) voor een quickstart die gebruikmaakt van het oude azure-servicebus-pakket.


## <a name="prerequisites"></a>Vereisten
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen wachtrij hebt om te gebruiken, volgt u de stappen in het artikel [De Azure-portal gebruiken om een Service Bus-wachtrij te maken](service-bus-quickstart-portal.md) om een wachtrij te maken. Noteer de **verbindingsreeks** voor uw Service Bus-naamruimte en de naam van de **wachtrij** die u hebt gemaakt.
- Installeer de [Azure-SDK voor Java][Azure SDK for Java]. Als u Eclipse gebruikt, kunt u de [Azure-toolkit voor Eclipse][Azure Toolkit for Eclipse] installeren die de Azure-SDK voor Java bevat. U kunt vervolgens de **Microsoft Azure-bibliotheken voor Java** toevoegen aan uw project. Als u IntelliJ gebruikt, raadpleegt u [De Azure-toolkit voor IntelliJ installeren](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
In deze sectie maakt u een Java-consoleproject en voegt u code toe om berichten te verzenden naar de wachtrij die u eerder hebt gemaakt. 

### <a name="create-a-java-console-project"></a>Een Java-consoleproject maken
Maak een Java-project met Eclipse of een hulpprogramma van uw keuze. 

### <a name="configure-your-application-to-use-service-bus"></a>Uw toepassing configureren voor het gebruik van Service Bus
Verwijzingen toevoegen aan Azure core-en Azure Service Bus-bibliotheken. 

Als u een verduistering gebruikt en een Java-Console toepassing hebt gemaakt, converteert u uw Java-project naar een Maven: Klik met de rechter muisknop op het project in het venster **pakket Verkenner** en selecteer **Configure**  ->  **Convert to Maven project**. Voeg vervolgens afhankelijkheden toe aan deze twee bibliotheken, zoals wordt weer gegeven in het volgende voor beeld.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Code toevoegen om berichten naar de wachtrij te verzenden
1. Voeg de volgende `import`-instructies toe aan het onderwerp van het Java-bestand. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. In de klasse definieert u de variabelen die de verbindingstekenreeks en wachtrijnaam bevatten, zoals hieronder wordt weergegeven: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Vervang `<NAMESPACE CONNECTION STRING>` door de verbindingstekenreeks voor uw Service Bus-naamruimte. Vervang `<QUEUE NAME>` door de naam van de wachtrij.
3. Voeg in de klasse een methode toe met de naam `sendMessage` om een bericht naar de wachtrij te verzenden. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Voeg in de klasse een methode toe met de naam `createMessages` om een lijst met berichten te maken. Normaal gesproken ontvangt u deze berichten van verschillende onderdelen van uw toepassing. Hier maken we een lijst met voorbeeldberichten.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Voeg een methode toe met de naam `sendMessageBatch` om berichten te verzenden naar de wachtrij die u hebt gemaakt. Met deze methode maakt u een `ServiceBusSenderClient` voor de wachtrij, roept u de methode `createMessages` aan om de lijst met berichten op te halen, bereidt u een of meer batches voor en verzendt u de batches naar de wachtrij. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
In deze sectie voegt u code toe om berichten uit de wachtrij op te halen. 

1. Voeg een methode toe met de naam `receiveMessages` om berichten van de wachtrij te ontvangen. Met deze methode maakt u een `ServiceBusProcessorClient` voor de wachtrij door een handler op te geven voor de verwerking van berichten en een andere voor het afhandelen van fouten. Vervolgens wordt de verwerker gestart, wordt er een paar seconden gewacht, worden de ontvangen berichten getoond en wordt de verwerker gestopt en gesloten.

    > [!IMPORTANT]
    > Vervang `QueueTest` in `QueueTest::processMessage` de code door de naam van uw klasse. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }   
    ```
2. Voeg de `processMessage` methode toe om een bericht te verwerken dat is ontvangen van het service bus-abonnement. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Voeg de `processError` methode toe om fout berichten af te handelen.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
2. Werk de methode `main` bij om de methoden `sendMessage`, `sendMessageBatch`en `receiveMessages` aan te roepen en `InterruptedException` te genereren.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>De app uitvoeren
Wanneer u de toepassing uitvoert, ziet u de volgende berichten in het consolevenster. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

Op de pagina **Overzicht** voor de Service Bus-naamruimte in Azure Portal ziet u het aantal **inkomende** en **uitgaande** berichten. Mogelijk moet u ongeveer een minuut wachten en de pagina vervolgens vernieuwen om de meest recente waarden te zien. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Aantal inkomende en uitgaande berichten" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Selecteer de wachtrij op de pagina **Overzicht** om naar de pagina **Service Bus-wachtrij** te gaan. U ziet ook de **inkomende** en **uitgaande** berichten op deze pagina. U ziet ook andere informatie, zoals de **huidige grootte** van de wachtrij, **maximum grootte**, **aantal actieve berichten**, enzovoort. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Details van wachtrij" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documentatie en voorbeelden:

- [Azure Service Bus-clientbibliotheek voor Java - Leesmij](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Voorbeelden op GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Naslaginformatie over de Java-API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage