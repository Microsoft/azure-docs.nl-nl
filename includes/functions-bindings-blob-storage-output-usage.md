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
ms.openlocfilehash: e375a12be73c280f2778e6e28efb709b9116a4cf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381648"
---
### <a name="default"></a>Standaard

U kunt binden aan de volgende typen om blobs te schrijven:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>twee</sup>
* `CloudBlockBlob`<sup>twee</sup>
* `CloudPageBlob`<sup>twee</sup>
* `CloudAppendBlob`<sup>twee</sup>

<sup>1</sup> vereist de binding in `direction` *function.js* in of `FileAccess.Read` in een C#-klassen bibliotheek. U kunt echter het container object gebruiken dat de runtime biedt om schrijf bewerkingen uit te voeren, zoals blobs uploaden naar de container.

<sup>2</sup> vereist de binding ' InOut ' `direction` in *function.js* in `FileAccess.ReadWrite` of in een C#-klassebibliotheek.

Als u probeert verbinding te maken met een van de typen Storage-SDK en er een foutbericht wordt weergegeven, moet u ervoor zorgen dat u een verwijzing hebt naar [de juiste Storage-SDK-versie](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Binding met `string` of `Byte[]` wordt alleen aanbevolen als de Blob-grootte klein is, omdat de gehele blob-inhoud in het geheugen wordt geladen. Over het algemeen is het raadzaam om een type `Stream` of `CloudBlockBlob` te gebruiken. Zie voor meer informatie gelijktijdigheid [en geheugen gebruik](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) eerder in dit artikel.

### <a name="additional-types"></a>Aanvullende typen

Apps die gebruikmaken [van de 5.0.0 of hogere versie van de opslag uitbreiding](../articles/azure-functions/functions-bindings-storage-blob.md#storage-extension-5x-and-higher) , kunnen ook gebruikmaken van de typen van de [Azure SDK voor .net](/dotnet/api/overview/azure/storage.blobs-readme). Deze versie wordt niet ondersteund voor de verouderde `CloudBlobContainer` ,,,, `CloudBlobDirectory` `ICloudBlob` `CloudBlockBlob` `CloudPageBlob` en `CloudAppendBlob` typen van de volgende typen:

- [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient)<sup>1</sup>
- [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)<sup>2</sup>
- [BlockBlobClient](/dotnet/api/azure.storage.blobs.specialized.blockblobclient)<sup>2</sup>
- [PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.pageblobclient)<sup>2</sup>
- [AppendBlobClient](/dotnet/api/azure.storage.blobs.specialized.appendblobclient)<sup>2</sup>
- [BlobBaseClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient)<sup>2</sup>

<sup>1</sup> vereist de binding in `direction` *function.js* in of `FileAccess.Read` in een C#-klassen bibliotheek. U kunt echter het container object gebruiken dat de runtime biedt om schrijf bewerkingen uit te voeren, zoals blobs uploaden naar de container.

<sup>2</sup> vereist de binding ' InOut ' `direction` in *function.js* in `FileAccess.ReadWrite` of in een C#-klassebibliotheek.

Zie [de GitHub-opslag plaats voor de uitbrei ding](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Blobs#examples)voor voor beelden van het gebruik van deze typen.
