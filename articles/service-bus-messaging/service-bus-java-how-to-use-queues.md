---
title: Azure Service Bus-wachtrijen gebruiken met Java (azure-messaging-servicebus)
description: In deze zelfstudie leert u hoe u Java gebruikt om berichten te verzenden naar en te ontvangen van een Azure Service Bus-wachtrij. U gebruikt het nieuwe pakket azure-messaging-servicebus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: d95c96e76a3463a77cc64234a909cc1e3d093837
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630221"
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
Voeg een verwijzing naar de Azure Service Bus-bibliotheek toe. De Java-clientbibliotheek voor Service Bus is beschikbaar in de [Centrale opslagplaats voor Maven](https://search.maven.org/search?q=a:azure-messaging-servicebus). U kunt naar deze bibliotheek verwijzen met behulp van de volgende afhankelijkheidsdeclaratie in uw Maven-projectbestand:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Code toevoegen om berichten naar de wachtrij te verzenden
1. Voeg de volgende `import`-instructies toe aan het onderwerp van het Java-bestand. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
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

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
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
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

Op de pagina **Overzicht** voor de Service Bus-naamruimte in Azure Portal ziet u het aantal **inkomende** en **uitgaande** berichten. Mogelijk moet u ongeveer een minuut wachten en de pagina vervolgens vernieuwen om de meest recente waarden te zien. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Aantal inkomende en uitgaande berichten" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Selecteer de wachtrij op de pagina **Overzicht** om naar de pagina **Service Bus-wachtrij** te gaan. U ziet ook de **inkomende** en **uitgaande** berichten op deze pagina. U ziet ook andere informatie, zoals de **huidige grootte** van de wachtrij, **maximum grootte**, **aantal actieve berichten**, enzovoort. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Details van wachtrij" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documentatie en voorbeelden:

- [Azure Service Bus-clientbibliotheek voor Java - Leesmij](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Voorbeelden op GitHub](https://docs.microsoft.com/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Naslaginformatie over de Java-API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
