---
title: Een Java-functie maken met behulp van Visual Studio Code - Azure Functions
description: Leer hoe u een Java-functie maakt en publiceer vervolgens het lokale project naar serverloze hosting in Azure Functions met behulp van de Azure Functions-extensie in Visual Studio Code.
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c84bf4ebc73857fa0280ffbcbf46b68c2da630f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031715"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Quickstart: Een Java-functie maken in Azure met behulp van Visual Studio Code

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Gebruik Visual Studio code om een Java-functie te maken die reageert op HTTP-aanvragen. Test de code lokaal en implementeer deze in de serverloze omgeving van Azure Functions.

Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw <abbr title="Het profiel waarmee facturerings gegevens voor Azure-gebruik worden bijgehouden.">Azure-account</abbr>.

Als u liever niet ontwikkelt met Visual Studio Code, raadpleegt u onze vergelijkbare zelfstudies voor Java-ontwikkelaars met [Maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) en [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions).

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

Voordat u aan de slag kunt gaan, moet u beschikken over de volgende vereisten:

+ Een Azure-account met een actief <abbr title="De basis organisatie structuur waarin u resources in azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versie 8 of 11.

+ [Apache Maven](https://maven.apache.org), versie 3.0 of hoger.

+ [Visual Studio Code](https://code.visualstudio.com/) op een van de [ondersteunde platforms](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ Het [Java-uitbreidingspakket](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ De [Azure Functions-extensie](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) voor Visual Studio Code.

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> uw lokale functions-project maken

1. Kies het pictogram van Azure in de **activiteiten balk** en selecteer vervolgens in het gebied **Azure: functions** het pictogram **Nieuw project maken...** .

    ![Een nieuw project maken kiezen](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Kies een maplocatie** voor de Project werkruimte en kies vervolgens **selecteren**.

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer een taal voor uw functieproject**: Kies `Java`.

    + **Een versie van Java selecteren**: Kies `Java 8` of `Java 11`, de Java-versie waarmee uw functies in Azure worden uitgevoerd. Kies een Java-versie die u lokaal hebt geverifieerd.

    + **Geef een groeps-id op**: Kies `com.function`.

    + **Geef een artefact-id op**: Kies `myFunction`.

    + **Geef een versie op**: Kies `1.0-SNAPSHOT`.

    + **Geef een pakketnaam op**: Kies `com.function`.

    + **Geef een app-naam op**: Kies `myFunction-12345`.

    + **Autorisatieniveau**: Kies `Anonymous`, waarmee iedereen uw functie-eindpunt kan aanroepen.

    + **Selecteer hoe u uw project wilt openen**: Kies `Add to workspace`.

<br/>

<details>
<summary><strong>Kunt u geen functie project maken?</strong></summary>

De meest voorkomende problemen bij het maken van een project met lokale functies zijn:
* U beschikt niet over de uitbrei ding Azure Functions geïnstalleerd. 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Voer de functie lokaal uit

1. Druk op <kbd>F5</kbd> om het functie-app-project te starten.

1. In de **Terminal**, zie het URL-eind punt van de functie die lokaal wordt uitgevoerd.

    ![Lokale functie versus code-uitvoer](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Ga naar het gedeelte **functies van Azure:** als u kern hulpprogramma's uitvoert. Vouw onder **functies** **lokale project**  >  **functies** uit. Klik met de rechter muisknop (Windows) of <kbd>CTRL</kbd> (macOS) op de `HttpExample` functie en kies **functie nu uitvoeren...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="De functie nu uitvoeren vanuit Visual Studio code":::

1. In de **hoofd tekst** van de aanvraag ziet u de waarde van de aanvraag bericht hoofdtekst van `{ "name": "Azure" }` . Druk op <kbd>Enter</kbd> om dit aanvraag bericht naar uw functie te verzenden.  

1. Wanneer de functie lokaal wordt uitgevoerd en een antwoord retourneert, wordt er een melding gegenereerd in Visual Studio code. Informatie over de uitvoering van de functie wordt weer gegeven in het deel venster **Terminal** .

1. Druk op <kbd>CTRL + C</kbd> om Core Tools te stoppen en de verbinding met het foutopsporingsprogramma te verbreken.

<br/>

<details>
<summary><strong>Kunt u de functie niet lokaal uitvoeren?</strong></summary>

De meest voorkomende problemen bij het uitvoeren van een project met lokale functies zijn:
* U hebt niet de kern Hulpprogramma's geïnstalleerd. 
*  Als u problemen ondervindt met het uitvoeren van Windows, moet u ervoor zorgen dat de standaard Terminal Shell voor Visual Studio code niet is ingesteld op WSL bash. 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4. Meld u aan bij Azure

Meld u aan bij Azure om uw app te publiceren. Als u al bent aangemeld, gaat u naar het volgende gedeelte.

1. Kies het pictogram van Azure in de activiteiten balk en kies in het gebied **Azure: functions** de optie **Aanmelden bij Azure...**.

    ![Aanmelden bij Azure binnen VS Code](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Wanneer u hierom wordt gevraagd, **kiest u uw Azure-account** en **meldt u zich** aan met de referenties van uw Azure-account.

1. Nadat u zich hebt aangemeld, sluit u het nieuwe browser venster en gaat u terug naar Visual Studio code.

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5. Publiceer het project naar Azure

Uw eerste implementatie van uw code omvat het maken van een functie resource in uw Azure-abonnement.

1. Kies het Azure-pictogram in de activiteitenbalk en selecteer vervolgens in het gedeelte **Azure: In het gebied** Functies kiest u de knop **Implementeren in functie-app ...** .

    ![Uw project naar Azure publiceren](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer map**: Kies de map die de functie-app bevat. 

    + **Selecteer abonnement**: Kies het abonnement dat u wilt gebruiken. Dit ziet u niet als u maar één abonnement hebt.

    + **Selecteer functie-app in Azure**: Kies `Create new Function App`.

    + **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die uniek is voor Azure in een URL-pad. De naam die u typt, wordt gevalideerd om wereld wijd uniek te zijn.

    - **Selecteer een locatie voor nieuwe resources**:  Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt.

1. Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. Selecteer **uitvoer weer geven** om de resultaten voor het maken en implementeren te bekijken.

    ![Volledige melding maken](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

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

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6. Voer de functie uit in azure

1. Ga terug naar het gebied **Azure: functions** in de zijbalk, vouw uw abonnement uit, uw nieuwe functie-app en **functions**. Klik met de rechter muisknop (Windows) of <kbd>CTRL</kbd> (macOS) op de `HttpExample` functie en kies **functie nu uitvoeren...**.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="De functie nu uitvoeren in azure vanuit Visual Studio code":::

1. In de **hoofd tekst** van de aanvraag ziet u de waarde van de aanvraag bericht hoofdtekst van `{ "name": "Azure" }` . Druk op ENTER om dit aanvraag bericht naar uw functie te verzenden.  

1. Wanneer de functie wordt uitgevoerd in Azure en een antwoord retourneert, wordt er een melding gegenereerd in Visual Studio code.

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7. resources opschonen

Als u niet van plan bent om door te gaan naar de [volgende stap](#next-steps), verwijdert u de functie-app en de bijbehorende resources om te voor komen dat er verdere kosten in rekening worden gebracht.

1. In Visual Studio code selecteert u het pictogram van Azure in de activiteiten balk en selecteert u vervolgens het gebied functies in de balk aan de zijkant.
1. Selecteer de functie-app, klik met de rechter muisknop en selecteer **functie-app verwijderen...**.

<br/>
<hr/>

## <a name="next-steps"></a>Volgende stappen

Vouw de functie uit door een <abbr title="In Azure Storage is dit een manier om een functie te koppelen aan een opslag wachtrij, zodat deze berichten kan maken in de wachtrij.">uitvoer binding</abbr>. Met deze binding wordt de tekenreeks van de HTTP-aanvraag naar een bericht in een Azure Queue Storage-wachtrij geschreven.

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
