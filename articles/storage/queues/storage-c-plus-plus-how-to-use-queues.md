---
title: Queue Storage gebruiken (C++)-Azure Storage
description: Meer informatie over het gebruik van de Queue Storage-service in Azure. Voor beelden zijn geschreven in C++.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/16/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 44d64c54049c02b6602f01b97effcc33b03dbcfe
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591324"
---
# <a name="how-to-use-queue-storage-from-c"></a>Queue Storage gebruiken met C++

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Overzicht

In deze hand leiding wordt uitgelegd hoe u algemene scenario's uitvoert met behulp van de Azure Queue Storage-service. De voor beelden zijn geschreven in C++ en gebruiken de [Azure Storage-client bibliotheek voor C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). De gedekte scenario's zijn het **Invoegen**, **inspecteren**, **ophalen** en **verwijderen** van wachtrij berichten, en het **maken en verwijderen van wacht rijen**.

> [!NOTE]
> Deze hand leiding is gericht op de Azure Storage-client bibliotheek voor C++ v-1.0.0 en hoger. De aanbevolen versie is Azure Storage client library v 2.2.0, die beschikbaar is via [NuGet](https://www.nuget.org/packages/wastorage) of [github](https://github.com/Azure/azure-storage-cpp/).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Een C++-toepassing maken

In deze hand leiding maakt u gebruik van opslag functies die kunnen worden uitgevoerd binnen een C++-toepassing.

Hiervoor moet u de Azure Storage-client bibliotheek voor C++ installeren en een Azure Storage-account maken in uw Azure-abonnement.

<!-- docutune:casing "Getting Started on Linux" -->

Als u de Azure Storage-client bibliotheek voor C++ wilt installeren, kunt u de volgende methoden gebruiken:

- **Linux:** Volg de instructies in de [Azure Storage-client bibliotheek voor C++ Leesmij: aan de slag op de Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) -pagina.
- **Windows:** In Windows gebruikt u [vcpkg](https://github.com/microsoft/vcpkg) als afhankelijkheidsbeheerder. Volg de [Quick](https://github.com/microsoft/vcpkg#quick-start) start om te initialiseren `vcpkg` . Voer vervolgens de volgende opdracht uit om de bibliotheek te installeren:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

U vindt een hand leiding voor het maken van de bron code en het exporteren naar NuGet in het [Leesmij](https://github.com/Azure/azure-storage-cpp#download--install) -bestand.

## <a name="configure-your-application-to-access-queue-storage"></a>Uw toepassing configureren voor toegang tot Queue Storage

Voeg de volgende include-instructies toe aan het begin van het C++-bestand waar u de Azure Storage-Api's wilt gebruiken om toegang te krijgen tot wacht rijen:

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Een Azure-opslagverbindingstekenreeks instellen

Een Azure Storage-client gebruikt een opslag connection string om eind punten en referenties op te slaan voor toegang tot Data Management Services. Wanneer u in een client toepassing uitvoert, moet u de opslag connection string in de volgende indeling opgeven, waarbij u de naam van uw opslag account en de toegangs sleutel voor opslag gebruikt voor het opslag account dat wordt vermeld in de [Azure Portal](https://portal.azure.com) voor de `AccountName` `AccountKey` waarden en. Zie [over Azure Storage-accounts](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie over opslag accounts en toegangs sleutels. In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Als u uw toepassing wilt testen op uw lokale Windows-computer, kunt u de [Azurite-opslag emulator](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)gebruiken. Azurite is een hulp programma waarmee u Azure Blob Storage en Queue Storage op uw lokale ontwikkel computer simuleert. In het volgende voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks naar uw lokale opslagemulator:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Zie [de Azurite-emulator gebruiken voor het ontwikkelen van lokale Azure Storage](../common/storage-use-azurite.md)om Azurite te starten.

In de volgende voorbeelden wordt ervan uitgegaan dat u een van deze twee methoden hebt gebruikt om de opslagverbindingstekenreeks op te halen.

## <a name="retrieve-your-connection-string"></a>De verbindingsreeks ophalen

U kunt de klasse `cloud_storage_account` gebruiken als representatie van uw opslagaccountgegevens. Als u de gegevens van uw opslag account wilt ophalen uit de opslag connection string, kunt u de- `parse` methode gebruiken.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Procedure: een wachtrij maken

Met een- `cloud_queue_client` object kunt u referentie objecten voor wacht rijen ophalen. Met de volgende code wordt een- `cloud_queue_client` object gemaakt.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Gebruik het `cloud_queue_client` object om een verwijzing naar de wachtrij die u wilt gebruiken op te halen. U kunt de wachtrij maken als deze niet bestaat.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Procedure: een bericht in een wachtrij invoegen

Als u een bericht wilt invoegen in een bestaande wachtrij, maakt u eerst een nieuw `cloud_queue_message` . Vervolgens roept u de- `add_message` methode aan. A `cloud_queue_message` kan worden gemaakt op basis van een teken reeks (in UTF-8-indeling) of een byte matrix. Hier is code waarmee een wachtrij wordt gemaakt (als deze niet bestaat) en het volgende bericht wordt ingevoegd `Hello, World` :

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Procedure: het volgende bericht bekijken

U kunt het bericht aan de voor kant van een wachtrij bekijken zonder het uit de wachtrij te verwijderen door de methode aan te roepen `peek_message` .

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Procedure: de inhoud van een bericht in de wachtrij wijzigen

U kunt de inhoud van een bericht in de wachtrij wijzigen. Als het bericht een werktaak vertegenwoordigt, kunt u deze functie gebruiken om de status van de werktaak bij te werken. Met de volgende code wordt het bericht in de wachtrij bijgewerkt met nieuwe inhoud en wordt de time-out voor de zichtbaarheid met 60 seconden verlengd. Hiermee wordt de status van de werkitems die aan het bericht zijn gekoppeld, opgeslagen en krijgt de client een extra minuut om aan het bericht te blijven werken. U kunt deze techniek gebruiken om werk stromen met meerdere stappen bij te houden in wachtrij berichten, zonder dat u vanaf het begin hoeft te beginnen als een verwerkings stap mislukt als gevolg van een hardware-of software fout. Doorgaans houdt u ook het aantal nieuwe pogingen bij en als het bericht meer dan n keer opnieuw is geprobeerd, verwijdert u het. Dit biedt bescherming tegen berichten die een toepassingsfout activeren telkens wanneer ze worden verwerkt.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-dequeue-the-next-message"></a>Procedure: het volgende bericht uit de wachtrij verwijderen

Met uw code wordt een bericht uit een wachtrij in twee stappen uit de wachtrij verwijderd. Wanneer u aanroept `get_message` , wordt het volgende bericht weer gegeven in een wachtrij. Een bericht dat wordt geretourneerd van is niet `get_message` zichtbaar voor andere code die berichten uit deze wachtrij leest. Als u het verwijderen van het bericht uit de wachtrij wilt volt ooien, moet u ook bellen `delete_message` . Dit proces in twee stappen voor het verwijderen van een bericht zorgt ervoor dat als de code er niet in slaagt een bericht te verwerken vanwege hardware- of softwareproblemen, een ander exemplaar van uw code hetzelfde bericht kan ophalen en het opnieuw kan proberen. Uw code aanroepen `delete_message` direct nadat het bericht is verwerkt.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-use-additional-options-for-dequeuing-messages"></a>Procedure: aanvullende opties voor het dewachtrijen van berichten gebruiken

Er zijn twee manieren waarop u het ophalen van berichten uit een wachtrij kunt aanpassen. Ten eerste kunt u berichten batchgewijs (maximaal 32) ophalen. Ten tweede kunt u een langere of kortere time-out voor onzichtbaarheid instellen, zodat uw code meer of minder tijd krijgt voor het volledig verwerken van elk bericht. In het volgende code voorbeeld wordt de `get_messages` methode gebruikt om 20 berichten in één aanroep op te halen. Vervolgens wordt elk bericht met een `for` lus verwerkt. De time-out voor onzichtbaarheid wordt ingesteld op vijf minuten voor elk bericht. Houd er rekening mee dat de vijf minuten voor alle berichten tegelijk worden gestart, dus nadat vijf minuten zijn verstreken sinds de aanroep naar `get_messages` , worden alle berichten die niet zijn verwijderd, weer zichtbaar.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Procedure: de lengte van de wachtrij ophalen

U kunt een schatting ophalen van het aantal berichten in de wachtrij. De `download_attributes` methode retourneert wachtrij-eigenschappen met inbegrip van het aantal berichten. De- `approximate_message_count` methode haalt het geschatte aantal berichten in de wachtrij op.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Procedure: een wachtrij verwijderen

Als u een wachtrij en alle berichten erin wilt verwijderen, roept u de `delete_queue_if_exists` methode aan in het wachtrij object.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Volgende stappen

Nu u de basis principes van Queue Storage hebt geleerd, volgt u deze koppelingen voor meer informatie over Azure Storage.

- [Blob Storage gebruiken vanuit C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [Table Storage gebruiken vanuit C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Azure Storage-resources in C++ weergeven](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Naslag informatie over Azure Storage-client bibliotheek voor C++](https://azure.github.io/azure-storage-cpp)
- [Documentatie voor Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
