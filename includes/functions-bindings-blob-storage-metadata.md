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
ms.openlocfilehash: c6b038297945ca900508a822460e1358a2524d23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455908"
---
De blob-trigger biedt verschillende metagegevenseigenschappen. Deze eigenschappen kunnen worden gebruikt als onderdeel van bindingsexpressies in andere bindingen of als parameters in uw code. Deze waarden hebben dezelfde semantiek als het [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob)-type.

|Eigenschap  |Type  |Beschrijving  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Het pad naar de activeringsblob.|
|`Uri`|`System.Uri`|De URI van de blob voor de primaire locatie.|
|`Properties` |[BlobProperties](/dotnet/api/microsoft.azure.storage.blob.blobproperties)|De systeemeigenschappen van de blob. |
|`Metadata` |`IDictionary<string,string>`|De door de gebruiker gedefinieerde metagegevens voor de blob.|

Met de volgende C# Script- en JavaScript-voorbeelden wordt bijvoorbeeld het pad naar de activeringsblob geregistreerd, inclusief de container:

```csharp
public static void Run(string myBlob, string blobTrigger, ILogger log)
{
    log.LogInformation($"Full blob path: {blobTrigger}");
} 
```