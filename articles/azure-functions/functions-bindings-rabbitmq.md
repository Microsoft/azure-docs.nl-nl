---
title: Azure RabbitMQ-bindingen voor Azure Functions
description: Meer informatie over het verzenden van Azure RabbitMQ-triggers en-bindingen in Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: a38015d9f7560930d77d5d50ac70dca5bcdde6a6
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672505"
---
# <a name="rabbitmq-bindings-for-azure-functions-overview"></a>RabbitMQ-bindingen voor Azure Functions-overzicht

> [!NOTE]
> De RabbitMQ-bindingen worden alleen volledig ondersteund in **Windows Premium en speciale** abonnementen. Verbruik en Linux worden momenteel niet ondersteund.

Azure Functions integreert met [RabbitMQ](https://www.rabbitmq.com/) via [Triggers en bindingen](./functions-triggers-bindings.md). Met de Azure Functions RabbitMQ-extensie kunt u berichten verzenden en ontvangen met behulp van de RabbitMQ-API met functies.

| Bewerking | Type |
|---------|---------|
| Een functie uitvoeren wanneer een RabbitMQ-bericht door de wachtrij wordt opgehaald | [Trigger](./functions-bindings-rabbitmq-trigger.md) |
| RabbitMQ-berichten verzenden |[Uitvoer binding](./functions-bindings-rabbitmq-output.md) |

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

Om aan de slag te gaan met het ontwikkelen met deze uitbrei ding, moet u eerst [een RabbitMQ-eind punt instellen](https://github.com/Azure/azure-functions-rabbitmq-extension/wiki/Setting-up-a-RabbitMQ-Endpoint). Bekijk de [pagina aan](https://www.rabbitmq.com/getstarted.html)de slag voor meer informatie over RabbitMQ.

### <a name="functions-3x-and-higher"></a>Functies 3. x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 4. x | |
| C#-script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's] wordt aanbevolen voor gebruik met Visual Studio code. |
| C#-script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.RabbitMQ
[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ./functions-bindings-register.md
[Extensie van Azure-Hulpprogram Ma's]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x-and-2x"></a>Functions 1. x en 2. x

RabbitMQ-bindings extensies worden niet ondersteund voor functions 1. x en 2. x. Gebruik de functies 3. x en hoger.

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren wanneer een RabbitMQ-bericht wordt gemaakt (trigger)](./functions-bindings-rabbitmq-trigger.md)
- [RabbitMQ-berichten verzenden vanuit Azure Functions (uitvoer binding)](./functions-bindings-rabbitmq-output.md)