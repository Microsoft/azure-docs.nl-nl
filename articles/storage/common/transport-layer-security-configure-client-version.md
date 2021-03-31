---
title: Transport Layer Security (TLS) configureren voor een client toepassing
titleSuffix: Azure Storage
description: Configureer een client toepassing om met Azure Storage te communiceren met een minimum versie van Transport Layer Security (TLS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/08/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 37b8c79df5b208feea185292fa09c323b64fa27d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89001804"
---
# <a name="configure-transport-layer-security-tls-for-a-client-application"></a>Transport Layer Security (TLS) configureren voor een client toepassing

Uit veiligheids overwegingen kan Azure Storage het nodig zijn dat clients een minimale versie van Transport Layer Security (TLS) gebruiken om aanvragen te verzenden. Aanroepen naar Azure Storage mislukken als de client een versie van TLS gebruikt die lager is dan de mini maal vereiste versie. Als een opslag account bijvoorbeeld TLS 1,2 vereist, zal een aanvraag die wordt verzonden door een client die TLS 1,1 gebruikt, mislukken.

In dit artikel wordt beschreven hoe u een client toepassing kunt configureren voor het gebruik van een bepaalde versie van TLS. Zie voor meer informatie over het configureren van een mini maal vereiste versie van TLS voor een Azure Storage-account [Mini maal vereiste versie van Transport Layer Security (TLS) configureren voor een opslag account](transport-layer-security-configure-minimum-version.md).

## <a name="configure-the-client-tls-version"></a>De TLS-versie van de client configureren

Een client kan alleen een aanvraag met een bepaalde versie van TLS verzenden als deze versie wordt ondersteund door het besturings systeem.

In de volgende voor beelden ziet u hoe de TLS-versie van de client wordt ingesteld op 1,2 in Power shell of .NET. Het .NET Framework dat door de client wordt gebruikt, moet TLS 1,2 ondersteunen. Zie [ondersteuning voor TLS 1,2](/dotnet/framework/network-programming/tls#support-for-tls-12)voor meer informatie.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

In het volgende voor beeld ziet u hoe u TLS 1,2 inschakelt in een Power shell-client:

```powershell
# Set the TLS version used by the PowerShell client to TLS 1.2.
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;

# Create a new container.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context
New-AzStorageContainer -Name "sample-container" -Context $ctx
```

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

In het volgende voor beeld ziet u hoe u TLS 1,2 inschakelt in een .NET-client met behulp van versie 12 van de Azure Storage-client bibliotheek:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Networking.cs" id="Snippet_ConfigureTls12":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

In het volgende voor beeld ziet u hoe u TLS 1,2 inschakelt in een .NET-client met versie 11 van de Azure Storage-client bibliotheek:

```csharp
static void EnableTls12()
{
    // Enable TLS 1.2 before connecting to Azure Storage
    System.Net.ServicePointManager.SecurityProtocol = System.Net.SecurityProtocolType.Tls12;

    // Add your connection string here.
    string connectionString = "";

    // Connect to Azure Storage and create a new container.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
    container.CreateIfNotExists();
}
```

---

## <a name="verify-the-tls-version-used-by-a-client"></a>Controleer de TLS-versie die wordt gebruikt door een client

Als u wilt controleren of de opgegeven versie van TLS door de client is gebruikt om een aanvraag te verzenden, kunt u [Fiddler](https://www.telerik.com/fiddler) of een vergelijkbaar hulp programma gebruiken. Open Fiddler om te beginnen met het vastleggen van client netwerk verkeer en voer vervolgens een van de voor beelden in de vorige sectie uit. Bekijk de Fiddler-tracering om te bevestigen dat de juiste versie van TLS is gebruikt voor het verzenden van de aanvraag, zoals wordt weer gegeven in de volgende afbeelding.

:::image type="content" source="media/transport-layer-security-configure-client-version/fiddler-trace-tls-version.png" alt-text="Scherm opname van Fiddler Trace waarmee de TLS-versie wordt aangegeven die op aanvraag wordt gebruikt":::

## <a name="next-steps"></a>Volgende stappen

- [Mini maal vereiste versie van Transport Layer Security (TLS) configureren voor een opslag account](transport-layer-security-configure-minimum-version.md)
- [Beveiligings aanbevelingen voor Blob Storage](../blobs/security-recommendations.md)
