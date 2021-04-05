---
title: Een BLOB-moment opname maken en beheren in .NET
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van de .NET-client bibliotheek voor het maken van een alleen-lezen momentopname van een blob om een back-up van blobgegevens op een bepaald moment in de tijd.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543371"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Een BLOB-moment opname maken en beheren in .NET

Een moment opname is een alleen-lezen versie van een blob die op een bepaald moment wordt uitgevoerd. In dit artikel wordt beschreven hoe u BLOB-moment opnamen maakt en beheert met behulp [van de Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage).

Zie [BLOB-moment opnamen](snapshots-overview.md)voor meer informatie over blob-moment opnamen in azure Storage.

## <a name="create-a-snapshot"></a>Een momentopname maken

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Gebruik een van de volgende methoden om een moment opname van een blok-BLOB te maken met versie 12. x van de Azure Storage-client bibliotheek voor .NET.

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

In het volgende code voorbeeld ziet u hoe u een moment opname maakt met versie 12. x. Voeg een verwijzing naar de [Azure. Identity](https://www.nuget.org/packages/azure.identity) -bibliotheek toe om uw Azure AD-referenties te gebruiken om aanvragen voor de service te autoriseren.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Gebruik een van de volgende methoden om een moment opname van een blok-BLOB te maken met versie 11. x van de Azure Storage-client bibliotheek voor .NET.

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

In het volgende code voorbeeld ziet u hoe u een moment opname maakt met versie 11. x. In dit voor beeld worden aanvullende meta gegevens voor de moment opname opgegeven wanneer deze wordt gemaakt.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

## <a name="delete-snapshots"></a>Moment opnamen verwijderen

Als u een BLOB wilt verwijderen, moet u eerst alle moment opnamen van die BLOB verwijderen. U kunt een moment opname afzonderlijk verwijderen of opgeven dat alle moment opnamen moeten worden verwijderd wanneer de bron-BLOB wordt verwijderd. Als u probeert een BLOB te verwijderen die nog moment opnamen bevat, treedt er een fout op.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u een BLOB en de bijbehorende moment opnamen wilt verwijderen met versie 12. x van de Azure Storage-client bibliotheek voor .NET, gebruikt u een van de volgende methoden en voegt u de [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) -Enum toe:

- [Verwijderen](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

In het volgende code voorbeeld ziet u hoe u een BLOB en de bijbehorende moment opnamen in .NET verwijdert, waarbij `blobClient` een object van het type [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)is):

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u een BLOB en de bijbehorende moment opnamen wilt verwijderen met versie 11. x van de Azure Storage-client bibliotheek voor .NET, gebruikt u een van de volgende methoden voor het verwijderen van blobs en voegt u de [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) -Enum toe:

- [Verwijderen](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

In het volgende code voorbeeld ziet u hoe u een BLOB en de bijbehorende moment opnamen in .NET verwijdert, waarbij `blockBlob` een object van het type [CloudBlockBlob] [dotnet_CloudBlockBlob] is:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Volgende stappen

- [BLOB-moment opnamen](snapshots-overview.md)
- [BLOB-versies](versioning-overview.md)
- [Blobs voorlopig verwijderen](./soft-delete-blob-overview.md)