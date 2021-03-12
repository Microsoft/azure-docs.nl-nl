---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 05d136093bd509e8c23ce8622423216326b0f1f2
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623547"
---
## <a name="add-to-your-functions-app"></a>Toevoegen aan uw functions-app

### <a name="functions-2x-and-higher"></a>Functions 2.x en hoger

Voor het werken met de trigger en bindingen moet u verwijzen naar het juiste pakket. Het NuGet-pakket wordt gebruikt voor .NET-klassen bibliotheken terwijl de uitbreidings bundel wordt gebruikt voor alle andere toepassings typen.

| Taal                                        | Toevoegen door...                                   | Opmerkingen 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Het [NuGet-pakket]installeren, versie 3. x | |
| C#-script, Java, java script, Python, Power shell | De [uitbreidings bundel] registreren          | De [extensie voor Azure-Hulpprogram ma's] wordt aanbevolen voor gebruik met Visual Studio code. |
| C#-script (alleen online in Azure Portal)         | Een binding toevoegen                            | Zie [uw extensies bijwerken]om bestaande bindings extensies bij te werken zonder uw functie-app opnieuw te publiceren. |

[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs
[core tools]: ../articles/azure-functions/functions-run-local.md
[uitbreidings bundel]: ../articles/azure-functions/functions-bindings-register.md#extension-bundles
[Uw extensies bijwerken]: ../articles/azure-functions/functions-bindings-register.md
[Extensie van Azure-Hulpprogram Ma's]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="event-hubs-extension-5x-and-higher"></a>Event Hubs extensie 5. x en hoger

Een nieuwe versie van de uitbrei ding voor de Event Hubs-bindingen is beschikbaar als een [Preview-NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/5.0.0-beta.1). Deze preview introduceert de mogelijkheid om [verbinding te maken met een identiteit in plaats van een geheim](../articles/azure-functions/functions-reference.md#configure-an-identity-based-connection). Voor .NET-toepassingen worden ook de typen gewijzigd waarmee u een binding kunt maken, waarbij de typen worden vervangen door `Microsoft.Azure.EventHubs` nieuwere typen van [Azure. Messa ging. Event hubs](/dotnet/api/azure.messaging.eventhubs).

> [!NOTE]
> Het preview-pakket is niet opgenomen in een uitbreidings bundel en moet hand matig worden geïnstalleerd. Voor .NET-Apps voegt u een verwijzing naar het pakket toe. Voor alle andere typen apps raadpleegt [u uw extensies bijwerken].

[core tools]: ./functions-run-local.md
[uitbreidings bundel]: ./functions-bindings-register.md#extension-bundles
[NuGet-pakket]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs/
[Uw extensies bijwerken]: ./functions-bindings-register.md
[Extensie van Azure-Hulpprogram Ma's]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Functions 1. x apps hebben automatisch een verwijzing naar het pakket [micro soft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) , versie 2. x.

## <a name="hostjson-settings"></a>host.jsop instellingen
<a name="host-json"></a>

Het [host.json](../articles/azure-functions/functions-host-json.md#eventhub)-bestand bevat instellingen die het gedrag van Event Hubs-triggers regelen. De configuratie verschilt afhankelijk van de versie van Azure Functions.

[!INCLUDE [functions-host-json-event-hubs](../articles/azure-functions/../../includes/functions-host-json-event-hubs.md)]