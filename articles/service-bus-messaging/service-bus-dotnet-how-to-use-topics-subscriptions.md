---
title: Berichten verzenden naar Azure Service Bus-onderwerpen met behulp van azure-messaging-servicebus
description: In deze quickstart wordt uitgelegd hoe u berichten kunt verzenden naar Azure Service Bus-onderwerpen met behulp van het pakket azure-messaging-servicebus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 60504bcf9e2c3f9460eee9a2e72d18767c0cfa71
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631671"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Berichten verzenden naar een Azure Service Bus-onderwerp en berichten ontvangen van abonnementen op het onderwerp (.NET)
In deze zelfstudie leert u hoe u een .NET Core-consoletoepassing kunt maken die berichten verzendt naar een Service Bus-onderwerp en berichten ontvangt van een abonnement op het onderwerp. 

> [!Important]
> Deze quickstart maakt gebruik van het nieuwe pakket **Azure.Messaging.ServiceBus**. Zie [Send and receive messages using the Microsoft.Azure.ServiceBus package](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md) (Berichten verzenden en ontvangen met het Microsoft.Azure.ServiceBus-pakket) voor een quickstart die gebruikmaakt van het oude Microsoft.Azure.ServiceBus-pakket.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Een Azure-abonnement. U hebt een Azure-account nodig om deze zelfstudie te voltooien. U kunt uw [voordelen als Visual Studio- of MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) of u aanmelden voor een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Volg de stappen in de [Quickstart: Gebruik Azure Portal om een Service Bus-onderwerp en abonnementen voor het onderwerp te maken](service-bus-quickstart-topics-subscriptions-portal.md). Noteer de verbindingsreeks, onderwerpnaam en abonnementsnaam. U gebruikt slechts één abonnement voor deze quickstart. 

## <a name="send-messages-to-a-topic"></a>Berichten verzenden naar een onderwerp
In deze sectie maakt u een .NET Core-consoletoepassing in Visual Studio en voegt u code toe om berichten te verzenden naar het onderwerp dat u hebt gemaakt. 

### <a name="create-a-console-application"></a>Een consoletoepassing maken
Start Visual Studio en maak een nieuwe **Consoletoepassing (.NET Core)** voor C#. 

### <a name="add-the-service-bus-nuget-package"></a>Het Service Bus NuGet-pakket toevoegen

1. Klik met de rechtermuisknop op het nieuwe project en selecteer **NuGet-pakketten beheren**.
1. Selecteer **Bladeren**. Zoek en selecteer **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** .
1. Selecteer **Installeren** om de installatie te voltooien en sluit vervolgens de NuGet Package Manager.

### <a name="add-code-to-send-messages-to-the-topic"></a>Code toevoegen om berichten naar het onderwerp te verzenden 

1. Voeg in Program.cs de volgende `using` instructies aan het begin van de naamruimtedefinitie toe voor de klassedeclaratie:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. Declareer in de klasse `Program` de volgende variabelen:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    Vervang de volgende waarden:
    - `<NAMESPACE CONNECTION STRING>` door de verbindingsreeks voor uw Service Bus-naamruimte
    - `<TOPIC NAME>` door de naam van het onderwerp
    - `<SUBSCRIPTION NAME>` door de naam van het abonnement
2. Voeg een methode toe met de naam `SendMessageToTopicAsync` die één bericht naar het onderwerp verzendt. 

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
1. Voeg een methode met de naam `SendMessageBatchAsync` toe aan de klasse `Program` en voeg de volgende code toe. Met deze methode worden voor een wachtrij met berichten een of meer batches voorbereid voor verzending naar het Service Bus-onderwerp. 

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
1. Vervang de `Main()`-methode door de volgende **async** `Main`-methode. Hiermee worden de verzendmethoden aangeroepen om één bericht én een batch berichten naar het onderwerp te verzenden.  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. Voer de toepassing uit. U moet de volgende uitvoer zien:

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
- [Voorbeelden op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET-API-referentie](/dotnet/api/azure.messaging.servicebus?preserve-view=true)