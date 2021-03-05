---
title: 'Quickstart: Computer Vision-clientbibliotheek voor Java'
description: Ga in deze quickstart aan de slag met de Computer Vision-clientbibliotheek voor Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 59f7d40d64e10266937aab8dacb39bd6c647ce07
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102193832"
---
<a name="HOLTop"></a>

Gebruik de Computer Vision-clientbibliotheek voor het volgende:

* Een afbeelding analyseren op tags, tekstbeschrijvingen, gezichten, inhoud voor volwassenen, en meer.
* Afgedrukte en handgeschreven tekst lezen met de Read-API.

[Referentiedocumentatie](/java/api/overview/azure/cognitiveservices/client/computervision) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision) |[Artefact (Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [Voorbeelden](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder.
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
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

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Deze quickstart maakt gebruik van de Gradle-afhankelijkheidsmanager. U vindt de clientbibliotheek en informatie voor andere afhankelijkheidsbeheerders in de [Maven Central Repository](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

Zoek *build.gradle.kts* en open het met uw favoriete IDE of teksteditor. Kopieer het vervolgens in de volgende buildconfiguratie. Deze configuratie definieert het project als een Java-toepassing waarvan het toegangspunt de klasse **ComputerVisionQuickstarts** is. Hiermee wordt de Computer Vision-bibliotheek geïmporteerd.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>Een Java-bestand maken

Voer de volgende opdracht uit vanuit uw werkmap om een projectbronmap te maken:

```console
mkdir -p src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *ComputerVisionQuickstarts.java*. Open het bestand in uw voorkeurseditor of IDE en voeg de volgende `import`-instructies toe:

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java), waar de codevoorbeelden uit deze quickstart zich bevinden.

Definieer de klasse **ComputerVisionQuickstarts**.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_classdef_1)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_classdef_2)]

Maak in de **ComputerVisionQuickstarts** -klasse variabelen voor de sleutel en het eind punt van uw resource.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_creds)]


> [!IMPORTANT]
> Ga naar Azure Portal. Als de Computer Vision-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

Voeg in de **hoofdmethode** van de toepassing aanroepen toe voor de methoden die in deze quickstart worden gebruikt. U definieert deze later.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_maincalls)]

> [!div class="nextstepaction"]
> [Ik heb de client ingesteld](?success=set-up-client#object-model) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Computer Vision Java SDK.

|Naam|Beschrijving|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken.|
|[ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision)| Met deze klasse, die afkomstig is uit het clientobject, worden alle afbeeldingsbewerkingen, zoals het analyseren van afbeeldingen, detecteren van tekst en genereren van miniaturen, direct afgehandeld.|
|[VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes)| Deze opsomming definieert de verschillende typen afbeeldingsanalyse die kunnen worden uitgevoerd in een standaard analysebewerking. U geeft een set VisualFeatureTypes-waarden op, afhankelijk van uw behoeften. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Computer Vision-clientbibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Een afbeelding analyseren](#analyze-an-image)
* [Afgedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer in een nieuwe methode een [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)-object met uw eindpunt en sleutel.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Ik heb de client geverifieerd](?success=authenticate-client#analyze-an-image) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)

## <a name="analyze-an-image"></a>Een afbeelding analyseren

Met de volgende code wordt een methode, `AnalyzeLocalImage`, gedefinieerd waarmee via het clientobject een lokale afbeelding wordt geanalyseerd en de resultaten worden afgedrukt. Met de methode wordt een tekstbeschrijving, categorisatie, een lijst met tags, gedetecteerde gezichten, markeringen vanwege inhoud voor volwassenen, hoofdkleuren en afbeeldingstype geretourneerd.

> [!TIP]
> U kunt ook een externe afbeelding analyseren met behulp van de bijbehorende URL. Zie de [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision)-methoden, bijvoorbeeld **AnalyzeImage**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) voor scenario's met betrekking tot externe afbeeldingen.

### <a name="set-up-test-image"></a>Testafbeelding instellen

Maak eerst een map **Resources/** in de map **src/main/** van uw project en voeg een afbeelding toe die u wilt analyseren. Voeg vervolgens de volgende methodedefinitie toe aan uw **ComputerVisionQuickstarts**-klasse. Wijzig de waarde van `pathToLocalImage`, zodat deze overeenkomt met het afbeeldingsbestand. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

### <a name="specify-visual-features"></a>Visuele kenmerken opgeven

Geef vervolgens op welke visuele kenmerken u wilt extraheren in uw analyse. Zie de opsomming [VisualFeatureTypes](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes) voor een volledige lijst.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>Analyseren
Met dit blok worden gedetailleerde resultaten voor elke omvang van de afbeeldings analyse in de console afgedrukt. Met de **analyzeImageInStream**-methode wordt een **ImageAnalysis**-object geretourneerd dat alle geëxtraheerde informatie bevat.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

In de volgende secties ziet u hoe u deze gegevens uitgebreid kunt parseren.

### <a name="get-image-description"></a>Beschrijving van afbeelding ophalen

Met de volgende code wordt de lijst met gegenereerde bijschriften voor de afbeelding opgehaald. Zie [Afbeeldingen beschrijven](../../concept-describing-images.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>Categorie van de afbeelding ophalen

Met de volgende code wordt de gedetecteerde categorie van de afbeelding opgehaald. Zie [Afbeeldingen categoriseren](../../concept-categorizing-images.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>Afbeeldingstags ophalen

Met de volgende code wordt de set met gedetecteerde tags in de afbeelding opgehaald. Zie [Inhoudstags](../../concept-tagging-images.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>Gezichten detecteren

Met de volgende code worden de gedetecteerde gezichten in de afbeelding met hun rechthoekcoördinaten als resultaat gegeven en worden gezichtskenmerken geselecteerd. Raadpleeg [Gezichtsdetectie](../../concept-detecting-faces.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-objects"></a>Objecten detecteren

Met de volgende code worden de gedetecteerde objecten in de afbeelding met hun coördinaten geretourneerd. Raadpleeg [Objectdetectie](../../concept-object-detection.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_objects)]


### <a name="detect-brands"></a>Merken detecteren

Met de volgende code worden de gedetecteerde merk logo's in de afbeelding met hun coördinaten geretourneerd. Zie voor meer informatie [merk detectie](../../concept-brand-detection.md).

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_brands)]



### <a name="detect-adult-racy-or-gory-content"></a>Inhoud voor volwassenen, of ongepaste of bloederige inhoud detecteren

Met de volgende code wordt de gedetecteerde aanwezigheid van inhoud voor volwassenen afgedrukt in de afbeelding. Zie [Inhoud voor volwassenen, racistische of ongepaste inhoud](../../concept-detecting-adult-content.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>Kleurenschema van de afbeelding ophalen

Met de volgende code worden de gedetecteerde kleurkenmerken in de afbeelding afgedrukt, zoals de dominante kleuren en accentkleur. Zie [Kleurenschema's](../../concept-detecting-color-schemes.md) voor meer informatie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>Domeinspecifieke inhoud ophalen

Computer Vision kan een speciaal model gebruiken om verdere analyse van afbeeldingen uit te voeren. Zie [Domeinspecifieke inhoud](../../concept-detecting-domain-content.md) voor meer informatie. 

Met de volgende code worden gegevens over gedetecteerde beroemdheden in de afbeelding geparseerd.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

Met de volgende code worden gegevens over gedetecteerde bezienswaardigheden in de afbeelding geparseerd.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>Het afbeeldingstype ophalen

Met de volgende code wordt informatie over het type afbeelding afgedrukt&mdash;, of het een illustratie of lijntekening is.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

> [!div class="nextstepaction"]
> [Ik heb een afbeelding geanalyseerd](?success=analyze-image#read-printed-and-handwritten-text) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=analyze-image)

### <a name="close-out-the-method"></a>De methode sluiten

Voltooi het blok try/catch en sluit de methode.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyze_catch)]


## <a name="read-printed-and-handwritten-text"></a>Afgedrukte en handgeschreven tekst lezen

Computer Vision kan zichtbare tekst in een afbeelding lezen en deze converteren naar een tekenstroom. In deze sectie wordt een methode gedefinieerd, `ReadFromFile`, die een lokaal bestandspad gebruikt en de tekst van de afbeelding naar de console verzendt.

> [!TIP]
> U kunt ook tekst lezen in een externe afbeelding waarnaar door een URL wordt verwezen. Zie de [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision)-methoden, bijvoorbeeld **read**. Of bekijk de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) voor scenario's met betrekking tot externe afbeeldingen.

### <a name="set-up-test-image"></a>Testafbeelding instellen

Maak een map **resources/** in de map **src/main/** van uw project en voeg een afbeelding toe waarvan u de tekst wilt lezen. U kunt een [voorbeeldafbeelding](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) downloaden en deze hier gebruiken.

Voeg vervolgens de volgende methodedefinitie toe aan uw **ComputerVisionQuickstarts**-klasse. Wijzig de waarde van `localFilePath`, zodat deze overeenkomt met het afbeeldingsbestand. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>De Read-API aanroepen

Voeg vervolgens de volgende code toe om de methode **readInStreamWithServiceResponseAsync** voor de gegeven afbeelding aan te roepen.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

Met het volgende codeblok wordt de bewerkings-ID opgehaald uit het antwoord van de aanroep Lezen. Deze ID wordt in combinatie met een helper-methode gebruikt om de leesresultaten naar de console te verzenden. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

Sluit het try/catch-blok en de methodedefinitie.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>Leesresultaten ophalen

Voeg vervolgens een definitie voor de help-methode toe. Deze methode gebruikt de bewerkings-ID uit de vorige stap om een query uit te voeren op de Leesbewerking en om OCR-resultaten op te halen wanneer deze beschikbaar zijn.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

De rest van de methode parseert de OCR-resultaten en verzendt deze naar de console.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Voeg tenslotte de andere gebruikte help-methode toe, waarmee de bewerkings-ID uit het eerste antwoord wordt geëxtraheerd.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [Ik heb tekst gelezen](?success=read-printed-handwritten-text#run-the-application) [Er is een fout opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app maken met:

```console
gradle build
```

De toepassing uitvoeren met de opdracht `gradle run`:

```console
gradle run
```

> [!div class="nextstepaction"]
> [Ik heb de toepassing uitgevoerd](?success=run-the-application#clean-up-resources) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application)

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Ik heb resources opgeschoond](?success=clean-up-resources#next-steps) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Computer Vision Java-bibliotheek kunt gebruiken om basistaken uit te voeren. Bestudeer daarna het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
>[Referentie voor de Computer Vision (Java)](/java/api/overview/azure/cognitiveservices/client/computervision)


* [Wat is Computer Vision?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
