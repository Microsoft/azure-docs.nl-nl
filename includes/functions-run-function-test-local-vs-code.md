---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: a3cb0986637d5ce238930fb87aef71fed684097a
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842344"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio Code integreert met [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) om u een project te laten uitvoeren vanaf uw lokale ontwikkelaarscomputer voordat u in Azure publiceert.

1. Druk op <kbd>F5</kbd> om het functie-app-project te starten en uw functie aan te roepen. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**. Als u problemen ondervindt met het uitvoeren op Windows, moet u ervoor zorgen dat de standaardterminal voor Visual Studio Code niet is ingesteld op **WSL Bash**.

1. Als u Azure Functions Core Tools nog niet hebt geïnstalleerd, selecteert u **Installeren** als u daar om wordt gevraagd. Wanneer Core Tools zijn geïnstalleerd, wordt u app gestart in het deelvenster **Terminal**. Kopieer het URL-eindpunt van uw functie die lokaal wordt uitgevoerd en door HTTP is geactiveerd.

    ![Lokale functie versus code-uitvoer](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Als Core Tools wordt uitgevoerd, navigeert u naar de volgende URL om een GET-aanvraag uit te voeren die de querytekenreeks `?name=Functions` bevat.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. Er wordt een antwoord geretourneerd die er in een browser als volgt uitziet:

    ![Browser - voorbeelduitvoer van localhost](./media/functions-run-function-test-local-vs-code/functions-test-local-browser.png)

1. Informatie over de aanvraag wordt weergegeven in het deelvenster **Terminal**.

    ![Taakhost starten - VS Code-terminaluitvoer](./media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Druk op <kbd>CTRL + C</kbd> om Core Tools te stoppen en de verbinding met het foutopsporingsprogramma te verbreken.
