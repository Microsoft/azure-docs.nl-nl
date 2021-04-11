---
title: Berichten verzenden naar Azure Service Bus-onderwerpen met behulp van azure-messaging-servicebus
description: In deze quickstart wordt uitgelegd hoe u berichten kunt verzenden naar Azure Service Bus-onderwerpen met behulp van het pakket azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 79eb7783fd3daf546539dd5b9048f4e9f484374f
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279796"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Berichten verzenden naar een Azure Service Bus-onderwerp en berichten ontvangen van abonnementen op het onderwerp (.NET)
In deze zelf studie maakt u een C#-toepassing om de volgende taken uit te voeren:

1. Berichten verzenden naar een Service Bus onderwerp. 

    Een Service Bus onderwerp bevat een eind punt voor het verzenden van berichten aan Sender Applications. Een onderwerp kan een of meer abonnementen hebben. Elk abonnement van een onderwerp haalt een kopie op van het bericht dat naar het onderwerp wordt verzonden. Zie [Wat is Azure service bus?](service-bus-messaging-overview.md)voor meer informatie over service bus onderwerpen. 
1. Berichten ontvangen van een abonnement op het onderwerp. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Service Bus-onderwerpen en -abonnementen":::

    > [!Important]
    > Deze quickstart maakt gebruik van het nieuwe pakket **Azure.Messaging.ServiceBus**. Als u het oude pakket micro soft. Azure. ServiceBus gebruikt, raadpleegt u [berichten verzenden en ontvangen met het pakket micro soft. Azure. ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Volg de stappen in deze [Snelstartgids](service-bus-quickstart-topics-subscriptions-portal.md) om een service bus onderwerp en abonnementen op het onderwerp te maken. 

    > [!NOTE]
    > In deze zelf studie gebruikt u de connection string voor de naam ruimte, de onderwerpnaam en de naam van een van de abonnementen op het onderwerp.  
- [Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
In deze sectie maakt u een .NET core-console toepassing in Visual Studio, voegt u code toe om berichten te verzenden naar het Service Bus onderwerp dat u hebt gemaakt. 

### <a name="create-a-console-application"></a>Een consoletoepassing maken
Maak een .NET core-console toepassing met behulp van Visual Studio. 

1. Start Visual Studio.  
1. Als u de pagina **aan de slag** ziet, selecteert u **een nieuw project maken**. 
1. Voer op de pagina **een nieuw project maken** de volgende stappen uit: 
    1. Voor de programmeer taal selecteert u **C#**. 
    1. Selecteer voor het project type **console**. 
    1. Selecteer **console-app (.net core)** in de lijst met sjablonen. 
    1. Selecteer vervolgens **Volgende**. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="Een console-app-project maken":::
1. Voer de volgende stappen uit op de pagina **uw nieuwe project configureren** : 
    1. Voer bij **project naam** een naam in voor het project. 
    1. Selecteer bij **locatie** een locatie voor het project en de oplossings bestanden. 
    1. Voer bij **oplossings naam** een naam in voor de oplossing. Een Visual Studio-oplossing kan een of meer projecten hebben. In deze Quick Start heeft de oplossing slechts één project. 
    1. Selecteer **Maken** om het project te maken. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="De naam en locatie van het project en de oplossing invoeren":::    


### <a name="add-the-service-bus-nuget-package"></a>Het Service Bus NuGet-pakket toevoegen
1. Klik met de rechtermuisknop op het nieuwe project en selecteer **NuGet-pakketten beheren**.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="Het menu NuGet-pakketten beheren":::        
1. Ga naar het tabblad **Bladeren** .
1. Zoek en selecteer **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Selecteer **Installeren** om de installatie uit te voeren.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Selecteer Service Bus NuGet-pakket.":::
5. Als het dialoog venster **voor beeld van wijzigingen** wordt weer gegeven, selecteert u **OK** om door te gaan. 
1. Als u de pagina **licentie acceptatie** ziet, selecteert u **Ik ga akkoord** om door te gaan. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>Code toevoegen om berichten naar het onderwerp te verzenden 

1. In het venster **Solution Explorer** dubbelklikt u op **Program. cs** om het bestand te openen in de editor. 
1. Voeg de volgende `using` instructies toe boven aan de definitie van de naam ruimte, vóór de klassen declaratie:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. `Program`Declareer de volgende variabelen in de-klasse, boven de `Main` functie:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    Vervang de volgende waarden:
    - `<NAMESPACE CONNECTION STRING>` door de verbindingsreeks voor uw Service Bus-naamruimte
    - `<TOPIC NAME>` door de naam van het onderwerp
    - `<SUBSCRIPTION NAME>` door de naam van het abonnement

### <a name="send-a-single-message-to-the-topic"></a>Eén bericht verzenden naar het onderwerp
Voeg een methode toe `SendMessageToTopicAsync` met de naam van de `Program` klasse boven of onder de- `Main` methode. Met deze methode wordt één bericht naar het onderwerp verzonden.

```csharp
    static async Task SendMessageToTopicAsync()
    {
        // create a Service Bus client 
        await using (ServiceBusClient client = new ServiceBusClient(connectionString))
        {
            // create a sender for the topic
            ServiceBusSender sender = client.CreateSender(topicName);
            await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
            Console.WriteLine($"Sent a single message to the topic: {topicName}");
        }
    }
```

Deze methode voert de volgende stappen uit: 
1. Hiermee maakt u een [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) -object met behulp van de Connection String aan de naam ruimte. 
1. Maakt gebruik `ServiceBusClient` van het-object om een [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) -object te maken voor het opgegeven service bus onderwerp. Deze stap maakt gebruik van de methode [ServiceBusClient. CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) .
1. Vervolgens verzendt de methode één test bericht naar het Service Bus onderwerp met behulp van de methode [ServiceBusSender. SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) . 

### <a name="send-a-batch-of-messages-to-the-topic"></a>Een batch berichten verzenden naar het onderwerp
1. Voeg een methode toe `CreateMessages` met de naam voor het maken van een wachtrij (.net-wachtrij, niet Service Bus wachtrij) van berichten naar de `Program` klasse. Normaal gesproken ontvangt u deze berichten van verschillende onderdelen van uw toepassing. Hier maken we een wachtrij met voorbeeldberichten. Zie [Queue. in wachtrij](/dotnet/api/system.collections.queue.enqueue)als u niet bekend bent met .net-wacht rijen.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Voeg een methode toe `SendMessageBatchAsync` met de naam van de `Program` klasse en voeg de volgende code toe. Met deze methode worden voor een wachtrij met berichten een of meer batches voorbereid voor verzending naar het Service Bus-onderwerp. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```    

    Dit zijn de belang rijke stappen uit de code:
    1. Hiermee maakt u een [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) -object met behulp van de Connection String aan de naam ruimte. 
    1. Roept de methode [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) aan voor het `ServiceBusClient` object om een [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) -object te maken voor het opgegeven service bus onderwerp. 
    1. Roept de hulp methode `GetMessages` aan om een wachtrij met berichten op te halen die naar het service bus onderwerp moeten worden verzonden. 
    1. Hiermee maakt u een [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) met behulp van de [ServiceBusSender. CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync).
    1. Voeg berichten toe aan de batch met behulp van de [ServiceBusMessageBatch. TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). Wanneer de berichten aan de batch worden toegevoegd, worden ze uit de .NET-wachtrij verwijderd. 
    1. Hiermee verzendt u de batch berichten naar het Service Bus onderwerp met behulp van de methode [ServiceBusSender. SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) .

### <a name="update-the-main-method"></a>De methode Main bijwerken
Vervang de `Main()`-methode door de volgende **async** `Main`-methode. Hiermee worden de verzendmethoden aangeroepen om één bericht én een batch berichten naar het onderwerp te verzenden.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>De app testen om berichten naar het onderwerp te verzenden
1. Voer de toepassing uit. U moet de volgende uitvoer zien:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. Volg deze stappen in Azure Portal:
    1. Navigeer naar uw Service Bus-naamruimte. 
    1. Ga op de pagina **Overzicht**, in het deelvenster midden onder, naar het tabblad **Onderwerpen** en selecteer het Service Bus-onderwerp. In het volgende voorbeeld is dat `mytopic`.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Onderwerp selecteren":::
    1. Op de pagina **Service Bus-onderwerp**, in de grafiek **Berichten** in de onderste sectie **Metrische gegevens**, ziet u dat er vier binnenkomende berichten voor het onderwerp zijn. Als u de waarde niet ziet, wacht u een paar minuten en vernieuwt u de pagina om de bijgewerkte grafiek weer te geven. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Berichten die naar het onderwerp zijn verzonden" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Selecteer het abonnement in het onderste deelvenster. In het volgende voorbeeld is dat **S1**. Op de pagina **Service Bus-abonnement** ziet u dat het **Aantal actieve berichten** **4** is. Het abonnement heeft de vier berichten ontvangen die u naar het onderwerp hebt verzonden, maar ze zijn nog niet door een ontvanger opgehaald. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Berichten die zijn ontvangen in het abonnement" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Berichten van een abonnement ontvangen

1. Voeg de volgende methoden toe aan de klasse `Program` die berichten en eventuele fouten verwerken. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Voeg de volgende methode `ReceiveMessagesFromSubscriptionAsync` toe aan de klasse `Program`.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```

    Dit zijn de belang rijke stappen uit de code:
    1. Hiermee maakt u een [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) -object met behulp van de Connection String aan de naam ruimte. 
    1. Roept de methode [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) aan voor het `ServiceBusClient` object om een [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) -object te maken voor het opgegeven service bus onderwerp en de combi natie van abonnementen. 
    1. Hiermee worden handlers opgegeven voor de [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) -en [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) -gebeurtenissen van het `ServiceBusProcessor` object. 
    1. De verwerking van berichten wordt gestart door het aanroepen van de [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) voor het `ServiceBusProcessor` object. 
    1. Wanneer de gebruiker op een toets drukt om de verwerking te beëindigen, wordt de [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) voor het `ServiceBusProcessor` object aangeroepen. 
1. Voeg een aanroep van de methode `ReceiveMessagesFromSubscriptionAsync` toe aan de methode `Main`. Maak de methode `SendMessagesToTopicAsync` onzichtbaar als u alleen het ontvangen van berichten wilt testen. Als u dit niet doet, ziet u nog vier berichten die naar het onderwerp zijn verzonden. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>De app uitvoeren
Voer de toepassing uit. Wacht een minuut en druk vervolgens op een willekeurige toets om het ontvangen van berichten te stoppen. U zou de volgende uitvoer moeten zien (spatiebalk als toets). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Controleer de portal opnieuw. 

- Op de pagina **Service Bus-onderwerp** in de grafiek **Berichten** worden acht binnenkomende berichten en acht uitgaande berichten weergegeven. Als u deze aantallen niet ziet, wacht u een paar minuten en vernieuwt u de pagina om de bijgewerkte grafiek weer te geven. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Verzonden en ontvangen berichten" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Op de pagina **Service Bus-abonnement** ziet u dat het **Aantal actieve berichten** nul is. Dat komt omdat een ontvanger berichten van dit abonnement heeft ontvangen en de berichten heeft voltooid. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Aantal actieve berichten in het abonnement aan het einde" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documentatie en voorbeelden:

- [Azure Service Bus-clientbibliotheek voor .NET - Leesmij](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [.NET-voor beelden voor Azure Service Bus op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET-API-referentie](/dotnet/api/azure.messaging.servicebus?preserve-view=true)