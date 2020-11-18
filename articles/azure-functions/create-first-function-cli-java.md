---
title: Een Java-functie maken via de opdrachtregel - Azure Functions
description: Ontdek hoe u een Java-functie maakt vanaf de opdrachtregel en vervolgens het lokale project publiceert op serverloze hosting in Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ms.openlocfilehash: 449f0a59cc8428ce8e19535d5cf0417bf4cf7ad0
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424800"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Quickstart: Een Java-functie maken in Azure vanaf de opdrachtregel

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In dit artikel gebruikt u opdrachtregelprogramma's om een Java-functie te maken die reageert op HTTP-aanvragen. Nadat u de code lokaal hebt getest, implementeert u deze in de serverloze omgeving van Azure Functions.

Voor het voltooien van deze quickstart worden kosten van een paar dollarcent of minder in rekening gebracht bij uw Azure-account.

> [!NOTE]
> Als u liever niet ontwikkelt met Maven, raadpleegt u onze vergelijkbare zelfstudies voor Java-ontwikkelaars met [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) en [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="configure-your-local-environment"></a>Uw lokale omgeving configureren

Voordat u begint, moet u het volgende hebben:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ De [Azure Functions Core Tools](functions-run-local.md#v2), versie 3.x.

+ [Azure CLI](/cli/azure/install-azure-cli) versie 2.4 of hoger.

+ [Java Developer Kit](https://aka.ms/azure-jdks), versie 8 of 11. De omgevingsvariabele `JAVA_HOME` moet zijn ingesteld op de installatielocatie van de juiste versie van de JDK.     

+ [Apache Maven](https://maven.apache.org), versie 3.0 of hoger.

### <a name="prerequisite-check"></a>Controle van vereisten

+ Voer in een terminal- of opdrachtvenster `func --version` uit om te controleren of de Azure Functions Core Tools versie 3.x zijn.

+ Voer `az --version` uit om te controleren of u versie 2.4 of hoger hebt van de Azure CLI.

+ Voer `az login` uit om u aan te melden bij Azure en te controleren of u een actief abonnement hebt.

## <a name="create-a-local-function-project"></a>Een lokaal functieproject maken

In Azure Functions is een functieproject een container voor een of meer afzonderlijke functies die elk op een bepaalde trigger reageren. Alle functies in een project delen dezelfde lokale configuratie en hostingconfiguratie. In deze sectie maakt u een functieproject dat één functie bevat.

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

    > [!IMPORTANT]
    > + Gebruik `-DjavaVersion=11` als u uw functies wilt uitvoeren in Java 11. Zie [Java-versies](functions-reference-java.md#java-versions) voor meer informatie. 
    > + De omgevingsvariabele `JAVA_HOME` moet zijn ingesteld op de installatielocatie van de juiste versie van de JDK om dit artikel te kunnen voltooien.

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

    Deze map bevat verschillende bestanden voor het project,waaronder configuratiebestanden met de naam [local.settings.json](functions-run-local.md#local-settings-file) en [host.json](functions-host-json.md). Omdat *local.settings.json* geheimen kan bevatten die zijn gedownload vanuit Azure, wordt het bestand standaard uitgesloten van broncodebeheer in het bestand *.gitignore*.

### <a name="optional-examine-the-file-contents"></a>(Optioneel) De inhoud van het bestand bekijken

Desgewenst kunt u doorgaan naar [De functie lokaal uitvoeren](#run-the-function-locally) en de inhoud van het bestand later bekijken.

#### <a name="functionjava"></a>Function.java
*Function.java* bevat een `run`-methode waarmee aanvraaggegevens in de `request`-variabele worden ontvangen. Dit is een [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) die is gedecoreerd met de aantekening [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger), waarmee het gedrag van de trigger wordt gedefinieerd. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Het antwoordbericht wordt gegenereerd door de API [HttpResponseMessage.Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder).

#### <a name="pomxml"></a>pom.xml

Instellingen voor de Azure-resources die zijn gemaakt voor het hosten van uw app, worden gedefinieerd in het element **Configuratie** van de invoegtoepassing met een **groupId** van `com.microsoft.azure` in het gegenereerde bestand pom.xml. Het configuratie-element hieronder geeft bijvoorbeeld een Maven-implementatie de instructie om een functie-app te maken in de resourcegroep `java-functions-group` in de regio `westus`. De functie-app zelf wordt uitgevoerd in Windows binnen het `java-functions-app-service-plan`-plan. Dit is standaard een serverloos verbruiksabonnement.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

U kunt deze instellingen wijzigen om te bepalen hoe resources worden gemaakt in Azure, zoals door `runtime.os` van `windows` te wijzigen in `linux` vóór de eerste implementatie. Zie de [Configuratiedetails](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details)voor een volledige lijst met instellingen die worden ondersteund door de Maven-invoegtoepassing.

#### <a name="functiontestjava"></a>FunctionTest.java

Het archetype genereert ook een moduletest voor uw functie. Wanneer u de functie wijzigt om bindingen of nieuwe functies aan het project toe te voegen, moet u ook de tests in het bestand *FunctionTest.java* wijzigen.

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

1. Voer uw functie uit door de lokale Azure Functions runtime host te starten vanuit de map *LocalFunctionProj*:

    ```console
    mvn clean package
    mvn azure-functions:run
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
    
    > [!NOTE]  
    > Als HttpExample niet verschijnt zoals hieronder weergegeven, hebt u waarschijnlijk de host gestart van buiten de hoofdmap van het project. In dat geval gebruikt u **Ctrl**+**C** om de host te stoppen, gaat u naar de hoofdmap van het project en voert u de vorige opdracht opnieuw uit.

1. Kopieer de URL van uw `HttpExample`-functie van deze uitvoer naar een browser en voeg de query tekenreeks toe `?name=<YOUR_NAME>`, waardoor de volledige URL verschijnt, zoals `http://localhost:7071/api/HttpExample?name=Functions`. In de browser moet een bericht als het volgende weergeven `Hello Functions`:

    ![Resultaat van de functie lokaal uitgevoerd in de browser](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    De terminal waarin u uw project hebt gestart, toont ook de logboek uitvoer wanneer u aanvragen doet.

1. Wanneer u klaar bent, gebruikt u **Ctrl**+**C** en kiest u `y` om de functiehost te stoppen.

## <a name="deploy-the-function-project-to-azure"></a>Het functieproject implementeren in Azure

Er worden een functie-app en gerelateerde resources in Azure gemaakt wanneer u uw functieproject voor het eerst implementeert. De instellingen voor de Azure-resources die zijn gemaakt om uw app te hosten, worden gedefinieerd in het [pom.xml-bestand](#pomxml). In dit artikel gaat u akkoord met de standaardinstellingen.

> [!TIP]
> Als u een functie-app wilt maken die wordt uitgevoerd op Linux in plaats van Windows, wijzigt u het `runtime.os`-element in het pom.xml-bestand van `windows` in `linux`. Het uitvoeren van Linux in een verbruiksplan wordt ondersteund in [deze regio's](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). U kunt apps die worden uitgevoerd op Linux en apps die worden uitgevoerd in Windows niet dezelfde resourcegroep gebruiken.

1. Voordat u de implementeren kunt starten moet u de Azure CLI-opdracht [az login](/cli/azure/authenticate-azure-cli) gebruiken om u aan te melden bij uw Azure-abonnement. 

    ```azurecli
    az login
    ```

1. Gebruik de volgende opdracht om uw project te implementeren in een nieuwe functie-app.

    ```console
    mvn azure-functions:deploy
    ```
    
    Hiermee maakt u de volgende resources in Azure:
    
    + Resourcegroep. Met de naam _java-functions-group_.
    + Opslagaccount. Vereist door Funtions. De naam wordt willekeurig gegenereerd op basis van de vereisten van het opslagaccount.
    + Hostingabonnement. Serverloze hosting voor uw functie-app in de regio _westus_. De naam is _java-functions-app-service-plan_.
    + Functie-app. Een functie-app is de implementatie- en uitvoeringseenheid voor uw functies. De naam wordt willekeurig gegenereerd op basis van uw _artifactId_,waaraan een willekeurig gegenereerd nummer wordt toegevoegd.
    
    De implementatie verpakt de projectbestanden en implementeert deze in de nieuwe functie-app met behulp van [zip implementation](functions-deployment-technologies.md#zip-deploy). De code wordt uitgevoerd vanuit het implementatiepakket in Azure.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u verdergaat met de [volgende stap](#next-steps) en een uitvoerbinding voor een Azure Storage-wachtrij toevoegt, kunt u alle resources het beste op dezelfde plaats laten staan. U gaat ze namelijk gebruiken in deze stap.

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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verbinding maken met een Azure Storage-wachtrij](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
