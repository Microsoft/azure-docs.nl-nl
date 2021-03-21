---
title: Azure Queue Storage-trigger en bindingen voor Azure Functions-overzicht
description: Meer informatie over het gebruik van de Azure Queue Storage-trigger en uitvoer binding in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381474"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Queue Storage-trigger en bindingen voor Azure Functions-overzicht

Azure Functions kunnen worden uitgevoerd als nieuwe Azure Queue-opslag berichten worden gemaakt en kunnen wachtrij berichten binnen een functie worden geschreven.

| Bewerking | Type |
|---------|---------|
| Een functie uitvoeren als gegevens wijzigingen in de wachtrij-opslag | [Trigger](./functions-bindings-storage-queue-trigger.md) |
| Wachtrij opslag berichten schrijven |[Uitvoer binding](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 3. x | |
| C#-script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wordt aanbevolen voor gebruik met Visual Studio code. |
| C#-script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

#### <a name="storage-extension-5x-and-higher"></a>Opslag extensie 5. x en hoger

Er is een nieuwe versie van de opslag bindingen uitbrei ding beschikbaar als [Preview-NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2). Deze preview introduceert de mogelijkheid om [verbinding te maken met een identiteit in plaats van een geheim](./functions-reference.md#configure-an-identity-based-connection). Voor .NET-toepassingen worden ook de typen gewijzigd waarmee u een binding kunt maken, waarbij de typen van `WindowsAzure.Storage` en `Microsoft.Azure.Storage` met nieuwere typen van [Azure. storage. queues](/dotnet/api/azure.storage.queues)worden vervangen.

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

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.jsop instellingen

In deze sectie worden de algemene configuratie-instellingen beschreven die beschikbaar zijn voor deze binding in versie 2. x en hoger. In het volgende voor beeld *host.jsin* het onderstaande bestand bevat alleen de instellingen van versie 2. x + voor deze binding. Zie voor meer informatie over globale configuratie-instellingen in versie 2. x en verder [host.jsop referentie voor Azure functions](functions-host-json.md).

> [!NOTE]
> Zie [host.jsbij verwijzing voor Azure functions 1. x](functions-host-json-v1.md)voor een referentie van host.jsin in functions 1. x.

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|maxPollingInterval|00:00:01|Het maximum interval tussen de polls van de wachtrij. Minimum is 00:00:00.100 (100 MS) en wordt verhoogd naar 00:01:00 (1 min.).  In functions 2. x en hoger is het gegevens type a `TimeSpan` , terwijl u in versie 1. x in milliseconden.|
|visibilityTimeout|00:00:00|Het tijds interval tussen nieuwe pogingen wanneer het verwerken van een bericht mislukt. |
|batchSize|16|Het aantal wachtrij berichten dat door de functions-runtime gelijktijdig wordt opgehaald en processen parallel. Wanneer het nummer dat wordt verwerkt, naar de wordt verwerk `newBatchThreshold` , wordt er door de runtime een andere batch opgehaald en worden deze berichten verwerkt. Het maximum aantal gelijktijdige berichten dat per functie wordt verwerkt, is `batchSize` plus `newBatchThreshold` . Deze limiet geldt afzonderlijk voor elke door de wachtrij geactiveerde functie. <br><br>Als u een parallelle uitvoering wilt voor komen voor berichten die worden ontvangen op één wachtrij, kunt u instellen `batchSize` op 1. Met deze instelling wordt de gelijktijdigheid echter geëlimineerd, zolang uw functie-app alleen op één virtuele machine (VM) wordt uitgevoerd. Als de functie-app wordt geschaald naar meerdere Vm's, kan elke virtuele machine één exemplaar van elke door de wachtrij geactiveerde functie uitvoeren.<br><br>De maximum waarde `batchSize` is 32. |
|maxDequeueCount|5|Het aantal keren dat een bericht moet worden verwerkt voordat het naar de verontreinigde wachtrij wordt verplaatst.|
|newBatchThreshold|batchSize/2|Telkens wanneer het aantal berichten dat gelijktijdig wordt verwerkt, naar dit nummer wordt opgehaald, haalt de runtime een andere batch op.|
|messageEncoding|base64| Deze instelling is alleen beschikbaar in [extensie versie 5.0.0 en hoger](#storage-extension-5x-and-higher). Het vertegenwoordigt de coderings indeling voor berichten. Geldige waarden zijn `base64` en `none` .|

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren als gegevens wijzigingen in de wachtrij-opslag (trigger)](./functions-bindings-storage-queue-trigger.md)
- [Wachtrij opslag berichten schrijven (uitvoer binding)](./functions-bindings-storage-queue-output.md)
