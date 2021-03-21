---
title: Een BLOB-container maken of verwijderen met .NET-Azure Storage
description: Meer informatie over het maken of verwijderen van een BLOB-container in uw Azure Storage-account met behulp van de .NET-client bibliotheek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/04/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: dac50d8e35080a083e42891732512e012fae8fbd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576444"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Een container maken of verwijderen in Azure Storage met .NET

Blobs in Azure Storage worden georganiseerd in containers. Voordat u een BLOB kunt uploaden, moet u eerst een container maken. In dit artikel wordt beschreven hoe u containers maakt en verwijdert met de [Azure Storage-client bibliotheek voor .net](/dotnet/api/overview/azure/storage).

## <a name="name-a-container"></a>Een container een naam

Een container naam moet een geldige DNS-naam zijn, omdat deze deel uitmaakt van de unieke URI die wordt gebruikt om de container of de blobs te adresseren. Volg deze regels bij het benoemen van een container:

- Container namen kunnen tussen de 3 en 63 tekens lang zijn.
- Container namen moeten beginnen met een letter of cijfer en mogen alleen kleine letters, cijfers en het streepje (-) bevatten.
- Twee of meer opeenvolgende streep tekens zijn niet toegestaan in container namen.

De URI voor een container heeft de volgende indeling:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Een container maken

Als u een container wilt maken, roept u een van de volgende methoden op:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [CreateBlobContainer](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainer)
- [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync)

Deze methoden genereren een uitzonde ring als er al een container met dezelfde naam bestaat.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Maken](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

De methoden **Create** en **CreateAsync** genereren een uitzonde ring als er al een container met dezelfde naam bestaat.

De **CreateIfNotExists** -en **CreateIfNotExistsAsync** -methoden retour neren een Booleaanse waarde die aangeeft of de container is gemaakt. Als er al een container met dezelfde naam bestaat, retour neren deze methoden **False** om aan te geven dat er geen nieuwe container is gemaakt.

---

Containers worden direct onder het opslag account gemaakt. Het is niet mogelijk om één container onder een andere te nesten.

In het volgende voor beeld wordt een container asynchroon gemaakt:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>De basis container maken

Een basis container fungeert als een standaard container voor uw opslag account. Elk opslag account kan één basis container hebben, die *$root* moet worden genoemd. De basis container moet expliciet worden gemaakt of verwijderd.

U kunt verwijzen naar een blob die is opgeslagen in de hoofd container zonder de naam van de hoofd container op te nemen. Met de hoofd container kunt u verwijzen naar een BLOB op het hoogste niveau van de hiërarchie van het opslag account. U kunt bijvoorbeeld verwijzen naar een blob die zich in de hoofd container op de volgende manier bevindt:

`https://myaccount.blob.core.windows.net/default.html`

In het volgende voor beeld wordt de hoofd container synchroon gemaakt:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>Een container verwijderen

Als u een container in .NET wilt verwijderen, gebruikt u een van de volgende methoden:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

- [Verwijderen](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [DeleteIfExists](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

- [Verwijderen](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

De methoden **Delete** en **DeleteAsync** genereren een uitzonde ring als de container niet bestaat.

De **DeleteIfExists** -en **DeleteIfExistsAsync** -methoden retour neren een Booleaanse waarde die aangeeft of de container is verwijderd. Als de opgegeven container niet bestaat, retour neren deze methoden **False** om aan te geven dat de container niet is verwijderd.

Nadat u een container hebt verwijderd, kunt u niet meer *dan 30 seconden* een container met dezelfde naam maken. Het maken van een container met dezelfde naam mislukt met HTTP-fout code 409 (conflict). Eventuele andere bewerkingen in de container of de blobs die deze bevat, mislukken met HTTP-fout code 404 (niet gevonden).

In het volgende voor beeld wordt de opgegeven container verwijderd en wordt de uitzonde ring verwerkt als de container niet bestaat:

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
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

In het volgende voor beeld ziet u hoe u alle containers verwijdert die beginnen met een opgegeven voor voegsel.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

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

- [Container bewerking maken](/rest/api/storageservices/create-container)
- [Bewerking voor het verwijderen van een container](/rest/api/storageservices/delete-container)
