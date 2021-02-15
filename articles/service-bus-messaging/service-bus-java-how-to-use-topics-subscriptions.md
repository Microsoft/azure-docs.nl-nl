---
title: Azure Service Bus-onderwerpen en -abonnementen met Java gebruiken (azure-messaging-servicebus)
description: In deze quickstart schrijft u Java-code die het pakket azure-messaging-servicebus gebruikt voor het verzenden van berichten naar een Azure Service Bus-onderwerp en ontvangt u vervolgens berichten van abonnementen op dit onderwerp.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.openlocfilehash: c5b930fb2c87a09a1f4801365936c62a7cf79f1d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516172"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Berichten verzenden naar een Azure Service Bus-onderwerp en berichten ontvangen van abonnementen op het onderwerp (Java)
In deze quickstart schrijft u Java-code die het pakket azure-messaging-servicebus gebruikt voor het verzenden van berichten naar een Azure Service Bus-onderwerp en ontvangt u vervolgens berichten van abonnementen op dit onderwerp.

> [!IMPORTANT]
> Deze quickstart maakt gebruik van het nieuwe pakket azure-messaging-servicebus. Raadpleeg [Berichten verzenden en ontvangen met azure-servicebus](service-bus-java-how-to-use-topics-subscriptions-legacy.md) voor een quickstart die gebruikmaakt van het oude azure-servicebus-pakket.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Volg de stappen in de [Quickstart: Gebruik Azure Portal om een Service Bus-onderwerp en abonnementen voor het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md). Noteer de verbindingsreeks, onderwerpnaam en abonnementsnaam. U gebruikt slechts één abonnement voor deze quickstart. 
- Installeer de [Azure-SDK voor Java][Azure SDK for Java]. Als u Eclipse gebruikt, kunt u de [Azure-toolkit voor Eclipse][Azure Toolkit for Eclipse] installeren die de Azure-SDK voor Java bevat. U kunt vervolgens de **Microsoft Azure-bibliotheken voor Java** toevoegen aan uw project. Als u IntelliJ gebruikt, raadpleegt u [De Azure-toolkit voor IntelliJ installeren](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
In deze sectie maakt u een Java-consoleproject en voegt u code toe om berichten te verzenden naar het onderwerp dat u hebt gemaakt. 

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

### <a name="add-code-to-send-messages-to-the-topic"></a>Code toevoegen om berichten naar het onderwerp te verzenden
1. Voeg de volgende `import`-instructies toe aan het onderwerp van het Java-bestand. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. In de klasse definieert u de variabelen die de verbindingstekenreeks en onderwerpnaam bevatten, zoals hieronder wordt weergegeven: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Vervang `<NAMESPACE CONNECTION STRING>` door de verbindingstekenreeks voor uw Service Bus-naamruimte. Vervang `<TOPIC NAME>` door de naam van het onderwerp.
3. Voeg in de klasse een methode toe met de naam `sendMessage` om een bericht naar het onderwerp te verzenden. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
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
1. Voeg een methode toe met de naam `sendMessageBatch` om berichten te verzenden naar het onderwerp dat u hebt gemaakt. Met deze methode maakt u een `ServiceBusSenderClient` voor het onderwerp, roept u de methode `createMessages` aan om de lijst met berichten op te halen, bereidt u een of meer batches voor en stuurt u de batch(es) naar het onderwerp. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
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
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen
In deze sectie voegt u code toe om berichten uit een abonnement op het onderwerp op te halen. 

1. Voeg een methode toe met de naam `receiveMessages` om berichten van het abonnement te ontvangen. Met deze methode maakt u een `ServiceBusProcessorClient` voor het abonnement door een handler op te geven voor de verwerking van berichten en een andere voor het afhandelen van fouten. Vervolgens wordt de verwerker gestart, wordt er een paar seconden gewacht, worden de ontvangen berichten getoond en wordt de verwerker gestopt en gesloten.

    > [!IMPORTANT]
    > Vervang `ServiceBusTopicTest` in `ServiceBusTopicTest::processMessage` de code door de naam van uw klasse. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(ServiceBusTopicTest::processMessage)
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
1. Werk de methode `main` bij om de methoden `sendMessage`, `sendMessageBatch`en `receiveMessages` aan te roepen en `InterruptedException` te genereren.     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>De app uitvoeren
Voer het programma uit om te controleren of the uitvoer lijkt op de volgende uitvoer:

```console
Sent a single message to the topic: mytopic
Sent a batch of messages to the topic: mytopic
Starting the processor
Processing message. Session: e0102f5fbaf646988a2f4b65f7d32385, Sequence #: 1. Contents: Hello, World!
Processing message. Session: 3e991e232ca248f2bc332caa8034bed9, Sequence #: 2. Contents: First message
Processing message. Session: 56d3a9ea7df446f8a2944ee72cca4ea0, Sequence #: 3. Contents: Second message
Processing message. Session: 7bd3bd3e966a40ebbc9b29b082da14bb, Sequence #: 4. Contents: Third message
```

Op de pagina **Overzicht** voor de Service Bus-naamruimte in Azure Portal ziet u het aantal **inkomende** en **uitgaande** berichten. Mogelijk moet u ongeveer een minuut wachten en de pagina vervolgens vernieuwen om de meest recente waarden te zien. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Aantal inkomende en uitgaande berichten" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Ga naar het tabblad **Onderwerpen** in het venster midden-onder en selecteer het onderwerp om de pagina **Service Bus-onderwerp** te bekijken voor uw onderwerp. Op deze pagina ziet u vier inkomende en vier uitgaande berichten in de grafiek **Berichten**. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Inkomende en uitgaande berichten" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Als u de aanroep `receiveMessages` met een commentaarteken uitschakelt in de methode `main` en de app opnieuw uitvoert, ziet u op de pagina **Service Bus-onderwerp** 8 inkomende berichten (4 nieuwe) maar vier uitgaande berichten. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Bijgewerkte onderwerppagina" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Als u op deze pagina een abonnement selecteert, krijgt u toegang tot de pagina **Service Bus-abonnement**. Op deze pagina ziet u het aantal actieve berichten, het aantal onbestelbare berichten, en meer. In dit voorbeeld zijn er vier actieve berichten die nog niet zijn ontvangen door een ontvanger. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Aantal actieve berichten" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documentatie en voorbeelden:

- [Azure Service Bus-clientbibliotheek voor Java - Leesmij](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Voorbeelden op GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Naslaginformatie over de Java-API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage