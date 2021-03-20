---
title: Azure Service Bus bindingen voor Azure Functions
description: Meer informatie over het verzenden van Azure Service Bus triggers en bindingen in Azure Functions.
author: craigshoemaker
ms.assetid: daedacf0-6546-4355-a65c-50873e74f66b
ms.topic: reference
ms.date: 02/19/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: b32f16d170df9963960862bc82aef1a4baf13896
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92104441"
---
# <a name="azure-service-bus-bindings-for-azure-functions"></a>Azure Service Bus bindingen voor Azure Functions

Azure Functions integreert met [Azure service bus](https://azure.microsoft.com/services/service-bus) via [Triggers en bindingen](./functions-triggers-bindings.md). Door te integreren met Service Bus kunt u functies bouwen die reageren op berichten over de wachtrij of het onderwerp.

| Bewerking | Type |
|---------|---------|
| Een functie uitvoeren wanneer een Service Bus wachtrij of onderwerps bericht wordt gemaakt | [Trigger](./functions-bindings-service-bus-trigger.md) |
| Azure Service Bus berichten verzenden |[Uitvoer binding](./functions-bindings-service-bus-output.md) |

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

> [!NOTE]
> De binding van de Service Bus biedt momenteel geen ondersteuning voor verificatie met behulp van een beheerde identiteit. Gebruik in plaats daarvan een [Service Bus hand tekening voor gedeelde toegang](../service-bus-messaging/service-bus-authentication-and-authorization.md#shared-access-signature).

### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 4. x | |
| C#-script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's] wordt aanbevolen voor gebruik met Visual Studio code. |
| C#-script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.ServiceBus/
[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ./functions-bindings-register.md
[Extensie van Azure-Hulpprogram Ma's]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x apps hebben automatisch een verwijzing naar het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versie 2. x.

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een Service Bus wachtrij of onderwerp bericht wordt gemaakt (trigger)](./functions-bindings-service-bus-trigger.md)
- [Azure Service Bus berichten verzenden vanuit Azure Functions (uitvoer binding)](./functions-bindings-service-bus-output.md)