---
title: Gebeurtenissen verzenden of ontvangen van Azure Event Hubs met behulp van .NET (meest recente versie)
description: Dit artikel bevat een overzicht van het maken van een .NET Core-toepassing die gebeurtenissen verzend/ontvangt naar/van Azure Event Hubs met behulp van het meest recente Azure.Messaging.EventHubs-pakket.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 36551a52bc095a37aa5176ab945dd5d27c640954
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504607"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Gebeurtenissen verzenden naar en ontvangen van Azure Event Hubs - .NET (Azure.Messaging.EventHubs) 
In deze quickstart ziet u hoe u gebeurtenissen kunt verzenden naar en ontvangen van een Event Hub met behulp van de .NET-bibliotheek **Azure.Messaging.EventHubs**. 

> [!IMPORTANT]
> In deze snelstartgids wordt gebruik gemaakt van de nieuwe **Azure.Messaging.EventHubs**-bibliotheek. Zie [Event Hubs verzenden en ontvangen met de Microsoft.Azure.EventHubs-bibliotheek](event-hubs-dotnet-standard-get-started-send-legacy.md) voor een quickstart die gebruikmaakt van de oude **Microsoft.Azure.EventHubs**-bibliotheek. 



## <a name="prerequisites"></a>Vereisten
Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u het [Event Hubs-overzicht](event-hubs-about.md) voordat u deze quickstart uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, met inbegrip van Azure Event Hubs, hebt u een abonnement nodig.  Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen als MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. De Azure Event Hubs-clientbibliotheek maakt gebruik van nieuwe functies die in C# 8.0 zijn geïntroduceerd.  U kunt de bibliotheek nog steeds gebruiken met eerdere versies van C#, maar de nieuwe syntaxis is dan niet beschikbaar. Als u gebruik wilt maken van de volledige syntaxis, is het raadzaam om te compileren met [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 of hoger en de [taalversie](/dotnet/csharp/language-reference/configure-language-version#override-a-default) ingesteld op `latest`. Als u Visual Studio gebruikt, weet dan dat de versies vóór Visual Studio 2019 niet compatibel met de hulpprogramma's die nodig zijn om C# 8.0 projecten te bouwen. Visual Studio 2019, met inbegrip van de gratis Community-editie, [hier](https://visualstudio.microsoft.com/vs/) worden gedownload.
- **Een Event Hubs-naamruimte en een Event Hub maken**. In de eerste stap gebruikt u [Azure Portal](https://portal.azure.com) om een naamruimte van het type Event Hubs te maken en de beheerreferenties te verkrijgen die de toepassing nodig heeft om met de Event Hub te communiceren. Volg de procedure in [dit artikel](event-hubs-create.md) om een naamruimte en een Event Hub te maken. Haal vervolgens de **verbindingsreeks voor de Event Hubs-naamruimte** op door de instructies in het artikel te volgen: [Verbindingstekenreeks ophalen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). U gebruikt de verbindingsreeks later in deze quickstart.

## <a name="send-events"></a>Gebeurtenissen verzenden 
In deze sectie wordt beschreven hoe u een .NET Core-consoletoepassing maakt voor het verzenden van gebeurtenissen naar een Event Hub. 

### <a name="create-a-console-application"></a>Een consoletoepassing maken

1. Start Visual Studio 2019. 
1. Selecteer **Een nieuw project maken**. 
1. Voer in het dialoogvenster **Een nieuw project maken** de volgende stappen uit: Als dit dialoogvenster niet wordt weergegeven, selecteert u in het menu **Bestand**, **Nieuw** en vervolgens **Project**. 
    1. Selecteer de programmeertaal **C#** .
    1. Selecteer **Console** als het type toepassing. 
    1. Selecteer **Console-app (.NET Core)** in de lijst met resultaten. 
    1. Selecteer vervolgens **Volgende**. 

        ![Het dialoogvenster New Project](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Voer **EventHubsSender** in als projectnaam, **EventHubsQuickStart** als naam van de oplossing en selecteer **OK** om het project te maken. 

    ![C# > Console-app](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

1. Selecteer **Tools** > **NuGet-pakketbeheer** > **Package Manager Console** in het menu. 
1. Voer de volgende opdracht uit om het NuGet-pakket **Azure.Messaging.EventHubs** te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Code schrijven om berichten te verzenden naar de event hub

1. Voeg aan het begin van het bestand `using`Program.cs **de volgende**-instructies toe:

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Voeg aan de `Program`-klasse constanten toe voor de Event Hubs-verbindingsreeks en de naam van de Event Hub. Vervang de tijdelijke aanduidingen tussen de haakjes door de juiste waarden die zijn verkregen bij het maken van de Event Hub. Zorg ervoor dat `{Event Hubs namespace connection string}` de verbindingsreeks op naamruimteniveau is en niet de event hub-tekenreeks. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Vervang de `Main`-methode door de volgende `async Main`-methode. Zie de opmerkingen bij de code voor meer informatie. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Bouw het project en controleer of er geen fouten zijn.
6. Voer het programma uit en wacht op het bevestigingsbericht. 
7. In de Azure-portal kunt u controleren of de Event Hub de berichten heeft ontvangen. Schakel over naar de weergave **Berichten** in de sectie **Metrische gegevens**. U moet de pagina vernieuwen om de grafiek bij te werken. Het kan een paar seconden duren voordat wordt weergegeven dat de berichten zijn ontvangen. 

    [![Controleren of de Event Hub de berichten heeft ontvangen](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Zie [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md) voor de volledige broncode met meer informatieve opmerkingen

## <a name="receive-events"></a>Gebeurtenissen ontvangen
In deze sectie ziet u hoe u een .NET Core-consoletoepassing schrijft die met gebeurtenisprocessor berichten ontvangt van een Event Hub. De gebeurtenisprocessor vereenvoudigt het ontvangen van gebeurtenissen van Event Hubs door permanente controlepunten en parallelle ontvangst van deze Event Hubs te beheren. Een gebeurtenisprocessor is gekoppeld aan een specifieke Event hub en een consumentengroep. Er worden gebeurtenissen van meerdere partities in de Event Hub ontvangen en de worden aan een handler-gemachtigde doorgegeven voor verwerking met code die u opgeeft. 


> [!WARNING]
> Als u deze code op Azure Stack Hub uitvoert, treden er runtimefouten op tenzij u zich richt op een specifieke versie van de Storage-API. Dat komt doordat de Event Hubs-SDK de meest recente Azure Storage-API gebruikt die beschikbaar is in Azure maar die niet beschikbaar is op uw Azure Stack Hub-platform. Azure Stack Hub biedt mogelijk ondersteuning voor een andere versie van de Storage Blob-SDK dan de versies die doorgaans in Azure beschikbaar zijn. Als u Azure Blob-opslag gebruikt als controlepuntopslag, controleert u de [ondersteunde versie van de Azure Storage-API voor uw build van Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) en stelt u die versie in uw code als doel in. 
>
> Als u bijvoorbeeld op Azure Stack Hub versie 2005 uitvoert, is de hoogste beschikbare versie van de Storage-service versie 2019-02-02. De Event Hubs-SDK-clientbibliotheek maakt standaard gebruik van de hoogste beschikbare versie op Azure (2019-07-07 op het moment van de release van de SDK). In dit geval moet u naast de volgende stappen in deze sectie ook code toevoegen om de API-versie van de Storage-service te richten op 2019-02-02. Zie [dit voorbeeld op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) voor een voorbeeld van hoe u een specifieke versie van Storage-API instelt. 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Een Azure Storage en een blobcontainer maken
In deze quickstart gebruikt u Azure Storage als controlepuntopslag. Volg deze stappen om een Azure Storage-account te maken. 

1. [Een Azure Storage-account maken](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Een blobcontainer maken](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [De verbindingsreeks voor het opslagaccount ophalen](../storage/common/storage-configure-connection-string.md)

    Noteer de verbindingsreeks en de naam van de container. U gebruikt deze in de receive-code. 


### <a name="create-a-project-for-the-receiver"></a>Een project maken voor de ontvanger

1. Klik in het Solution Explorer-venster met de rechtermuisknop op de oplossing **EventHubQuickStart**, wijs naar **Toevoegen** en selecteer **Nieuw project**. 
1. Selecteer **Consoletoepassing (.NET Core)** en selecteer **Volgende**. 
1. Voer **EventHubsReceiver** in als **Projectnaam** en selecteer **Maken**. 

### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

1. Selecteer **Tools** > **NuGet-pakketbeheer** > **Package Manager Console** in het menu. 
1. Voer de volgende opdracht uit om het NuGet-pakket **Azure.Messaging.EventHubs** te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Voer de volgende opdracht uit om het NuGet-pakket **Azure.Messaging.EventHubs.Processor** te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>De main-methode bijwerken 

1. Voeg aan het begin van het bestand `using`Program.cs **de volgende**-instructies toe.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Voeg aan de `Program`-klasse constanten toe voor de Event Hubs-verbindingsreeks en de naam van de Event Hub. Vervang de tijdelijke aanduidingen tussen de haakjes door de juiste waarden die zijn verkregen bij het maken van de Event Hub. Vervang tijdelijke aanduidingen tussen de haakjes door de juiste waarden die u hebt verkregen bij het maken van de Event Hub en het opslagaccount (toegangssleutels - primaire verbindingsreeks). Zorg ervoor dat `{Event Hubs namespace connection string}` de verbindingsreeks op naamruimteniveau is en niet de event hub-tekenreeks.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Vervang de `Main`-methode door de volgende `async Main`-methode. Zie de opmerkingen bij de code voor meer informatie. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Voeg nu de volgende gebeurtenis- en foutafhandelingsmethoden aan de klasse toe. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Bouw het project en controleer of er geen fouten zijn.

    > [!NOTE]
    > Zie [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md) voor de volledige broncode met meer informatieve opmerkingen.
6. Voer de ontvangende toepassing uit. 
1. Als het goed is, ziet u nu een bericht dat de gebeurtenis is ontvangen. 

    ![Gebeurtenis ontvangen](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Deze gebeurtenissen zijn de drie gebeurtenissen die u eerder naar de Event Hub hebt verzonden door het verzendprogramma uit te voeren. 


## <a name="next-steps"></a>Volgende stappen
Bekijk de voorbeelden op GitHub. 

- [Event Hubs-voorbeelden op Github](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Voorbeelden van gebeurtenisprocessor op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Azure RBAC-voorbeeld (op rollen gebaseerd toegangsbeheer)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
