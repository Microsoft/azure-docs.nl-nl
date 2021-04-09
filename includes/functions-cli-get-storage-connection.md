---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80673375"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage-verbindingsreeks ophalen

U hebt eerder een Azure Storage-account gemaakt dat door de functie-app moet worden gebruikt. De verbindingsreeks voor dit account wordt veilig opgeslagen in de app-instellingen in Azure. Door de instelling in het bestand *local.settings.json* te downloaden, kunt u deze verbinding gebruiken om naar een Storage-wachtrij in hetzelfde account te schrijven wanneer u de functie lokaal uitvoert. 

1. Voer in de hoofdmap van het project de volgende opdracht uit en vervang `<app_name>` door de naam van uw functie-app uit de vorige quickstart. Met deze opdracht worden alle bestaande waarden in het bestand overschreven.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Open het bestand *local.settings.json* en zoek waarde `AzureWebJobsStorage`. Dit is de verbindingsreeks voor het Storage-account. U gebruikt de naam `AzureWebJobsStorage` en de verbindingsreeks in andere secties van dit artikel.

> [!IMPORTANT]
> Omdat *local.settings.json* geheimen bevat die zijn gedownload van Azure, moet u dit bestand altijd uitsluiten van broncodebeheer. Met het bestand *.gitignore*, dat met een lokaal Functions-project is gemaakt, wordt het bestand standaard uitgesloten.