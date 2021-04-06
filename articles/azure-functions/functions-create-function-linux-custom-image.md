---
title: Een functie van Azure Functions maken in Linux met een aangepaste installatiekopie
description: Informatie over het maken van Azure Functions uitgevoerd op een aangepaste installatiekopie van Linux.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 1c7a9fd83131ea6282d2ef4860b744fa348153ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98070912"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Een functie in Linux maken met een aangepaste container

In deze zelfstudie maakt en implementeert u uw code in Azure Functions als een aangepaste Docker-container met behulp van een Linux-basisinstallatiekopie. Een aangepaste installatiekopie gebruikt u meestal wanneer uw functies een specifieke taalversie nodig hebben of een specifieke afhankelijkheid of configuratie vereisen die niet wordt aangeboden door de ingebouwde installatiekopie.

::: zone pivot="programming-language-other"
Azure Functions ondersteunt een taal of runtime met behulp van [aangepaste handlers](functions-custom-handlers.md). Voor sommige talen, zoals de R-programmeertaal die in deze zelfstudie wordt gebruikt, moet u de runtime of extra bibliotheken installeren als afhankelijkheden waarvoor het gebruik van een aangepaste container is vereist.
::: zone-end

Om uw functiecode te implementeren in een aangepaste Linux-container is hosting met een [Premium-abonnement](functions-premium-plan.md) of een [Dedicated (App Service)-abonnement](dedicated-plan.md) vereist. Bij het voltooien van deze zelfstudie worden er kosten ter hoogte van een paar Amerikaanse dollars in rekening gebracht op uw Azure-account. U kunt deze kosten minimaliseren door [resources op te schonen](#clean-up-resources) wanneer u klaar bent.

U kunt ook een standaardcontainer van Azure App Service gebruiken, zoals wordt beschreven in [Create your first function hosted on Linux](./create-first-function-cli-csharp.md?pivots=programming-language-python) (Uw eerste functie maken die wordt gehost in Linux). Ondersteunde basisinstallatie kopieën voor Azure Functions vindt u in de [opslagplaats voor basisinstallatiekopieën van Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).

In deze zelfstudie leert u het volgende:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Een functie-app en Dockerfile maken met behulp van Azure Functions Core Tools.
> * Een aangepaste installatiekopie met behulp van Docker bouwen.
> * Een aangepaste installatiekopie naar een containerregister publiceren.
> * Ondersteunende resources in Azure maken voor de functie-app
> * Een functie-app vanuit Docker Hub implementeren.
> * Toepassingsinstellingen toevoegen aan de functie-app.
> * Continue implementatie inschakelen.
> * SSH-verbindingen naar de container inschakelen.
> * Een uitvoerbinding voor Queue Storage toevoegen. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Een functie-app en Dockerfile maken met behulp van Azure Functions Core Tools.
> * Een aangepaste installatiekopie met behulp van Docker bouwen.
> * Een aangepaste installatiekopie naar een containerregister publiceren.
> * Ondersteunende resources in Azure maken voor de functie-app
> * Een functie-app vanuit Docker Hub implementeren.
> * Toepassingsinstellingen toevoegen aan de functie-app.
> * Continue implementatie inschakelen.
> * SSH-verbindingen naar de container inschakelen.
::: zone-end

U kunt deze zelfstudie volgen op elke computer met Windows, macOS of Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ Een [Docker-ID](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Voer `docker login` uit om u aan te melden bij Docker. Deze opdracht mislukt als Docker niet actief is. In dat geval start u Docker en voert u de opdracht opnieuw uit.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Het lokale Functions-project maken en testen

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Voer in een terminal of bij een opdrachtprompt de volgende opdracht uit voor de taal die u hebt gekozen om een functie-app-project te maken in een map met de naam `LocalFunctionsProject`.  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
Voer in een lege map de volgende opdracht uit om het Functions-project te genereren op basis van een [Maven-archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

De parameter `-DjavaVersion` laat de Functions-runtime weten welke versie van Java moet worden gebruikt. Gebruik `-DjavaVersion=11` als u uw functies wilt uitvoeren in Java 11. Wanneer u `-DjavaVersion` niet opgeeft, wordt Maven standaard ingesteld op Java 8. Zie [Java-versies](functions-reference-java.md#java-versions) voor meer informatie.

> [!IMPORTANT]
> De omgevingsvariabele `JAVA_HOME` moet zijn ingesteld op de installatielocatie van de juiste versie van de JDK om dit artikel te kunnen voltooien.

U wordt door Maven gevraagd om de waarden die nodig zijn om het project op het moment van implementatie te kunnen genereren.   
Geef de volgende waarden op als daarom wordt gevraagd:

| Vraag | Waarde | Beschrijving |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Een waarde die uw project uniek identificeert binnen alle projecten, overeenkomstig de [regels voor de naamgeving van pakketten](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) voor Java. |
| **artifactId** | `fabrikam-functions` | Een waarde die bestaat uit de naam van het JAR-bestand, zonder een versienummer. |
| **version** | `1.0-SNAPSHOT` | Kies de standaardwaarde. |
| **package** | `com.fabrikam.functions` | Een waarde die het Java-pakket aangeeft voor de gegenereerde functiecode. Gebruik de standaard. |

Typ `Y` of druk op Enter om te bevestigen.

Maven maakt de projectbestanden in een nieuwe map met de naam van _artifactId_; in dit voorbeeld is dat `fabrikam-functions`. 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

Met de optie `--docker` wordt een `Dockerfile` voor het project gegenereerd. Hiermee wordt een geschikte aangepaste container gedefinieerd voor gebruik met Azure Functions en de geselecteerde runtime.

Navigeer naar de projectmap:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Voeg een functie toe aan uw project met behulp van de volgende opdracht, waarbij het argument `--name` de unieke naam van de functie is en het argument `--template` de trigger van de functie. `func new` maakt een submap met de naam van de functie die een codebestand bevat dat geschikt is voor de taal van het project en een configuratiebestand met de naam *function.json*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Voeg een functie toe aan uw project met behulp van de volgende opdracht, waarbij het argument `--name` de unieke naam van de functie is en het argument `--template` de trigger van de functie. `func new` maakt een submap met de naam van de functie die een configuratiebestand bevat met de naam *function.json*.

```console
func new --name HttpExample --template "HTTP trigger"
```

Maak in een teksteditor een bestand in de projectmap met de naam *handler. R*. Voeg de volgende inhoud toe.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

Wijzig in *host. json* de sectie `customHandler` om de opstartopdracht van de aangepaste handler te configureren.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Als u de functie lokaal wilt testen, start u de lokale runtimehost van Azure Functions in de hoofdmap van de projectmap: 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

Zodra het eindpunt `HttpExample` wordt weergegeven in de uitvoer, gaat u naar `http://localhost:7071/api/HttpExample?name=Functions`. In de browser wordt als het goed is het bericht 'hello' weergeven als echo van `Functions`, de waarde die is opgegeven voor de queryparameter `name`.

Gebruik **Ctrl**-**C** om de host te stoppen.

## <a name="build-the-container-image-and-test-locally"></a>De containerinstallatiekopie samenstellen en lokaal testen

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
(Optioneel) Bekijk het *Dockerfile* in de hoofdmap van het project. Het Dockerfile beschrijft de omgeving die vereist is om de functie-app uit te voeren in Linux.  De complete lijst met ondersteunde basisinstallatiekopieën voor Azure Functions vindt u op [deze pagina over basisinstallatiekopieën van Azure Functions](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Bekijk het *Dockerfile* in de hoofdmap van het project. Het Dockerfile beschrijft de omgeving die vereist is om de functie-app uit te voeren in Linux. Aangepaste handler-toepassingen gebruiken de `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice`-installatiekopie als basis.

Wijzig het *Dockerfile* om R te installeren. Vervang de inhoud van *Dockerfile* door het volgende.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

Voer in de hoofdmap van het project de opdracht [docker build](https://docs.docker.com/engine/reference/commandline/build/) uit en geef een naam, `azurefunctionsimage` en een tag, `v1.0.0`, op. Vervang `<DOCKER_ID>` door de ID van uw Docker Hub-account. Met deze opdracht wordt de Docker-installatiekopie voor de container gebouwd.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Wanneer de opdracht is voltooid, kunt u de nieuwe container lokaal uitvoeren.
    
Als u de build wilt testen, voert u de installatiekopie uit in een lokale container met behulp van de opdracht [docker run](https://docs.docker.com/engine/reference/commandline/run/), waarbij u `<DOCKER_ID` opnieuw vervangt door de Docker-ID en het argument poorten toevoegt, `-p 8080:80`:

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Zodra de installatiekopie wordt uitgevoerd in een lokale container, gaat u in een browser naar `http://localhost:8080`. Hier moet u de tijdelijke aanduiding voor afbeeldingen zien die hier hieronder wordt weergegeven. De afbeelding wordt op dit moment weergegeven omdat uw functie wordt uitgevoerd in de lokale container, zoals dat zou gebeuren in Azure. Dit betekent dat de functie wordt beveiligd door een toegangssleutel zoals is gedefinieerd in *function.json* met de eigenschap `"authLevel": "function"`. De container is echter nog niet gepubliceerd naar een functie-app in Azure, dus is de sleutel nog niet beschikbaar. Als u wilt testen met de lokale container, stopt u Docker, wijzigt u de autorisatie-eigenschap in `"authLevel": "anonymous"`, bouwt u de installatiekopie opnieuw op en start u Docker opnieuw. Stel vervolgens `"authLevel": "function"` in *function.json* opnieuw in. Zie [Function access keys](functions-bindings-http-webhook-trigger.md#authorization-keys) (Toegangssleutels voor functie) voor meer informatie.

![Tijdelijke aanduiding voor afbeelding die aangeeft dat de container lokaal wordt uitgevoerd](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Zodra de installatiekopie wordt uitgevoerd in een lokale container, bladert u naar `http://localhost:8080/api/HttpExample?name=Functions`, waar weer net als eerder het bericht 'hello' moet worden weergegeven. Omdat met het Maven-archetype een door HTTP geactiveerde functie wordt gegenereerd die gebruikmaakt van anonieme autorisatie, kunt u de functie toch aanroepen, ondanks dat deze wordt uitgevoerd in de container. 
::: zone-end  

Nadat u de functie-app in de container hebt geverifieerd, stopt u Docker met behulp van **Ctrl**+**C**.

## <a name="push-the-image-to-docker-hub"></a>De installatiekopie pushen naar Docker Hub

Docker Hub is een containerregister die als host fungeert voor installatiekopieën en die services voor installatiekopieën en containers biedt. Als u uw installatiekopie wilt delen, waaronder implementatie naar Azure, moet u de kopie naar een register pushen.

1. Als u zich nog niet hebt aangemeld bij Docker, doet u dit met de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/), waarbij u `<docker_id>` vervangt door uw Docker-ID. Voor deze opdracht moet u uw gebruikersnaam en wachtwoord opgeven. Het bericht 'Login Succeeded' verschijnt als u bent aangemeld.

    ```console
    docker login
    ```
    
1. Nadat u zich hebt aangemeld, pusht u de installatiekopie naar Docker Hub met behulp van de opdracht [docker push](https://docs.docker.com/engine/reference/commandline/push/). Vervang ook hier `<docker_id>` door uw Docker-ID.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. Afhankelijk van de snelheid van uw netwerk, kan het pushen van de installatiekopie de eerste keer enkele minuten duren (het pushen van volgende wijzigingen gaat veel sneller). Terwijl u wacht, kunt u verdergaan met de volgende sectie en Azure-resources maken in een andere terminal.

## <a name="create-supporting-azure-resources-for-your-function"></a>Ondersteunende Azure-resources maken voor uw functie

Als u uw functiecode wilt implementeren in Azure, moet u drie resources maken:

- Een resourcegroep, wat een logische container is voor gerelateerde resources.
- Een Azure Storage-account, waarin de status en andere gegevens van uw projecten worden onderhouden.
- Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Een functie-app wordt gekoppeld aan uw lokale functieproject en maakt het mogelijk om functies te groeperen als een logische eenheid, zodat u resources eenvoudiger kunt beheren, implementeren en delen.

U gebruikt opdrachten van de Azure CLI om deze items te maken. Elke opdracht biedt JSON-uitvoer bij voltooiing.

1. Meld u aan bij Azure met de opdracht [az login](/cli/azure/reference-index#az-login):

    ```azurecli
    az login
    ```
    
1. Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam `AzureFunctionsContainers-rg` gemaakt in de regio `westeurope`. (Over het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt, waarbij u een beschikbare regio kiest met behulp van de opdracht `az account list-locations`.)

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Het is niet mogelijk om Linux- en Windows-apps in dezelfde resourcegroep te hosten. Als u een bestaande resourcegroep met de naam `AzureFunctionsContainers-rg` hebt die een Windows-functie-app of web-app bevat, moet u een andere resourcegroep gebruiken.
    
1. Maak een algemeen opslagaccount in de resourcegroep en regio met behulp van de opdracht [az storage account create](/cli/azure/storage/account#az-storage-account-create). Vervang in het volgende voorbeeld `<storage_name>` door een globaal unieke naam van uw keuze. Namen mogen drie tot 24 tekens bevatten en u mag alleen kleine letters gebruiken. Met `Standard_LRS` geeft u een typische account voor algemeen gebruik op.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Voor het opslagaccount worden gedurende deze zelfstudie slechts een paar dollarcenten in rekening gebracht.
    
1. Gebruik de opdracht voor het maken van een Premium-abonnement voor Azure Functions met de naam `myPremiumPlan` in de prijscategorie **Elastic Premium 1** (`--sku EP1`), in de regio Europa -west (`-location westeurope`, of gebruik een geschikte regio bij u in de buurt) en in een Linux-container (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    We gebruiken hier het Premium-abonnement, maar dit kan naar behoefte worden aangepast. Zie [deze vergelijking van hostingabonnementen van Azure Functions](functions-scale.md) voor meer informatie over hosting. Zie de [pagina met prijzen voor Functions](https://azure.microsoft.com/pricing/details/functions/) om de kosten te berekenen.

    Met deze opdracht wordt in dezelfde resourcegroep ook een gekoppelde instantie van Azure Application Insights ingericht, waarmee u uw functie-app kunt bewaken en logboeken kunt weergeven. Zie [Monitor Azure Functions](functions-monitoring.md) (Azure Functions bewaken) voor meer informatie. Er worden pas kosten in rekening gebracht voor de instantie als u deze activeert.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Een functie-app in Azure maken en configureren met de installatiekopie

Een functie-app in Azure beheert de uitvoering van uw functies in uw hostingabonnement. In dit gedeelte gebruikt u de Azure-resources uit het vorige gedeelte om een functie-app te maken op basis van een installatiekopie in Docker Hub en deze met behulp van een verbindingsreeks te configureren voor Azure Storage.

1. Maak de functie-app met behulp van de opdracht [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Vervang in het volgende voorbeeld `<storage_name>` door de naam die u in het vorige gedeelte hebt gebruikt voor het opslagaccount. Vervang ook `<app_name>` door een globaal unieke naam van uw keuze en `<docker_id>` door uw Docker-ID.

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    Met de parameter *deployment-container-image-name* wordt de installatiekopie opgegeven die moet worden gebruikt voor de functie-app. U kunt de opdracht [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) gebruiken om informatie weer te geven over de installatiekopie die wordt gebruikt voor de implementatie. U kunt ook de opdracht [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) gebruiken om vanuit een andere installatiekopie te implementeren.

1. Gebruik de opdracht [az storage account show-connection-string](/cli/azure/storage/account) om de verbindingsreeks weer te geven voor het opslagaccount dat u hebt gemaakt. Vervang `<storage-name>` door de naam van het opslagaccount dat u hierboven hebt gemaakt:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Voeg deze instelling toe aan de functie-app met behulp van de opdracht [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Vervang in de volgende opdracht `<app_name>` door de naam van uw functie-app en vervang `<connection_string>` door de verbindingsreeks uit de vorige stap (een lange gecodeerde tekenreeks die begint met 'DefaultEndpointProtocol='):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > In Bash kunt u een shell-variabele gebruiken om de verbindingsreeks vast te leggen in plaats van het klembord te gebruiken. Gebruik eerst de volgende opdracht om een variabele te maken met de verbindingsreeks:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Verwijs vervolgens naar de variabele in de tweede opdracht:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. De functie kan deze verbindingsreeks nu gebruiken om toegang te krijgen tot het opslagaccount.

> [!NOTE]    
> Als u uw aangepaste installatiekopie publiceert naar een persoonlijk containeraccount, moet u in plaats daarvan omgevingsvariabelen in het Dockerfile gebruiken voor de verbindingsreeks. Zie de [instructie ENV](https://docs.docker.com/engine/reference/builder/#env) voor meer informatie. U moet ook de variabelen `DOCKER_REGISTRY_SERVER_USERNAME` en `DOCKER_REGISTRY_SERVER_PASSWORD` instellen. Als u de waarden wilt gebruiken, moet u de installatiekopie opnieuw opbouwen, de installatiekopie naar het register pushen en vervolgens de functie-app opnieuw starten in Azure.

## <a name="verify-your-functions-on-azure"></a>Uw functies controleren in Azure

Als de installatiekopie is geïmplementeerd in de functie-app in Azure, kunt u de functie nu via HTTP-aanvragen aanroepen. Omdat de definitie van *function.json* de eigenschap `"authLevel": "function"` bevat, moet u eerst de toegangssleutel (of 'functiesleutel') verkrijgen en deze als een URL-parameter opnemen in alle aanvragen voor het eindpunt.

1. Haal de functie-URL op met de toegangssleutel (functiesleutel) via de Azure-portal of gebruik de Azure CLI met de opdracht `az rest`.

    # <a name="portal"></a>[Portal](#tab/portal)

    1. Meld u aan bij de Azure-portal, en zoek en selecteer **Functie-app**.

    1. Selecteer de functie die u wilt verifiëren.

    1. Selecteer **Functies** in het navigatievenster aan de linkerkant en selecteer vervolgens de functie die u wilt controleren.

        ![De functie kiezen in de Azure-portal](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Selecteer **Functie-URL ophalen**.

        ![De functie-URL ophalen in de Azure-portal](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. Selecteer in het pop-upvenster **standaard (functietoets)** en kopieer de URL naar het klembord. De sleutel wordt gevormd door de tekenreeks volgend op `?code=`.

        ![De standaardtoegangssleutel voor de functie kiezen](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Omdat uw functie-app is geïmplementeerd als een container, kunt u de functiecode niet wijzigen vanuit de portal. U moet in plaats daarvan het project bijwerken in de lokale installatiekopie, de installatiekopie opnieuw naar het register pushen en vervolgens opnieuw implementeren naar Azure. U kunt in een later stadium continue implementatie instellen.
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/azurecli)

    1. Maak een URL-tekenreeks met de volgende indeling, vervang `<subscription_id>`, `<resource_group>` en `<app_name>` respectievelijk door de id van uw Azure-abonnement, de resourcegroep van de functie-app en de naam van uw functie-app:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        De URL kan er bijvoorbeeld uitzien als het volgende adres:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Voor het gemak kunt u in plaats daarvan de URL toewijzen aan een omgevingsvariabele en deze gebruiken in de opdracht `az rest`.
    
    1. Voer de volgende opdracht `az rest` uit (beschikbaar in Azure CLI versie 2.0.77 en hoger), waarbij u `<uri>` vervangt door de URI-tekenreeks uit de laatste stap, inclusief de aanhalingstekens:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. De uitvoer van de opdracht is de functiesleutel. De volledige functie-URL wordt dan `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, waarbij `<app_name>`, `<function_name>`en `<key>` moeten worden vervangen door uw specifieke waarden.
    
        > [!NOTE]
        > De sleutel die hier wordt opgehaald, is de *host*-sleutel die werkt voor alle functies in de functie-app. Met de methode die wordt weergegeven voor de portal wordt de sleutel voor slechts één functie opgehaald.

    ---

1. Plak de functie-URL in de adresbalk van uw browser en voeg de parameter `&name=Azure` toe aan het einde van deze URL. Er moet nu tekst zoals 'Hello, Azure' worden weergegeven in de browser.

    ![Het antwoord van de functie in de browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Als u de autorisatie wilt testen, verwijdert u de parameter `code=` uit de URL en controleert u of u inderdaad geen reactie krijgt van de functie.


## <a name="enable-continuous-deployment-to-azure"></a>Continue implementatie in Azure inschakelen

U kunt instellen dat Azure Functions uw implementatie van een installatiekopie automatisch bijwerkt wanneer u de installatiekopie in het register bijwerkt.

1. Schakel continue implementatie in met behulp van de opdracht [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config), waarbij u `<app_name>` vervangt door de naam van uw functie-app:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Met deze opdracht wordt continue implementatie ingeschakeld en wordt de webhook-URL van de implementatie geretourneerd. (U kunt deze URL later op elk gewenst moment ophalen met behulp van de opdracht [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url).)

1. Kopieer de webhook-URL van de implementatie naar het klembord.

1. Open [Docker Hub](https://hub.docker.com/), meld u aan en selecteer **Repositories** op de navigatiebalk. Zoek en selecteer de installatiekopie, selecteer het tabblad **Webhooks**, geef een waarde op voor **Webhook name**, plak uw URL in **Webhook URL** en selecteer ten slotte **Maken**:

    ![De webhook toevoegen aan de opslagplaats van Docker Hub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Als de webhook is ingesteld, implementeert Azure Functions uw installatiekopie automatisch opnieuw wanneer u deze bijwerkt in Docker Hub.

## <a name="enable-ssh-connections"></a>SSH-verbindingen inschakelen

SSH maakt veilige communicatie tussen een container en een client mogelijk. Als SSH is ingeschakeld, kunt u verbinding maken met uw container met behulp van geavanceerde hulpmiddelen van App Service (Kudu). Azure Functions biedt een basisinstallatiekopie waarvoor SSH al is ingeschakeld, zodat u eenvoudig verbinding kunt maken met uw container via SSH. U hoeft dan alleen uw Dockerfile te bewerken, en de installatiekopie vervolgens opnieuw opbouwen en implementeren. U kunt daarna verbinding maken met de container via de geavanceerde hulpmiddelen (Kudu)

1. Voeg in uw Dockerfile de tekenreeks `-appservice` toe aan de basisinstallatiekopie in de instructie `FROM`:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. Bouw de installatiekopie opnieuw op met behulp van de opdracht `docker build`, waarbij u `<docker_id>` vervangt door uw Docker-ID:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Push de bijgewerkte installatiekopie naar Docker Hub. Dit moet een stuk sneller gaan dan bij eerste push omdat alleen de bijgewerkte segmenten van de installatiekopie moeten worden geüpload.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Azure Functions implementeert de installatiekopie automatisch opnieuw in uw functie-app. Dit proces duurt minder dan één minuut.

1. Open `https://<app_name>.scm.azurewebsites.net/` in een browser en vervang `<app_name>` door uw unieke naam. Deze URL is het eindpunt van de geavanceerde hulpmiddelen (Kudu) voor uw functie-app-container.

1. Meld u aan bij uw Azure-account en selecteer vervolgens **SSH-** om een verbinding met de container tot stand te brengen. Het maken van een verbinding kan enige tijd duren als Azure de containerinstallatiekopie nog aan het bijwerken is.

1. Nadat er een verbinding tot stand is gebracht met uw container, voert u de opdracht `top` uit om de actieve processen weer te geven. 

    ![top-opdracht van Linux die in een SSH-sessie wordt uitgevoerd](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Wegschrijven naar Azure Storage-wachtrij

Met Azure Functions kunt u uw functies verbinden met andere Azure-services en -resources zonder dat u uw eigen integratiecode hoeft te schrijven. Deze zogenaamde *bindingen*, die zowel invoer als uitvoer vertegenwoordigen, worden gedeclareerd binnen de functiedefinitie. Gegevens van bindingen worden als parameters doorgegeven aan de functie. Een *trigger* is een speciaal type invoerbinding. Hoewel een functie slechts één trigger heeft, kan deze meerdere invoer- en uitvoerbindingen hebben. Zie [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md) (Concepten van Azure Functions-triggers en -bindingen) voor meer informatie.

In dit gedeelte wordt beschreven hoe u uw functie integreert met een Azure Storage-wachtrij. De uitvoerbinding die u aan deze functie toevoegt, schrijft gegevens van een HTTP-aanvraag naar een bericht in de wachtrij.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Code toevoegen die gebruikmaakt van de uitvoerbinding

Als de wachtrijbinding is gedefinieerd, kunt u de functie bijwerken om de uitvoerparameter `msg` te ontvangen en berichten te schrijven naar de wachtrij.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>De installatiekopie bijwerken in het register

1. Voer `docker build` opnieuw uit in de hoofdmap en werk deze keer de versie in de tag bij naar `v1.0.1`. Vervang `<docker_id>` zoals eerder door de id van uw Docker Hub-account:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Push de bijgewerkte installatiekopie terug naar de opslagplaats met `docker push`:

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Omdat u continue levering hebt geconfigureerd, wordt de functie-app in Azure automatisch bijgewerkt wanneer u de installatiekopie aanpast in het register.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Het bericht weergeven in de wachtrij van Azure Storage

Gebruik in een browser dezelfde URL als eerder om uw functie aan te roepen. De browser moet hetzelfde antwoord weergeven als hiervoor, omdat u dat deel van de functiecode niet hebt gewijzigd. Met de toegevoegde code werd er echter een bericht geschreven naar de Storage-wachtrij `outqueue` met behulp van de URL-parameter `name`.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven werken met de Azure-functie met behulp van de resources die u in deze zelfstudie hebt gemaakt, kunt u alle resources behouden. Omdat u een Premium-abonnement voor Azure Functions hebt gemaakt, zal er per dag één of twee dollar in rekening worden gebracht voor lopende kosten.

Om deze lopende kosten te voorkomen, verwijdert u de resourcegroep `AzureFunctionsContainer-rg` om alle resources in de groep op te schonen: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Volgende stappen

+ [Functies bewaken](functions-monitoring.md)
+ [Opties voor schalen en hosten](functions-scale.md)
+ [Op Kubernetes gebaseerde serverloze hosting](functions-kubernetes-keda.md)
