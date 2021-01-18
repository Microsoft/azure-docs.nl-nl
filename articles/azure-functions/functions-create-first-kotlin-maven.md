---
title: Een Kotlin-functie in Azure Functions maken met behulp van Maven
description: Maak een door HTTP getriggerde functie-app en publiceer deze naar Azure Functions met Kotlin en Maven.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6f7b79b6e3e72b34a27e5b4f0e1fb5426c539699
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035237"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>Quickstart: Uw eerste functie maken met behulp van Kotlin en Maven

In dit artikel wordt uitgelegd hoe u met behulp van het opdrachtregelprogramma Maven een Kotlin-functieproject voor Azure Functions kunt bouwen en publiceren. Wanneer u klaar bent, wordt uw functiecode uitgevoerd in het [Verbruiksabonnement](consumption-plan.md) in Azure en kan deze worden geactiveerd met behulp van een HTTP-aanvraag.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Als u functies wilt ontwikkelen met behulp van Kotlin, moet het volgende zijn geïnstalleerd:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versie 8
- [Apache Maven](https://maven.apache.org), versie 3.0 of hoger
- [Azure-CLI](/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2), versie 2.6.666 of hoger

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om deze quickstart te kunnen voltooien.

## <a name="generate-a-new-azure-functions-project"></a>Een nieuw Azure Functions-project genereren

Voer in een lege map de volgende opdracht uit om het Azure Functions-project te genereren op basis van een [Maven-archtype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> Als u problemen ondervindt met het uitvoeren van de opdracht, controleert u welke versie van `maven-archetype-plugin` er wordt gebruikt. Omdat u de opdracht uitvoert in een lege map zonder `.pom`-bestand, wordt er mogelijk geprobeerd om een invoegtoepassing met de oudere versie te gebruiken van `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` als u Maven hebt bijgewerkt op basis van een oudere versie. Als dit het geval is, verwijdert u de map `maven-archetype-plugin` en voert u de opdracht opnieuw uit.

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

U wordt door Maven gevraagd om de waarden die nodig zijn om het project te kunnen genereren. Informatie over de waarden voor _groupId_, _artifactId_ en _version_ kunt u vinden in de Engelstalige [naslag van Maven over naamconventies](https://maven.apache.org/guides/mini/guide-naming-conventions.html). De waarde voor _appName_ moet uniek zijn binnen Azure. Om die reden genereert Maven standaard een app-naam op basis van de eerder opgegeven waarde voor _artifactId_. De waarde voor _packageName_ bepaalt het Kotlin-pakket voor de gegenereerde functiecode.

De id's `com.fabrikam.functions` en `fabrikam-functions` hieronder worden gebruikt als een voorbeeld en om latere stappen in deze snelstart makkelijker te kunnen lezen. In deze stap wordt u aangeraden om Maven van uw eigen waarden te voorzien.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

Maven maakt de projectbestanden in een nieuwe map met de naam _artifactId_; in dit voorbeeld `fabrikam-functions`. De gegenereerde code in het project kan meteen worden uitgevoerd en is een eenvoudige, [door HTTP getriggerde](./functions-bindings-http-webhook.md) functie die de hoofdtekst van de aanvraag weergeeft:

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Ga naar de zojuist gemaakte projectmap en gebruik Maven om de functie te bouwen en uit te voeren:

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Als u deze uitzondering krijgt: `javax.xml.bind.JAXBException` met Java 9, bekijkt u de tijdelijke oplossing op [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

U ziet deze uitvoer als de functie lokaal op uw systeem wordt uitgevoerd en klaar is om op HTTP-aanvragen te reageren:

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

Activeer de functie vanaf de opdrachtregel met de opdracht curl in een nieuw terminalvenster:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

Gebruik `Ctrl-C` in de terminal om de functiecode te stoppen.

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

Bij het implementeren naar Azure Functions worden accountreferenties uit de Azure CLI gebruikt. [Meld u aan met Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) voordat u doorgaat.

```azurecli
az login
```

Implementeer de code in een nieuwe functie-app met behulp van de Maven-target `azure-functions:deploy`.

> [!NOTE]
> Wanneer u Visual Studio Code gebruikt om uw functie-app te implementeren, moet u een betaald abonnement kiezen om te voorkomen dat er een foutmelding wordt weergegeven. U kunt uw abonnement aan de linkerkant van de IDE bekijken.

```
mvn azure-functions:deploy
```

Als het implementeren is voltooid, ziet u de URL die u kunt gebruiken voor toegang tot de functie-app:

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

Test de functie-app in Azure met behulp van `cURL`. Wijzig de URL in onderstaand voorbeeld om deze overeen te laten komen met de geïmplementeerde URL voor uw eigen functie-app uit de vorige stap.

> [!NOTE]
> Zorg ervoor dat u **Toegangsrechten** instelt op `Anonymous`. Wanneer u het standaardniveau `Function` kiest, moet u de [functiesleutel](functions-bindings-http-webhook-trigger.md#authorization-keys) opnemen in aanvragen om toegang te krijgen tot het eindpunt van de functie.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Wijzigingen maken en opnieuw implementeren

Bewerk het bronbestand `src/main.../Function.java` in het gegenereerde project om de tekst te wijzigen die door de functie-app is geretourneerd. Wijzig deze regel:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

In de volgende code:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

Sla de wijzigingen op en implementeer opnieuw door `azure-functions:deploy` uit te voeren vanaf de terminal, zoals u eerder hebt gedaan. De functie-app wordt bijgewerkt, en deze aanvraag:

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

U ziet de bijgewerkte uitvoer:

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>Verwijzingsbindingen

Als u wilt werken met andere [Azure Functions-triggers en -bindingen](functions-triggers-bindings.md) dan HTTP-trigger en Timer-trigger, moet u bindingsuitbreidingen installeren. Hoewel dit niet vereist is voor dit artikel, moet u weten hoe u uitbreidingen kunt inschakelen wanneer u met andere bindingstypen gaat werken.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt een Kotlin-functie-app gemaakt met een eenvoudige HTTP-trigger en deze geïmplementeerd naar Azure Functions.

- Neem de [Azure Functions Java-handleiding voor ontwikkelaars](functions-reference-java.md) door voor meer informatie over het ontwikkelen van Java- en Kotlin-functies.
- U kunt extra functies met verschillende triggers toevoegen aan uw project met behulp van de Maven-target `azure-functions:add`.
- Gebruik [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) en [Eclipse](functions-create-maven-eclipse.md) om functies lokaal te schrijven en fouten op te sporen. 
- Gebruik Visual Studio Code om fouten op te sporen in functies die zijn geïmplementeerd in Azure. Raadpleeg de Visual Studio Code-documentatie over [serverloze Java-toepassingen](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) voor instructies.
