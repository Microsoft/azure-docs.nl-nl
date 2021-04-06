---
title: 'Quickstart: Azure Queue Storage-clientbibliotheek v12 - .NET'
description: Leer hoe u de Azure Queue Storage-clientbibliotheek v12 voor .NET kunt gebruiken om een wachtrij te maken en berichten aan de wachtrij toe te voegen. Vervolgens leert u hoe u berichten uit de wachtrij kunt lezen en verwijderen. U leert ook hoe u een wachtrij kunt verwijderen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 22038e4145acabc067083177fcf297464972ad58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589522"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Quickstart: Azure Queue Storage-clientbibliotheek v12 voor .NET

Ga aan de slag met de Azure Queue Storage-clientbibliotheek versie 12 voor .NET. Azure Queue Storage is een service waarmee u grote aantallen berichten kunt opslaan om later op te halen en te verwerken. Volg deze stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen.

Gebruik de Azure Queue Storage-clientbibliotheek v12 voor .NET voor het volgende:

- Een wachtrij maken
- Berichten aan een wachtrij toevoegen
- Berichten in een wachtrij bekijken
- Een bericht in een wachtrij bijwerken
- Berichten van een wachtrij ontvangen
- Berichten uit een wachtrij verwijderen
- Een wachtrij verwijderen

Aanvullende bronnen:

- [API-referentiedocumentatie](/dotnet/api/azure.storage.queues)
- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues)
- [Pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0)
- [Voorbeelden](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- Azure Storage-account: [maak een opslagaccount](../common/storage-account-create.md)
- De huidige versie van de [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturingssysteem. Zorg ervoor dat u de SDK en niet de runtime ophaalt.

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Queue Storage-clientbibliotheek v12 voor .NET.

### <a name="create-the-project"></a>Het project maken

Maak een .NET Core-toepassing met de naam `QueuesQuickstartV12`.

1. Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `QueuesQuickstartV12`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand genaamd: `Program.cs`.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Schakel over naar de zojuist gemaakte map `QueuesQuickstartV12`.

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Het pakket installeren

Blijf in de toepassingsmap en installeer met de opdracht `dotnet add package` de Azure Queue Storage-clientbibliotheek voor het .NET-pakket.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open het bestand `Program.cs` in de editor
1. Verwijder de instructie `Console.WriteLine("Hello, World");`
1. Voeg `using`-instructies toe
1. De declaratie van de `Main`-methode bijwerken [ter ondersteuning van async-code](/dotnet/csharp/whats-new/csharp-7#async-main)

Hier volgt de code:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Queue Storage is een service om grote aantallen berichten op te slaan. Een wachtrijbericht kan maximaal 64 KB groot zijn. Een wachtrij kan miljoenen berichten bevatten, tot aan de totale capaciteitslimiet van een opslagaccount. Wachtrijen worden vaak gebruikt om een voorraad werk te maken dat asynchroon moet worden verwerkt. Queue Storage biedt drie typen resources:

- Het opslagaccount
- Een wachtrij in het opslagaccount
- Berichten in de wachtrij

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de Queue Storage-architectuur](./media/storage-queues-introduction/queue1.png)

Gebruik de volgende .NET-klassen om te communiceren met deze resources:

- [`QueueServiceClient`](/dotnet/api/azure.storage.queues.queueserviceclient): Met de `QueueServiceClient` kunt u alle wachtrijen in uw opslagaccount beheren.
- [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient): Met de `QueueClient`-klasse kunt u een afzonderlijke wachtrij en de bijbehorende berichten beheren en bewerken.
- [`QueueMessage`](/dotnet/api/azure.storage.queues.models.queuemessage): De `QueueMessage`-klasse vertegenwoordigt de afzonderlijke objecten die worden geretourneerd wanneer [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) wordt aangeroepen voor een wachtrij.

## <a name="code-examples"></a>Codevoorbeelden

Deze voorbeeldcodefragmenten laten zien hoe u de volgende acties kunt uitvoeren met de Azure Queue Storage-clientbibliotheek voor .NET:

- [De verbindingsreeks ophalen](#get-the-connection-string)
- [Een wachtrij maken](#create-a-queue)
- [Berichten aan een wachtrij toevoegen](#add-messages-to-a-queue)
- [Berichten in een wachtrij bekijken](#peek-at-messages-in-a-queue)
- [Een bericht in een wachtrij bijwerken](#update-a-message-in-a-queue)
- [Berichten van een wachtrij ontvangen](#receive-messages-from-a-queue)
- [Berichten uit een wachtrij verwijderen](#delete-messages-from-a-queue)
- [Een wachtrij verwijderen](#delete-a-queue)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

Met de volgende code wordt de verbindingsreeks voor het opslagaccount opgehaald. De verbindingsreeks is opgeslagen in de omgevingsvariabele die is gemaakt in de sectie [De opslagverbindingsreeks configureren](#configure-your-storage-connection-string).

Voeg deze code toe in de methode `Main`:

```csharp
Console.WriteLine("Azure Queue Storage client library v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Een wachtrij maken

Verzin een naam voor de nieuwe wachtrij. Met de volgende code wordt een GUID-waarde aan de wachtrijnaam toegevoegd om te verzekeren dat deze uniek is.

> [!IMPORTANT]
> Wachtrijnamen mogen alleen kleine letters, cijfers en afbreekstreepjes bevatten en moeten beginnen met een letter of cijfer. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter. De naam moet bovendien tussen 3 en 63 tekens lang zijn. Zie [Naamgeving van wachtrijen en metagegevens](/rest/api/storageservices/naming-queues-and-metadata) voor meer informatie.

Maak een exemplaar van de [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient)-klasse. Roep vervolgens de methode [`CreateAsync`](/dotnet/api/azure.storage.queues.queueclient.createasync) aan om de wachtrij in uw opslagaccount te maken.

Voeg deze code toe aan het einde van de `Main`-methode:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Berichten aan een wachtrij toevoegen

Met het volgende codefragment worden berichten asynchroon aan de wachtrij toegevoegd door de methode [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) aan te roepen. Ook wordt een [`SendReceipt`](/dotnet/api/azure.storage.queues.models.sendreceipt) opgeslagen die met een `SendMessageAsync`-aanroep wordt geretourneerd. Het resultaat wordt gebruikt om het bericht later in het programma bij te werken.

Voeg deze code toe aan het einde van de `Main`-methode:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Berichten in een wachtrij bekijken

Bekijk de berichten in de wachtrij door de methode [`PeekMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) aan te roepen. Met deze methode worden één of meer berichten vooraan in de wachtrij opgehaald, maar wordt de zichtbaarheid van het bericht niet gewijzigd.

Voeg deze code toe aan het einde van de `Main`-methode:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Een bericht in een wachtrij bijwerken

Werk de inhoud van een bericht bij door de methode [`UpdateMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) aan te roepen. Deze methode kan de time-out voor zichtbaarheid en de inhoud van een bericht wijzigen. De inhoud van het bericht moet een UTF-8-gecodeerde tekenreeks zijn die maximaal 64 KB groot is. Geef behalve de nieuwe inhoud voor het bericht ook de waarden uit de `SendReceipt` door die eerder in de code waren opgeslagen. De `SendReceipt`-waarden identificeren welk bericht moet worden bijgewerkt.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Berichten van een wachtrij ontvangen

Download eerder toegevoegde berichten door de methode [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) aan te roepen.

Voeg deze code toe aan het einde van de `Main`-methode:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Berichten uit een wachtrij verwijderen

Verwijder berichten uit de wachtrij nadat ze zijn verwerkt. In dit geval betekent ‘verwerken’ gewoon dat het bericht wordt weergegeven in de console.

De app pauzeert voor gebruikersinvoer door `Console.ReadLine` aan te roepen voordat deze de berichten verwerkt en verwijdert. Verifieer in uw [Azure-portal](https://portal.azure.com) dat de resources correct zijn gemaakt, voordat ze worden verwijderd. Berichten die niet expliciet worden verwijderd, worden uiteindelijk weer zichtbaar in de wachtrij, zodat u nog een kans hebt om ze te verwerken.

Voeg deze code toe aan het einde van de `Main`-methode:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Een wachtrij verwijderen

Met de volgende code worden de resources opgeschoond die met de app zijn gemaakt, door de wachtrij te verwijderen met de methode [`DeleteAsync`](/dotnet/api/azure.storage.queues.queueclient.deleteasync).

Voeg deze code toe aan het einde van de `Main`-methode:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app worden drie berichten gemaakt en aan een Azure-wachtrij toegevoegd. De code toont de berichten in de wachtrij, haalt ze op en verwijdert ze, en verwijdert uiteindelijk de wachtrij.

Navigeer in uw consolevenster naar de toepassingsmap, bouw de toepassing en voer deze uit.

```console
dotnet build
```

```console
dotnet run
```

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Queue Storage client library v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Wanneer de app pauzeert voordat deze berichten ontvangt, controleert u uw opslagaccount in de [Azure-portal](https://portal.azure.com). Verifieer dat de berichten in de wachtrij staan.

Druk op de `Enter`-toets om de berichten te ontvangen en verwijderen. Druk nogmaals op de `Enter`-toets wanneer u daarom wordt gevraagd, om de wachtrij te verwijderen en de demo te voltooien.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een wachtrij maakt en berichten eraan toevoegt met behulp van asynchrone .NET-code. Vervolgens leerde u hoe u berichten kunt bekijken, ophalen en verwijderen. Tot slot leerde u hoe u een berichtenwachtrij verwijdert.

Voor zelfstudies, voorbeelden, quickstarts en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Azure voor ontwikkelaars van .NET en .NET Core](/dotnet/azure/)

- Zie de [Azure Storage-bibliotheken voor .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage) voor meer informatie.
- Raadpleeg [Azure Queue Storage-clientbibliotheek voor .NET - voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples) voor meer Azure Queue Storage-voorbeeld-apps.
- Zie voor meer informatie over .NET Core [Aan de slag met .NET in 10 minuten](https://www.microsoft.com/net/learn/get-started/).
