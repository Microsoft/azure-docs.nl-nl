---
title: Azure Table Storage-bindingen voor Azure Functions
description: Meer informatie over het gebruik van Azure Table Storage-bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4f2b890dc60cd50b5fcaefabe8d418268b738c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92096722"
---
# <a name="azure-table-storage-bindings-for-azure-functions"></a>Azure Table Storage-bindingen voor Azure Functions

Azure Functions integreert met [Azure Storage](../storage/index.yml) via [Triggers en bindingen](./functions-triggers-bindings.md). Door te integreren met tabel opslag kunt u functies bouwen die tabel opslag gegevens lezen en schrijven.

| Bewerking | Type |
|---------|---------|
| Tabel opslag gegevens lezen in een functie | [Invoer binding](./functions-bindings-storage-table-input.md) |
| Een functie toestaan om tabel opslag gegevens te schrijven |[Uitvoer binding](./functions-bindings-storage-table-output.md) |

## <a name="packages---functions-2x-and-higher"></a>Pakketten-functions 2. x en hoger

De tabel opslag bindingen zijn opgenomen in het [micro soft. Azure. webjobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-pakket, versie 3. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables) .

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="packages---functions-1x"></a>Pakketten-functions 1. x

De tabel opslag bindingen zijn opgenomen in het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet versie 2. x. De bron code voor het pakket bevindt zich in de GitHub-opslag plaats [Azure-webjobs-SDK](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.Storage/Table) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Volgende stappen

- [Table Storage-gegevens lezen wanneer een functie wordt uitgevoerd](./functions-bindings-storage-table-input.md)
- [Table Storage-gegevens schrijven vanuit een functie](./functions-bindings-storage-table-output.md)
