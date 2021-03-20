---
title: Azure Event Grid bindingen voor Azure Functions
description: Meer informatie over het afhandelen van Event Grid gebeurtenissen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92104543"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Azure Event Grid bindingen voor Azure Functions

In deze Naslag informatie wordt uitgelegd hoe u [Event grid](../event-grid/overview.md) gebeurtenissen in azure functions kunt verwerken. Zie [gebeurtenissen ontvangen naar een HTTP-eind](../event-grid/receive-events.md)punt voor meer informatie over het afhandelen van Event grid berichten in een http-eind punten.

Event Grid is een Azure-service die HTTP-aanvragen verzendt om u op de hoogte te stellen van gebeurtenissen die zich in *uitgevers* voordoen. Een uitgever is de service of resource die van de gebeurtenis afkomstig is. Een Azure Blob-opslag account is bijvoorbeeld een uitgever en [een BLOB uploaden of verwijderen is een gebeurtenis](../storage/blobs/storage-blob-event-overview.md). Sommige [Azure-Services hebben ingebouwde ondersteuning voor het publiceren van gebeurtenissen naar Event grid](../event-grid/overview.md#event-sources).

Gebeurtenis- *handlers* ontvangen en verwerken gebeurtenissen. Azure Functions is een van [de verschillende Azure-Services met ingebouwde ondersteuning voor het verwerken van Event grid-gebeurtenissen](../event-grid/overview.md#event-handlers). In deze Naslag informatie kunt u een Event Grid trigger gebruiken om een functie aan te roepen wanneer een gebeurtenis wordt ontvangen van Event Grid en om de uitvoer binding te gebruiken voor het verzenden van gebeurtenissen naar een [Event grid aangepast onderwerp](../event-grid/post-to-custom-topic.md).

Als u wilt, kunt u een HTTP-trigger gebruiken om Event Grid gebeurtenissen te verwerken. Zie [gebeurtenissen ontvangen naar een HTTP-eind punt](../event-grid/receive-events.md). Op dit moment kunt u geen Event Grid trigger gebruiken voor een Azure Functions-app wanneer de gebeurtenis wordt bezorgd in het [CloudEvents-schema](../event-grid/cloudevents-schema.md#azure-functions). Gebruik in plaats daarvan een HTTP-trigger.

| Bewerking | Type |
|---------|---------|
| Een functie uitvoeren wanneer een Event Grid gebeurtenis wordt verzonden | [Trigger](./functions-bindings-event-grid-trigger.md) |
| Hiermee wordt een Event Grid gebeurtenis verzonden |[Uitvoer binding](./functions-bindings-event-grid-output.md) |

De code in deze referentie wordt standaard ingesteld op .NET core-syntaxis, gebruikt in functions versie 2. x en hoger. Zie voor meer informatie over de syntaxis van 1. x de [functies sjablonen van 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 2. x | |
| C#-script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wordt aanbevolen voor gebruik met Visual Studio code. |
| C#-script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Uw extensies bijwerken]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x apps hebben automatisch een verwijzing naar het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versie 2. x.

## <a name="next-steps"></a>Volgende stappen
* [Een functie uitvoeren wanneer een Event Grid gebeurtenis wordt verzonden](./functions-bindings-event-grid-trigger.md)
* [Een Event Grid gebeurtenis verzenden](./functions-bindings-event-grid-trigger.md)