---
title: BLOB-containers weer geven met .NET-Azure Storage
description: Meer informatie over het weer geven van BLOB-containers in uw Azure Storage-account met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96937250"
---
# <a name="list-blob-containers-with-net"></a>BLOB-containers weer geven met .NET

Wanneer u de containers in een Azure Storage-account in uw code opneemt, kunt u een aantal opties opgeven om te beheren hoe de resultaten worden geretourneerd door Azure Storage. In dit artikel wordt beschreven hoe u containers kunt weer geven met behulp [van de Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage).  

## <a name="understand-container-listing-options"></a>Informatie over container opties

Als u containers in uw opslag account wilt weer geven, roept u een van de volgende methoden aan:

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

De Overloads voor deze methoden bieden extra opties voor het beheren van de manier waarop containers door de vermelding worden geretourneerd. Deze opties worden in de volgende secties beschreven.

### <a name="manage-how-many-results-are-returned"></a>Bepalen hoeveel resultaten er worden geretourneerd

Standaard retourneert een lijst bewerking Maxi maal 5000 resultaten per keer. Als u een kleinere set resultaten wilt retour neren, geeft u een andere waarde dan nul op voor de grootte van de pagina met resultaten die moeten worden geretourneerd.

Als uw opslag account meer dan 5000 containers bevat of als u een pagina grootte hebt opgegeven, zodat de lijst bewerking een subset van containers in het opslag account retourneert, retourneert Azure Storage een *vervolg token* met de lijst met containers. Een vervolg token is een ondoorzichtige waarde die u kunt gebruiken om de volgende set resultaten op te halen uit Azure Storage.

Controleer in uw code de waarde van het vervolg token om te bepalen of deze leeg is (voor .NET V12) of Null (voor .NET V11 en eerder). Wanneer het vervolg token null is, is de set met resultaten voltooid. Als het vervolg token niet null is, roept u de vermelding opnieuw aan, waarbij u in het vervolg token de volgende set resultaten ophaalt, totdat het vervolg token null is.

### <a name="filter-results-with-a-prefix"></a>Resultaten filteren met een voor voegsel

Als u de lijst met containers wilt filteren, geeft u een teken reeks op voor de `prefix` para meter. De voorvoegsel teken reeks kan een of meer tekens bevatten. Azure Storage retourneert vervolgens alleen de containers waarvan de namen met het voor voegsel beginnen.

### <a name="return-metadata"></a>Meta gegevens retour neren

Als u de meta gegevens van de container met de resultaten wilt retour neren, geeft u de **meta gegevens** waarde op voor de [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) Enum (voor .net V12) of [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) Enum (voor .net V11 en eerder). Azure Storage bevat de meta gegevens van elke geretourneerde container, zodat u de meta gegevens van de container niet hoeft op te halen.

## <a name="example-list-containers"></a>Voor beeld: containers weer geven

In het volgende voor beeld worden de containers in een opslag account die beginnen met een opgegeven voor voegsel, asynchroon weer gegeven. Het voor beeld bevat een lijst met containers die beginnen met het opgegeven voor voegsel en retourneert het opgegeven aantal resultaten per aanroep van de vermelding. Vervolgens wordt het vervolg token gebruikt om het volgende segment met resultaten op te halen. Het voor beeld retourneert ook container-meta gegevens met de resultaten.

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zie ook

- [Containers weer geven](/rest/api/storageservices/list-containers2)
- [BLOB-resources opsommen](/rest/api/storageservices/enumerating-blob-resources)
