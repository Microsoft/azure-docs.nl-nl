---
title: Berichten publiceren naar en verwerken van Atlas Kafka-onderwerpen van Azure Purview via Event Hubs met behulp van .NET
description: Dit artikel bevat een overzicht voor het maken van een .NET Core-toepassing die gebeurtenissen verzendt/ontvangt naar/van Apache Atlas Kafka-onderwerpen van Purview met behulp van het nieuwste Azure.Messaging.EventHubs-pakket.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 60c177c913c78dbcfcbfe1d465044b69b0e6dd2e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590414"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Berichten publiceren naar en verwerken van Atlas Kafka-onderwerpen van Azure Purview via Event Hubs met behulp van .NET 
In deze quickstart ziet u hoe u gebeurtenissen verzendt naar en ontvangt van Atlas Kafka-onderwerpen van Azure Purview via event hub met behulp van de **Azure.Messaging.EventHubs** .NET-bibliotheek. 

> [!IMPORTANT]
> Er wordt een beheerde Event Hub gemaakt als onderdeel van het maken van een Purview-account. Zie [Account maken voor purview.](create-catalog-portal.md) U kunt berichten publiceren naar het Kafka-onderwerp van de Event Hub ATLAS_HOOK en Purview verbruikt en verwerkt deze. Met Purview worden entiteitswijzigingen in het Kafka-onderwerp van event hubs ATLAS_ENTITIES en kan de gebruiker deze gebruiken en verwerken. In deze quickstart wordt gebruikgemaakt van de nieuwe **Azure.Messaging.EventHubs-bibliotheek.** 


## <a name="prerequisites"></a>Vereisten
Als u nog geen ervaring hebt met Azure Event Hubs, raadpleegt u het [Event Hubs-overzicht](../event-hubs/event-hubs-about.md) voordat u deze quickstart uitvoert. 

Voor het voltooien van deze snelstart moet aan de volgende vereisten worden voldaan:

- **Microsoft Azure-abonnement**. Als u Azure-services wilt gebruiken, met inbegrip van Azure Event Hubs, hebt u een abonnement nodig.  Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/free/) of uw voordelen als MSDN-abonnee gebruiken wanneer u [een account maakt](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. De Azure Event Hubs-clientbibliotheek maakt gebruik van nieuwe functies die in C# 8.0 zijn geïntroduceerd.  U kunt de bibliotheek nog steeds gebruiken met eerdere versies van C#, maar de nieuwe syntaxis is dan niet beschikbaar. Als u gebruik wilt maken van de volledige syntaxis, is het raadzaam om te compileren met [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 of hoger en de [taalversie](/dotnet/csharp/language-reference/configure-language-version#override-a-default) ingesteld op `latest`. Als u Visual Studio gebruikt, weet dan dat de versies vóór Visual Studio 2019 niet compatibel met de hulpprogramma's die nodig zijn om C# 8.0 projecten te bouwen. Visual Studio 2019, met inbegrip van de gratis Community-editie, [hier](https://visualstudio.microsoft.com/vs/) worden gedownload.

## <a name="publish-messages-to-purview"></a>Berichten publiceren naar Purview 
In deze sectie ziet u hoe u een .NET Core-consoletoepassing maakt voor het verzenden van gebeurtenissen naar een Purview via event hub **kafka-onderwerp ATLAS_HOOK.** 

## <a name="create-a-visual-studio-project"></a>Een Visual Studio-project maken

Maak vervolgens een C# .NET-consoletoepassing in Visual Studio:

1. Start **Visual Studio**.
2. Selecteer in het Startvenster de optie **Een nieuw project maken** > **Console-app (.NET Framework)** . .NET versie 4.5.2 of hoger is vereist.
3. Voer **bij Projectnaam** **PurviewKafkaProducer in.**
4. Selecteer **Maken** om het project te maken.

### <a name="create-a-console-application"></a>Een consoletoepassing maken

1. Start Visual Studio 2019. 
1. Selecteer **Een nieuw project maken**. 
1. Voer in het dialoogvenster **Een nieuw project maken** de volgende stappen uit: Als dit dialoogvenster niet wordt weergegeven, selecteert u in het menu **Bestand**, **Nieuw** en vervolgens **Project**. 
    1. Selecteer de programmeertaal **C#** .
    1. Selecteer **Console** als het type toepassing. 
    1. Selecteer **Console-app (.NET Core)** in de lijst met resultaten. 
    1. Selecteer vervolgens **Volgende**. 


### <a name="add-the-event-hubs-nuget-package"></a>Het Event Hubs NuGet-pakket toevoegen

1. Selecteer **Tools** > **NuGet-pakketbeheer** > **Package Manager Console** in het menu. 
1. Voer de volgende opdracht uit om het **NuGet-pakket Azure.Messaging.EventHubs** en **het Azure.Messaging.EventHubs.Producer** NuGet-pakket te installeren:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
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

2. Voeg constanten toe aan de `Program` klasse voor de Event Hubs connection string en Event Hub-naam. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    U kunt de Event Hub-naamruimte die is gekoppeld aan een purview-account, krijgen door te kijken naar de primaire/secundaire verbindingsreeksen van het Atlas Kafka-eindpunt op het tabblad Eigenschappen van het Account voor weergave.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Event Hub-naamruimte":::

    De naam van de Event Hub moet **ATLAS_HOOK** voor het verzenden van berichten naar Purview.

3. Vervang de `Main` methode door de volgende methode en voeg een toe om berichten naar `async Main` `async ProduceMessage` Purview te pushen. Zie de opmerkingen bij de code voor meer informatie. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Bouw het project en controleer of er geen fouten zijn.
6. Voer het programma uit en wacht op het bevestigingsbericht. 

    > [!NOTE]
    > Zie [dit bestand op de GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md) voor de volledige broncode met meer informatieve opmerkingen

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>Voorbeeldbericht Entity JSON maken om een SQL-tabel met twee kolommen te maken.

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>Berichten van Purview gebruiken
In deze sectie ziet u hoe u een .NET Core-consoletoepassing schrijft die met gebeurtenisprocessor berichten ontvangt van een Event Hub. U moet ATLAS_ENTITIES Event Hub gebruiken om berichten van Purview te ontvangen. De gebeurtenisprocessor vereenvoudigt het ontvangen van gebeurtenissen van Event Hubs door permanente controlepunten en parallelle ontvangsten van deze Event Hubs te beheren. 

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
1. Voer **PurviewKafkaConsumer in** als **projectnaam** en selecteer **Maken.** 

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
    
    U kunt de Event Hub-naamruimte die is gekoppeld aan een purview-account, krijgen door te kijken naar de primaire/secundaire verbindingsreeksen van het Atlas Kafka-eindpunt op het tabblad Eigenschappen van het Account voor weergave.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Event Hub-naamruimte":::

    De naam van de Event Hub moet **ATLAS_ENTITIES** voor het verzenden van berichten naar Purview.

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

### <a name="sample-message-received-from-purview"></a>Voorbeeldbericht ontvangen van Purview

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas ondersteunt momenteel de volgende bewerkingstypen: **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2**. Het pushen van berichten naar Purview is momenteel standaard ingeschakeld. Als het scenario betrekking heeft op het lezen van Purview, neem dan contact met ons op, aangezien dit in de lijst met toegestane informatie moet worden vermeld. (geef de abonnements-id en de naam van het account Voor beeld op).


## <a name="next-steps"></a>Volgende stappen
Bekijk de voorbeelden op GitHub. 

- [Event Hubs-voorbeelden op Github](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Voorbeelden van gebeurtenisprocessor op GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Atlas-inleiding tot meldingen](https://atlas.apache.org/2.0.0/Notifications.html)