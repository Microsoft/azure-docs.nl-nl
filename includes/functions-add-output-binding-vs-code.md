---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493939"
---
Met Visual Studio code kunt u bindingen toevoegen aan uw function.jsop bestand door een handige set prompts te volgen. 

Als u een binding wilt toevoegen, opent u de opdracht pallet (F1) en typt u **Azure functions: binding toevoegen...** kiest u de functie voor de nieuwe binding en volgt u de prompts, die variëren afhankelijk van het type binding dat wordt toegevoegd aan de functie. 

Hieronder ziet u een voor beeld van een nieuwe opslag-uitvoer binding:

| Prompt | Waarde | Beschrijving |
| -------- | ----- | ----------- |
| **Bindingsrichting selecteren** | `out` | De binding is een uitvoerbinding. |
| **Binding met richting selecteren** | `Azure Queue Storage` | De binding is een Azure Storage-wachtrijbinding. |
| **De naam voor het identificeren van deze binding in uw code** | `msg` | Naam die de bindingsparameter identificeert waar in uw code naar wordt verwezen. |
| **De wachtrij waarnaar het bericht wordt verzonden** | `outqueue` | De naam van de wachtrij waarnaar de binding schrijft. Wanneer de *queueName* niet bestaat, wordt deze bij het eerste gebruik door de binding gemaakt. |
| **Selecteer een instelling in het local.settings.js** | `MyStorageConnection` | De naam van een toepassings instelling die de connection string voor het opslag account bevat. De `AzureWebJobsStorage` instelling bevat de Connection String voor het opslag account dat u hebt gemaakt met de functie-app. |

U kunt ook met de rechter muisknop (CTRL + klik op macOS) rechtstreeks op de **function.js** in het bestand in de map functie een **binding toevoegen** selecteren en dezelfde prompts volgen.

In dit voor beeld wordt de volgende binding toegevoegd aan de `bindings` matrix in uw function.jsin het bestand:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```