---
title: 'Quickstart: Azure Storage V11 voor .NET gebruiken om een wachtrij te beheren'
description: In deze quickstart leert u hoe u de Azure Storage-clientbibliotheek voor .NET kunt gebruiken om een wachtrij te maken en er berichten aan toe te voegen. Vervolgens leert u hoe u berichten uit de wachtrij kunt lezen en verwerken.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 23703dc507aa909aea4711289a4d7d5c5e6a170e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97588196"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>Quickstart: De Azure Storage SDK V11 voor .NET gebruiken om een wachtrij te beheren

In deze quickstart leert u hoe u de Azure Storage-clientbibliotheek v11 voor .NET kunt gebruiken om een wachtrij te maken en er berichten aan toe te voegen. Vervolgens leert u hoe u berichten uit de wachtrij kunt lezen en verwerken.

> [!NOTE]
> In deze quickstart wordt gebruikgemaakt van een oudere versie van de Azure Queue Storage-clientbibliotheek. Als u aan de slag wilt gaan met de vorige versie, raadpleegt u [Quickstart: Azure Queue Storage-clientbibliotheek v12 voor .NET](storage-quickstart-queues-dotnet.md).

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Download en installeer vervolgens .NET Core 2.0 voor uw besturingssysteem. Als u Windows uitvoert, kunt u desgewenst Visual Studio installeren en .NET Framework gebruiken. U kunt ook een editor installeren die u wilt gebruiken bij uw besturingssysteem.

### <a name="windows"></a>Windows

- Installeer [.NET Core voor Windows](https://www.microsoft.com/net/download/windows) of [.NET Framework](https://www.microsoft.com/net/download/windows) (opgenomen in Visual Studio voor Windows)
- Installeer [Visual Studio voor Windows](https://www.visualstudio.com/). Als u van .NET Core gebruikmaakt, is het installeren van Visual Studio optioneel.

Zie [Choose between .NET Core and .NET Framework for server apps](/dotnet/standard/choosing-core-framework-server) (Kiezen tussen .NET Core en .NET Framework voor server-apps) voor informatie over de keuze tussen .NET Core en .NET Framework.

### <a name="linux"></a>Linux

- Installeer [.NET Core voor Linux](https://www.microsoft.com/net/download/linux)
- Installeer desgewenst [Visual Studio Code](https://www.visualstudio.com/) en de [C#-extensie](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

### <a name="macos"></a>macOS

- Installeer [.NET Core voor macOS](https://www.microsoft.com/net/download/macos).
- Installeer desgewenst [Visual Studio voor Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

De voorbeeldtoepassing die in deze quickstart wordt gebruikt, is een basisconsoletoepassing. U kunt de voorbeeldtoepassing verkennen op [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Gebruik [Git](https://git-scm.com/) om een kopie van de toepassing te downloaden naar de ontwikkelomgeving.

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Met deze opdracht wordt de opslagplaats naar uw lokale Git-map gekloond. Zoek voor het openen van de Visual Studio-oplossing naar de map `storage-queues-dotnet-quickstart`, open deze en dubbelklik op `storage-queues-dotnet-quickstart.sln`.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

U moet de verbindingsreeks voor uw opslagaccount opgeven om de toepassing uit te voeren. In de voorbeeldtoepassing wordt de verbindingsreeks uit een omgevingsvariabele gelezen en gebruikt om aanvragen voor Azure Storage te autoriseren.

Nadat u de verbindingsreeks hebt gekopieerd, schrijft u deze naar een nieuwe omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `<yourconnectionstring>` door de feitelijke verbindingsreeks:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Nadat u de omgevingsvariabele hebt toegevoegd, moet u actieve programma's die de omgevingsvariabele moeten lezen, opnieuw starten. Start ook het consolevenster opnieuw. Als u bijvoorbeeld Visual Studio als editor gebruikt, start u Visual Studio opnieuw voordat u het voorbeeld uitvoert.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

### <a name="macos"></a>macOS

Bewerk uw .bash_profile en voeg de omgevingsvariabele toe:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

De voorbeeldtoepassing maakt een wachtrij en voegt er een bericht aan toe. De toepassing kijkt eerst naar het bericht zonder het uit de wachtrij te verwijderen, haalt het bericht op en verwijdert het uit de wachtrij.

### <a name="windows"></a>Windows

Als u Visual Studio als editor gebruikt, drukt u op `F5` om het uit te voeren.

Navigeer anders naar de toepassingsmap en voer de toepassing uit met de opdracht `dotnet run`.

```
dotnet run
```

### <a name="linux"></a>Linux

Navigeer naar de toepassingsmap en voer de toepassing uit met de opdracht `dotnet run`.

```
dotnet run
```

### <a name="macos"></a>macOS

Navigeer naar de toepassingsmap en voer de toepassing uit met de opdracht `dotnet run`.

```
dotnet run
```

De uitvoer van de voorbeeldtoepassing is vergelijkbaar met het volgende voorbeeld:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>De voorbeeldcode begrijpen

We gaan nu de voorbeeldcode verkennen, zodat u begrijpt hoe deze werkt.

### <a name="try-parsing-the-connection-string"></a>Probeer de verbindingsreeks te parseren

Het voorbeeld controleert eerst of de omgevingsvariabele een verbindingsreeks bevat die kan worden geparseerd om een [`CloudStorageAccount`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)-object te maken dat naar het opslagaccount wijst. Het voorbeeld gebruikt de methode [`TryParse`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) om te controleren of de verbindingsreeks geldig is. Als `TryParse` is geslaagd, wordt de variabele `storageAccount` geïnitialiseerd en wordt `true` geretourneerd.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>De wachtrij maken

Eerst maakt het voorbeeld een wachtrij en voegt het er een bericht aan toe.

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name
// is unique in your storage account.
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Een bericht toevoegen

Vervolgens voegt het voorbeeld een bericht achteraan de wachtrij toe.

Een bericht moet in een formaat zijn dat kan worden opgenomen in een XML-aanvraag met UTF-8-codering, en mag maximaal 64 KB groot zijn. Als een bericht binaire gegevens bevat, wordt aanbevolen het bericht met Base64 te coderen.

Standaard is de maximale time-to-live voor een bericht ingesteld op 7 dagen. U kunt elk willekeurig positief getal opgeven voor de time-to-live van het bericht.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Als u een bericht wilt toevoegen dat niet verloopt, gebruikt u `Timespan.FromSeconds(-1)` in uw aanroep naar [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Een specifiek bericht in de wachtrij bekijken

Het voorbeeld laat zien hoe je een bericht uit een wachtrij kunt bekijken. Wanneer u een bericht bekijkt, kunt u de inhoud van het bericht lezen. Het bericht blijft echter zichtbaar voor andere clients, zodat een andere client het bericht vervolgens kan ophalen en verwerken.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's
// visibility, so that another client can still retrieve and process it.
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Een bericht uit de wachtrij halen

Het voorbeeld laat ook zien hoe u een bericht uit de wachtrij kunt halen. Wanneer u een bericht uit de wachtrij weghaalt, haalt u het bericht vooraan in de wachtrij op en maakt u het tijdelijk onzichtbaar voor andere clients. Standaard blijft een bericht 30 seconden lang onzichtbaar. Gedurende deze tijd kan uw code het bericht verwerken. Om het bericht definitief uit de wachtrij te halen, verwijdert u het bericht onmiddellijk na verwerking, zodat een andere client hetzelfde bericht niet uit de wachtrij haalt.

Als uw code een bericht niet kan verwerken vanwege een hardware- of softwarefout, wordt het bericht weer zichtbaar nadat de periode van onzichtbaarheid voorbij is. Een andere client kan hetzelfde bericht ophalen en het opnieuw proberen.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Resources opschonen

Het voorbeeld ruimt de resources op die het heeft aangemaakt, door de wachtrij te verwijderen. Als u de wachtrij verwijdert, worden ook alle berichten uit de wachtrij verwijderd.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Resources voor het ontwikkelen van .NET-toepassingen met wachtrijen

Zie de volgende aanvullende bronnen voor .NET-ontwikkeling met Azure Queue Storage:

### <a name="binaries-and-source-code"></a>Binaire bestanden en broncode

- Download de NuGet-pakketten voor de nieuwste versie van de [Azure Storage-clientbibliotheek voor .NET](/dotnet/api/overview/azure/storage)
  - [Algemeen](https://www.nuget.org/packages/microsoft.azure.storage.common/)
  - [Wachtrijen](https://www.nuget.org/packages/Azure.Storage.Queues/)
- Bekijk de [broncode van de .NET-clientbibliotheek](https://github.com/Azure/azure-storage-net) op GitHub.

### <a name="azure-storage-client-library-reference-and-samples"></a>Naslaginformatie en voorbeelden voor de Azure Storage-clientbibliotheek

- Bekijk de [naslaginformatie voor Azure Storage-clientbibliotheken voor .NET](/dotnet/api/overview/azure/storage) voor meer informatie over .NET-clientbibliotheken.
- Verken [Queue Storage-voorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) die zijn geschreven met de .NET-clientbibliotheek.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd u hoe u berichten aan een wachtrij kunt toevoegen, berichten uit een wachtrij kunt bekijken en weghalen en hoe u berichten kunt verwerken met behulp van .NET.

> [!div class="nextstepaction"]
> [Communiceren tussen toepassingen met Azure Queue Storage](/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Zie voor meer informatie over .NET Core [Aan de slag met .NET in 10 minuten](https://www.microsoft.com/net/learn/get-started/).
