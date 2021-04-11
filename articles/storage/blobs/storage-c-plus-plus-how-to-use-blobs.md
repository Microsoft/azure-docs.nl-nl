---
title: Object (BLOB)-opslag gebruiken vanuit C++-Azure | Microsoft Docs
description: Meer informatie over het opslaan van ongestructureerde gegevens (blobs) in de Cloud met Azure Blob-opslag (object) met C++.
author: twooley
ms.author: twooley
ms.date: 07/16/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 61c7dca25e90450c695a5137dd2ee900c1282bf0
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278045"
---
# <a name="how-to-use-blob-storage-from-c"></a>Blob Storage gebruiken vanuit C++

In deze hand leiding wordt gedemonstreerd hoe u algemene scenario's uitvoert met behulp van Azure Blob-opslag. De voor beelden laten zien hoe u blobs kunt uploaden, weer geven, downloaden en verwijderen. De voorbeelden zijn geschreven in C++ en maken gebruik van de [Azure Storage-clientbibliotheek voor C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
Zie [Inleiding tot Azure Blob-opslag](storage-blobs-introduction.md)voor meer informatie over Blob Storage.

> [!NOTE]
> Deze handleiding is gericht op de Azure-opslagclientbibliotheek voor C++ versie 1.0.0 en hoger. Micro soft raadt u aan de nieuwste versie van de Storage-client bibliotheek voor C++ te gebruiken, die beschikbaar is via [NuGet](https://www.nuget.org/packages/wastorage) of [github](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Een C++-toepassing maken
In deze hand leiding maakt u gebruik van opslag functies die kunnen worden uitgevoerd binnen een C++-toepassing.

Hiervoor moet u de Azure-opslagclientbibliotheek voor C++ installeren en een Azure-opslagaccount in uw Azure-abonnement maken.

Voor het installeren van de Azure-opslagclientbibliotheek voor C++ kunt u de volgende methoden gebruiken:

- **Linux:** Volg de instructies op de pagina met het [Leesmij-bestand voor de Azure Storage-clientbibliotheek voor C++: Aan de slag met de Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux).
- **Windows:** In Windows gebruikt u [vcpkg](https://github.com/microsoft/vcpkg) als afhankelijkheidsbeheerder. Volg de [Quick](https://github.com/microsoft/vcpkg#quick-start) start om vcpkg te initialiseren. Voer vervolgens de volgende opdracht uit om de bibliotheek te installeren:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

U vindt een hand leiding voor het maken van de bron code en het exporteren naar NuGet in het [Leesmij](https://github.com/Azure/azure-storage-cpp#download--install) -bestand.

## <a name="configure-your-application-to-access-blob-storage"></a>Uw toepassing configureren voor toegang tot Blob Storage
Voeg de volgende include-instructies toe aan het begin van het C++-bestand waarin u de Azure Storage-Api's wilt gebruiken om toegang te krijgen tot blobs:

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Een Azure Storage-connection string instellen
Een Azure-opslagclient gebruikt een opslagverbindingstekenreeks om eindpunten en referenties voor toegang tot gegevensbeheerservices op te slaan. Wanneer u uitvoert in een client toepassing, moet u de opslag connection string in de volgende indeling opgeven, waarbij u de naam van uw opslag account en de toegangs sleutel voor opslag gebruikt voor het opslag account dat wordt vermeld in de [Azure Portal](https://portal.azure.com) voor de waarden *AccountName* en *AccountKey* . Zie [over Azure Storage-accounts](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie over opslag accounts en toegangs sleutels. In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Als u uw toepassing wilt testen op uw lokale Windows-computer, kunt u de [Azurite-opslag emulator](../common/storage-use-azurite.md)gebruiken. Azurite is een hulp programma voor het simuleren van de BLOB-en Queue-services die beschikbaar zijn in azure op uw lokale ontwikkel machine. In het volgende voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingstekenreeks naar uw lokale opslagemulator:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));
```

Zie [de Azurite-emulator gebruiken voor het ontwikkelen van lokale Azure Storage](../common/storage-use-azurite.md)om Azurite te starten.

In de volgende voorbeelden wordt ervan uitgegaan dat u een van deze twee methoden hebt gebruikt om de opslagverbindingstekenreeks op te halen.

## <a name="retrieve-your-storage-account"></a>Uw opslag account ophalen
U kunt de klasse **cloud_storage_account** gebruiken om de gegevens van uw opslag account te vertegenwoordigen. Voor het ophalen van uw opslagaccountgegevens uit de opslagverbindingstekenreeks kunt u de methode **parse** gebruiken.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Vervolgens krijgt u een verwijzing naar een **cloud_blob_client** klasse, zodat u objecten kunt ophalen die containers en blobs vertegenwoordigen die zijn opgeslagen in Blob Storage. Met de volgende code wordt een **cloud_blob_client** -object gemaakt met het object voor het opslag account dat we hierboven hebben opgehaald:

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Procedure: een container maken
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

In dit voorbeeld ziet u hoe u een container kunt maken als deze nog niet bestaat:

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

De nieuwe container is standaard privé en u moet uw opslag toegangs sleutel opgeven om blobs uit deze container te downloaden. Als u de bestanden (blobs) binnen de container voor iedereen beschikbaar wilt maken, kunt u de container als openbaar instellen met de volgende code:

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Iedereen op internet kan blobs in een open bare container zien, maar u kunt ze alleen wijzigen of verwijderen als u de juiste toegangs sleutel hebt.

## <a name="how-to-upload-a-blob-into-a-container"></a>Procedure: een BLOB uploaden naar een container
Azure Blob-opslag ondersteunt blok-blobs en pagina-blobs. In de meeste gevallen is een blok-blob het aangewezen type om te gebruiken.

Om een bestand naar een blok-blob te uploaden, haalt u een containerverwijzing op en gebruikt u deze om een blok-blobverwijzing op te halen. Zodra u een BLOB-verwijzing hebt, kunt u elke gegevens stroom naar de referentie uploaden door de **upload_from_stream** -methode aan te roepen. Met deze bewerking wordt de blob gemaakt (als deze nog niet bestaat) of overschreven (als deze wel al bestaat). Het volgende voorbeeld laat zien hoe u een blob uploadt naar een container. Hierbij wordt ervan uitgegaan dat de container al is gemaakt.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

U kunt ook de methode **upload_from_file** gebruiken om een bestand te uploaden naar een blok-blob.

## <a name="how-to-list-the-blobs-in-a-container"></a>Procedure: de blobs in een container weer geven
Als u een lijst van de blobs in een container wilt weergeven, moet u eerst een containerverwijzing ophalen. Vervolgens kunt u de **list_blobs** methode van de container gebruiken om de blobs en/of de mappen erin op te halen. Voor toegang tot de uitgebreide set eigenschappen en methoden voor een geretourneerde **list_blob_item** moet u de methode **list_blob_item. as _blob** aanroepen om een  **cloud_blob** -object op te halen, of de methode **list_blob. as _directory** om een cloud_blob_directory-object op te halen. De volgende code laat zien hoe u de URI van elk item in de container **My-steek-container** kunt ophalen en uitvoeren:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Zie [overzicht van Azure storage resources in C++](../common/storage-c-plus-plus-enumeration.md)voor meer informatie over het weer geven van bewerkingen.

## <a name="how-to-download-blobs"></a>Procedure: blobs downloaden
Als u blobs wilt downloaden, haalt u eerst een BLOB-verwijzing op en roept u vervolgens de **download_to_stream** methode aan. In het volgende voor beeld wordt de **download_to_stream** methode gebruikt om de blob-inhoud over te dragen naar een Stream-object dat u vervolgens naar een lokaal bestand kunt verplaatsen.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

U kunt ook de **download_to_file** -methode gebruiken om de inhoud van een BLOB naar een bestand te downloaden.
Daarnaast kunt u ook de methode **download_text** gebruiken om de inhoud van een BLOB te downloaden als een teken reeks.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Procedure: blobs verwijderen
Als u een BLOB wilt verwijderen, moet u eerst een BLOB-verwijzing ophalen en vervolgens de **delete_blob** -methode aanroepen.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Volgende stappen
Nu u de basis principes van Blob Storage hebt geleerd, volgt u deze koppelingen voor meer informatie over Azure Storage.

- [Queue Storage gebruiken met C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
- [Table Storage gebruiken vanuit C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Azure Storage resources in C++ weer geven](../common/storage-c-plus-plus-enumeration.md)
- [Naslag informatie over Storage-client bibliotheek voor C++](https://azure.github.io/azure-storage-cpp)
- [Documentatie over Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
- [Gegevensoverdracht met het AzCopy-opdrachtregelprogramma](../common/storage-use-azcopy-v10.md)