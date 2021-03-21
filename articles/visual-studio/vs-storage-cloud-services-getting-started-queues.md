---
title: Aan de slag met Queue Storage met behulp van Visual Studio (Cloud Services)
description: Aan de slag met Azure Queue Storage in een Cloud service project in Visual Studio nadat u verbinding hebt gemaakt met een opslag account met behulp van Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: da587aac-5e64-4e9a-8405-44cc1924881d
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 94f248edfebd6c6fedb78a54eee220c0ef38b4ab
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95545858"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Aan de slag met aan Azure Queue Storage en Visual Studio verbonden services (cloudserviceprojecten)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht
In dit artikel wordt beschreven hoe u aan de slag gaat met Azure Queue Storage in Visual Studio nadat u een Azure-opslag account in een Cloud Services-project hebt gemaakt of gerefereerd met behulp van het dialoog venster **verbonden services toevoegen** van Visual Studio.

We laten u zien hoe u een wachtrij in code kunt maken. We laten ook zien hoe u eenvoudige wachtrij bewerkingen kunt uitvoeren, zoals het toevoegen, wijzigen, lezen en verwijderen van wachtrij berichten. De voor beelden zijn geschreven in C#-code en gebruiken de [Microsoft Azure Storage-client bibliotheek voor .net](/previous-versions/azure/dn261237(v=azure.100)).

Met de bewerking **verbonden services toevoegen** installeert u de juiste NuGet-pakketten voor toegang tot Azure Storage in uw project en voegt u de Connection String voor het opslag account toe aan uw project configuratie bestanden.

* Zie [aan de slag met Azure Queue Storage met behulp van .net](../storage/queues/storage-dotnet-how-to-use-queues.md) voor meer informatie over het bewerken van wacht rijen in code.
* Zie [opslag documentatie](https://azure.microsoft.com/documentation/services/storage/) voor algemene informatie over Azure Storage.
* Raadpleeg de [documentatie van Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/) voor algemene informatie over Azure Cloud Services.
* Zie [ASP.net](https://www.asp.net) voor meer informatie over het Program meren van ASP.NET-toepassingen.

Azure Queue Storage is een service voor de opslag van grote aantallen berichten die via HTTP of HTTPS overal vandaan kunnen worden opgevraagd met geverifieerde aanroepen. Een enkel wachtrijbericht mag maximaal 64 KB groot zijn en een wachtrij kan miljoenen berichten bevatten, tot de totale capaciteitslimiet van een opslagaccount.

## <a name="access-queues-in-code"></a>Toegang tot wacht rijen in code
Als u toegang wilt krijgen tot wacht rijen in Visual Studio Cloud Services-projecten, moet u de volgende items toevoegen aan een C#-bron bestand dat toegang heeft tot de Azure-wachtrij opslag.

1. Zorg ervoor dat de naam ruimte declaraties boven in het C#-bestand de volgende **using** -instructies bevatten.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
2. Een **Cloud Storage account** -object ophalen dat de gegevens van uw opslag account vertegenwoordigt. Gebruik de volgende code om de gegevens van uw opslag connection string en het opslag account op te halen uit de Azure-service configuratie.
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
3. Een **CloudQueueClient** -object ophalen om te verwijzen naar de wachtrij objecten in uw opslag account.  
   
    ```csharp
    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
4. Een **CloudQueue** -object ophalen om te verwijzen naar een specifieke wachtrij.
   
    ```csharp
    // Get a reference to a queue named "messageQueue"
    CloudQueue messageQueue = queueClient.GetQueueReference("messageQueue");
    ```

**Opmerking:** Gebruik alle bovenstaande code vóór de code in de volgende voor beelden.

## <a name="create-a-queue-in-code"></a>Een wachtrij maken in code
Als u de wachtrij in code wilt maken, voegt u een aanroep naar **CreateIfNotExists** toe.

```csharp
// Create the CloudQueue if it does not exist
messageQueue.CreateIfNotExists();
```

## <a name="add-a-message-to-a-queue"></a>Een bericht aan een wachtrij toevoegen
Als u een bericht wilt invoegen in een bestaande wachtrij, maakt u een nieuw **CloudQueueMessage** -object en roept u vervolgens de methode **AddMessage** aan.

U kunt een **CloudQueueMessage** -object maken op basis van een teken reeks (in UTF-8-indeling) of een byte matrix.

Hier volgt een voor beeld waarin het bericht ' Hello, World ' wordt ingevoegd.

```csharp
// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
messageQueue.AddMessage(message);
```

## <a name="read-a-message-in-a-queue"></a>Een bericht in een wachtrij lezen
U kunt het bericht vooraan in een wachtrij bekijken zonder het uit de wachtrij te verwijderen, door de methode **PeekMessage** aan te roepen.

```csharp
// Peek at the next message
CloudQueueMessage peekedMessage = messageQueue.PeekMessage();
```

## <a name="read-and-remove-a-message-in-a-queue"></a>Een bericht in een wachtrij lezen en verwijderen
Met uw code kan een bericht uit een wachtrij in twee stappen worden verwijderd (uit de wachtrij).

1. Roep **GetMessage** aan om het volgende bericht in een wachtrij op te halen. Een bericht dat wordt geretourneerd door **GetMessage**, wordt onzichtbaar voor andere codes die berichten lezen uit deze wachtrij. Standaard blijft het bericht onzichtbaar gedurende 30 seconden.
2. Roep **DeleteMessage** aan om het verwijderen van het bericht uit de wachtrij te volt ooien.

Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. Met de volgende code wordt **DeleteMessage** direct aangeroepen nadat het bericht is verwerkt.

```csharp
// Get the next message in the queue.
CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

// Process the message in less than 30 seconds

// Then delete the message.
await messageQueue.DeleteMessage(retrievedMessage);
```


## <a name="use-additional-options-to-process-and-remove-queue-messages"></a>Gebruik aanvullende opties om wachtrij berichten te verwerken en te verwijderen
Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen.

* U kunt een batch berichten ontvangen (Maxi maal 32).
* U kunt een langere of korte time-out voor onzichtbaarheid instellen, waardoor uw code meer of minder tijd nodig is om elk bericht volledig te verwerken. In het volgende codevoorbeeld wordt de methode **GetMessages** gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht verwerkt met behulp van een **foreach**-lus. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht. Houd voor ogen dat de periode van 5 minuten voor alle berichten op hetzelfde moment start. Nadat er 5 minuten zijn verstreken sinds de aanroep van **GetMessages**, worden dus alle berichten die niet zijn verwijderd, opnieuw zichtbaar.

Hier volgt een voorbeeld:

```csharp
foreach (CloudQueueMessage message in messageQueue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.

    // Then delete the message after processing
    messageQueue.DeleteMessage(message);

}
```

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen
U kunt een schatting ophalen van het aantal berichten in de wachtrij. De methode **FetchAttributes** vraagt de Queue-service de wachtrij-kenmerken, zoals het aantal berichten, op te halen. De eigenschap **ApproximateMethodCount** retourneert de laatste waarde die is opgehaald door de methode **FetchAttributes** , zonder de Queue-service aan te roepen.

```csharp
// Fetch the queue attributes.
messageQueue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = messageQueue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="use-the-async-await-pattern-with-common-azure-queue-apis"></a>Het Async-Await patroon gebruiken met algemene Azure-wachtrij-Api's
Dit voor beeld laat zien hoe u het Async-Await patroon kunt gebruiken met algemene Azure-wachtrij-Api's. In het voor beeld wordt de async-versie van elk van de opgegeven methoden aangeroepen. Dit kan worden gezien door de **asynchrone** na reparatie van elke methode. Wanneer een asynchrone methode wordt gebruikt, wordt het async-await-patroon onderbroken totdat de aanroep is voltooid. Dit gedrag zorgt ervoor dat de huidige thread andere werkzaamheden kan uitvoeren, waardoor prestatie knelpunten worden voor komen en de algehele reactie snelheid van uw toepassing wordt verbeterd. Zie [Async en Await (C# en Visual Basic)](/previous-versions/hh191443(v=vs.140)) voor meer informatie over het gebruik van het Async-Await-patroon in .NET.

```csharp
// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Add the message asynchronously
await messageQueue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Delete the message asynchronously
await messageQueue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="delete-a-queue"></a>Een wachtrij verwijderen
Als u een wachtrij en alle berichten hierin wilt verwijderen, roept u de methode **Delete** aan in het wachtrijobject.

```csharp
// Delete the queue.
messageQueue.Delete();
```

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]