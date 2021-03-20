---
title: Uw eerste duurzame functie maken in Azure Functions met behulp van PowerShell
description: Maak en publiceer een duurzame Azure-functie in PowerShell met behulp van Visual Studio Code.
author: anthonychu
ms.topic: quickstart
ms.date: 08/10/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: edd02085abe63b124082255247362f096248ba82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317256"
---
# <a name="create-your-first-durable-function-in-powershell"></a>Uw eerste duurzame functie maken in PowerShell

*Durable Functions* is een extensie van [Azure Functions](../functions-overview.md) waarmee u stateful functies kunt schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd.

In dit artikel leert u hoe u de Azure Functions-extensie van Visual Studio Code kunt gebruiken om lokaal een duurzame ‘Hallo wereld’-functie te maken en te testen.  Met deze functie worden aanroepen naar andere functies ingedeeld en aan elkaar gekoppeld. Vervolgens publiceert u de functiecode op Azure.

![Duurzame functie uitvoeren in Azure](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* Installeer [Visual Studio Code](https://code.visualstudio.com/download).

* De [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) VS Code-extensie installeren

* Zorg ervoor dat u beschikt over de nieuwste versie van de [Azure Functions Core Tools](../functions-run-local.md).

* Voor Durable Functions is een Azure-opslagaccount vereist. U hebt een Azure-abonnement nodig.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Uw lokale project maken 

In deze sectie gebruikt u Visual Studio Code om een lokaal Azure Functions-project te maken. 

1. Druk in Visual Studio Code op F1 (of Ctrl+Shift+P) om het opdrachtenpalet te openen. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Create New Project...`.

    ![Functie maken](media/quickstart-js-vscode/functions-create-project.png)

1. Kies een lege map voor uw project en kies **Selecteren**.

1. Volg de instructies en geef de volgende informatie op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een taal voor uw functie-appproject | PowerShell | Maak een lokaal PowerShell Functions-project. |
    | Een versie selecteren | Azure Functions v3 | U ziet deze optie alleen wanneer de Core Tools niet al zijn geïnstalleerd. In dit geval worden de Core Tools geïnstalleerd wanneer u de app voor het eerst uitvoert. |
    | Selecteer een sjabloon voor de eerste functie van uw project | Nu overslaan | |
    | Selecteer hoe u uw project wilt openen | In het huidige venster openen | Opent VS Code opnieuw in de map die u hebt geselecteerd. |

Visual Studio Code installeert zo nodig de Azure Functions Core Tools. Er wordt ook een functie-app-project gemaakt in een map. Dit project bevat de configuratiebestanden [host.json](../functions-host-json.md) en [local.settings.json](../functions-run-local.md#local-settings-file).

Er wordt ook een package.json-bestand gemaakt in de hoofdmap.

### <a name="configure-function-app-to-use-powershell-7"></a>Functie-app configureren om PowerShell 7 te gebruiken

Open het bestand *local.settings.json* en controleer of een instelling met de naam `FUNCTIONS_WORKER_RUNTIME_VERSION` is ingesteld op `~7`. Als deze ontbreekt of op een andere waarde is ingesteld, werkt u de inhoud van het bestand bij.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

## <a name="create-your-functions"></a>Uw functies maken

De meest eenvoudige Durable Functions-app bevat drie functies:

* *Orchestrator-functie*: beschrijft een werkstroom die andere functies organiseert.
* *Activiteitfunctie*: wordt aangeroepen door de Orchestrator-functie, voert het werk uit en retourneert optioneel een waarde.
* *Clientfunctie*: een reguliere Azure-functie waarmee een Orchestrator-functie wordt gestart. In dit voorbeeld wordt een door HTTP geactiveerde functie gebruikt.

### <a name="orchestrator-function"></a>Orchestrator-functie

U gebruikt een sjabloon om de code van de Durable Function te maken in uw project.

1. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Create Function...`.

1. Volg de instructies en geef de volgende informatie op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een sjabloon voor uw functie | Durable Functions-orchestrator (preview) | Een Durable Functions-indeling maken |
    | Geef een functienaam op | HelloOrchestrator | Naam van uw Durable Function |

U hebt een orchestrator toegevoegd om de activiteitsfuncties te coördineren. Open *HelloOrchestrator/run.ps1* om de orchestrator-functie weer te geven. Elke aanroep van de `Invoke-ActivityFunction` cmdlet roept een activiteitfunctie aan met de naam `Hello`.

Vervolgens voegt u de `Hello`-activiteitsfunctie toe waarnaar wordt verwezen.

### <a name="activity-function"></a>Activiteitsfunctie

1. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Create Function...`.

1. Volg de instructies en geef de volgende informatie op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een sjabloon voor uw functie | Durable Functions-activiteit (preview) | Een ectiviteitsfunctie maken |
    | Geef een functienaam op | Hello | Naam van uw activiteitsfunctie |

U hebt de `Hello`-activiteitsfunctie toegevoegd die wordt aangeroepen door de orchestrator. Open *Hello/run.ps1* om te zien dat deze een naam als invoer gebruikt en een begroeting retourneert. Met een activiteitsfunctie kunt u acties uitvoeren, zoals het maken van een database-aanroep of het uitvoeren van een berekening.

Ten slotte voegt u een door HTTP geactiveerde functie toe waarmee de indeling wordt gestart.

### <a name="client-function-http-starter"></a>Clientfunctie (HTTP-starter)

1. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Create Function...`.

1. Volg de instructies en geef de volgende informatie op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een sjabloon voor uw functie | HTTP-starter Durable Functions (preview) | Een HTTP-starterfunctie maken |
    | Geef een functienaam op | HttpStart | Naam van uw activiteitsfunctie |
    | Autorisatieniveau | Anoniem | Sta omwille van de demo toe dat de functie wordt aangeroepen zonder verificatie |

U hebt een door HTTP geactiveerde functie toegevoegd waarmee een indeling wordt gestart. Open *HttpStart/run.ps1* om te zien dat de cmdlet van `Start-NewOrchestration` wordt gebruikt om een nieuwe indeling te starten. Vervolgens wordt de `New-OrchestrationCheckStatusResponse` cmdlet gebruikt om een HTTP-antwoord te retourneren met URL's die kunnen worden gebruikt om de nieuwe indeling te controleren en beheren.

U hebt nu een Durable Functions-app die lokaal kan worden uitgevoerd en in Azure kan worden geïmplementeerd.

## <a name="test-the-function-locally"></a>De functie lokaal testen

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie-app vanuit Visual Studio Code start, wordt u gevraagd deze hulpprogramma's te installeren.

1. Stel een onderbrekingspunt in code van de `Hello`-activiteitsfunctie (*Hello/run.ps1*) in om uw functie te testen. Druk op F5 of selecteer `Debug: Start Debugging` in het opdrachtpalet om het project met de functie-app te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

    > [!NOTE]
    > Raadpleeg de [Durable Functions-diagnoses](durable-functions-diagnostics.md#debugging) voor meer informatie over foutopsporing.

1. Durable Functions vereist een Azure-opslagaccount om te kunnen worden uitgevoerd. Kies **Opslagaccount selecteren** wanneer VS Code u vraagt een opslagaccount te selecteren.

    ![Een opslagaccount maken](media/quickstart-js-vscode/functions-select-storage.png)

1. Volg de aanwijzingen en geef de volgende informatie op om een nieuw opslagaccount te maken in Azure.

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Abonnement selecteren | *naam van uw abonnement* | Selecteer uw Azure-abonnement |
    | Selecteer een opslagaccount | Een nieuw opslagaccount maken |  |
    | Voer de naam van het nieuwe opslagaccount in | *unieke naam* | Naam van het opslagaccount dat moet worden gemaakt |
    | Een resourcegroep selecteren | *unieke naam* | Naam van de resourcegroep die moet worden gemaakt |
    | Selecteer een locatie | *regio* | Kies een regio bij u in de buurt |

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](media/quickstart-js-vscode/functions-f5.png)

1. Verzend met behulp van uw browser of een hulpprogramma zoals [Postman](https://www.getpostman.com/) of [cURL](https://curl.haxx.se/) een HTTP POST-aanvraag naar het URL-eindpunt. Vervang het laatste segment door de naam van de Orchestrator-functie (`HelloOrchestrator`). De URL moet vergelijkbaar zijn met `http://localhost:7071/api/orchestrators/HelloOrchestrator`.

   De reactie is het eerste resultaat van de HTTP-functie dat u laat weten dat de duurzame indeling is gestart. Dit is nog niet het eindresultaat van de orchestrator. De reactie bevat enkele nuttige URL's. Maar eerst gaan we de status van de orchestrator opvragen.

1. Kopieer de URL-waarde voor `statusQueryGetUri`, plak deze in de adresbalk van de browser en voer de aanvraag uit. Eventueel kunt u Postman blijven gebruiken om de GET-aanvraag uit te voeren.

   De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet een eindige reactie krijgen, die laat zien dat het exemplaar is voltooid en die de uitvoer of resultaten van de Durable Function bevat. Deze ziet er als volgt uit: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Druk op **Shift + F5** in VS Code als u wilt stoppen met het opsporen van fouten.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren in Azure.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

In deze sectie maakt u een functie-app en de bijbehorende resources in uw Azure-abonnement en implementeert u vervolgens uw code. 

> [!IMPORTANT]
> Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven. 


1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: In het gebied** Functies kiest u de knop **Implementeren in functie-app ...** .

    ![Uw project naar Azure publiceren](../../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer map**: Kies een map in uw werkruimte of blader naar een map die de functie-app bevat. Dit wordt niet weergegeven als u al een geldige functie-app hebt geopend.

    + **Selecteer abonnement**: Kies het abonnement dat u wilt gebruiken. Dit ziet u niet als u maar één abonnement hebt.

    + **Selecteer functie-app in Azure**: Kies `+ Create new Function App`. (Kies niet de optie `Advanced` die niet in dit artikel wordt behandeld.)
      
    + **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om er zeker van te zijn dat deze uniek is in Azure Functions. 

    + **Selecteer een runtime**: Kies de versie van PowerShell die u lokaal uitvoert. U kunt de opdracht `pwsh -version` gebruiken om uw versie te controleren.

        > [!NOTE]
        > De Azure Functions VS Code-uitbreiding ondersteunt mogelijk PowerShell 7 nog niet. Als PowerShell 7 niet beschikbaar is als optie, kies dan PowerShell 6.x voor nu en [werk de versie handmatig bij](#update-function-app-ps7) nadat de functie-app is gemaakt.

    + **Selecteer een locatie voor nieuwe resources**:  Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. 
    
1.  Wanneer dit is voltooid, worden de volgende Azure-resources in uw abonnement gemaakt met behulp van namen op basis van de naam van uw functie-app:
    
    + Een resourcegroep, wat een logische container is voor gerelateerde resources.
    + Een standaard Azure Storage-account, waarin de status en andere gegevens van uw projecten worden onderhouden.
    + Een verbruiksplan dat de onderliggende host definieert voor uw serverloze functie-app. 
    + Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen binnen hetzelfde hostingabonnement.
    + Een Application Insights-exemplaar dat is verbonden met de functie-app, die het gebruik van uw serverloze functie bijhoudt.

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven.

1. <a name="update-function-app-ps7"></a>Als u niet eerder *PowerShell 7* kon kiezen bij het maken van de functie-app, druk dan op F1 (of Ctrl/Cmd+Shift+P) om het opdrachtenpalet te openen. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Upload Local Settings...`. Volg de aanwijzingen om de functie-app te selecteren die u hebt gemaakt. Als u wordt gevraagd de bestaande instellingen te overschrijven, selecteert u *Nee op alles*.
    
1. Selecteer in deze melding de optie **Uitvoer weergeven** om de resultaten van het maken en implementeren te bekijken, inclusief de Azure-resources die u hebt gemaakt. Als u de melding mist, selecteert u het belpictogram in de rechterbenedenhoek om deze opnieuw weer te geven.

    ![Volledige melding maken](../../../includes/media/functions-publish-project-vscode/function-create-notifications.png)


## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de URL van de HTTP-trigger vanuit het deelvenster **Output** (Uitvoer). De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio Code gebruikt om een duurzame PowerShell-functie-app te maken en te publiceren.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)
