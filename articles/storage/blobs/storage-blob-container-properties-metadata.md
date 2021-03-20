---
title: .NET gebruiken voor het beheren van eigenschappen en meta gegevens voor een BLOB-container
titleSuffix: Azure Storage
description: Meer informatie over het instellen en ophalen van systeem eigenschappen en het opslaan van aangepaste meta gegevens op BLOB-containers in uw Azure Storage-account met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/01/2020
ms.author: tamram
ms.custom: devx-track-csharp
ms.openlocfilehash: 9fb77179a00969da7a3dc372dc70c99cfe4220ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92090995"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>Container eigenschappen en meta gegevens beheren met .NET

BLOB-containers ondersteunen systeem eigenschappen en door de gebruiker gedefinieerde meta gegevens, naast de gegevens die ze bevatten. In dit artikel wordt beschreven hoe u systeem eigenschappen en door de gebruiker gedefinieerde meta gegevens beheert met de [Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage).

## <a name="about-properties-and-metadata"></a>Over eigenschappen en meta gegevens

- **Systeem eigenschappen**: systeem eigenschappen bestaan op elke Blob Storage-Resource. Sommige van deze kunnen worden gelezen of ingesteld, terwijl anderen alleen-lezen zijn. Onder de kaften sommige systeem eigenschappen overeenkomen met bepaalde standaard-HTTP-headers. De Azure Storage-client bibliotheek voor .NET houdt deze eigenschappen voor u bij.

- Door de **gebruiker gedefinieerde META** gegevens: door de gebruiker gedefinieerde meta gegevens bestaan uit een of meer naam/waarde-paren die u opgeeft voor een Blob Storage-Resource. U kunt meta gegevens gebruiken om aanvullende waarden met de resource op te slaan. Meta gegevens waarden zijn alleen bedoeld voor eigen doel einden en zijn niet van invloed op de werking van de resource.

Naam/waarde-paren van meta gegevens zijn geldige HTTP-headers en moeten dus voldoen aan alle beperkingen voor HTTP-headers. Namen van meta gegevens moeten geldige namen voor HTTP-headers en geldige C#-id's zijn, mogen alleen ASCII-tekens bevatten en moeten worden behandeld als niet hoofdletter gevoelig. Meta gegevens waarden met niet-ASCII-tekens moeten base64-gecodeerd of met URL-code ring zijn.

## <a name="retrieve-container-properties"></a>Container eigenschappen ophalen

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

Roep een van de volgende methoden aan om container eigenschappen op te halen:

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync)

In het volgende code voorbeeld worden de systeem eigenschappen van een container opgehaald en worden enkele eigenschaps waarden naar een console venster geschreven:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Het ophalen van eigenschaps-en meta gegevens waarden voor een Blob Storage-Resource is een proces dat uit twee stappen bestaat. Voordat u deze waarden kunt lezen, moet u deze expliciet ophalen door de **FetchAttributes** -of **FetchAttributesAsync** -methode aan te roepen. De uitzonde ring op deze regel is dat de methoden **Exists** en **ExistsAsync** de juiste **FetchAttributes** -methode aanroepen onder de kaften. Wanneer u een van deze methoden aanroept, hoeft u **FetchAttributes** ook niet aan te roepen.

> [!IMPORTANT]
> Als u merkt dat de eigenschaps-of meta gegevens waarden voor een opslag bron niet zijn ingevuld, controleert u of de code de methode **FetchAttributes** of **FetchAttributesAsync** aanroept.

Roep een van de volgende methoden aan om container eigenschappen op te halen:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

In het volgende code voorbeeld worden de systeem eigenschappen van een container opgehaald en worden enkele eigenschaps waarden naar een console venster geschreven:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

## <a name="set-and-retrieve-metadata"></a>Meta gegevens instellen en ophalen

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

U kunt meta gegevens opgeven als een of meer naam/waarde-paren voor een BLOB of container resource. Als u meta gegevens wilt instellen, voegt u naam/waarde-paren toe aan een [IDictionary](/dotnet/api/system.collections.idictionary) -object en roept u vervolgens een van de volgende methoden aan om de waarden te schrijven:

- [SetMetadata](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadataasync)

De naam van uw meta gegevens moet voldoen aan de naamgevings conventies voor C#-id's. Namen van meta gegevens behouden het hoofdletter gebruik dat is gemaakt, maar zijn niet hoofdletter gevoelig bij het instellen of lezen. Als twee of meer meta gegevens headers met dezelfde naam worden verzonden voor een resource, wordt de komma door komma's gescheiden en worden de twee waarden en retour code van het HTTP-antwoord 200 (OK) aaneengeschakeld.

In het volgende code voorbeeld worden meta gegevens voor een container ingesteld.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddContainerMetadata":::

Roep een van de volgende methoden aan om meta gegevens op te halen:

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync).

Lees vervolgens de waarden, zoals wordt weer gegeven in het onderstaande voor beeld.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

U kunt meta gegevens opgeven als een of meer naam/waarde-paren voor een BLOB of container resource. Als u meta gegevens wilt instellen, voegt u naam/waarde-paren toe aan de verzameling **meta gegevens** voor de resource en roept u vervolgens een van de volgende methoden aan om de waarden te schrijven:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

De naam van uw meta gegevens moet voldoen aan de naamgevings conventies voor C#-id's. Namen van meta gegevens behouden het hoofdletter gebruik dat is gemaakt, maar zijn niet hoofdletter gevoelig bij het instellen of lezen. Als twee of meer meta gegevens headers met dezelfde naam worden verzonden voor een resource, wordt de komma door komma's gescheiden en worden de twee waarden en retour code van het HTTP-antwoord 200 (OK) aaneengeschakeld.

In het volgende code voorbeeld worden meta gegevens voor een container ingesteld. Er wordt één waarde ingesteld met behulp van de methode **add** van de verzameling. De andere waarde wordt ingesteld met behulp van de syntaxis van de impliciete sleutel/waarde. Beide zijn geldig.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Als u meta gegevens wilt ophalen, roept u de methode **FetchAttributes** of **FetchAttributesAsync** aan op uw BLOB of container om de **META** gegevensverzameling te vullen. Lees vervolgens de waarden, zoals wordt weer gegeven in het onderstaande voor beeld.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Zie ook

- [Bewerking container eigenschappen ophalen](/rest/api/storageservices/get-container-properties)
- [Meta gegevens van de container instellen](/rest/api/storageservices/set-container-metadata)
- [Meta gegevens van container ophalen](/rest/api/storageservices/get-container-metadata)
