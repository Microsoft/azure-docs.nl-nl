---
title: .NET gebruiken voor het beheren van gegevens in Azure Data Lake Storage Gen2
description: Gebruik de Azure Storage-client bibliotheek voor .NET voor het beheren van mappen en bestanden in opslag accounts waarvoor een hiërarchische naam ruimte is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650317"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>.NET gebruiken voor het beheren van mappen en bestanden in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u .NET gebruikt voor het maken en beheren van mappen en bestanden in opslag accounts met een hiërarchische naam ruimte.

Zie [.net gebruiken voor het beheren van acl's in azure data Lake Storage Gen2](data-lake-storage-acl-dotnet.md)voor meer informatie over het ophalen, instellen en bijwerken van de acl's (toegangs beheer lijsten) van mappen en bestanden.

[Pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [API-verwijzing](/dotnet/api/azure.storage.files.datalake)  |  Toewijzing van gen1 [naar Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account waarvoor een hiërarchische naam ruimte is ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Om aan de slag te gaan, installeert u het [Azure. storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet-pakket.

Zie [pakketten installeren en beheren in Visual Studio met behulp van de NuGet-pakket beheer](/nuget/consume-packages/install-use-packages-visual-studio)voor meer informatie over het installeren van NuGet-pakketten.

Voeg deze instructies vervolgens toe aan de bovenkant van het code bestand.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar gemaakt met behulp van een account sleutel.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Verbinding maken met behulp van Azure Active Directory (Azure AD)

U kunt de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voor beeld wordt een [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  Zie [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md)om deze waarden op te halen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Zie de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) -documentatie voor meer voor beelden.

## <a name="create-a-container"></a>Een container maken

Een container fungeert als bestands systeem voor uw bestanden. U kunt er een maken door de methode [DataLakeServiceClient. CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) aan te roepen.

In dit voor beeld wordt een container gemaakt met de naam `my-file-system` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Een map maken

Maak een verwijzing naar een directory door de methode [DataLakeFileSystemClient. CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) aan te roepen.

In dit voor beeld wordt een map met de naam van een `my-directory` container toegevoegd en wordt vervolgens een submap met de naam toegevoegd `my-subdirectory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam of verplaats een map door de methode [DataLakeDirectoryClient. RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in de naam `my-subdirectory-renamed` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

In dit voor beeld wordt een map verplaatst met de naam `my-subdirectory-renamed` naar een submap van een map met de naam `my-directory-2` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory door de methode [DataLakeDirectoryClient. Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) aan te roepen.

In dit voor beeld wordt een map met de naam verwijderd `my-directory` .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Maak eerst een bestands verwijzing in de doel directory door een instantie van de klasse [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) te maken. Upload een bestand door de methode [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) aan te roepen. Zorg ervoor dat u de upload voltooit door de methode [DataLakeFileClient. FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Als de bestands grootte groot is, moet uw code meerdere aanroepen naar de [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). U kunt in plaats daarvan de methode [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) gebruiken. Op die manier kunt u het volledige bestand in één aanroep uploaden.
>
> Zie de volgende sectie voor een voor beeld.

## <a name="upload-a-large-file-to-a-directory"></a>Een groot bestand uploaden naar een map

Gebruik de methode [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) om grote bestanden te uploaden zonder meerdere aanroepen naar de methode [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) te hoeven maken.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Downloaden uit een directory 

Maak eerst een [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de methode [DataLakeFileClient. ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)  en parser de retour waarde voor het verkrijgen van een [Stream](/dotnet/api/system.io.stream) -object. Gebruik een API voor het verwerken van .NET-bestanden om bytes van de stroom naar een bestand op te slaan. 

In dit voor beeld wordt een [BinaryReader](/dotnet/api/system.io.binaryreader) en een [FileStream](/dotnet/api/system.io.filestream) gebruikt om bytes op te slaan in een bestand. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Mapinhoud weergeven

Mapinhoud weer geven door de methode [FileSystemClient. GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) aan te roepen en vervolgens de resultaten te inventariseren.

In dit voor beeld worden de namen van elk bestand dat zich in een map met de naam bevindt `my-directory` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Zie ook

- [API-referentiedocumentatie](/dotnet/api/azure.storage.files.datalake)
- [Pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)