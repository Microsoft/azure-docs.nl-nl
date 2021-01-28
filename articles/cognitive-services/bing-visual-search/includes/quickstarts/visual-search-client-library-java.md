---
title: Quickstart Java-clientbibliotheek Bing Visual Search
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.custom: devx-track-java, devx-track-csharp
ms.author: aahi
ms.openlocfilehash: d1c9589265726bae01b86c152853c40f79825ceb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948626"
---
In deze quickstart leert u hoe u met de Java-clientbibliotheek afbeeldingsinzichten krijgt uit de Bing Visual Search-service. Hoewel Bing Visual Search een REST API heeft die compatibel is met de meeste programmeertalen, biedt de clientbibliotheek een eenvoudige manier om de service in uw toepassingen te integreren. De broncode voor deze quickstart is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVisualSearch).

Gebruik de Bing Visual Search-clientbibliotheek voor Java om het volgende te doen:

* Een afbeelding uploaden om een visuele zoekopdracht te verzenden.
* Het afbeeldingsinzichttoken en de visuele zoektags ophalen.

[Referentiedocumentatie](/java/api/overview/azure/cognitiveservices/client/bingvisualsearch) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [Artefact (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

Nadat u een sleutel van uw resource hebt opgehaald, [maakt u een omgevingsvariabele](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `BING_SEARCH_V7_SUBSCRIPTION_KEY`.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `gradle init` uit vanuit uw werkmap. Met deze opdracht maakt u essentiële buildbestanden voor Gradle, inclusief *build.gradle.kts*, dat tijdens runtime wordt gebruikt om de toepassing te maken en te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

Zoek *build.gradle.kts* en open het met uw favoriete IDE of teksteditor. Kopieer het vervolgens in deze buildconfiguratie:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Maak een map voor de voorbeeld-app. Voer de volgende opdracht uit vanuit uw werkmap:

```console
mkdir -p src/main/java
```

Maak een map voor de afbeelding die u wilt uploaden naar de API. Plaats de afbeelding in de **resources**-map.

```console
mkdir -p src/main/resources
``` 

Ga naar de nieuwe map en maak een bestand met de naam *BingVisualSearchSample.java*. Open het bestand in uw voorkeurseditor of IDE en voeg de volgende `import`-instructies toe:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Maak vervolgens een nieuwe klasse.

```java
public class BingVisualSearchSample {
}
```

Maak in de methode `main` van de toepassing variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele. Maak vervolgens een `byte[]` voor de afbeelding die u wilt uploaden. Maak een `try`-blok voor de methoden die u later gaat definiëren en laad de afbeelding en converteer deze naar bytes met behulp van `toByteArray()`.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Deze quickstart maakt gebruik van de Gradle-afhankelijkheidsmanager. U vindt de clientbibliotheek en informatie voor andere afhankelijkheidsbeheerders in de [Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Zorg ervoor dat u in het bestand *build.gradle.kts* de clientbibliotheek opneemt als een `implementation`-instructie. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Bing Visual Search-clientbibliotheek en Java:

* [De client verifiëren](#authenticate-the-client)
* [Een visuele zoekaanvraag verzenden](#send-a-visual-search-request)
* [Het afbeeldingsinzichttoken en de visuele zoektags printen](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>De client verifiëren

> [!NOTE]
> In deze quickstart wordt ervan uitgegaan dat u [een omgevingsvariabele hebt gemaakt](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor uw Bing Visual Search-sleutel, met de naam `BING_SEARCH_V7_SUBSCRIPTION_KEY`.


Zorg er in de hoofdmethode voor dat u uw abonnementssleutel gebruikt om een [BingVisualSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi)-object te instantiëren.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Een visuele zoekaanvraag verzenden

Verzend in een nieuwe methode de bytematrix van de afbeelding (die is gemaakt in de methode `main()`) met behulp van de methode [bingImages().visualSearch()](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) van de client. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Het afbeeldingsinzichttoken en de visuele zoektags printen

Controleer of het object [ImageKnowledge](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge) null is. Als dat niet het geval is, print u het afbeeldingsinzichttoken, het aantal tags, het aantal acties en het eerste actietype.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app maken met:

```console
gradle build
```

De toepassing uitvoeren met het doel `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app van één pagina maken](../../tutorial-bing-visual-search-single-page-app.md).
