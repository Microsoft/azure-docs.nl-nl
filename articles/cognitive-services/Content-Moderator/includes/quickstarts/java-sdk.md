---
title: 'Quickstart: Content Moderator Java-clientbibliotheek'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u aan de slag gaat met de Azure Content Moderator-clientbibliotheek voor Java. Voeg software voor het filteren van inhoud toe aan uw app om te voldoen aan de regelgeving of om de beoogde omgeving voor gebruikers te behouden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 147985ce5aff54f3784363720a576b7e46d6e647
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947770"
---
Ga aan de slag met de Azure Content Moderator-clientbibliotheek voor Java. Voer deze stappen uit om het Maven-pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. 

Content Moderator is een AI-service waarmee u inhoud kunt afhandelen die mogelijk aanstootgevend, riskant of anderszins ongewenst is. Gebruik de service voor inhoudsbeheer op basis van AI, waarmee tekst, afbeeldingen en video's worden gescand en automatisch inhoudsmarkeringen worden toegepast. Voeg software voor het filteren van inhoud toe aan uw app om te voldoen aan de regelgeving of om de beoogde omgeving voor gebruikers te behouden.

Gebruik de Content Moderator-clientbibliotheek voor Java om:

* Tekst modereren
* Afbeeldingen modereren

[Referentiedocumentatie](/java/api/overview/azure/cognitiveservices/client/contentmoderator) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator) |[Artefact (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [Voorbeelden](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder.
* Zodra u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Een Content Moderator-resource maken"  target="_blank">een Content Moderator-resource maken <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met Content Moderator. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

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

## <a name="install-the-client-library"></a>De clientbibliotheek installeren

Zoek *build.gradle.kts* op en open het met uw favoriete IDE of teksteditor. Kopieer het vervolgens in de volgende buildconfiguratie. Deze configuratie definieert het project als een Java-toepassing waarvan het toegangspunt de klasse **ContentModeratorQuickstart** is. Hiermee worden de Content Moderator-clientbibliotheek en de Gson-SDK voor JSON-serialisatie geïmporteerd.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>Een Java-bestand maken


Voer de volgende opdracht uit vanuit uw werkmap om een projectbronmap te maken:

```console
mkdir -p src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *ContentModeratorQuickstart.java*. Open het bestand in uw voorkeurseditor of IDE en voeg de volgende `import`-instructies toe:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak in de klasse **ContentModeratorQuickstart** van de toepassing variabelen voor de sleutel en het eindpunt van uw resource.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Content Moderator-resource die u in de sectie **Vereisten** hebt gemaakt, is geïmplementeerd, klikt u op de knop **Naar de resource gaan** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

Voeg in de **hoofdmethode** van de toepassing aanroepen toe voor de methoden die in deze quickstart worden gebruikt. U definieert deze later.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Objectmodel

De volgende klassen worden gebruikt voor enkele van de belangrijkste functies van de Content Moderator Java-clientbibliotheek.

|Naam|Beschrijving|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|Deze klasse is nodig voor alle Content Moderator-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken.|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|Deze klasse biedt de functionaliteit voor het analyseren van afbeeldingen op inhoud voor volwassenen, persoonlijke gegevens of menselijke gezichten.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|Deze klasse biedt de functionaliteit voor het analyseren van tekst op taal, grove taal, fouten en persoonlijke gegevens.|
|[Reviews](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|Deze klasse biedt de functionaliteit van de Review-API's, waaronder de methoden voor het maken van taken, aangepaste werkstromen en menselijke beoordelingen.|


## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Content Moderator-clientbibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Tekst modereren](#moderate-text)
* [Afbeeldingen modereren](#moderate-images)


## <a name="authenticate-the-client"></a>De client verifiëren

Maak in de `main`-methode van de toepassing een [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)-object met behulp van de eindpuntwaarde van uw abonnement en de abonnementssleutel.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Tekst modereren

### <a name="set-up-sample-text"></a>Voorbeeldtekst instellen

Definieer bovenaan de **ContentModeratorQuickstart** -klasse een verwijzing naar een lokaal tekstbestand. Voeg een .txt-bestand toe aan uw projectmap en voer de tekst in die u wilt analyseren.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Tekst analyseren

Maak een nieuwe methode die het .txt-bestand leest en de **screenText** -methode op elke regel aanroept.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Resultaten van tekstbeheer afdrukken

Voeg de volgende code toe om de beheerresultaten naar een. JSON-bestand in de projectmap af te drukken.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

Sluit de `try`- en `catch`-instructie om de methode uit te voeren.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Afbeeldingen modereren

### <a name="set-up-sample-image"></a>Voorbeeldafbeelding instellen

Maak in een nieuwe methode een **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** -object met een opgegeven URL-tekenreeks die naar een afbeelding verwijst.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Helperklasse definiëren

Voeg vervolgens in uw *ContentModeratorQuickstart.java*-bestand de volgende klassedefinitie toe aan de klasse **ContentModeratorQuickstart**. Deze binnenste klasse wordt gebruikt in het proces voor afbeeldingsmoderatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>Inhoud analyseren
Met deze coderegel wordt de afbeelding op de opgegeven URL op inhoud voor volwassenen of racistische inhoud gecontroleerd. Zie de conceptuele handleiding voor afbeeldingsmoderatie voor informatie over deze voorwaarden.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Controleren op tekst
Met deze coderegel wordt de afbeelding gecontroleerd op zichtbare tekst.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Controleren op gezichten
Met deze coderegel wordt de afbeelding gecontroleerd op menselijke gezichten.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Sla tot slot de geretourneerde informatie op in de lijst `EvaluationData`.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Resultaten afdrukken

Voeg na de `while`-lus de volgende code toe, waarmee de resultaten worden afgedrukt op de console en op een uitvoerbestand, *src/main/resources/ModerationOutput.json*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Sluit de `try`-instructie en voeg een `catch`-instructie toe om de methode uit te voeren.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app maken met:

```console
gradle build
```

De toepassing uitvoeren met de opdracht `gradle run`:

```console
gradle run
```

Ga vervolgens naar de bestand *src/main/resources/ModerationOutput.json* en bekijk de resultaten van de inhoud van uw inhoudsmoderatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Content Moderator Java-bibliotheek kunt gebruiken om moderatietaken uit te voeren. Nu kunt u doorgaan en in conceptgids meer lezen over het modereren van afbeeldingen en andere media.

> [!div class="nextstepaction"]
> [Concepten voor afbeeldingsmoderatie](../../image-moderation-api.md)

* [Wat is Azure Content Moderator?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).
