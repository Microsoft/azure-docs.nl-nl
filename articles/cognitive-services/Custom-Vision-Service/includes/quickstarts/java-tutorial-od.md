---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 5b8d0e677fc623a5fd1e8ba755db62931a8f3495
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948354"
---
Ga aan de slag met de Custom Vision-clientbibliotheek voor Java om een objectdetectiemodel te bouwen. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. Gebruik dit voorbeeld als een sjabloon om uw eigen beeldherkennings-app te maken.

> [!NOTE]
> Als u een objectdetectiemodel wilt bouwen en trainen _zonder_ code te schrijven, raadpleegt u de [handleiding voor browsers](../../get-started-build-detector.md).

Gebruik de Custom Vision-clientbibliotheek voor Java voor het volgende:

* Een nieuw project maken in de Custom Vision-service
* Label aan het project toevoegen
* Afbeeldingen uploaden en labelen
* Het project trainen
* De huidige herhaling publiceren
* Voorspellingseindpunt testen

[Referentiedocumentatie](/java/api/overview/azure/cognitiveservices/client/customvision) | Bibliotheekbroncode [(training)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(voorspelling)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction) | Artefact (Maven) [(training)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(voorspelling)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar) | 
[Voorbeelden](/samples/browse/?products=azure&terms=custom%20vision)


## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van de [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Het [hulpprogramma Gradle](https://gradle.org/install/) of een andere afhankelijkheidsbeheerder.
* Zodra u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="een Custom Vision-resource maken"  target="_blank"> maakt u een Custom Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om een trainings- en voorspellingsresource te maken en uw sleutels en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met Custom Vision. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
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

Zoek *build.gradle.kts* en open het met uw favoriete IDE of teksteditor. Kopieer het vervolgens in de volgende buildconfiguratie. Deze configuratie definieert het project als een Java-toepassing waarvan het toegangspunt de klasse **CustomVisionQuickstart** is. De Custom Vision-bibliotheken worden geïmporteerd.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>Een Java-bestand maken


Voer de volgende opdracht uit vanuit uw werkmap om een projectbronmap te maken:

```console
mkdir -p src/main/java
```

Ga naar de nieuwe map en maak een bestand met de naam *CustomVisionQuickstart.java*. Open het bestand in uw voorkeurseditor of IDE en voeg de volgende `import`-instructies toe:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java), waar de codevoorbeelden uit deze quickstart zich bevinden.


Maak in de klasse **CustomVisionQuickstart** van de toepassing variabelen voor de sleutel en het eindpunt van uw resource.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Ga naar Azure Portal. Als de Custom Vision-resources die u in de sectie **Vereisten** hebt gemaakt, zijn geïmplementeerd, klikt u onder **Volgende stappen** op de knop **Ga naar resource**. U vindt de sleutels en het eindpunt op de pagina's over **sleutel en eindpunt** van de resources, onder **Resourcebeheer**. U moet uw trainings- en voorspellingssleutel samen met het eindpunt van de trainingsresources ophalen.
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

Voeg in de **hoofdmethode** van de toepassing aanroepen toe voor de methoden die in deze quickstart worden gebruikt. U definieert deze later.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls_od)]

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Custom Vision Java-clientbibliotheek.

|Naam|Beschrijving|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Deze klasse behandelt het maken, trainen en publiceren van uw modellen. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Deze klasse verwerkt de query op uw modellen voor objectdetectievoorspellingen.|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Deze klasse definieert een voorspelling van één object op één afbeelding. Het bevat eigenschappen voor de object-id en -naam, de locatie van het begrenzingsvak van het object en een betrouwbaarheidsscore.|

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Custom Vision-clientbibliotheek voor Java:

* [De client verifiëren](#authenticate-the-client)
* [Een nieuw project maken in de Custom Vision-service](#create-a-new-custom-vision-project)
* [Label aan het project toevoegen](#add-tags-to-the-project)
* [Afbeeldingen uploaden en labelen](#upload-and-tag-images)
* [Het project trainen](#train-the-project)
* [De huidige herhaling publiceren](#publish-the-current-iteration)
* [Voorspellingseindpunt testen](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>De client verifiëren

In uw **hoofd** methode instantieert u trainings- en voorspellingsclients met behulp van uw eindpunt en sleutels.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Een nieuw project maken in de Custom Vision-service

Met behulp van de volgende methode maakt u een objectdetectieproject. Het project wordt weergegeven op de [Custom Vision-website](https://customvision.ai/), die u eerder hebt bezocht. Raadpleeg de overloads van de methode [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) om andere opties op te geven wanneer u uw project maakt (uitgelegd in de webportalgids [Een detector maken](../../get-started-build-detector.md)).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>Tags toevoegen aan uw project

Met deze methode worden de tags gedefinieerd waarmee u het model gaat trainen.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Download eerst de voorbeeldafbeeldingen voor dit project. Sla de inhoud van de [map Voorbeeldafbeeldingen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) op uw lokale apparaat op.

> [!NOTE]
> Met Trove, een Microsoft Garage-project, kunt u sets afbeeldingen verzamelen en aanschaffen voor trainingsdoeleinden. Wanneer u uw afbeeldingen hebt verzameld kunt u ze downloaden en importeren naar uw Custom Vision-project. Ga naar de pagina [Trove](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3) voor meer informatie.

Als u afbeeldingen labelt in objectdetectieprojecten, dient u de regio van elk gelabeld object op te geven met behulp van genormaliseerde coördinaten. Met de volgende code wordt elk voorbeeld van een afbeelding aan de bijbehorende gelabelde regio gekoppeld.

> [!NOTE]
> Als u geen hulpprogramma voor klikken en slepen hebt om de coördinaten van regio's te markeren, kunt u de Web-UI op [Customvision.ai](https://www.customvision.ai/) gebruiken. In dit voorbeeld zijn de coördinaten al opgenomen.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

Met het volgende codeblok worden de afbeeldingen toegevoegd aan het project. U moet de argumenten van de `GetImage`-aanroepen wijzigen zodat deze verwijzen naar de locaties van de mappen **fork** en **scissors** die u hebt gedownload.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Het vorige codefragment maakt gebruik van twee hulpfuncties die de afbeeldingen als resourcestreams ophalen en ze naar de service uploaden (u kunt maximaal 64 afbeeldingen tegelijk uploaden). Definieer deze methoden. 

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>Het project trainen

Met deze methode wordt de eerste trainingsiteratie in het project gemaakt. Er wordt een query uitgevoerd op de service totdat de training is voltooid.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]


## <a name="publish-the-current-iteration"></a>De huidige herhaling publiceren

Met deze methode wordt de huidige iteratie van het model beschikbaar voor het uitvoeren van query's. U kunt de naam van het model gebruiken als referentie voor het verzenden van voorspellingsaanvragen. U moet uw eigen waarde invoeren voor `predictionResourceId`. U kunt de resource-id van de voorspelling vinden op het tabblad **Overzicht** van de resource in de Azure-portal, vermeld als **Abonnements-id**.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publishOD)]

## <a name="test-the-prediction-endpoint"></a>Voorspellingseindpunt testen

In deze methode wordt de testafbeelding geladen, wordt een query op het eindpunt van het model uitgevoerd en worden de gegevens van de voorspelling op de console weergegeven.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict_od)]

## <a name="run-the-application"></a>De toepassing uitvoeren

U kunt de app maken met:

```console
gradle build
```

De toepassing uitvoeren met de opdracht `gradle run`:

```console
gradle run
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu elke stap van het objectdetectieproces in code uitgevoerd. Met dit voorbeeld wordt één trainingsiteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken. In de volgende handleiding wordt classificatie van afbeeldingen behandeld. De principes zijn soortgelijk aan die van objectdetectie.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)

* Wat is Custom Vision?
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
