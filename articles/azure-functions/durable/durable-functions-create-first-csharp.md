---
title: Uw eerste Durable Function maken in Azure met behulp van C#
description: Maak en publiceer een Azure Durable Function met behulp van Visual Studio of Visual Studio Code.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: d5b6ecc12cee983cee0772da8b6f8f26a3b5a8ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008167"
---
# <a name="create-your-first-durable-function-in-c"></a>Uw eerste Durable Function maken in C\#

*Durable Functions* is een extensie van [Azure Functions](../functions-overview.md) waarmee u stateful functies kunt schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd.

::: zone pivot="code-editor-vscode"

In dit artikel leert u hoe u Visual Studio Code kunt gebruiken om lokaal een duurzame Hallo wereld-functie te maken en te testen.  Met deze functie organiseert en koppelt u aanroepen naar andere functies. Vervolgens publiceert u de functiecode op Azure. Deze hulpprogramma's zijn beschikbaar als onderdeel van de VS Code [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

![Schermopname van een Visual Studio-venster met een duurzame functie.](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* Installeer [Visual Studio Code](https://code.visualstudio.com/download).

* Installeer de volgende VS code-extensies:
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Zorg ervoor dat u beschikt over de nieuwste versie van de [Azure Functions Core Tools](../functions-run-local.md).

* Voor Durable Functions is een Azure-opslagaccount vereist. U hebt een Azure-abonnement nodig.

* Zorg ervoor dat versie 3.1 of een latere versie van de [.NET Core SDK](https://dotnet.microsoft.com/download) is geïnstalleerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Uw lokale project maken 

In deze sectie gebruikt u Visual Studio Code om een lokaal Azure Functions-project te maken. 

1. Druk in Visual Studio Code op F1 (of Ctrl+Shift+P) om het opdrachtenpalet te openen. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Create New Project...`.

    ![Een functieproject maken](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Kies een lege map voor uw project en kies **Selecteren**.

1. Volg de instructies en geef de volgende informatie op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een taal voor uw functie-appproject | C# | Een lokaal C# Functions-project maken. |
    | Een versie selecteren | Azure Functions v3 | U ziet deze optie alleen wanneer de Core Tools niet al zijn geïnstalleerd. In dit geval worden de Core Tools geïnstalleerd wanneer u de app voor het eerst uitvoert. |
    | Selecteer een sjabloon voor de eerste functie van uw project | Nu overslaan | |
    | Selecteer hoe u uw project wilt openen | In het huidige venster openen | Opent VS Code opnieuw in de map die u hebt geselecteerd. |

Visual Studio Code installeert zo nodig de Azure Functions Core Tools. Er wordt ook een functie-app-project gemaakt in een map. Dit project bevat de configuratiebestanden [host.json](../functions-host-json.md) en [local.settings.json](../functions-run-local.md#local-settings-file).

## <a name="add-functions-to-the-app"></a>Functies toevoegen aan de app

In de volgende stappen wordt een sjabloon gebruikt om de code van de Durable Function te maken in uw project.

1. In het opdrachtenpalet zoekt en selecteert u `Azure Functions: Create Function...`.

1. Volg de instructies en geef de volgende informatie op:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Selecteer een sjabloon voor uw functie | DurableFunctionsOrchestration | Een Durable Functions-indeling maken |
    | Geef een functienaam op | HelloOrchestration | De naam van de klasse waarin de functies zijn gemaakt |
    | Geef een naamruimte op | Company.Function | Naamruimte voor de gegenereerde klasse |

1. Kies **Opslagaccount selecteren** wanneer VS Code u vraagt een opslagaccount te selecteren. Volg de aanwijzingen en geef de volgende informatie op om een nieuw opslagaccount te maken in Azure.

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | Abonnement selecteren | *naam van uw abonnement* | Selecteer uw Azure-abonnement |
    | Selecteer een opslagaccount | Een nieuw opslagaccount maken |  |
    | Voer de naam van het nieuwe opslagaccount in | *unieke naam* | Naam van het opslagaccount dat moet worden gemaakt |
    | Een resourcegroep selecteren | *unieke naam* | Naam van de resourcegroep die moet worden gemaakt |
    | Selecteer een locatie | *regio* | Kies een regio bij u in de buurt |

Een klasse die de nieuwe functies bevat, wordt aan het project toegevoegd. VS Code voegt ook de verbindingstekenreeks van het opslagaccount toe aan *local.settings.json* en een verwijzing aan het [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) NuGet-pakket naar het *.csproj*-projectbestand.

Open het nieuwe bestand *HelloOrchestration.cs* om de inhoud weer te geven. Deze Durable Function is een eenvoudig voorbeeld van het koppelen van functies met behulp van de volgende methoden:  

| Methode | FunctionName | Beschrijving |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Hiermee beheert u de Durable-indeling. In dit geval wordt de indeling gestart, wordt er een lijst gemaakt en wordt het resultaat van drie functieaanroepen aan de lijst toegevoegd.  Wanneer de drie functieaanroepen zijn voltooid, wordt de lijst geretourneerd. |
| **`SayHello`** | `HelloOrchestration_Hello` | De functie geeft 'Hello' als resultaat. Deze functie bevat de bedrijfslogica die wordt ingedeeld. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | Een [door HTTP geactiveerde functie](../functions-bindings-http-webhook.md) die een exemplaar van de indeling start en een reactie voor de controlestatus retourneert. |

Nu u uw functieproject en een Durable Function hebt gemaakt, kunt u deze testen op uw lokale computer.

## <a name="test-the-function-locally"></a>De functie lokaal testen

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie vanuit Visual Studio Code start, wordt u gevraagd deze hulpprogramma's te installeren.

1. U kunt de functie testen door een onderbrekingspunt in de `SayHello`-activiteitsfunctiecode in te stellen en op F5 te drukken om het functie-appproject te starten. De uitvoer van Core Tools wordt weergegeven in het deelvenster **Terminal**.

    > [!NOTE]
    > Raadpleeg de [Durable Functions-diagnoses](durable-functions-diagnostics.md#debugging) voor meer informatie over foutopsporing.

1. Kopieer het URL-eindpunt van de door HTTP getriggerde functie in het deelvenster **Terminal**.

    ![Lokale Azure-uitvoer](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Verzend met behulp van een hulpprogramma zoals [Postman](https://www.getpostman.com/) of [cURL](https://curl.haxx.se/) een HTTP POST-aanvraag naar het URL-eindpunt.

   De reactie is het eerste resultaat van de HTTP-functie waarmee wordt aangegeven dat de orchestrator is gestart. Dit is nog niet het eindresultaat van de orchestrator. De reactie bevat enkele nuttige URL's. Maar eerst gaan we de status van de orchestrator opvragen.

1. Kopieer de URL-waarde voor `statusQueryGetUri`, plak deze in de adresbalk van de browser en voer de aanvraag uit. Eventueel kunt u Postman blijven gebruiken om de GET-aanvraag uit te voeren.

   De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet een eindige reactie krijgen, die laat zien dat het exemplaar is voltooid en die de uitvoer of resultaten van de Durable Function bevat. Deze ziet er als volgt uit: 

    ```json
    {
        "name": "HelloOrchestration",
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

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de URL van de HTTP-trigger vanuit het deelvenster **Output** (Uitvoer). De URL die uw HTTP-geactiveerde functie aanroept, moet de volgende indeling hebben:

    `https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart`

1. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio Code gebruikt om een Durable Function-app in C# te maken en te publiceren.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

In dit artikel leert u hoe u Visual Studio 2019 kunt gebruiken om lokaal een duurzame Hallo wereld-functie te maken en te testen.  Met deze functie organiseert en koppelt u aanroepen naar andere functies. Vervolgens publiceert u de functiecode op Azure. Deze hulpprogramma's zijn beschikbaar als onderdeel van de Azure-ontwikkelworkload in Visual Studio 2019.

![Schermopname van een Visual Studio 2019-venster met een duurzame functie.](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Vereisten

Vereisten voor het voltooien van deze zelfstudie:

* Installeer [Visual Studio 2019](https://visualstudio.microsoft.com/vs/). Zorg ervoor dat de werkbelasting **Azure development** ook is geïnstalleerd. Visual Studio 2017 ondersteunt ook Durable Functions-ontwikkeling, maar de gebruikersinterface en stappen verschillen.

* Controleer of de [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) is geïnstalleerd en wordt uitgevoerd.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Een functie-appproject maken

De Azure Functions-sjabloon maakt een project dat kan worden gepubliceerd in een functie-app in Azure. Met een functie-app kunt u functies groeperen in een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren, schalen en delen.

1. Selecteer **Nieuw** > **Project** in het menu **Bestand** in Visual Studio.

1. Zoek in het dialoogvenster **Een nieuw project maken** naar `functions`, kies de **Azure Functions**-sjabloon en selecteer **Volgende**. 

    ![Het dialoogvenster Nieuw project om een functie in Visual Studio te maken](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Typ een **projectnaam** voor het project en selecteer **OK**. De projectnaam moet geldig zijn als een C#-naamruimte. Gebruik dus geen onderstrepingstekens, afbreekstreepjes of andere niet-alfanumerieke tekens.

1. In **Een nieuwe Azure Functions-toepassing maken** gebruikt u de instellingen die zijn opgegeven in de tabel die volgt op de installatiekopie.

    ![Een nieuw Azure Functions-app-dialoogvenster in Visual Studio maken](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versie** | Azure Functions 3.0 <br />(.NET Core) | Hiermee wordt een functieproject gemaakt dat gebruikmaakt van versie 3.0. van de runtime van Azure Functions, die ondersteuning biedt voor .NET Core 3.1. Zie [Een versie kiezen voor de runtime van Azure Functions](../functions-versions.md) voor meer informatie.   |
    | **Sjabloon** | Leeg | Hiermee wordt een lege functie-app gemaakt. |
    | **Opslagaccount**  | Opslagemulator | Een opslagaccount is vereist voor het statusbeheer van Durable Functions. |

4. Selecteer **Maken** om een leeg Function-project te maken. Dit project bevat de basisconfiguratiebestanden die nodig zijn voor het uitvoeren van uw functies.

## <a name="add-functions-to-the-app"></a>Functies toevoegen aan de app

In de volgende stappen wordt een sjabloon gebruikt om de code van de Durable Function te maken in uw project.

1. Klik met de rechtermuisknop op het project in Visual Studio en selecteer **Toevoegen** > **Nieuwe Azure-functie**.

    ![Nieuwe functie toevoegen](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Controleer of **Azure Function** is geselecteerd in het menu Toevoegen en geef het C#-bestand een naam. Selecteer vervolgens **Toevoegen**.

1. Selecteer de sjabloon **Durable Functions Orchestration** en selecteer **OK**

    ![Durable sjabloon selecteren](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

Er wordt een nieuwe Durable Function toegevoegd aan de app.  Open het nieuwe CS-bestand om de inhoud te bekijken. Deze Durable Function is een eenvoudig voorbeeld van het koppelen van functies met behulp van de volgende methoden:  

| Methode | FunctionName | Beschrijving |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Hiermee beheert u de Durable-indeling. In dit geval wordt de indeling gestart, wordt er een lijst gemaakt en wordt het resultaat van drie functieaanroepen aan de lijst toegevoegd.  Wanneer de drie functieaanroepen zijn voltooid, wordt de lijst geretourneerd. |
| **`SayHello`** | `<file-name>_Hello` | De functie geeft 'Hello' als resultaat. Deze functie bevat de bedrijfslogica die wordt ingedeeld. |
| **`HttpStart`** | `<file-name>_HttpStart` | Een [door HTTP geactiveerde functie](../functions-bindings-http-webhook.md) die een exemplaar van de indeling start en een reactie voor de controlestatus retourneert. |

Nu u uw functieproject en een Durable Function hebt gemaakt, kunt u deze testen op uw lokale computer.

## <a name="test-the-function-locally"></a>De functie lokaal testen

Met Azure Functions Core-hulpprogramma's kunt u een Azure Functions-project uitvoeren op uw lokale ontwikkelcomputer. De eerste keer dat u een functie vanuit Visual Studio start, wordt u gevraagd deze hulpprogramma's te installeren.

1. Druk op F5 om de functie testen. Accepteer desgevraagd de aanvraag van Visual Studio om Azure Functions Core (CLI)-hulpprogramma's te downloaden en installeren. Mogelijk moet u ook een firewall-uitzondering inschakelen, zodat de hulpprogramma's HTTP-aanvragen kunnen afhandelen.

2. Kopieer de URL van uw functie vanuit de uitvoer van de Azure Functions-runtime.

    ![Lokale Azure-runtime](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Plak de URL van de HTTP-aanvraag in de adresbalk van uw browser en voer de aanvraag uit. Hieronder ziet u de reactie op de lokale GET-aanvraag die door de functie wordt geretourneerd, weergegeven in de browser:

    ![Schermopname met een browservenster met statusQueryGetUri gemarkeerd.](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    De reactie is het eerste resultaat van de HTTP-functie waarmee wordt aangegeven dat de orchestrator is gestart.  Dit is nog niet het eindresultaat van de orchestrator.  De reactie bevat enkele nuttige URL's.  Maar eerst gaan we de status van de orchestrator opvragen.

4. Kopieer de URL-waarde voor `statusQueryGetUri`, plak deze in de adresbalk van de browser en voer de aanvraag uit.

    De aanvraag voert een query uit op het orchestrator-exemplaar voor de status. U moet uiteindelijk een reactie krijgen die lijkt op de volgende.  Deze uitvoer laat zien dat het exemplaar is voltooid en dat het de uitvoer of resultaten van de Durable Function bevat.

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. Als u wilt stoppen met fouten opsporen, drukt u op **Shift + F5**.

Nadat u hebt gecontroleerd of de functie correct wordt uitgevoerd op uw lokale computer, is het tijd om het project te publiceren naar Azure.

## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

Voordat u uw project kunt publiceren, moet u een functie-app in uw Azure-abonnement hebben. U kunt rechtstreeks vanuit Visual Studio een functie-app maken.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Uw functie testen in Azure

1. Kopieer de basis-URL van de functie-app van de pagina Profiel publiceren. Vervang het `localhost:port`-deel van de URL dat u hebt gebruikt bij het lokaal testen van de functie door de nieuwe basis-URL.

    De URL die de HTTP-trigger van uw Durable Function aanroept, moet de volgende indeling hebben:

    `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart`

2. Plak deze nieuwe URL van de HTTP-aanvraag in de adresbalk van uw browser. U krijgt dezelfde statusreactie als eerder, toen u de gepubliceerde app gebruikte.

## <a name="next-steps"></a>Volgende stappen

U hebt Visual Studio gebruikt om een Durable Function-app in C# te maken en te publiceren.

> [!div class="nextstepaction"]
> [Meer informatie over algemene patronen van duurzame functies](durable-functions-overview.md#application-patterns)

::: zone-end
