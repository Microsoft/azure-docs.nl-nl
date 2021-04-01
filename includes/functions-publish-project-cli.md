---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93424740"
---
## <a name="deploy-the-function-project-to-azure"></a>Het functieproject implementeren in Azure

Nadat u uw functie-app in Azure hebt gemaakt, kunt u nu uw lokale functieproject implementeren met behulp van de opdracht [func azure functionapp publish](../articles/azure-functions/functions-run-local.md#project-file-deployment).  

Vervang `<APP_NAME>` in het volgende voorbeeld door de naam van uw app.

```console
func azure functionapp publish <APP_NAME>
```

Met de publicatieopdracht worden de resultaten weergegeven die vergelijkbaar zijn met de volgende uitvoer (afgekapt voor de leesbaarheid):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
