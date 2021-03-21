---
title: 'Quickstart: Azure Blob Storage-bibliotheek v12 - C++'
description: In deze quickstart leert u hoe u de Azure Blob Storage-clientbibliotheek versie 12 voor C++ kunt gebruiken om een container en een blob-in-blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 246d2677c784daee4cc6b083cff04b6993ab3818
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006486"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Quickstart: Azure Blob Storage-clientbibliotheek v12 voor C++

Aan de slag met de Azure Blob Storage-clientbibliotheek v12 voor C++. Azure Blob Storage is de oplossing voor opslag van objecten in de cloud van Microsoft. Volg de stappen om het pakket te installeren en voorbeeldcode voor basistaken uit te proberen. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

Gebruik de Azure Blob Storage-clientbibliotheek v12 voor C++ voor het volgende:

- Een container maken
- Een blob uploaden naar Azure Storage
- Alle blobs in een container weergeven
- De blob downloaden op uw lokale computer
- Een container verwijderen

Resources:

- [API-referentiedocumentatie](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [Voorbeelden](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

- [Azure-abonnement](https://azure.microsoft.com/free/)
- [Azure Storage-account](../common/storage-account-create.md)
- [C++ compiler](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg - C en C++ pakketbeheer](https://github.com/microsoft/vcpkg/blob/master/docs/index.md)
- [LibCurl](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>Instellen

In dit gedeelte wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-clientbibliotheek v12 voor C++.

### <a name="install-the-packages"></a>De pakketten installeren

Als u dat nog niet hebt gedaan, installeert u de LibCurl- en LibXML2-pakketten met behulp van de opdracht `vcpkg install`.

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

Volg de instructies op GitHub om de [Azure SDK voor C++](https://github.com/Azure/azure-sdk-for-cpp/)te verkrijgen en te compileren.

### <a name="create-the-project"></a>Het project maken

Maak in Visual Studio een nieuwe C++-consoletoepassing voor Windows met de naam *BlobQuickstartV12*.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Dialoogvenster van Visual Studio voor het configureren van een nieuwe Windows-console-app voor C++":::

Voeg de volgende bibliotheken toe aan het project:

- libcurl.lib
- libxml2.lib
- bcrypt.lib
- Crypt32.Lib
- WS2_32.Lib
- azure-core.lib
- azure-storage-common.lib
- azure-storage-blobs.lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor Blob Storage:

- Het opslagaccount
- Een container in het opslagaccount
- Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de Blob Storage-architectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende C++-klassen om te communiceren met deze resources:

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): Met de klasse `BlobServiceClient` kunt u Azure Storage-resources en blob-containers bewerken.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): Met de klasse `BlobContainerClient` kunt u Azure Storage-containers en de bijbehorende blobs bewerken.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): Met de klasse `BlobClient` kunt u Azure Storage-blobs bewerken. Het is de basisklasse voor alle gespecialiseerde blob-klassen.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): Met de klasse `BlockBlobClient` kunt u Azure Storage-blok-blobs bewerken.

## <a name="code-examples"></a>Codevoorbeelden

Deze voorbeeldcodefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Azure Blob Storage-clientbibliotheek voor C++:

- [Include-bestanden toevoegen](#add-include-files)
- [De verbindingsreeks ophalen](#get-the-connection-string)
- [Een container maken](#create-a-container)
- [Blobs uploaden naar een container](#upload-blobs-to-a-container)
- [De blobs in een container weergeven](#list-the-blobs-in-a-container)
- [Blobs downloaden](#download-blobs)
- [Container verwijderen](#delete-a-container)

### <a name="add-include-files"></a>Include-bestanden toevoegen

Ga als volgt te werk vanuit de projectmap:

1. Open het *BlobQuickstartV12.sln*-oplossingenbestand in Visual Studio
1. Open in Visual Studio het bronbestand *BlobQuickstartV12.cpp*
1. Alle code in `main` verwijderen die automatisch is gegenereerd
1. Voeg `#include`-instructies toe

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor uw opslagaccount op van de omgevingsvariabele die is gemaakt in [De opslagverbindingsreeks configureren](#configure-your-storage-connection-string).

Voeg deze code toe in `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Een container maken

Maak een exemplaar van de klasse [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) door de functie [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe) aan te roepen. Roep vervolgens [Maken](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047) aan om de werkelijke container in uw opslagaccount te maken.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Voeg deze code toe aan het einde van `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Declareert een tekenreeks die ‘Hello Azure!’ bevat.
1. Hiermee wordt een verwijzing opgehaald naar een object [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) door het aanroepen van [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) op de container vanuit de sectie [Een container maken](#create-a-container).
1. Hiermee wordt de tekenreeks naar de blob geüpload door de functie [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) aan te roepen. Met deze functie wordt de blob gemaakt als deze nog niet bestaat, of wordt deze bijgewerkt als dat wel het geval is.

Voeg deze code toe aan het einde van `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Blobs in een container vermelden

Geef de blobs in de container weer door de functie [ListBlobsFlatSegment](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c) aan te roepen. Er is slechts één blob aan de container toegevoegd, dus de bewerking retourneert alleen die blob.

Voeg deze code toe aan het einde van `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blobs downloaden

De eigenschappen van de geüploade blob ophalen. Vervolgens declareert en wijzigt u het formaat van een nieuw `std::string`-object met behulp van de eigenschappen van de geüploade blob. Download de eerder gemaakte blob naar het nieuwe `std::string`-object door de functie [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) in de basisklasse [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) aan te roepen. Ten slotte geeft u de gedownloade blob-gegevens weer.

Voeg deze code toe aan het einde van `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Een blob verwijderen

Met de volgende code wordt de blob uit de Azure Blob Storage-container verwijderd door de functie [BlobClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615) aan te roepen.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code worden de resources opgeruimd die door de app zijn gemaakt door de hele container te verwijderen met behulp van [BlobContainerClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178).

Voeg deze code toe aan het einde van `main()`:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>De code uitvoeren

Met deze app maakt u een container en uploadt u een tekstbestand naar Azure Blob Storage. Vervolgens wordt een lijst gemaakt van de blobs in de container, wordt het bestand gedownload en wordt de bestandsinhoud weergegeven. Ten slotte verwijdert de app de blob en de container.

De uitvoer van de app lijkt op die in het volgende voorbeeld:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u blobs kunt uploaden, downloaden en er een lijst van kunt maken met behulp van C++. U hebt ook geleerd hoe u een Azure Blob Storage-container maakt en verwijdert.

Als u een voorbeeld wilt zien van Blob Storage voor C++, ga dan naar:

> [!div class="nextstepaction"]
> [Voorbeeld van Azure Blob Storage SDK v12 voor C++](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)