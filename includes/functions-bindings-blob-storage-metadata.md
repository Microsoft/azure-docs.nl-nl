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
ms.openlocfilehash: f4e6d5fb41769544b7be0f689447364988d0380d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95998826"
---
De blob-trigger biedt verschillende metagegevenseigenschappen. Deze eigenschappen kunnen worden gebruikt als onderdeel van bindingsexpressies in andere bindingen of als parameters in uw code. Deze waarden hebben dezelfde semantiek als het [CloudBlob](/dotnet/api/microsoft.azure.storage.blob.cloudblob?view=azure-dotnet)-type.

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