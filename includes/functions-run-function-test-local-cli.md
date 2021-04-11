---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d400533039ea74a878cb8e543c22de02ee77e4f5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283008"
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

1. Kopieer de URL van uw `HttpExample`-functie van deze uitvoer naar een browser en voeg de query tekenreeks toe `?name=<YOUR_NAME>`, waardoor de volledige URL verschijnt, zoals `http://localhost:7071/api/HttpExample?name=Functions`. In de browser wordt een antwoord bericht weer gegeven waarin de waarde van de query reeks wordt geretourneerd. De terminal waarin u uw project hebt gestart, toont ook de logboek uitvoer wanneer u aanvragen doet.

1. Wanneer u klaar bent, gebruikt u **Ctrl**+**C** en kiest u `y` om de functiehost te stoppen.
