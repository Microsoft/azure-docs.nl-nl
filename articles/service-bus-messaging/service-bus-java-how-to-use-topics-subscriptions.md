---
title: Azure Service Bus-onderwerpen en -abonnementen met Java gebruiken (azure-messaging-servicebus)
description: In deze quickstart schrijft u Java-code die het pakket azure-messaging-servicebus gebruikt voor het verzenden van berichten naar een Azure Service Bus-onderwerp en ontvangt u vervolgens berichten van abonnementen op dit onderwerp.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 46dc6bed7e51a5157d7eb42dac75c0240d440780
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881615"
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
Voeg een verwijzing naar de Azure Service Bus-bibliotheek toe. De Java-clientbibliotheek voor Service Bus is beschikbaar in de [Centrale opslagplaats voor Maven](https://search.maven.org/search?q=a:azure-messaging-servicebus). U kunt naar deze bibliotheek verwijzen met behulp van de volgende afhankelijkheidsdeclaratie in uw Maven-projectbestand:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Code toevoegen om berichten naar het onderwerp te verzenden
1. Voeg de volgende `import`-instructies toe aan het onderwerp van het Java-bestand. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
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

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
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
Voer het programma uit om te controleren of the uitvoer lijkt op de volgende uitvoer:

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
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