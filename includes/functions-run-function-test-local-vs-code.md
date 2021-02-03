---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2021
ms.author: glenga
ms.openlocfilehash: 1a0521f76a2cf986f7036d1f701a40a156d16ee7
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493455"
---
## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Visual Studio Code integreert met [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md) om u een project te laten uitvoeren vanaf uw lokale ontwikkelaarscomputer voordat u in Azure publiceert.

1. Druk op <kbd>F5</kbd> om het functie-app-project te starten en uw functie aan te roepen. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**. Uw app wordt gestart in het deel venster **Terminal** . Kopieer het URL-eindpunt van uw functie die lokaal wordt uitgevoerd en door HTTP is geactiveerd.

    ![Lokale functie versus code-uitvoer](./media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

    Als u problemen ondervindt met het uitvoeren op Windows, moet u ervoor zorgen dat de standaardterminal voor Visual Studio Code niet is ingesteld op **WSL Bash**.

1. Ga naar het gedeelte **functies van Azure:** als u kern hulpprogramma's uitvoert. Vouw onder **functies** **lokale project**  >  **functies** uit. Klik met de rechter muisknop (Windows) of <kbd>CTRL</kbd> (macOS) op de `HttpExample` functie en kies **functie nu uitvoeren...**.

    :::image type="content" source="media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="De functie nu uitvoeren vanuit Visual Studio code":::

1. In de **hoofd tekst** van de aanvraag ziet u de waarde van de aanvraag bericht hoofdtekst van `{ "name": "Azure" }` . Druk op ENTER om dit aanvraag bericht naar uw functie te verzenden.  

1. Wanneer de functie lokaal wordt uitgevoerd en een antwoord retourneert, wordt er een melding gegenereerd in Visual Studio code. Informatie over de uitvoering van de functie wordt weer gegeven in het deel venster **Terminal** .

1. Druk op <kbd>CTRL + C</kbd> om Core Tools te stoppen en de verbinding met het foutopsporingsprogramma te verbreken.
