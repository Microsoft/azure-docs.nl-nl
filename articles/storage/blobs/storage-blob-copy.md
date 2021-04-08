---
title: Een blob met Azure Storage-Api's kopiëren
description: Meer informatie over het kopiëren van een blob met behulp van de Azure Storage-client bibliotheken.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/08/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 28b23555f97e58dde038c5e3e12a4b741b81776d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059038"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>Een BLOB kopiëren met Azure Storage-client bibliotheken

In dit artikel wordt beschreven hoe u een BLOB kopieert in een Azure Storage-account. Ook wordt uitgelegd hoe u een asynchrone Kopieer bewerking afbreekt. De voorbeeld code maakt gebruik van de Azure Storage-client bibliotheken.

## <a name="about-copying-blobs"></a>Over het kopiëren van blobs

Wanneer u een BLOB in hetzelfde opslag account kopieert, is dit een synchrone bewerking. Wanneer u meerdere accounts kopieert, is dit een asynchrone bewerking.

De bron-BLOB voor een Kopieer bewerking kan een blok-blob, een toevoeg-blob, een pagina-BLOB of een moment opname zijn. Als de doel-BLOB al bestaat, moet deze van hetzelfde type BLOB als de bron-BLOB zijn. Een bestaande doel-BLOB wordt overschreven.

De doel-Blob kan niet worden gewijzigd terwijl een Kopieer bewerking wordt uitgevoerd. Een bestemmings-Blob kan slechts één openstaande Kopieer bewerking hebben. Met andere woorden, een BLOB kan niet het doel zijn voor meerdere in behandeling zijnde Kopieer bewerkingen.

De gehele bron-BLOB of het volledige bestand wordt altijd gekopieerd. Het kopiëren van een bereik van bytes of een set blokken wordt niet ondersteund.

Wanneer een BLOB wordt gekopieerd, worden de systeem eigenschappen gekopieerd naar de doel-blob met dezelfde waarden.

Een Kopieer bewerking kan een van de volgende vormen hebben:

- Kopieer een bron-BLOB naar een doel-blob met een andere naam. De doel-Blob kan een bestaande blob van hetzelfde BLOB-type zijn (blok keren, toevoegen of pagina), of kan een nieuwe BLOB zijn die wordt gemaakt door de Kopieer bewerking.
- Kopieer een bron-BLOB naar een doel-blob met dezelfde naam, waardoor de doel-BLOB effectief wordt vervangen. Een dergelijke Kopieer bewerking verwijdert alle onverwerkte blokken en overschrijft de meta gegevens van de doel-blob.
- Kopieer een bron bestand in de Azure File-Service naar een bestemmings-blob. De doel-Blob kan een bestaande blok-BLOB zijn, of kan een nieuwe blok-BLOB zijn die wordt gemaakt door de Kopieer bewerking. Kopiëren van bestanden naar pagina-blobs of toevoeg-blobs wordt niet ondersteund.
- Een moment opname kopiëren over de basis-blob. Door een moment opname te promo veren tot de positie van de basis-blob, kunt u een eerdere versie van een BLOB herstellen.
- Kopieer een moment opname naar een doel-blob met een andere naam. De resulterende doel-blob is een schrijf bare Blob en geen moment opname.

## <a name="copy-a-blob"></a>Een BLOB kopiëren

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u een BLOB wilt kopiëren, roept u een van de volgende methoden aan:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

De `StartCopyFromUri` `StartCopyFromUriAsync` methoden en retour neren een [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) -object dat informatie over de Kopieer bewerking bevat.

Het volgende code voorbeeld haalt een [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) op die een eerder gemaakte BLOB vertegenwoordigt en kopieert deze naar een nieuwe Blob in dezelfde container:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u een BLOB wilt kopiëren, roept u een van de volgende methoden aan:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

De `StartCopy` `StartCopyAsync` methoden en retour neren een copy id-waarde die wordt gebruikt om de status te controleren of de Kopieer bewerking af te breken.

Het volgende code voorbeeld haalt een verwijzing op naar een eerder gemaakte Blob en kopieert deze naar een nieuwe Blob in dezelfde container:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

Als u een BLOB wilt kopiëren, roept u de [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) -methode aan. De- `start_copy_from_url` methode retourneert een woorden lijst met informatie over de Kopieer bewerking.

Het volgende code voorbeeld haalt een [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) op die een eerder gemaakte BLOB vertegenwoordigt en kopieert deze naar een nieuwe Blob in dezelfde container:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Een Kopieer bewerking afbreken

Het afbreken van een Kopieer bewerking resulteert in een doel-blob met de lengte nul. De meta gegevens voor de doel-BLOB hebben echter de nieuwe waarden gekopieerd van de bron-BLOB of worden expliciet ingesteld tijdens de Kopieer bewerking. Als u wilt dat de oorspronkelijke meta gegevens vóór de kopie worden bewaard, maakt u een moment opname van de doel-BLOB voordat u een van de Kopieer methoden aanroept.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Controleer de eigenschap [BlobProperties. CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) op de doel-blob om de status van de Kopieer bewerking op te halen. De uiteindelijke BLOB wordt doorgevoerd wanneer de Kopieer bewerking is voltooid.

Wanneer u een Kopieer bewerking afbreekt, wordt de Kopieer status van de doel-BLOB ingesteld op [CopyStatus. abort](/dotnet/api/microsoft.azure.storage.blob.copystatus).

De [AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) -en [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) -methoden annuleren een doorlopende Kopieer bewerking.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Controleer de eigenschap [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) op de doel-blob om de status van de Kopieer bewerking op te halen. De uiteindelijke BLOB wordt doorgevoerd wanneer de Kopieer bewerking is voltooid.

Wanneer u een Kopieer bewerking afbreekt, wordt de Kopieer status van de doel-BLOB ingesteld op [CopyStatus. abort](/dotnet/api/microsoft.azure.storage.blob.copystatus).

De [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) -en [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) -methoden annuleren een doorlopende Kopieer bewerking.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

# <a name="python-v12"></a>[Python-V12](#tab/python)

Controleer de status vermelding in de [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) -woorden lijst die is geretourneerd door [get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) methode om de status van de Kopieer bewerking op te halen. De uiteindelijke BLOB wordt doorgevoerd wanneer de Kopieer bewerking is voltooid.

Wanneer u een Kopieer bewerking afbreekt, wordt de [status](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) ingesteld op afgebroken.

De methode [abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) annuleert een doorlopende Kopieer bewerking.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Azure-SDK's

Meer informatie over Azure Sdk's:

 - [Azure-SDK voor .NET](https://github.com/azure/azure-sdk-for-net)
 - [Azure SDK voor Java](https://github.com/azure/azure-sdk-for-java)
 - [Azure-SDK voor Python](https://github.com/azure/azure-sdk-for-python)
 - [Azure-SDK voor JavaScript](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Volgende stappen

De volgende onderwerpen bevatten informatie over het kopiëren van blobs en het afbreken van doorlopende Kopieer bewerkingen met behulp van de Azure REST Api's.

- [Blob kopiëren](/rest/api/storageservices/copy-blob)
- [Kopiëren van BLOB afbreken](/rest/api/storageservices/abort-copy-blob)
