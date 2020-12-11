---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/19/2020
ms.author: glenga
ms.openlocfilehash: a5c113849296275432acf1f5603377a1909a2c04
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842265"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Azure Functions Core Tools integreert met Visual Studio Code, zodat u Azure Functions-projecten lokaal kunt uitvoeren en problemen erin kunt oplossen. raadpleeg [Fouten lokaal opsporen in PowerShell Azure Functions](../articles/azure-functions/functions-debug-powershell-local.md) voor informatie over hoe u fouten oplost in Visual Studio Code. 
1. Druk op <kbd>F5</kbd> om het functie-app-project te starten en uw functie aan te roepen. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**. Als u problemen ondervindt met het uitvoeren op Windows, moet u ervoor zorgen dat de standaardterminal voor Visual Studio Code niet is ingesteld op **WSL Bash**.

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](./media/functions-run-function-test-local-vs-code-ps/functions-vscode-f5.png)

1. Voeg de querytekenreeks `?name=<yourname>` toe aan deze URL en gebruik `Invoke-RestMethod` vervolgens in een tweede PowerShell-opdrachtprompt om de aanvraag als volgt uit te voeren:

    ```powershell
    PS > Invoke-RestMethod -Method Get -Uri http://localhost:7071/api/HttpTrigger?name=PowerShell
    Hello PowerShell
    ```

    U kunt ook de GET-aanvraag uitvoeren vanaf een browser via de volgende URL:

    `http://localhost:7071/api/HttpExample?name=PowerShell`

    Wanneer u het HttpTrigger-eindpunt aanroept zonder een `name`-parameter door te voeren als queryparameter of in hoofdtekst, retourneert de functie de fout `BadRequest`. Wanneer u de code bekijkt in run.ps1 ziet u dat deze fout opzettelijk optreedt.

1. Informatie over de aanvraag wordt weergegeven in het deelvenster **Terminal**.

    ![Functie-uitvoering in het deelvenster Terminal](./media/functions-run-function-test-local-vs-code-ps/function-execution-terminal.png)

1. Wanneer u klaar bent, drukt u op **CTRL + C** om Core Tools te stoppen.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.
