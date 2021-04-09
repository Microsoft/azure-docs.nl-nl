---
title: bestand opnemen
description: bestand opnemen
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 938f55ae0ba911ea3a97cd49e6424bf8aaefdc76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381682"
---
### <a name="default"></a>Standaard

U kunt de volgende parameter typen gebruiken voor de BLOB-invoer binding:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> vereist 'inout'-binding `direction` in *function.json* of `FileAccess.ReadWrite` in een C#-klassebibliotheek.

Als u probeert verbinding te maken met een van de typen Storage-SDK en er een foutbericht wordt weergegeven, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage-SDK-versie](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Binding met `string` of `Byte[]` wordt alleen aanbevolen als de Blob-grootte klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een type `Stream` of `CloudBlockBlob` te gebruiken. Zie voor meer informatie gelijktijdigheid [en geheugen gebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) eerder in dit artikel.

### <a name="additional-types"></a>Aanvullende typen

Apps die gebruikmaken [van de 5.0.0 of hogere versie van de opslag uitbreiding](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) , kunnen ook gebruikmaken van de typen van de [Azure SDK voor .net](/dotnet/api/overview/azure/storage.blobs-readme). Deze versie wordt niet ondersteund voor de verouderde `CloudBlobContainer` ,,,, `CloudBlobDirectory` `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` en `CloudAppendBlob` typen van de volgende typen:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>1</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>1</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>1</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>1</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>1</sup>

<sup>1</sup> vereist 'inout'-binding `direction` in *function.json* of `FileAccess.ReadWrite` in een C#-klassebibliotheek.

Zie [de GitHub-opslag plaats voor de uitbrei ding](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples)voor voor beelden van het gebruik van deze typen.
