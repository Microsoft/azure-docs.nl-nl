---
title: Quickstart Java-clientbibliotheek Bing Custom Search
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 300c602a62b0d6b3ba579931b2222d2cd8667656
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948418"
---
Aan de slag met de clientbibliotheek van Bing Custom Search voor Java. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. Met de API van Bing Custom Search kunt u op maat gemaakte zoekervaringen zonder advertenties maken voor onderwerpen die u interesseren. De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

Gebruik de Bing Custom Search-clientbibliotheek voor Java om het volgende te doen:

* Zoekresultaten zoeken op internet vanuit uw exemplaar van Bing Custom Search.

[Referentiedocumentatie](/java/api/overview/azure/cognitiveservices/client/bingcustomsearch) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch) | [Artefact (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/cognitive-services/).
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder.
* Een Bing Custom Search-exemplaar. Zie [Quickstart: Uw eerste Bing Custom Search-exemplaar maken](../../quick-start.md) voor meer informatie.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Nadat u een sleutel van uw resource hebt opgehaald, [maakt u een omgevingsvariabele](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de sleutel met de naam `AZURE_BING_CUSTOM_SEARCH_API_KEY`.

### <a name="create-a-new-gradle-project"></a>Een nieuw Gradle-project maken

> [!TIP]
> Als u Gradle niet gebruikt, vindt u de clientbibliotheekinformatie voor andere afhankelijkheidsbeheerders in de [Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map.

```console
mkdir myapp && cd myapp
```

Voer de opdracht `gradle init` uit vanuit uw werkmap. Met deze opdracht maakt u essentiële buildbestanden voor Gradle, inclusief een *build.gradle.kts*-bestand, dat tijdens runtime wordt gebruikt om uw toepassing te configureren.

```console
gradle init --type basic
```

Wanneer u wordt gevraagd om een **DSL** te kiezen, selecteert u **Kotlin**.

## <a name="install-the-client-library"></a>De clientbibliotheek installeren

Zoek *build.gradle.kts* op en open het met uw favoriete IDE of teksteditor. Kopieer het vervolgens in deze buildconfiguratie. Zorg ervoor dat u de clientbibliotheek opgeeft onder `dependencies`:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Maak een map voor de voorbeeld-app. Voer de volgende opdracht uit vanuit uw werkmap:

```console
mkdir src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *BingCustomSearchSample.java*. Open het en voeg de volgende `import`-instructies toe:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Maak een klasse met de naam `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

Maak in de klasse een `main`-methode en een variabele voor de sleutel van uw resource. Als u de omgevingsvariabele hebt gemaakt nadat u de toepassing hebt gestart, moet u de editor, IDE of shell waarmee deze wordt uitgevoerd, sluiten en opnieuw openen om toegang te krijgen tot de variabele. U definieert later de methoden.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objectmodel

De Bing Custom Search-client is een [BingCustomSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi)-object dat is gemaakt op basis van de [authenticate()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager)-methode van het [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate)-object. U kunt een zoekopdracht verzenden met behulp van de [BingCustomInstances.search()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__)-methode van de client.

Het API-antwoord is een [SearchResponse](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse)-object dat informatie bevat over de zoekopdracht en de zoekresultaten.

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Bing Custom Search-clientbibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Zoekresultaten ophalen van uw aangepaste zoekopdrachtexemplaar](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>De client verifiëren

De hoofdmethode moet een [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi)-object bevatten dat uw sleutel gebruikt en de `authenticate()` ervan aanroept.

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Zoekresultaten ophalen van uw aangepaste zoekopdrachtexemplaar

Gebruik de functie [BingCustomInstances.search()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) van de client om een zoekopdracht naar uw aangepaste exemplaar te verzenden. Stel de `withCustomConfig` in op uw aangepaste configuratie-ID, of op de standaardwaarde `1`. Nadat u een antwoord van de API hebt opgehaald, controleert u of er zoekresultaten zijn gevonden. Als dit het geval is, kunt u het eerste zoekresultaat ophalen door de functie `webPages().value().get()` van het antwoord aan te roepen en de naam van het resultaat en de URL af te drukken.

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Bouw de app met de volgende opdracht uit de hoofdmap van uw project:

```console
gradle build
```

Voer de toepassing uit met het doel `run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app voor aangepaste zoekopdrachten bouwen](../../tutorials/custom-search-web-page.md)
