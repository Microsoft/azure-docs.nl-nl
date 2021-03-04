---
title: Een Python-functie maken met behulp van Visual Studio Code - Azure Functions
description: Leer hoe u een Python-functie maakt en publiceer vervolgens het lokale project naar serverloze hosting in Azure Functions met behulp van de Azure Functions-extensie in Visual Studio Code.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 42f07b76cefed38aad53caba9ba35c74238540fe
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031732"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Quickstart: Een functie maken in Azure met Python met behulp van Visual Studio Code

> [!div class="op_single_selector" title1="Uw functietaal selecteren: "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Anders (Go/Rust)](create-first-function-vs-code-other.md)

In dit artikel gebruikt u Visual Studio Code om een Python-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de <abbr title="Een runtime Computing Environment waarin alle details van de server transparant zijn voor toepassings ontwikkelaars, waardoor het proces van het implementeren en beheren van code wordt vereenvoudigd.">serverloos</abbr> omgeving van <abbr title="Een Azure-service die een voordelige serverloze computer omgeving biedt voor toepassingen.">Azure Functions</abbr>.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

Er is ook een [op een CLI gebaseerde versie](create-first-function-cli-python.md) van dit artikel.

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

Voordat u aan de slag kunt gaan, moet u beschikken over de volgende vereisten:

+ Een Azure <abbr title="Het profiel waarmee facturerings gegevens voor Azure-gebruik worden bijgehouden.">account</abbr> met een actieve <abbr title="De basis organisatie structuur waarin u resources in azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ De [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools), versie 3.x.

+ [Python 3.8](https://www.python.org/downloads/release/python-381/), [Python 3.7](https://www.python.org/downloads/release/python-375/), [Python 3.6](https://www.python.org/downloads/release/python-368/) worden ondersteund door Azure Functions (x64).

+ [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ De [Python-extensie](https://marketplace.visualstudio.com/items?itemName=ms-python.python) voor Visual Studio Code.  

+ De [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio Code.

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a> uw lokale project maken

1. Kies het pictogram van Azure in de <abbr title="De verticale groep met pictogrammen aan de linkerkant van het venster Visual Studio code.">Activiteiten balk</abbr>en selecteer in het gebied **Azure: functions** het pictogram **Nieuw project maken...** .

    ![Een nieuw project maken kiezen](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Kies een maplocatie voor de werkruimte van uw project en kies **Selecteren**.

    > [!NOTE]
    > Deze stappen waren bedoeld om buiten een werkruimte te worden voltooid. Selecteer in dit geval geen projectmap die deel uitmaakt van een werkruimte.

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer een taal voor uw functieproject**: Kies `Python`.

    + **Selecteer een Python-alias voor het maken van een virtuele omgeving**: Kies de locatie van de Python-interpreter. Als de locatie niet wordt weergegeven, typt u het volledige pad naar uw binaire Python-bestand.  

    + **Selecteer een sjabloon voor de eerste functie van uw project**: Kies `HTTP trigger`.

    + **Geef een functienaam op**: Typ `HttpExample`.

    + **Autorisatieniveau**: Kies `Anonymous`, waarmee iedereen uw functie-eindpunt kan aanroepen. Zie [autorisatie sleutels](functions-bindings-http-webhook-trigger.md#authorization-keys)voor meer informatie over autorisatie niveaus.

    + **Selecteer hoe u uw project wilt openen**: Kies `Add to workspace`.

<br/>
<details>
<summary><strong>Kunt u geen functie project maken?</strong></summary>

De meest voorkomende problemen bij het maken van een project met lokale functies zijn:
* U beschikt niet over de uitbrei ding Azure Functions geïnstalleerd. 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

1. Druk op <kbd>F5</kbd> om het functie-app-project te starten.

1. In het deel venster **Terminal** raadpleegt u het URL-eind punt van de functie die lokaal wordt uitgevoerd.

    ![Lokale functie versus code-uitvoer](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Ga naar het gedeelte **functies van Azure:** als u kern hulpprogramma's uitvoert. Vouw onder **functies** **lokale project**  >  **functies** uit. Klik met de rechter muisknop (Windows) of <kbd>CTRL</kbd> (macOS) op de `HttpExample` functie en kies **functie nu uitvoeren...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="De functie nu uitvoeren vanuit Visual Studio code":::

1. In de **hoofd tekst** van de aanvraag ziet u de waarde van de aanvraag bericht hoofdtekst van `{ "name": "Azure" }` . Druk op ENTER om dit aanvraag bericht naar uw functie te verzenden.  

1. Wanneer de functie lokaal wordt uitgevoerd en een antwoord retourneert, wordt er een melding gegenereerd in Visual Studio code. Informatie over de uitvoering van de functie wordt weer gegeven in het deel venster **Terminal** .

1. Druk op <kbd>CTRL + C</kbd> om Core Tools te stoppen en de verbinding met het foutopsporingsprogramma te verbreken.

<br/>
<details>
<summary><strong>Kunt u de functie niet lokaal uitvoeren?</strong></summary>

De meest voorkomende problemen bij het uitvoeren van een project met lokale functies zijn:
* U hebt niet de kern Hulpprogramma's geïnstalleerd. 
*  Als u problemen ondervindt met het uitvoeren van Windows, moet u ervoor zorgen dat de standaard Terminal Shell voor Visual Studio code niet is ingesteld op **WSL bash**. 
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

    + **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om er zeker van te zijn dat deze <abbr title="De naam moet uniek zijn binnen alle Azure-klanten wereld wijd. U kunt bijvoorbeeld een combi natie van uw persoonlijke of organisatie naam, toepassings naam en een numerieke id gebruiken, zoals in contoso-bizapp-func-20.">uniek in azure</abbr>. 

    + **Selecteer een runtime**: Kies de versie van Python die u lokaal hebt uitgevoerd. U kunt de opdracht `python --version` gebruiken om uw versie te controleren.

    + **Selecteer een locatie voor nieuwe resources**: Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt.

    De uitbrei ding toont de status van afzonderlijke resources, aangezien deze worden gemaakt in Azure in het systeemvak.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Melding van het maken van Azure-resources":::

1. Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. Selecteer **uitvoer weer geven** om de resultaten voor het maken en implementeren weer te geven. 

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

1. Ga terug naar de zijde balk van **Azure: functions** en vouw de nieuwe functie-app uit.
1. Vouw **functies** uit, klik met de rechter muisknop op (Windows) of <kbd>CTRL</kbd> (macOS) `HttpExample` en klik op **functie nu uitvoeren...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="De functie nu uitvoeren in azure vanuit Visual Studio code":::

1. In de **hoofd tekst** van de aanvraag ziet u de waarde van de aanvraag bericht hoofdtekst van `{ "name": "Azure" }` .

    Druk op ENTER om dit aanvraag bericht naar uw functie te verzenden.  

1. Wanneer de functie wordt uitgevoerd in Azure en een antwoord retourneert, wordt er een melding gegenereerd in Visual Studio code.

## <a name="7-clean-up-resources"></a>7. resources opschonen

Wanneer u doorgaat met de [volgende stap](#next-steps) en een <abbr title="Een manier om een functie te koppelen aan een opslag wachtrij, zodat deze berichten kan maken in de wachtrij.">Uitvoer binding van Azure Storage wachtrij</abbr> Als u uw functie wilt gebruiken, moet u ervoor zorgen dat al uw resources op de juiste plaats staan om te kunnen bouwen op wat u al hebt gedaan.

Als dat niet het geval is, kunt u de volgende stappen gebruiken om de functie-app en de bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

Zie [Kosten voor verbruiksplannen inschatten](functions-consumption-costs.md) voor meer informatie over de kosten voor Functions.

## <a name="next-steps"></a>Volgende stappen

Deze functie uitvouwen door een uitvoer toe te voegen <abbr title="Een declaratieve verbinding tussen een functie en andere resources. Een invoer binding biedt gegevens voor de functie. een uitvoer binding biedt gegevens van de functie aan andere resources.">dwingen</abbr>. Met deze binding wordt de tekenreeks van de HTTP-aanvraag naar een bericht in een Azure Queue Storage-wachtrij geschreven. 

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
