---
title: Geef een door de klant opgegeven sleutel op een aanvraag naar Blob-opslag met .NET-Azure Storage
description: Meer informatie over hoe u een door de klant opgegeven sleutel kunt opgeven voor een aanvraag voor Blob-opslag met behulp van .NET.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693514"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Geef een door de klant opgegeven sleutel op een aanvraag naar Blob-opslag met .NET

Clients die aanvragen indienen voor Azure Blob-opslag hebben de optie om een AES-256-versleutelings sleutel op te geven voor een afzonderlijke aanvraag. Met inbegrip van de versleutelings sleutel op de aanvraag biedt gedetailleerde controle over de versleutelings instellingen voor Blob Storage-bewerkingen. Door de klant verschafte sleutels kunnen worden opgeslagen in Azure Key Vault of in een andere sleutel opslag.

In dit artikel wordt uitgelegd hoe u een door de klant opgegeven sleutel kunt opgeven voor een aanvraag met .NET.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Zie de [Azure Identity client-bibliotheek voor .net voor](/dotnet/api/overview/azure/identity-readme)meer informatie over het verifiëren met de Azure Identity client-bibliotheek.

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Een door de klant verschafte sleutel gebruiken om naar een BLOB te schrijven

Het volgende voor beeld bevat een AES-256-sleutel bij het uploaden van een blob met de V12-client bibliotheek voor Blob Storage. In het voor beeld wordt het [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) -object gebruikt voor het autoriseren van de schrijf aanvraag met Azure AD, maar u kunt ook de aanvraag met gedeelde sleutel referenties autoriseren.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Geef een versleutelings sleutel op voor een aanvraag voor Blob-opslag](encryption-customer-provided-keys.md)
- [Azure Storage-versleuteling voor inactieve gegevens](../common/storage-service-encryption.md)
