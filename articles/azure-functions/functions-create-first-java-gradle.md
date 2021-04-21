---
title: Java en Gradle gebruiken om een functie te publiceren naar Azure
description: Maak en publiceer een http-geactiveerde functie naar Azure met Java en Gradle.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: d7f8aa990f5a5e64d2d5c59b52457149187acddd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773977"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Java en Gradle gebruiken om een functie te maken en te publiceren naar Azure

In dit artikel wordt beschreven hoe u een Java-functieproject maakt en publiceert Azure Functions met het opdrachtregelprogramma Gradle. Wanneer u klaar bent, wordt uw functiecode uitgevoerd in Azure in een [serverloos hostingplan](consumption-plan.md) en geactiveerd door een HTTP-aanvraag. 

> [!NOTE]
> Als Gradle niet uw favoriete ontwikkelhulpprogramma is, bekijkt u onze vergelijkbare zelfstudies voor Java-ontwikkelaars die [Maven,](./create-first-function-cli-java.md) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) en [VS Code gebruiken.](./create-first-function-vs-code-java.md)

## <a name="prerequisites"></a>Vereisten

Als u functies wilt ontwikkelen met behulp van Java, moet het volgende zijn geïnstalleerd:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versie 8
- [Azure-CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2), versie 2.6.666 of hoger
- [Gradle,](https://gradle.org/)versie 4.10 en hoger

U hebt ook een actief Azure-abonnement nodig. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om deze quickstart te kunnen voltooien.

## <a name="prepare-a-functions-project"></a>Een Functions-project voorbereiden

Gebruik de volgende opdracht om het voorbeeldproject te klonen:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Open en wijzig de in de volgende sectie in een unieke naam om conflicten met de domeinnaam `build.gradle` te voorkomen bij de implementatie in `appName` Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Open het nieuwe bestand Function.java vanuit het *pad src/main/java* in een teksteditor en controleer de gegenereerde code. Deze code is een [door HTTP geactiveerde](functions-bindings-http-webhook.md) functie die de body van de aanvraag echot. 

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Voer de volgende opdracht uit om het functieproject te bouwen:

```bash
gradle jar --info
gradle azureFunctionsRun
```
De uitvoer ziet er als volgt uit Azure Functions Core Tools het project lokaal wordt uitgevoerd:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Activeer de functie vanaf de opdrachtregel met behulp van de volgende cURL-opdracht in een nieuw terminalvenster:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

De verwachte uitvoer is als volgt:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Als u authLevel instelt op of , is de functiesleutel `FUNCTION` niet vereist wanneer deze lokaal wordt `ADMIN` uitgevoerd. [](functions-bindings-http-webhook-trigger.md#authorization-keys)  

Gebruik `Ctrl+C` in de terminal om de functiecode te stoppen.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

Er worden een functie-app en gerelateerde resources gemaakt in Azure wanneer u uw functie-app voor het eerst implementeert. Voordat u de implementeren kunt starten moet u de Azure CLI-opdracht [az login](/cli/azure/authenticate-azure-cli) gebruiken om u aan te melden bij uw Azure-abonnement. 

```azurecli
az login
```

> [!TIP]
> Als uw account toegang heeft tot meerdere abonnementen, gebruikt [u az account set om](/cli/azure/account#az_account_set) het standaardabonnement voor deze sessie in te stellen. 

Gebruik de volgende opdracht om uw project te implementeren in een nieuwe functie-app. 

```bash
gradle azureFunctionsDeploy
```

Hiermee maakt u de volgende resources in Azure, op basis van de waarden in het bestand build.gradle:

+ Resourcegroep. Met de naam van _de resourceGroup_ die u hebt opgegeven.
+ Opslagaccount. Vereist door Funtions. De naam wordt willekeurig gegenereerd op basis van de vereisten van het opslagaccount.
+ App Service plan. Serverloos verbruiksplannen hosten voor uw functie-app in de opgegeven _appRegion_. De naam wordt willekeurig gegenereerd.
+ Functie-app. Een functie-app is de implementatie- en uitvoeringseenheid voor uw functies. De naam is uw _appName,_ die wordt toegevoegd met een willekeurig gegenereerd nummer. 

De implementatie verpakt ook de projectbestanden en implementeert [](functions-deployment-technologies.md#zip-deploy)deze in de nieuwe functie-app met behulp van zip-implementatie, met run-from-package-modus ingeschakeld.

Het authLevel voor HTTP-trigger in het voorbeeldproject is `ANONYMOUS` , waarmee de verificatie wordt overgeslagen. Als u echter een ander authLevel gebruikt, zoals of , moet u de functiesleutel op halen om het functie-eindpunt `FUNCTION` via HTTP aan te `ADMIN` roepen. De eenvoudigste manier om de functiesleutel op te halen, is via [Azure Portal].

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>De HTTP-trigger-URL downloaden

U kunt de URL die is vereist voor het activeren van uw functie, met de functiesleutel, uit de Azure Portal. 

1. Blader naar [de Azure Portal,]meld u aan, typ de  _appName_ van uw functie-app in Zoeken boven aan de pagina en druk op Enter.
 
1. Selecteer functies in uw functie-app, kies uw functie en klik vervolgens op **</> functie-URL** in de rechterbovenbalk. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="De functie-URL vanuit Azure Portal kopiëren":::

1. Kies **standaard (functietoets)** en selecteer **Kopiëren.** 

U kunt nu de gekopieerde URL gebruiken voor toegang tot uw functie.

## <a name="verify-the-function-in-azure"></a>De functie in Azure controleren

Als u wilt controleren of de functie-app wordt uitgevoerd in Azure met behulp van , vervangt u de URL uit het onderstaande voorbeeld door de URL die u `cURL` hebt gekopieerd uit de portal.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Hiermee wordt een POST-aanvraag naar het eindpunt van de functie met `AzureFunctions` in de body van de aanvraag verzendt. U ziet het volgende antwoord.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Volgende stappen

U hebt een Java Functions-project gemaakt met een door HTTP geactiveerde functie, deze uitgevoerd op uw lokale computer en geïmplementeerd in Azure. Breid nu uw functie uit met...

> [!div class="nextstepaction"]
> [Een uitvoerbinding Azure Storage wachtrij toevoegen](functions-add-output-binding-storage-queue-java.md)


[Azure-CLI]: /cli/azure
[Azure-portal]: https://portal.azure.com