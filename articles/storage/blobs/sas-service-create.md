---
title: Een service-SAS maken voor een container of BLOB
titleSuffix: Azure Storage
description: Leer hoe u een SAS (Shared Access Signature) voor een service maakt voor een container of BLOB met behulp van Azure Storage bibliotheek voor Blob Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/20/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b2d18165bf2c5a4f70f1cbc555db79020ce988f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95250614"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>Een service-SAS maken voor een container of BLOB

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe u de sleutel van het opslag account gebruikt om een service-SAS te maken voor een container of BLOB met de Azure Storage-client bibliotheek voor Blob Storage.

## <a name="create-a-service-sas-for-a-blob-container"></a>Een service-SAS voor een BLOB-container maken

In het volgende code voorbeeld wordt een SAS voor een container gemaakt. Als de naam van een bestaand opgeslagen toegangs beleid wordt gegeven, wordt dat beleid gekoppeld aan de SAS. Als er geen opgeslagen toegangs beleid wordt gegeven, maakt de code een ad-hoc-SAS in de container.

### <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Een service-SAS is ondertekend met de toegangs sleutel voor het account. Gebruik de [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) -klasse om de referentie te maken die wordt gebruikt om de sa's te ondertekenen. Maak vervolgens een nieuw [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) -object en roep de [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) aan om de SAS-token teken reeks op te halen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Als u een service-SAS voor een container wilt maken, roept u de methode [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) aan.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Een service-SAS is ondertekend met de toegangs sleutel voor het account. Gebruik de [StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) -klasse om de referentie te maken die wordt gebruikt om de sa's te ondertekenen. Roep vervolgens de functie [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) aan die de vereiste opties biedt om de SAS-token teken reeks op te halen.

```javascript
function getContainerSasUri(containerClient, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        permissions: ContainerSASPermissions.parse("c")
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob container is: ${sasToken}`);

    return `${containerClient.url}?${sasToken}`;
}
```

---

## <a name="create-a-service-sas-for-a-blob"></a>Een service-SAS maken voor een BLOB

In het volgende code voorbeeld wordt een SAS gemaakt op een blob. Als de naam van een bestaand opgeslagen toegangs beleid wordt gegeven, wordt dat beleid gekoppeld aan de SAS. Als er geen opgeslagen toegangs beleid wordt gegeven, maakt de code een ad-hoc-SA'S op de blob.

# <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

Een service-SAS is ondertekend met de toegangs sleutel voor het account. Gebruik de [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) -klasse om de referentie te maken die wordt gebruikt om de sa's te ondertekenen. Maak vervolgens een nieuw [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) -object en roep de [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) aan om de SAS-token teken reeks op te halen.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Als u een service-SAS voor een BLOB wilt maken, roept u de methode [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) aan.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12"></a>[JavaScript v12](#tab/javascript)

Als u een service-SAS voor een BLOB wilt maken, roept u de methode [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) aan.

Als u een service-SAS voor een BLOB wilt maken, roept u de [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) -functie aan die de vereiste opties biedt.

```javascript
function getBlobSasUri(containerClient, blobName, sharedKeyCredential, storedPolicyName) {
    const sasOptions = {
        containerName: containerClient.containerName,
        blobName: blobName
    };

    if (storedPolicyName == null) {
        sasOptions.startsOn = new Date();
        sasOptions.expiresOn = new Date(new Date().valueOf() + 3600 * 1000);
        sasOptions.permissions = BlobSASPermissions.parse("r");
    } else {
        sasOptions.identifier = storedPolicyName;
    }

    const sasToken = generateBlobSASQueryParameters(sasOptions, sharedKeyCredential).toString();
    console.log(`SAS token for blob is: ${sasToken}`);

    return `${containerClient.getBlockBlobClient(blobName).url}?${sasToken}`;
}
```

---

## <a name="create-a-service-sas-for-a-directory"></a>Een service-SAS voor een directory maken

In een opslag account waarvoor een hiërarchische naam ruimte is ingeschakeld, kunt u een service-SAS voor een directory maken. Als u de service-SA'S wilt maken, controleert u of u versie 12.5.0 of hoger van het pakket [Azure. storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) hebt geïnstalleerd.

In het volgende voor beeld ziet u hoe u een service-SAS maakt voor een map met de V12-client bibliotheek voor .NET:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>Volgende stappen

- [Beperkte toegang verlenen tot Azure Storage-resources met behulp van Shared Access signatures (SAS)](../common/storage-sas-overview.md)
- [Een service-SAS maken](/rest/api/storageservices/create-service-sas)
