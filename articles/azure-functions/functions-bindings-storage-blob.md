---
title: Trigger-en bindingen voor Azure Blob Storage voor Azure Functions
description: Meer informatie over het gebruik van de Azure Blob Storage-trigger en bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100381525"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Blob-opslag bindingen voor Azure Functions-overzicht

Azure Functions integreert met [Azure Storage](../storage/index.yml) via [Triggers en bindingen](./functions-triggers-bindings.md). Door te integreren met Blob Storage kunt u functies bouwen die reageren op wijzigingen in BLOB-gegevens en lees-en schrijf waarden.

| Bewerking | Type |
|---------|---------|
| Een functie uitvoeren als wijzigingen in Blob Storage-gegevens | [Trigger](./functions-bindings-storage-blob-trigger.md) |
| Blob Storage-gegevens in een functie lezen | [Invoer binding](./functions-bindings-storage-blob-input.md) |
| Een functie toestaan Blob Storage-gegevens te schrijven |[Uitvoer binding](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 3. x | |
| C#-script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wordt aanbevolen voor gebruik met Visual Studio code. |
| C#-script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

#### <a name="storage-extension-5x-and-higher"></a>Opslag extensie 5. x en hoger

Er is een nieuwe versie van de opslag bindingen uitbrei ding beschikbaar als [Preview-NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Deze preview introduceert de mogelijkheid om [verbinding te maken met een identiteit in plaats van een geheim](./functions-reference.md#configure-an-identity-based-connection). Voor .NET-toepassingen worden ook de typen gewijzigd waarmee u een binding kunt maken, waarbij de typen van `WindowsAzure.Storage` en `Microsoft.Azure.Storage` met nieuwere typen van [Azure. storage. blobs](/dotnet/api/azure.storage.blobs)worden vervangen.

> [!NOTE]
> Het preview-pakket is niet opgenomen in een uitbreidings bundel en moet hand matig worden geïnstalleerd. Voor .NET-Apps voegt u een verwijzing naar het pakket toe. Voor alle andere typen apps raadpleegt [u uw extensies bijwerken].

[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Uw extensies bijwerken]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x apps hebben automatisch een verwijzing naar het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versie 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="hostjson-settings"></a>host.jsop instellingen

> [!NOTE]
> Deze sectie is niet van toepassing wanneer u extensie versies vóór 5.0.0 gebruikt. Voor deze versies zijn er geen globale configuratie-instellingen voor blobs.

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding wanneer u [extensie versie 5.0.0 en hoger](#storage-extension-5x-and-higher)gebruikt. In het volgende voor beeld *host.jsin* het onderstaande bestand bevat alleen de instellingen van versie 2. x + voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in de functies versie 2. x en hoger het [host.jsop referentie voor Azure functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (het aantal beschik bare kernen)|Het gehele aantal gelijktijdige aanroepen dat is toegestaan voor elke BLOB-geactiveerde functie. De mini maal toegestane waarde is 1.|

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer Blob Storage-gegevens worden gewijzigd](./functions-bindings-storage-blob-trigger.md)
- [Blob Storage-gegevens lezen wanneer een functie wordt uitgevoerd](./functions-bindings-storage-blob-input.md)
- [Blob Storage-gegevens van een functie schrijven](./functions-bindings-storage-blob-output.md)
