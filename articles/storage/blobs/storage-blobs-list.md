---
title: Blobs weer geven met .NET-Azure Storage
description: Meer informatie over het weer geven van blobs in een container in uw Azure Storage-account met behulp van de .NET-client bibliotheek. Code voorbeelden laten zien hoe blobs in een platte lijst worden weer gegeven of hoe blobs hiërarchisch moeten worden vermeld, alsof ze in mappen of mappen zijn ingedeeld.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ddd19c90c8c47016497e2c3b00e04595a94e7715
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95543065"
---
# <a name="list-blobs-with-net"></a>Blobs weer geven met .NET

Wanneer u blobs uit uw code opneemt, kunt u een aantal opties opgeven om te beheren hoe de resultaten van Azure Storage worden geretourneerd. U kunt het aantal resultaten opgeven dat moet worden geretourneerd in elke set resultaten en vervolgens de volgende sets ophalen. U kunt een voor voegsel opgeven voor het retour neren van blobs waarvan de naam met dat teken of teken reeks begint. En u kunt blobs in een platte lijst structuur of hiërarchisch vermelden. Een hiërarchische vermelding retourneert blobs alsof ze zijn ingedeeld in mappen.

In dit artikel wordt beschreven hoe u blobs kunt weer geven met behulp [van de Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage).  

## <a name="understand-blob-listing-options"></a>Informatie over de opties voor BLOB-vermeldingen

Als u de blobs in een opslag account wilt weer geven, roept u een van deze methoden aan:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [BlobContainerClient.GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs)
- [BlobContainerClient.GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync)
- [BlobContainerClient.GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)
- [BlobContainerClient.GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

Als u de blobs in een container wilt weer geven, roept u een van deze methoden aan:

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

De Overloads voor deze methoden bieden extra opties voor het beheren van de manier waarop blobs door de vermelding worden geretourneerd. Deze opties worden in de volgende secties beschreven.

---

### <a name="manage-how-many-results-are-returned"></a>Bepalen hoeveel resultaten er worden geretourneerd

Standaard retourneert een lijst bewerking Maxi maal 5000 resultaten per keer, maar u kunt het aantal resultaten opgeven dat elke vermelding moet retour neren. In de voor beelden in dit artikel ziet u hoe u resultaten kunt retour neren op pagina's.

### <a name="filter-results-with-a-prefix"></a>Resultaten filteren met een voor voegsel

Als u de lijst met blobs wilt filteren, geeft u een teken reeks op voor de `prefix` para meter. De voorvoegsel teken reeks kan een of meer tekens bevatten. Azure Storage retourneert vervolgens alleen de blobs waarvan de namen met het voor voegsel beginnen.

### <a name="return-metadata"></a>Meta gegevens retour neren

U kunt BLOB-meta gegevens met de resultaten retour neren.

- Als u de .NET V12 SDK gebruikt, geeft u de **meta gegevens** waarde voor de [BlobTraits](/dotnet/api/azure.storage.blobs.models.blobtraits) -inventarisatie op.

- Als u de .NET V11 SDK gebruikt, geeft u de **meta gegevens** waarde voor de [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) -inventarisatie op. Azure Storage bevat meta gegevens voor elke geretourneerde blob, dus u hoeft niet een van de **FetchAttributes** -methoden in deze context aan te roepen om de BLOB-meta gegevens op te halen.

### <a name="list-blob-versions-or-snapshots"></a>BLOB-versies of moment opnamen weer geven

Als u BLOB-versies of moment opnamen wilt weer geven met de .NET V12-client bibliotheek, geeft u de para meter [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) op met het veld **versie** of **moment opname** . Versies en moment opnamen worden weer gegeven van oudste naar nieuwste. Zie [BLOB-versies](versioning-enable.md#list-blob-versions)weer geven voor meer informatie over versies van vermeldingen.

### <a name="flat-listing-versus-hierarchical-listing"></a>Platte aanbieding versus hiërarchische lijst

Blobs in Azure Storage zijn ingedeeld in een plat paradigma, in plaats van een hiërarchisch paradigma (zoals een klassiek bestands systeem). U kunt blobs echter indelen in *virtuele mappen* om een mapstructuur te simuleren. Een virtuele map vormt een deel van de naam van de BLOB en wordt aangeduid met het scheidings teken.

Als u blobs wilt indelen in virtuele mappen, gebruikt u een scheidings teken in de naam van de blob. Het standaard scheidings teken is een slash (/), maar u kunt een wille keurig teken als scheidings tekens opgeven.

Als u uw Blobs een naam geven met behulp van een scheidings teken, kunt u ervoor kiezen om blobs hiërarchisch te vermelden. Voor een hiërarchische lijst bewerking retourneert Azure Storage virtuele mappen en blobs onder het bovenliggende object. U kunt de vermelding recursief aanroepen om door de hiërarchie te bladeren, vergelijkbaar met het programmatisch door lopen van een klassiek bestands systeem.

## <a name="use-a-flat-listing"></a>Een platte vermelding gebruiken

Standaard retourneert een lijst bewerking blobs in een platte vermelding. In een platte lijst worden blobs niet ingedeeld op virtuele map.

In het volgende voor beeld worden de blobs in de opgegeven container weer gegeven met een platte vermelding, met een optionele segment grootte die is opgegeven, en wordt de naam van de BLOB naar een console venster geschreven.

Als u de functie hiërarchische naam ruimte hebt ingeschakeld voor uw account, zijn mappen niet virtueel. In plaats daarvan zijn ze concreet, onafhankelijk objecten. Daarom worden directory's in de lijst weer gegeven als blobs met een lengte van nul.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als een lijst bewerking meer dan 5000 blobs retourneert, of als het aantal blobs dat beschikbaar is, groter is dan het aantal dat u hebt opgegeven, retourneert Azure Storage een *vervolg token* met de lijst met blobs. Een vervolg token is een ondoorzichtige waarde die u kunt gebruiken om de volgende set resultaten op te halen uit Azure Storage.

Controleer in uw code de waarde van het vervolg token om te bepalen of deze null is. Wanneer het vervolg token null is, is de set met resultaten voltooid. Als het vervolg token niet null is, roept u de vermelding opnieuw aan, waarbij u in het vervolg token de volgende set resultaten ophaalt, totdat het vervolg token null is.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
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

De voorbeeld uitvoer is vergelijkbaar met:

```
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Een hiërarchische lijst gebruiken

Wanneer u een vermelding hiërarchisch aanroept, retourneert Azure Storage de virtuele mappen en blobs op het eerste niveau van de hiërarchie. De eigenschap [prefix](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) van elke virtuele map wordt zo ingesteld dat u het voor voegsel in een recursieve aanroep kunt door geven om de volgende map op te halen.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Als u blobs hiërarchisch wilt weer geven, roept u de [BlobContainerClient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)of de [BlobContainerClient. GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync) -methode aan.

In het volgende voor beeld ziet u de blobs in de opgegeven container met behulp van een hiërarchische lijst, met een optionele segment grootte die is opgegeven en schrijft u de naam van de BLOB naar het console venster.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Als u blobs hiërarchisch wilt weer geven, stelt u de `useFlatBlobListing` para meter van de vermeldings methode in op **Onwaar**.

In het volgende voor beeld worden de blobs in de opgegeven container weer gegeven met een platte vermelding, met een optionele segment grootte die is opgegeven, en wordt de naam van de BLOB naar het console venster geschreven.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
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

De voorbeeld uitvoer is vergelijkbaar met:

```
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> BLOB-moment opnamen kunnen niet worden weer gegeven in een hiërarchische lijst bewerking.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Volgende stappen

- [Blobs weer geven](/rest/api/storageservices/list-blobs)
- [BLOB-resources opsommen](/rest/api/storageservices/enumerating-blob-resources)