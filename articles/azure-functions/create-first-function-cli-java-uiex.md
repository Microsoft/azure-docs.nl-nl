---
title: Een Java-functie maken via de opdrachtregel - Azure Functions
description: Ontdek hoe u een Java-functie maakt vanaf de opdrachtregel en vervolgens het lokale project publiceert op serverloze hosting in Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32d91f01fcebec3e1ae0d19b1bff29a71f41c0d1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050423"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Quickstart: Een Java-functie maken in Azure vanaf de opdrachtregel

> [!div class="op_single_selector" title1="Uw functietaal selecteren: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Gebruik opdracht regel Programma's om een Java-functie te maken die reageert op HTTP-aanvragen. Test de code lokaal en implementeer deze in de serverloze omgeving van Azure Functions.

Het volt ooien van deze Quick Start brengt een kleine prijs van een paar USD cent of minder in uw <abbr title="Het profiel waarmee facturerings gegevens voor Azure-gebruik worden bijgehouden.">Azure-account</abbr>.

Als u liever niet ontwikkelt met Maven, raadpleegt u onze vergelijkbare zelfstudies voor Java-ontwikkelaars met [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) en [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief <abbr title="De basis organisatie structuur waarin u resources in azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ De [Azure Functions Core Tools](functions-run-local.md#v2), versie 3.x.

+ [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger.

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versie 8 of 11. De omgevingsvariabele `JAVA_HOME` moet zijn ingesteld op de installatielocatie van de juiste versie van de JDK.

+ [Apache Maven](https://maven.apache.org), versie 3.0 of hoger.

### <a name="prerequisite-check"></a>Controle van vereisten

+ Voer in een Terminal-of opdracht venster uit `func --version` om te controleren of de <abbr title="De set opdracht regel Programma's voor het werken met Azure Functions op uw lokale computer.">Azure Functions Core Tools</abbr> zijn versie 3. x.

+ Voer `az --version` uit om te controleren of u versie 2.4 of hoger hebt van de Azure CLI.

+ Voer `az login` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. een lokale functie project maken

In Azure Functions is een functie project een container voor een of meer afzonderlijke functies die elke reageert op een specifiek <abbr title="Het type gebeurtenis waarmee de code van de functie wordt aangeroepen, zoals een HTTP-aanvraag, een wachtrij bericht of een specifieke tijd.">activeren</abbr>. Alle functies in een project delen dezelfde lokale configuratie en hostingconfiguratie. In deze sectie maakt u een functieproject dat één functie bevat.

1. Voer in een lege map de volgende opdracht uit om het Functions-project te genereren op basis van een [Maven-archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
    ```

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
    ```

    ---

    <br/>
    <details>
    <summary><strong>Functies uitvoeren op Java 11</strong></summary>

    Gebruik `-DjavaVersion=11` als u uw functies wilt uitvoeren in Java 11. Zie [Java-versies](functions-reference-java.md#java-versions) voor meer informatie.
    </details>

1. U wordt door Maven gevraagd om de waarden die nodig zijn om het project op het moment van implementatie te kunnen genereren.
    Geef de volgende waarden op als daarom wordt gevraagd:

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Een waarde die uw project uniek identificeert binnen alle projecten, overeenkomstig de [regels voor de naamgeving van pakketten](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) voor Java. |
    | **artifactId** | `fabrikam-functions` | Een waarde die bestaat uit de naam van het JAR-bestand, zonder een versienummer. |
    | **version** | `1.0-SNAPSHOT` | Kies de standaardwaarde. |
    | **package** | `com.fabrikam` | Een waarde die het Java-pakket aangeeft voor de gegenereerde functiecode. Gebruik de standaard. |

1. Typ `Y` of druk op Enter om te bevestigen.

    Maven maakt de projectbestanden in een nieuwe map met de naam van _artifactId_; in dit voorbeeld is dat `fabrikam-functions`. 

1. Navigeer naar de projectmap:

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>Wat wordt er gemaakt in de map LocalFunctionProj?</strong></summary>

Deze map bevat verschillende bestanden voor het project, zoals *Function. java*, *FunctionTest. java* en *pom.xml*. Er zijn ook configuratie bestanden met de naam [local.settings.jsop](functions-run-local.md#local-settings-file) en [host.jsop](functions-host-json.md). Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload vanuit Azure, wordt het bestand standaard uitgesloten van broncodebeheer in het bestand *.gitignore*.
</details>

<br/>
<details>
<summary><strong>Code voor functie. java</strong></summary>

*Function.java* bevat een `run`-methode waarmee aanvraaggegevens in de `request`-variabele worden ontvangen. Dit is een [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) die is gedecoreerd met de aantekening [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger), waarmee het gedrag van de trigger wordt gedefinieerd. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Het antwoordbericht wordt gegenereerd door de API [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder).

Het archetype genereert ook een moduletest voor uw functie. Wanneer u de functie wijzigt om bindingen of nieuwe functies aan het project toe te voegen, moet u ook de tests in het bestand *FunctionTest.java* wijzigen.
</details>

<br/>
<details>
<summary><strong>Code voor pom.xml</strong></summary>

Instellingen voor de Azure-resources die zijn gemaakt om uw app te hosten, worden gedefinieerd in het **configuratie** -element van de invoeg toepassing met een **groupid** van `com.microsoft.azure` in het gegenereerde *pom.xml* -bestand. Het configuratie-element hieronder geeft bijvoorbeeld een Maven-implementatie voor het maken van een functie-app in de `java-functions-group` resource groep in de `westus` <abbr title="Een geografische verwijzing naar een specifiek Azure-Data Center waarin resources worden toegewezen.">regio</abbr>. De functie-app zelf wordt uitgevoerd in Windows binnen het `java-functions-app-service-plan`-plan. Dit is standaard een serverloos verbruiksabonnement.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

U kunt deze instellingen wijzigen om te bepalen hoe resources worden gemaakt in Azure, zoals door `runtime.os` van `windows` te wijzigen in `linux` vóór de eerste implementatie. Zie de [Configuratiedetails](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)voor een volledige lijst met instellingen die worden ondersteund door de Maven-invoegtoepassing.
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Voer de functie lokaal uit

1. **Voer uw functie** uit door de lokale Azure functions runtime host te starten vanuit de map *LocalFunctionProj* :

    ```console
    mvn clean package
    mvn azure-functions:run
    ```

    Naar het einde van de uitvoer moeten de volgende regels worden weergegeven:

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...

    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.

    Http Functions:

            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    </pre>

    Als HttpExample niet verschijnt zoals hierboven weergegeven, hebt u waarschijnlijk de host gestart van buiten de hoofdmap van het project. In dat geval gebruikt u <kbd>CTRL + C</kbd> om de host te stoppen, navigeert u naar de hoofdmap van het project en voert u de vorige opdracht opnieuw uit.

1. **Kopieer de URL** van uw `HttpExample` functie van deze uitvoer naar een browser en voeg de query reeks toe `?name=<YOUR_NAME>` , zodat u de volledige URL kunt gebruiken `http://localhost:7071/api/HttpExample?name=Functions` . In de browser moet een bericht als het volgende weergeven `Hello Functions`:

    ![Resultaat van de functie lokaal uitgevoerd in de browser](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    De terminal waarin u uw project hebt gestart, toont ook de logboek uitvoer wanneer u aanvragen doet.

1. Wanneer u klaar bent, gebruikt u <kbd>CTRL + C</kbd> en kiest u <kbd>y</kbd> om de functions-host te stoppen.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. Implementeer het functie project in azure

Er worden een functie-app en gerelateerde resources in Azure gemaakt wanneer u uw functieproject voor het eerst implementeert. De instellingen voor de Azure-resources die zijn gemaakt om uw app te hosten, worden gedefinieerd in het *pom.xml* -bestand. In dit artikel gaat u akkoord met de standaardinstellingen.

<br/>
<details>
<summary><strong>Een functie-app maken die wordt uitgevoerd op Linux</strong></summary>

Als u een functie-app wilt maken die wordt uitgevoerd op Linux in plaats van Windows, wijzigt `runtime.os` u het element in het *pom.xml* bestand van in `windows` `linux` . Het uitvoeren van Linux in een verbruiksplan wordt ondersteund in [deze regio's](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). U kunt apps die worden uitgevoerd op Linux en apps die worden uitgevoerd in Windows niet dezelfde resourcegroep gebruiken.
</details>

1. Voordat u kunt implementeren, meldt u zich aan bij uw Azure-abonnement met behulp van Azure CLI of Azure PowerShell. 

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Met de opdracht [az login](/cli/azure/reference-index#az-login) meldt u zich aan bij uw Azure-account.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) meldt u zich aan bij uw Azure-account.

    ---

1. Gebruik de volgende opdracht om uw project te implementeren in een nieuwe functie-app.

    ```console
    mvn azure-functions:deploy
    ```

    De implementatie verpakt de projectbestanden en implementeert deze in de nieuwe functie-app met behulp van [zip implementation](functions-deployment-technologies.md#zip-deploy). De code wordt uitgevoerd vanuit het implementatiepakket in Azure.

<br/>
<details>
<summary><strong>Wat wordt er gemaakt in azure?</strong></summary>

+ Resourcegroep. Met de naam _java-functions-group_.
+ Opslagaccount. Vereist door Funtions. De naam wordt willekeurig gegenereerd op basis van de vereisten van het opslagaccount.
+ Hostingabonnement. Serverloze hosting voor uw functie-app in de regio _westus_. De naam is _java-functions-app-service-plan_.
+ Functie-app. Een functie-app is de implementatie- en uitvoeringseenheid voor uw functies. De naam wordt willekeurig gegenereerd op basis van uw _artifactId_,waaraan een willekeurig gegenereerd nummer wordt toegevoegd.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. de functie aanroepen op Azure

Omdat uw functie gebruikmaakt van een HTTP-trigger, **roept u deze aan door een HTTP-aanvraag in te stellen op de URL** in de browser of met een hulp programma zoals <abbr title="Een opdracht regel programma voor het genereren van HTTP-aanvragen naar een URL; kijken https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Browser](#tab/browser)

Kopieer de volledige **invoke-URL** die wordt weer gegeven in de uitvoer van de `publish` opdracht naar een adres balk van de browser en voeg de query parameter toe `&name=Functions` . De browser moet vergelijkbare uitvoer weergeven als u de functie lokaal hebt uitgevoerd.

![De uitvoer van de functie die wordt uitgevoerd op Azure in een browser](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Voer [`curl`](https://curl.haxx.se/) uit met de **aanroep-URL** en voeg de parameter `&name=Functions` toe. De uitvoer van de opdracht moet de tekst ‘Hallo Functions’ zijn.

![De uitvoer van de functie die wordt uitgevoerd op Azure met behulp van curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. resources opschonen

Als u doorgaat met de [volgende stap](#next-steps) en een Azure Storage toevoegt <abbr title="In Azure Storage is dit een manier om een functie te koppelen aan een opslag wachtrij, zodat deze berichten kan maken in de wachtrij.">uitvoer binding wachtrij</abbr>, zorgt u ervoor dat al uw resources op de juiste plaats staan, zoals u gaat bouwen op wat u al hebt gedaan.

Gebruik anders de volgende opdracht om de resourcegroep en alle bijbehorende resources te verwijderen om te voorkomen dat er verdere kosten in rekening worden gebracht.

 # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

<br>
<hr/>

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
