---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 11d426016cfe1a8a9ff843da518f57c08881be5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96842398"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

1. Voer uw functie uit door de lokale Azure Functions runtime host te starten vanuit de map *LocalFunctionProj*:

    ```
    func start
    ```

    Naar het einde van de uitvoer moeten de volgende regels worden weergegeven: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Als HttpExample niet verschijnt zoals hierboven weergegeven, hebt u waarschijnlijk de host gestart van buiten de hoofdmap van het project. In dat geval gebruikt u **Ctrl**+**C** om de host te stoppen, gaat u naar de hoofdmap van het project en voert u de vorige opdracht opnieuw uit.

1. Kopieer de URL van uw `HttpExample`-functie van deze uitvoer naar een browser en voeg de query tekenreeks toe `?name=<YOUR_NAME>`, waardoor de volledige URL verschijnt, zoals `http://localhost:7071/api/HttpExample?name=Functions`. In de browser moet een bericht als het volgende weergeven `Hello Functions`:

    ![Resultaat van de functie lokaal uitgevoerd in de browser](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. De terminal waarin u uw project hebt gestart, toont ook de logboek uitvoer wanneer u aanvragen doet.

1. Wanneer u klaar bent, gebruikt u **Ctrl**+**C** en kiest u `y` om de functiehost te stoppen.
