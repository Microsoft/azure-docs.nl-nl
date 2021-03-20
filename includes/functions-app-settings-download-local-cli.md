---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "72329546"
---
U hebt al een functie-app in Azure gemaakt, in combinatie met het vereiste Storage-account. De verbindingsreeks voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. In dit artikel schrijft u berichten naar een opslagwachtrij in hetzelfde account. Als u verbinding wilt maken met uw Storage-account wanneer u de functie lokaal uitvoert, moet u de app-instellingen downloaden naar het bestand local.settings.json. 

Voer in de hoofdmap van het project de volgende Azure Functions Core Tools-opdracht uit om instellingen te downloaden naar local.settings.json, waarbij u `<APP_NAME>` vervangt door de naam van uw functie-app uit het vorige artikel:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Mogelijk moet u zich aanmelden bij uw Azure-account.

> [!IMPORTANT]  
> Met deze opdracht worden alle bestaande instellingen overschreven met waarden uit uw functie-app in Azure.  
>
> Omdat het local.settings.json-bestand geheimen bevat, wordt het nooit gepubliceerd en dient het te worden uitgesloten van het broncodebeheer.  
> 

U hebt de waarde `AzureWebJobsStorage` nodig. Dit is de verbindingsreeks van het Storage-account. U gebruikt deze verbinding om te controleren of de uitvoerbinding werkt zoals verwacht.
