---
title: Aan de slag met Azure Queue Storage met behulp van .NET-Azure Storage
description: Azure Queue Storage bieden betrouw bare, asynchrone berichten tussen toepassings onderdelen. Met Cloud Messaging kunnen onderdelen van uw toepassing onafhankelijk van elkaar worden opgeschaald.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: d54b8f15c90aa8f6ffcc04453fee0349e501f47d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585748"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Aan de slag met Azure Queue Storage met .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Overzicht

Azure Queue Storage biedt Cloud berichten tussen toepassings onderdelen. Bij het ontwerpen van toepassingen voor schalen worden toepassings onderdelen vaak losgekoppeld zodat ze onafhankelijk kunnen worden geschaald. Queue Storage biedt asynchrone berichten tussen toepassings onderdelen, ongeacht of ze worden uitgevoerd in de Cloud, op het bureau blad, op een on-premises server of op een mobiel apparaat. Queue Storage biedt ook ondersteuning voor het beheren van asynchrone taken en het bouwen van werk stromen voor het proces.

### <a name="about-this-tutorial"></a>Over deze zelfstudie

Deze zelf studie laat zien hoe u .NET-code kunt schrijven voor een aantal algemene scenario's met Azure Queue Storage. Scenario's die aan bod komen, zijn onder meer het maken en verwijderen van wachtrijen en het toevoegen, lezen en verwijderen van wachtrijberichten.

**Geschatte duur:** 45 minuten

### <a name="prerequisites"></a>Vereisten

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- Een [Azure Storage-account](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Vervolgens stelt u in Visual Studio uw ontwikkelomgeving in, zodat u de codevoorbeelden in deze handleiding kunt uitproberen.

### <a name="create-a-windows-console-application-project"></a>Een Windows-consoletoepassingsproject maken

Maak in Visual Studio een nieuwe Windows-consoletoepassing. In de volgende stappen ziet u hoe u een consoletoepassing maakt in Visual Studio 2019. De stappen zijn nagenoeg gelijk in andere versies van Visual Studio.

1. Selecteer **bestand**  >  **Nieuw**  >  **project**
2. **Platform**  >  **Vensters** selecteren
3. **Console-app selecteren (.NET Framework)**
4. Selecteer **Volgende**
5. Voer in het veld **project naam** een naam in voor uw toepassing
6. Selecteer **Maken**

Alle codevoorbeelden in deze zelfstudie kunnen worden toegevoegd aan de methode `Main()` van het bestand `Program.cs` van de consoletoepassing.

U kunt de Azure Storage-client bibliotheken gebruiken in elk type .NET-toepassing, waaronder een Azure-Cloud service of web-app, en desktop-en mobiele toepassingen. In deze gids gebruiken we een consoletoepassing voor de eenvoud.

### <a name="use-nuget-to-install-the-required-packages"></a>NuGet gebruiken om de vereiste pakketten te installeren

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

U moet verwijzen naar de volgende vier pakketten in uw project om deze zelf studie te volt ooien:

- [Azure. core-bibliotheek voor .net](https://www.nuget.org/packages/azure.core/): dit pakket biedt gedeelde primitieven, abstracties en helpers voor moderne .net Azure SDK-client bibliotheken.
- [Azure. storage. algemene client bibliotheek voor .net](https://www.nuget.org/packages/azure.storage.common/): dit pakket biedt een infra structuur die wordt gedeeld door de andere Azure Storage-client bibliotheken.
- [Azure. storage. queues-client bibliotheek voor .net](https://www.nuget.org/packages/azure.storage.queues/): dit pakket maakt het mogelijk om met Azure Queue Storage berichten op te slaan die toegankelijk zijn voor een client.
- [System.Configuration.ConfigurationManager-bibliotheek voor .net](https://www.nuget.org/packages/system.configuration.configurationmanager/): dit pakket biedt toegang tot configuratie bestanden voor client toepassingen.

U kunt NuGet gebruiken om deze pakketten te verkrijgen. Volg deze stappen:

1. Klik met de rechter muisknop op uw project in **Solution Explorer** en kies **NuGet-pakketten beheren**.
1. **Bladeren** selecteren
1. Zoek online naar `Azure.Storage.Queues` en selecteer **installeren** om de Azure Storage-client bibliotheek en de afhankelijkheden ervan te installeren. Hiermee worden ook de bibliotheken Azure. storage. common en Azure. core geïnstalleerd. Dit zijn afhankelijkheden van de wachtrij bibliotheek.
1. Zoek online naar `System.Configuration.ConfigurationManager` en selecteer **installeren** om de Configuration Manager te installeren.

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

U moet verwijzen naar de volgende drie pakketten in uw project om deze zelf studie te volt ooien:

- [Micro soft. Azure. storage. algemene client bibliotheek voor .net](https://www.nuget.org/packages/microsoft.azure.storage.common/): dit pakket biedt programmatische toegang tot gegevens bronnen in uw opslag account.
- [Microsoft Azure Queue Storage-client bibliotheek voor .net](https://www.nuget.org/packages/microsoft.azure.storage.queue/): met deze client bibliotheek kunt u met Azure Queue Storage werken om berichten op te slaan die toegankelijk zijn voor een client.
- [Configuration Manager-bibliotheek van Microsoft Azure voor .NET](https://www.nuget.org/packages/microsoft.azure.configurationmanager/): dit pakket biedt een klasse voor het parseren van een verbindingsreeks in een configuratiebestand, ongeacht waar de toepassing wordt uitgevoerd.

U kunt NuGet gebruiken om deze pakketten te verkrijgen. Volg deze stappen:

1. Klik met de rechter muisknop op uw project in **Solution Explorer** en kies **NuGet-pakketten beheren**.
1. **Bladeren** selecteren
1. Zoek online naar `Microsoft.Azure.Storage.Queue` en selecteer **installeren** om de Azure Storage-client bibliotheek en de afhankelijkheden ervan te installeren. Hiermee wordt ook de bibliotheek micro soft. Azure. storage. common geïnstalleerd. Dit is een afhankelijkheid van de wachtrij bibliotheek.
1. Zoek online naar `Microsoft.Azure.ConfigurationManager` en selecteer **installeren** om de Azure Configuration Manager te installeren.

---

### <a name="determine-your-target-environment"></a>De doelomgeving bepalen

U kunt de voorbeelden in deze gids in twee omgevingen uitvoeren:

- U kunt de code uitvoeren met een Azure Storage-account in de cloud.
- U kunt uw code uitvoeren op basis van de Azurite-opslag emulator. Azurite is een lokale omgeving die een Azure Storage-account in de Cloud emuleert. Azurite is een gratis optie voor het testen en opsporen van fouten in uw code tijdens het ontwikkelen van uw toepassing. De emulator maakt gebruik van een bekend account en een bekende sleutel. Zie voor meer informatie [de Azurite-emulator gebruiken voor het ontwikkelen en testen van lokale Azure Storage](../common/storage-use-azurite.md).

> [!NOTE]
> Gebruik de opslagemulator als u mogelijke kosten in verband met Azure-opslag wilt vermijden. Als u echter een Azure Storage-account in de Cloud wilt richten, zullen de kosten voor het uitvoeren van deze zelf studie verwaarloosbaar zijn.

## <a name="get-your-storage-connection-string"></a>Uw opslag connection string ophalen

De Azure Storage-client bibliotheken voor .NET-ondersteuning met behulp van een opslag connection string voor het configureren van eind punten en referenties voor toegang tot opslag Services. Zie [Toegangssleutels voor een opslagaccount beheren](../common/storage-account-keys-manage.md) voor meer informatie.

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

De voorbeeldcode moet de toegang tot uw opslagaccount autoriseren. Om te autoriseren geeft u de toepassing de referenties van uw opslagaccount in de vorm van een verbindingsreeks. Om uw opslagaccountreferenties te zien, doet u het volgende:

1. Navigeer naar [Azure Portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. De toegangssleutels van uw account worden weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en klik op de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Zie [Azure Storage-verbindingsreeksen configureren](../common/storage-configure-connection-string.md) voor meer informatie over verbindingsreeksen.

> [!NOTE]
> De sleutel van uw opslagaccount is vergelijkbaar met het hoofdwachtwoord voor uw opslagaccount. Zorg dat de sleutel van uw opslagaccount altijd is beveiligd. Geef deze niet aan andere gebruikers en bewaar of noteer de sleutel op een veilige manier en plaats. Genereer een nieuwe sleutel via de Azure-portal als er mogelijk inbreuk op de sleutel heeft plaatsgevonden.

De beste manier om de opslagverbindingsreeks te onderhouden, is met een configuratiebestand. U configureert de verbindingsreeks door het bestand `app.config` te openen vanuit Solution Explorer in Visual Studio. Voeg de inhoud toe van het `<appSettings>` element dat hier wordt weer gegeven. Vervang door `connection-string` de waarde die u hebt gekopieerd uit uw opslag account in de portal:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

De configuratie-instelling ziet er ongeveer als volgt uit:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Als u de Azurite-opslag emulator wilt richten, kunt u een snelkoppeling gebruiken die is toegewezen aan de bekende account naam en-sleutel. In dat geval heeft de verbindingsreeks de volgende instelling:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Using-instructies toevoegen

Voeg de volgende `Program.cs`-instructies aan het begin van het bestand `using` toe:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>De Queue Storage-client maken

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Met de- [`QueueClient`](/dotnet/api/azure.storage.queues.queueclient) klasse kunt u wacht rijen ophalen die zijn opgeslagen in Queue Storage. Hier volgt één manier om de serviceclient te maken:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Met de- [`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) klasse kunt u wacht rijen ophalen die zijn opgeslagen in Queue Storage. Hier volgt één manier om de serviceclient te maken:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

U bent nu klaar om code te schrijven die gegevens leest uit en schrijft naar Queue Storage.

## <a name="create-a-queue"></a>Een wachtrij maken

In dit voor beeld ziet u hoe u een wachtrij maakt:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Een bericht in een wachtrij invoegen

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u een bericht wilt invoegen in een bestaande wachtrij, roept u de- [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) methode aan. Een bericht kan een teken reeks zijn (in UTF-8-indeling) of een byte matrix. Met de volgende code wordt een wachtrij gemaakt (als deze niet bestaat) en wordt er een bericht ingevoegd:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Als u een bericht wilt invoegen in een bestaande wachtrij, maakt u eerst een nieuw [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Vervolgens roept u de- [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) methode aan. A `CloudQueueMessage` kan worden gemaakt op basis van een teken reeks (in UTF-8-indeling) of een byte matrix. Dit is de code waarmee een wachtrij wordt gemaakt (als deze niet bestaat) en het volgende bericht wordt ingevoegd `Hello, World` : als u een bericht wilt invoegen in een bestaande wachtrij, maakt u eerst een nieuw [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Vervolgens roept u de- [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) methode aan. A `CloudQueueMessage` kan worden gemaakt op basis van een teken reeks (in UTF-8-indeling) of een byte matrix. Hier is code waarmee een wachtrij wordt gemaakt (als deze niet bestaat) en het volgende bericht wordt ingevoegd `Hello, World` :

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Bekijken van het volgende bericht

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

U kunt de berichten in de wachtrij bekijken zonder ze uit de wachtrij te verwijderen door de methode aan te roepen [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) . Als u geen waarde voor de `maxMessages` para meter doorgeeft, is de standaard instelling om één bericht te bekijken.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

U kunt het bericht aan de voor kant van een wachtrij bekijken zonder het uit de wachtrij te verwijderen door de methode aan te roepen [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) .

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>De inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. Met de volgende code wordt het bericht in de wachtrij bijgewerkt met nieuwe inhoud en wordt de time-out voor de zichtbaarheid met 60 seconden verlengd. Hiermee wordt de status van de werkitems die aan het bericht zijn gekoppeld, opgeslagen en krijgt de client een extra minuut om aan het bericht te blijven werken. U kunt deze techniek gebruiken om werk stromen met meerdere stappen bij te houden in wachtrij berichten, zonder dat u vanaf het begin hoeft te beginnen als een verwerkings stap mislukt als gevolg van een hardware-of software fout. Doorgaans houdt u ook het aantal nieuwe pogingen bij en als het bericht meer dan *n* keer opnieuw is geprobeerd, verwijdert u het. Dit biedt bescherming tegen berichten die een toepassingsfout activeren telkens wanneer ze worden verwerkt.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>Het volgende bericht uit de wachtrij verwijderen

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Een bericht uit een wachtrij in twee stappen uit de wachtrij verwijderen. Wanneer u aanroept [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) , wordt het volgende bericht weer gegeven in een wachtrij. Een bericht dat wordt geretourneerd van is niet `ReceiveMessages` zichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook bellen [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) . Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. Uw code aanroepen `DeleteMessage` direct nadat het bericht is verwerkt.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Met uw code wordt een bericht uit een wachtrij in twee stappen uit de wachtrij verwijderd. Wanneer u aanroept [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) , wordt het volgende bericht weer gegeven in een wachtrij. Een bericht dat wordt geretourneerd van is niet `GetMessage` zichtbaar voor andere code die berichten uit deze wachtrij leest. Standaard blijft het bericht onzichtbaar gedurende 30 seconden. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook bellen [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) . Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. Uw code aanroepen `DeleteMessage` direct nadat het bericht is verwerkt.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>Het Async-Await patroon gebruiken met algemene Queue Storage-Api's

In dit voor beeld ziet u hoe u het Async-Await patroon kunt gebruiken met algemene Queue Storage-Api's. In het voor beeld wordt de asynchrone versie van elk van de opgegeven methoden aangeroepen, zoals aangegeven door het `Async` achtervoegsel van elke methode. Wanneer een asynchrone methode wordt gebruikt, wordt de lokale uitvoering door het Async-Await-patroon onderbroken totdat de aanroep is voltooid. Dit gedrag stelt de huidige thread in staat andere bewerkingen uit te voeren, zodat knelpunten in de prestaties worden voorkomen en de algehele respons van uw toepassing verbetert. Zie [async en await (C# en Visual Basic)](/previous-versions/hh191443(v=vs.140)) voor meer informatie over het gebruik van het Async-Await patroon in .net.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>Aanvullende opties voor het dewachtrijen van berichten gebruiken

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

In het volgende code voorbeeld wordt de [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) methode gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `foreach` lus verwerkt. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht. Houd er rekening mee dat de vijf minuten voor alle berichten tegelijk worden gestart, dus nadat vijf minuten zijn verstreken sinds de aanroep naar `ReceiveMessages` , worden alle berichten die niet zijn verwijderd, weer zichtbaar.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

In het volgende code voorbeeld wordt de [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) methode gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `foreach` lus verwerkt. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht. Houd er rekening mee dat de vijf minuten voor alle berichten tegelijk worden gestart, dus nadat vijf minuten zijn verstreken sinds de aanroep naar `GetMessages` , worden alle berichten die niet zijn verwijderd, weer zichtbaar.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Lengte van de wachtrij ophalen

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties) methode retourneert wachtrij-eigenschappen met inbegrip van het aantal berichten. De [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) eigenschap bevat het geschatte aantal berichten in de wachtrij. Dit getal is niet lager dan het werkelijke aantal berichten in de wachtrij, maar dit kan hoger zijn.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) methode retourneert wachtrij kenmerken met inbegrip van het aantal berichten. De [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) eigenschap retourneert de laatste waarde die door de `FetchAttributes` methode is opgehaald, zonder Queue Storage aan te roepen.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Een wachtrij verwijderen

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) methode aan in het wachtrij object.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) methode aan in het wachtrij object.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Queue Storage hebt geleerd, volgt u deze koppelingen voor meer informatie over complexere opslag taken.

- Bekijk de Queue Storage referentie documentatie voor volledige informatie over beschik bare Api's:
  - [Azure Storage-client bibliotheek voor .NET-Naslag informatie](/dotnet/api/overview/azure/storage)
  - [Naslag informatie over Azure Storage REST API](/rest/api/storageservices/)
- Bekijk meer functiehandleidingen voor informatie over aanvullende mogelijkheden voor het opslaan van gegevens in Azure.
  - Aan de [slag met Azure Table Storage met .net](../../cosmos-db/tutorial-develop-table-dotnet.md) voor het opslaan van gestructureerde gegevens.
  - [Ga aan de slag met Azure Blob Storage met .net](../blobs/storage-quickstart-blobs-dotnet.md) voor het opslaan van ongestructureerde gegevens.
  - [Verbinding maken met SQL Database met behulp van .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md) voor het opslaan van relationele gegevens.
- Leer hoe u de code die u schrijft om te werken met Azure Storage, kunt vereenvoudigen met behulp van de [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
