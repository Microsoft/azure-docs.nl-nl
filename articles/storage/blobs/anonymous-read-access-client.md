---
title: Anoniem toegang krijgen tot open bare containers en blobs met .NET
titleSuffix: Azure Storage
description: Gebruik de Azure Storage-client bibliotheek voor .NET om open bare containers en blobs anoniem te benaderen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90088813"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>Anoniem toegang krijgen tot open bare containers en blobs met .NET

Azure Storage ondersteunt optionele open bare Lees toegang voor containers en blobs. Clients hebben anoniem toegang tot open bare containers en blobs met behulp van de Azure Storage-client Bibliotheken, en met andere hulpprogram ma's en hulpprogram ma's die ondersteuning bieden voor gegevens toegang tot Azure Storage.

In dit artikel wordt beschreven hoe u vanuit .NET toegang krijgt tot een open bare container of BLOB. Zie [anonieme open bare Lees toegang voor containers en blobs configureren](anonymous-read-access-configure.md)voor meer informatie over het configureren van anonieme lees toegang voor een container. Zie [anonieme open bare Lees toegang tot containers en blobs voor komen](anonymous-read-access-prevent.md)voor informatie over het voor komen van anonieme toegang tot een opslag account.

Een client die toegang heeft tot containers en blobs anoniem kan gebruikmaken van constructors waarvoor geen referenties zijn vereist. In de volgende voor beelden ziet u een aantal verschillende manieren om te verwijzen naar containers en blobs anoniem.

## <a name="create-an-anonymous-client-object"></a>Een anoniem client object maken

U kunt een nieuw service-client object voor anonieme toegang maken door het eind punt van de Blob-opslag voor het account op te geven. U moet echter ook de naam weten van een container in dat account die beschikbaar is voor anonieme toegang.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET V11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>Anoniem verwijzen naar een container

Als u de URL naar een container hebt die anoniem beschikbaar is, kunt u deze gebruiken om rechtstreeks naar de container te verwijzen.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET V11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>Anoniem verwijzen naar een BLOB

Als u de URL naar een BLOB hebt die beschikbaar is voor anonieme toegang, kunt u rechtstreeks naar de BLOB verwijzen met behulp van die URL:

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET V11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Volgende stappen

- [Anonieme open bare Lees toegang voor containers en blobs configureren](anonymous-read-access-configure.md)
- [Anonieme open bare Lees toegang voor containers en blobs voor komen](anonymous-read-access-prevent.md)
- [Toegang tot Azure Storage autoriseren](../common/storage-auth.md)
