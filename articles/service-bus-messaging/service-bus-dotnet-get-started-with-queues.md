---
title: Aan de slag met Azure Service Bus-wachtrijen (Azure.Messaging.ServiceBus)
description: In deze zelfstudie maakt u een .NET Core C#-toepassing om berichten te verzenden naar en te ontvangen van een Service Bus-wachtrij.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4335c1e81ead36d14ee1794fffbdd4cc1ff72a0a
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029605"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Berichten verzenden naar en berichten ontvangen van Azure Service Bus-wachtrijen (.NET)
In deze zelfstudie maakt u een .NET Core-consoletoepassing om berichten te verzenden naar en te ontvangen van een Service Bus-wachtrij met behulp van het pakket **Azure.Messaging.ServiceBus**. 

> [!Important]
> Deze quickstart maakt gebruik van het nieuwe pakket Azure.Messaging.ServiceBus. Zie [Gebeurtenissen verzenden en ontvangen met het Microsoft.Azure.ServiceBus-pakket](service-bus-dotnet-get-started-with-queues-legacy.md) voor een quickstart die gebruikmaakt van het oude Microsoft.Azure.ServiceBus-pakket.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) activeren of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Als u geen wachtrij hebt om te gebruiken, volgt u de stappen in het artikel [De Azure-portal gebruiken om een Service Bus-wachtrij te maken](service-bus-quickstart-portal.md) om een wachtrij te maken. Noteer de **verbindingsreeks** voor uw Service Bus-naamruimte en de naam van de **wachtrij** die u hebt gemaakt.

## <a name="send-messages-to-a-queue"></a>Berichten verzenden naar een wachtrij
In deze quickstart maakt u een C# .Net Core-consoletoepassing om berichten naar de wachtrij te verzenden.

### <a name="create-a-console-application"></a>Een consoletoepassing maken
Start Visual Studio en maak een nieuwe **Consoletoepassing (.NET Core)** voor C#. 

### <a name="add-the-service-bus-nuget-package"></a>Het Service Bus NuGet-pakket toevoegen

1. Klik met de rechtermuisknop op het nieuwe project en selecteer **NuGet-pakketten beheren**.
1. Selecteer **Bladeren**. Zoek en selecteer **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Selecteer **Installeren** om de installatie te voltooien en sluit vervolgens de NuGet Package Manager.

### <a name="add-code-to-send-messages-to-the-queue"></a>Code toevoegen om berichten naar de wachtrij te verzenden

1. Voeg in *Program.cs* de volgende `using`-instructies aan het begin van de naamruimtedefinitie toe voor de klassedeclaratie:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. Declareer in de klasse `Program` de volgende variabelen:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    Voer uw verbindingsstring in voor de naamruimte als de variabele `ServiceBusConnectionString`. Voer de naam van uw wachtrij in.

1. Vervang de `Main()`-methode door de volgende **async** `Main`-methode. Hiermee wordt de `SendMessagesAsync()`-methode aangeroepen die u in de volgende stap toevoegt om berichten naar de wachtrij te verzenden. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. Voeg direct na de `Main()` methode de volgende `SendMessagesAsync()` methode toe voor het uitvoeren van het werk van het verzenden van het aantal berichten dat is opgegeven door `numberOfMessagesToSend` (momenteel ingesteld op 10):

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. Voeg een methode met de naam `CreateMessages` toe om een wachtrij (.NET-wachtrij) te maken met berichten aan de klasse `Program`. Normaal gesproken ontvangt u deze berichten van verschillende onderdelen van uw toepassing. Hier maken we een wachtrij met voorbeeldberichten.

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
1. Voeg een methode met de naam `SendMessageBatchAsync` toe aan de klasse `Program` en voeg de volgende code toe. Met deze methode wordt een wachtrij met berichten genomen en worden een of meer batches voorbereid voor verzending naar de Service Bus-wachtrij. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. Vervang de `Main()`-methode door de volgende **async** `Main`-methode. Hiermee worden de verzendmethoden aangeroepen om één bericht én een batch berichten naar de wachtrij te verzenden. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. Voer de toepassing uit. Als het goed is, ziet u de volgende berichten. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. Volg deze stappen in Azure Portal:
    1. Navigeer naar uw Service Bus-naamruimte. 
    1. Selecteer op de pagina **Overzicht** de wachtrij in het deelvenster in het midden onderaan. 
    1. Let op de waarden in de sectie **Essentials**.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Berichten ontvangen met aantal en grootte" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Let op de volgende waarden:
    - De waarde voor **Aantal actieve berichten** voor de wachtrij is nu **4**. Telkens wanneer u deze zendtoepassing uitvoert zonder de berichten op te halen, wordt deze waarde verhoogd met 4.
    - De huidige grootte van de wachtrij verhoogt de **HUIDIGE** waarde in **Essentials** telkens wanneer de app berichten aan de wachtrij toevoegt.
    - U ziet in de grafiek **Berichten** in het onderste gedeelte **Metrische gegevens** dat er vier binnenkomende berichten voor de wachtrij zijn. 

## <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen
In deze sectie voegt u code toe om berichten uit de wachtrij op te halen.

1. Voeg de volgende methoden toe aan de klasse `Program` die berichten en eventuele fouten verwerken. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Voeg een methode met de naam `ReceiveMessagesAsync` toe aan de klasse `Program` en voeg de volgende code toe om berichten te ontvangen. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

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
1. Voeg vanuit de methode `Main` een aanroep toe aan `ReceiveMessagesAsync` methode. Maak de methode `SendMessagesAsync` onzichtbaar als u alleen het ontvangen van berichten wilt testen. Als u dit niet doet, ziet u nog vier berichten die naar de wachtrij worden verzonden. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>De app uitvoeren
Voer de toepassing uit. Wacht een minuut en druk vervolgens op een willekeurige toets om het ontvangen van berichten te stoppen. U zou de volgende uitvoer moeten zien (spatiebalk als toets). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

Controleer de portal opnieuw. 

- De waarden voor **Aantal actieve berichten** en **HUIDIGE** zijn nu **0**.
- U ziet in de grafiek **Berichten** in het onderste gedeelte **Metrische gegevens** dat er acht binnenkomende berichten en acht uitgaande berichten voor de wachtrij zijn. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Actieve berichten en grootte na ontvangst" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende documentatie en voorbeelden:

- [Azure Service Bus-clientbibliotheek voor .NET - Leesmij](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Voorbeelden op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET-API-referentie](https://docs.microsoft.com/dotnet/api/azure.messaging.servicebus?view=azure-dotnet-preview&preserve-view=true)

