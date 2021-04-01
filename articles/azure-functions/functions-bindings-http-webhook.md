---
title: Azure-functies HTTP-triggers en bindingen
description: Meer informatie over het gebruik van HTTP-triggers en-bindingen in Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.openlocfilehash: 5236a3be9f8908d886274764dfc0c0da5b3565bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104475"
---
# <a name="azure-functions-http-triggers-and-bindings-overview"></a>Overzicht van Azure Functions HTTP-triggers en-bindingen

Azure Functions kan worden aangeroepen via HTTP-aanvragen om serverloze Api's te bouwen en te reageren op [webhooks](https://en.wikipedia.org/wiki/Webhook).

| Bewerking | Type |
|---------|---------|
| Een functie uitvoeren vanuit een HTTP-aanvraag | [Trigger](./functions-bindings-http-webhook-trigger.md) |
| Een HTTP-antwoord retour neren van een functie |[Uitvoer binding](./functions-bindings-http-webhook-output.md) |

De code in dit artikel wordt standaard ingesteld op .NET core-syntaxis, gebruikt in functions versie 2. x en hoger. Zie voor meer informatie over de syntaxis van 1. x de [functies sjablonen van 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 3. x | |
| C#-script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) wordt aanbevolen voor gebruik met Visual Studio code. |
| C#-script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http
[Uw extensies bijwerken]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x apps hebben automatisch een verwijzing naar het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versie 2. x.

## <a name="next-steps"></a>Volgende stappen

- [Een functie uitvoeren vanuit een HTTP-aanvraag](./functions-bindings-http-webhook-trigger.md)
- [Een HTTP-antwoord retour neren van een functie](./functions-bindings-http-webhook-output.md)