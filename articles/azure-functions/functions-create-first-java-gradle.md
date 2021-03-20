---
title: Java-en Gradle gebruiken om een functie te publiceren in azure
description: Een door HTTP geactiveerde functie maken en publiceren naar Azure met Java en Gradle.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: c9ecb670cea022988efda8ec690c0724310cdb4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934846"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Java en Gradle gebruiken voor het maken en publiceren van een functie in azure

In dit artikel wordt beschreven hoe u een Java-functie project bouwt en publiceert naar Azure Functions met het opdracht regel programma Gradle. Wanneer u klaar bent, wordt de functie code in azure uitgevoerd in een [hosting abonnement](consumption-plan.md) op de server en wordt geactiveerd door een HTTP-aanvraag. 

> [!NOTE]
> Als Gradle niet uw voor keur is, Bekijk dan onze vergelijk bare zelf studies voor Java-Ontwikkel aars met [maven](./create-first-function-cli-java.md), [IntelliJ idee](/azure/developer/java/toolkit-for-intellij/quickstart-functions) en [VS code](./create-first-function-vs-code-java.md).

## <a name="prerequisites"></a>Vereisten

Als u functies wilt ontwikkelen met behulp van Java, moet het volgende zijn geïnstalleerd:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versie 8
- [Azure-CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2), versie 2.6.666 of hoger
- [Gradle](https://gradle.org/), versie 4,10 en hoger

U hebt ook een actief Azure-abonnement nodig. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om deze quickstart te kunnen voltooien.

## <a name="prepare-a-functions-project"></a>Een functie project voorbereiden

Gebruik de volgende opdracht om het voorbeeld project te klonen:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Open `build.gradle` en wijzig de `appName` in de volgende sectie in een unieke naam om te voor komen dat domein naam conflicteert bij het implementeren naar Azure. 

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

Open de nieuwe functie. java-bestand van het pad *src/main/Java* in een tekst editor en controleer de gegenereerde code. Deze code is een door [http geactiveerde](functions-bindings-http-webhook.md) functie die de hoofd tekst van de aanvraag echoert. 

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>De functie lokaal uitvoeren

Voer de volgende opdracht uit om het functie project vervolgens uit te voeren:

```bash
gradle jar --info
gradle azureFunctionsRun
```
U ziet uitvoer als het volgende van Azure Functions Core Tools wanneer u het project lokaal uitvoert:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Activeer de functie vanaf de opdracht regel met behulp van de volgende krul opdracht in een nieuw terminal venster:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

De verwachte uitvoer is als volgt:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Als u authLevel instelt op `FUNCTION` of `ADMIN` , is de [functie sleutel](functions-bindings-http-webhook-trigger.md#authorization-keys) niet vereist voor het lokaal uitvoeren.  

Gebruik `Ctrl+C` in de terminal om de functiecode te stoppen.

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

Een functie-app en gerelateerde resources worden in azure gemaakt wanneer u de functie-app voor het eerst implementeert. Voordat u de implementeren kunt starten moet u de Azure CLI-opdracht [az login](/cli/azure/authenticate-azure-cli) gebruiken om u aan te melden bij uw Azure-abonnement. 

```azurecli
az login
```

> [!TIP]
> Als uw account toegang heeft tot meerdere abonnementen, gebruikt u [AZ account set](/cli/azure/account#az-account-set) om het standaard abonnement voor deze sessie in te stellen. 

Gebruik de volgende opdracht om uw project te implementeren in een nieuwe functie-app. 

```bash
gradle azureFunctionsDeploy
```

Hiermee maakt u de volgende resources in azure, op basis van de waarden in het bestand build. gradle:

+ Resourcegroep. Met de naam van de _resourceGroup_ die u hebt opgegeven.
+ Opslagaccount. Vereist door Funtions. De naam wordt willekeurig gegenereerd op basis van de vereisten van het opslagaccount.
+ App Service plan. Serverloze verbruiks plan dat als host fungeert voor uw functie-app in de opgegeven _appRegion_. De naam wordt wille keurig gegenereerd.
+ Functie-app. Een functie-app is de implementatie- en uitvoeringseenheid voor uw functies. De naam is uw _AppName_, die wordt toegevoegd met een wille keurig gegenereerd nummer. 

De implementatie verpakt de project bestanden ook en implementeert deze in de nieuwe functie-app met behulp van [zip-implementatie](functions-deployment-technologies.md#zip-deploy), waarbij de modus uitvoeren vanaf pakket is ingeschakeld.

De authLevel voor de HTTP-trigger in het voorbeeld project is `ANONYMOUS` , waarmee de verificatie wordt overgeslagen. Als u echter andere authLevel gebruikt, zoals `FUNCTION` of `ADMIN` , moet u de functie code ophalen om het eind punt van de functie aan te roepen via http. De eenvoudigste manier om de functie code op te halen, is van de [Azure Portal].

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>De URL van de HTTP-trigger ophalen

U kunt de URL ophalen die is vereist om uw functie te activeren, met de functie sleutel van de Azure Portal. 

1. Blader naar de [Azure Portal], Meld u aan, typ de _AppName_ van uw functie-app in **zoeken** boven aan de pagina en druk op ENTER.
 
1. Selecteer in de functie-app **functies**, kies uw functie en klik vervolgens op **</> functie-URL rechtsboven weer** geven. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="De functie-URL vanuit Azure Portal kopiëren":::

1. Kies **standaard (functie toets)** en selecteer **kopiëren**. 

U kunt nu de gekopieerde URL gebruiken om toegang te krijgen tot uw functie.

## <a name="verify-the-function-in-azure"></a>De functie in azure controleren

Als u de functie-app die wordt uitgevoerd op Azure wilt controleren met `cURL` , vervangt u de URL uit het voor beeld hieronder door de URL die u hebt gekopieerd uit de portal.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Hiermee wordt een POST-aanvraag verzonden naar het eind punt van de functie `AzureFunctions` in de hoofd tekst van de aanvraag. U ziet het volgende antwoord.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Er is een fout opgetreden](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Volgende stappen

U hebt een Java-functies project gemaakt met een door HTTP geactiveerde functie, deze uitvoeren op uw lokale computer en geïmplementeerd in Azure. Breid uw functie nu uit door...

> [!div class="nextstepaction"]
> [Een Azure Storage wachtrij-uitvoer binding toevoegen](functions-add-output-binding-storage-queue-java.md)


[Azure-CLI]: /cli/azure
[Azure-portal]: https://portal.azure.com