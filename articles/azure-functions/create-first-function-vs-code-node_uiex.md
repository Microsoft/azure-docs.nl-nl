---
title: Een JavaScript-functie maken met behulp van Visual Studio Code - Azure Functions
description: Leer hoe u een JavaScript-functie maakt en publiceer vervolgens het lokale Node.js-project naar serverloze hosting in Azure Functions met behulp van de Azure Functions-extensie in Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom:
- devx-track-js
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cf47d04da51db898e667ef8b31d42d79c9f354e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748443"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>Quickstart: Een JavaScript-functie maken in Azure met behulp van Visual Studio Code

> [!div class="op_single_selector" title1="Uw functietaal selecteren: "]
> - [JavaScript](create-first-function-vs-code-node.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Anders (Go/Rust)](create-first-function-vs-code-other.md)

Gebruik Visual Studio code om een Java script-functie te maken die reageert op HTTP-aanvragen. Test de code lokaal en implementeer deze in de serverloze omgeving van Azure Functions.

Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw <abbr title="Het Azure-account is een wereld wijd unieke entiteit waarmee u toegang krijgt tot Azure-Services en uw Azure-abonnementen.">Azure-account</abbr>.

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

Voordat u aan de slag kunt gaan, moet u beschikken over de volgende vereisten:

+ Een Azure-account met een <abbr title="Een Azure-abonnement is een logische container die wordt gebruikt voor het inrichten van resources in Azure. Het abonnement bevat informatie over al uw resources, zoals virtuele machines (VM's), databases, enzovoort.">actief abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [Node.js 10.14.1 +](https://nodejs.org/)

+ [Visual Studio Code](https://code.visualstudio.com/)

+ [Azure functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio code.

+ [Azure Functions core-hulpprogram ma's](functions-run-local.md?tabs=linux%2Ccsharp%2Cbash#install-the-azure-functions-core-tools)

<hr/>
<br/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> uw lokale functions-project maken

1. Kies het pictogram van Azure in de <abbr title="">Activiteiten balk</abbr>en selecteer in het gebied **Azure: functions** het pictogram **Nieuw project maken...** .

    ![Een nieuw project maken kiezen](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Kies een maplocatie** voor de Project werkruimte en kies vervolgens **selecteren**. 

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer een taal voor uw functieproject**: Kies `JavaScript`.

    + **Selecteer een sjabloon voor de eerste functie van uw project**: Kies `HTTP trigger`.

    + **Geef een functienaam op**: Typ `HttpExample`.

    + **Autorisatieniveau**: Kies `Anonymous`, waarmee iedereen uw functie-eindpunt kan aanroepen.

    + **Selecteer hoe u uw project wilt openen**: Kies `Add to workspace`.




<br/>
<details>
<summary><strong>Kunt u geen functie project maken?</strong></summary>

De meest voorkomende problemen bij het maken van een project met lokale functies zijn:
* U beschikt niet over de uitbrei ding Azure Functions geïnstalleerd. 
</details>

<hr/>
<br/>

## <a name="3-run-the-function-locally"></a>3. Voer de functie lokaal uit


1. Druk op <kbd>F5</kbd> om het functie-app-project te starten. 

1. In de **Terminal**, zie het URL-eind punt van de functie die lokaal wordt uitgevoerd.

    ![Lokale functie versus code-uitvoer](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Kopieer de volgende URL en plak deze in een webbrowser en druk op ENTER.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. De geretourneerde reactie weer geven.


    ![Browser - voorbeelduitvoer van localhost](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. Bekijk de informatie in het deel venster **Terminal** over de aanvraag.

    ![Taakhost starten - VS Code-terminaluitvoer](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Druk op <kbd>CTRL + C</kbd> om Core Tools te stoppen en de verbinding met het foutopsporingsprogramma te verbreken.

<br/>
<details>
<summary><strong>Kunt u de functie niet lokaal uitvoeren?</strong></summary>

De meest voorkomende problemen bij het uitvoeren van een project met lokale functies zijn:
* U hebt niet de kern Hulpprogramma's geïnstalleerd. 
*  Als u problemen ondervindt met het uitvoeren van Windows, moet u ervoor zorgen dat de standaard Terminal Shell voor Visual Studio code niet is ingesteld op WSL bash. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. Meld u aan bij Azure

Meld u aan bij Azure om uw app te publiceren. Als u al bent aangemeld, gaat u naar het volgende gedeelte.

1. Kies het pictogram van Azure in de activiteiten balk en kies in het gebied **Azure: functions** de optie **Aanmelden bij Azure...**.

    ![Aanmelden bij Azure binnen VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Wanneer u hierom wordt gevraagd, **kiest u uw Azure-account** en **meldt u zich** aan met de referenties van uw Azure-account.

1. Nadat u zich hebt aangemeld, sluit u het nieuwe browser venster en gaat u terug naar Visual Studio code. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. Publiceer het project naar Azure

Uw eerste implementatie van uw code omvat het maken van een functie resource in uw Azure-abonnement. 

1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: In het gebied** Functies kiest u de knop **Implementeren in functie-app ...** .

    ![Uw project naar Azure publiceren](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer map**: Kies de map die de functie-app bevat. 

    + **Selecteer abonnement**: Kies het abonnement dat u wilt gebruiken. Dit ziet u niet als u maar één abonnement hebt.

    + **Selecteer functie-app in Azure**: Kies `+ Create new Function App`.

    + **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die uniek is voor Azure in een URL-pad. De naam die u typt, wordt gevalideerd om wereld wijd uniek te zijn.

    + **Selecteer een runtime**: Kies de versie van Node.js die u lokaal hebt uitgevoerd. U kunt de opdracht `node --version` gebruiken om uw versie te controleren.

    + **Selecteer een locatie voor nieuwe resources**:  Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. 

1. Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. Selecteer **uitvoer weer geven** om de resultaten voor het maken en implementeren te bekijken. 
    
    ![Volledige melding maken](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>Kunt u de functie niet publiceren?</strong></summary>

In deze sectie zijn de Azure-resources gemaakt en uw lokale code geïmplementeerd in de functie-app. Als dat niet lukt:

* Controleer de uitvoer op fout gegevens. Het klok pictogram in de rechter benedenhoek is een andere manier om de uitvoer weer te geven. 
* Hebt u gepubliceerd naar een bestaande functie-app? Met deze actie wordt de inhoud van die app overschreven in Azure.
</details>


<br/>
<details>
<summary><strong>Welke resources zijn gemaakt?</strong></summary>

Wanneer dit is voltooid, worden de volgende Azure-resources in uw abonnement gemaakt met behulp van namen op basis van de naam van uw functie-app: 
* **Resource groep**: een resource groep is een logische container voor gerelateerde resources in dezelfde regio.
* **Azure Storage account**: een opslag Resource houdt status en andere informatie over uw project.
* **Verbruiks plan**: een verbruiks abonnement definieert de onderliggende host voor uw serverloze functie-app.
* **Functie-app**: een functie-app biedt de omgeving voor het uitvoeren van uw functie code en groeps functies als een logische eenheid.
* **Application Insights**: Application Insights bijhouden van het gebruik van uw serverloze functie.

</details>





<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. Voer de functie uit in azure
1. Vouw in de balken van **Azure: functies** de nieuwe functie-app uit. 
1. Vouw **functies** uit, klik met de rechter muisknop op **HttpExample** en kies vervolgens **functie nu uitvoeren...**.

    ![De functie-URL kopiëren voor de nieuwe HTTP-trigger](../../includes/media/functions-vs-code-run-remote/execute-function-now.png)

1. **Druk op ENTER** om een standaard aanvraag bericht naar uw functie te verzenden. 

1. Er wordt een melding gegenereerd in Visual Studio code wanneer de uitvoering van de functie is voltooid.

<br/>
<details>
<summary><strong>Kan de functie-app op basis van de Cloud niet uitvoeren?</strong></summary>

* Weet u niet hoe u de query reeks aan het einde van de URL wilt toevoegen?

</details>

<hr/>
<br/>

## <a name="7-clean-up-resources"></a>7. resources opschonen

Verwijder de functie-app en de bijbehorende resources om te voor komen dat er verdere kosten in rekening worden gebracht.

1. In Visual Studio code selecteert u het pictogram van Azure in de activiteiten balk en selecteert u vervolgens het gebied functies in de balk aan de zijkant. 
1. Selecteer de functie-app, klik met de rechter muisknop en selecteer **functie-app verwijderen...**.

<hr/>
<br/>

## <a name="next-steps"></a>Volgende stappen

Vouw de functie uit door een <abbr title="Een binding met een functie is een manier om op declaratieve wijze een andere resource aan de functie te koppelen.">uitvoer binding</abbr>. Met deze binding wordt de tekenreeks van de HTTP-aanvraag naar een bericht in een Azure Queue Storage-wachtrij geschreven. 

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
