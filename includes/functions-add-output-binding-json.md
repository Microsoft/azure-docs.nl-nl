---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9b3859d854b3900cc30eda6a95b8425da6763e59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94341026"
---
Bindingskenmerken worden rechtstreeks in het bestand function.json gedefinieerd. Afhankelijk van het type binding zijn er aanvullende eigenschappen vereist. In de [configuratie van de wachtrij uitvoer](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) worden de vereiste velden beschreven voor een Azure Storage-wachtrijbinding. De extensie maakt het eenvoudig om bindingen toe te voegen aan het bestand function.json. 

Als u een binding wilt maken, klikt u met de rechtermuisknop (ctrl + klik op macOS) op het bestand `function.json` in uw HttpTrigger-map en kiest u **Binding toevoegen...** . Volg de aanwijzingen om de volgende bindingseigenschappen te definiëren voor de nieuwe binding:

| Vraag | Waarde | Beschrijving |
| -------- | ----- | ----------- |
| **Bindingsrichting selecteren** | `out` | De binding is een uitvoerbinding. |
| **Binding met richting selecteren...** | `Azure Queue Storage` | De binding is een Azure Storage-wachtrijbinding. |
| **De naam voor het identificeren van deze binding in uw code** | `msg` | Naam die de bindingsparameter identificeert waar in uw code naar wordt verwezen. |
| **De wachtrij waarnaar het bericht wordt verzonden** | `outqueue` | De naam van de wachtrij waarnaar de binding schrijft. Wanneer de *queueName* niet bestaat, wordt deze bij het eerste gebruik door de binding gemaakt. |
| **Selecteer de instelling in 'local.setting.json'** | `AzureWebJobsStorage` | De naam van een toepassingsinstelling die de verbindingsreeks voor het Storage-account bevat. De instelling `AzureWebJobsStorage` bevat de verbindingsreeks voor het Storage-account dat u hebt gemaakt met de functie-app. |

Er wordt een binding toegevoegd aan de matrix `bindings` in uw function.json, die er als volgt uit hoort te zien:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::
