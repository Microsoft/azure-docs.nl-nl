---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 07aff220e211578a6a4b18e8d88ff44a4cc39a86
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582587"
---
Dit artikel biedt informatie en voorbeeldcode om u op weg te helpen met de Custom Vision-clientbibliotheek voor Node.js om een afbeeldingsclassificatiemodel te maken. U maakt een project, voegt tags toe, traint het project en gebruikt de voorspellingseindpunt-URL van het project om het programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen beeldherkennings-app te maken.

> [!NOTE]
> Als u een classificatiemodel wilt bouwen en trainen _zonder_ code te schrijven, raadpleegt u de [handleiding voor browsers](../../getting-started-build-a-classifier.md).

Gebruik de Custom Vision-clientbibliotheek voor .NET voor het volgende:

* Een nieuw project maken in de Custom Vision-service
* Label aan het project toevoegen
* Afbeeldingen uploaden en labelen
* Het project trainen
* De huidige herhaling publiceren
* Voorspellingseindpunt testen

Referentiedocumentatie [(training)](/javascript/api/@azure/cognitiveservices-customvision-training/?view=azure-node-latest) [(voorspelling)](/javascript/api/@azure/cognitiveservices-customvision-prediction/?view=azure-node-latest) | Bibliotheekbroncode [(training)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(voorspelling)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Pakket (npm) [(training)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(voorspelling)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction) | [Voorbeelden](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van [Node.js](https://nodejs.org/)
* Zodra u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="een Custom Vision-resource maken"  target="_blank"> maakt u een Custom Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om een trainings- en voorspellingsresource te maken en uw sleutels en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met Custom Vision. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init` uit om een knooppunttoepassing te maken met een `package.json`-bestand. 

```console
npm init
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Als u een beeldanalyse-app wilt schrijven met Custom Vision voor Node.js, hebt u de Custom Vision NPM-pakketten nodig. Voer de volgende opdracht uit in PowerShell om ze te installeren:

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

Maak een bestand met de naam `index.js` en importeer de volgende bibliotheken:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_imports)]


> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak variabelen voor het Azure-eindpunt en de Azure-sleutels voor uw resource. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Custom Vision Training-resources die u in de sectie **Vereisten** hebt gemaakt, zijn geïmplementeerd, klikt u onder **Volgende stappen** op de knop **Ga naar resource**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

Voeg ook velden toe voor de projectnaam en een time-outparameter voor asynchrone aanroepen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_vars)]


## <a name="object-model"></a>Objectmodel

|Naam|Beschrijving|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient?view=azure-node-latest) | Deze klasse behandelt het maken, trainen en publiceren van uw modellen. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient?view=azure-node-latest)| Deze klasse verwerkt de query op uw modellen voor voorspellingen met betrekking tot de classificatie van afbeeldingen.|
|[Voorspelling](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction?view=azure-node-latest)| Deze interface definieert één voorspelling van één afbeelding. De klasse bevat eigenschappen voor de id en de naam van het object en een betrouwbaarheidsscore.|

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Custom Vision-clientbibliotheek voor JavaScript:

* [De client verifiëren](#authenticate-the-client)
* [Een nieuw project maken in de Custom Vision-service](#create-a-new-custom-vision-project)
* [Label aan het project toevoegen](#add-tags-to-the-project)
* [Afbeeldingen uploaden en labelen](#upload-and-tag-images)
* [Het project trainen](#train-the-project)
* [De huidige herhaling publiceren](#publish-the-current-iteration)
* [Voorspellingseindpunt testen](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer clientobjecten met uw eindpunt en sleutel. Maak een **ApiKeyCredentials**-object met uw sleutel en gebruik het met uw eindpunt om een [TrainingAPIClient-](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient?view=azure-node-latest) en [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient?view=azure-node-latest)-object te maken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_auth)]


## <a name="create-a-new-custom-vision-project"></a>Een nieuw project maken in de Custom Vision-service

Start een nieuwe functie die al uw Custom Vision-functieaanroepen omvat. Als u een nieuw Custom Vision Service-project wilt maken, voegt u de volgende code toe.


[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Label aan het project toevoegen

Voeg de volgende code toe aan uw functie om classificatielabels voor uw project te maken:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Download eerst de voorbeeldafbeeldingen voor dit project. Sla de inhoud van de [map Voorbeeldafbeeldingen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) op uw lokale apparaat op.

Als u de voorbeeldafbeeldingen aan het project wilt toevoegen, voegt u de volgende code in nadat u de tag hebt gemaakt. Met deze code wordt elke afbeelding met de bijbehorende tag geüpload.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_upload)]

> [!IMPORTANT]
> U moet het pad naar de afbeeldingen (`sampleDataRoot`) wijzigen, gebaseerd op waar u de opslagplaats Cognitive Services Python-SDK-voorbeelden hebt gedownload.

## <a name="train-the-project"></a>Het project trainen

Met deze code wordt de eerste iteratie van het voorspellingsmodel gemaakt. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_train)]

## <a name="publish-the-current-iteration"></a>De huidige herhaling publiceren

Met deze code wordt de getrainde iteratie gepubliceerd naar het voorspellingseindpunt. De naam die is opgegeven voor de gepubliceerde iteratie, kan worden gebruikt voor het verzenden van voorspellingsaanvragen. Er is pas na publicatie een iteratie beschikbaar in het voorspellingseindpunt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Voorspellingseindpunt testen

Als u een afbeelding naar het voorspellingseindpunt wilt verzenden en de voorspelling wilt ophalen, voegt u de volgende code toe aan uw functie. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_test)]

Sluit vervolgens uw Custom Vision-functie en roep deze aan.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js?name=snippet_function_close)]


## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```

Als het goed is, is de uitvoer van de toepassing vergelijkbaar met de volgende tekst:

```console
Creating project...
Adding images...
Training...
Training started...
Training status: Training
Training status: Training
Training status: Training
Training status: Completed
Results:
         Hemlock: 94.97%
         Japanese Cherry: 0.01%
```

U kunt vervolgens controleren of de testafbeelding (in **<sampleDataRoot>/Test/** ) correct is gelabeld. U kunt altijd teruggaan naar de [Custom Vision-website](https://customvision.ai) en de huidige status bekijken van het nieuwe project dat u hebt gemaakt.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu gezien hoe elke stap van het objectdetectieproces in code kan worden uitgevoerd. Met dit voorbeeld wordt één trainingsiteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)

* Wat is Custom Vision?
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ImageClassification/CustomVisionQuickstart.js)
* [SDK-referentiedocumentatie (training)](/javascript/api/@azure/cognitiveservices-customvision-training/?view=azure-node-latest)
* [SDK-referentiedocumentatie (voorspelling)](/javascript/api/@azure/cognitiveservices-customvision-prediction/?view=azure-node-latest)
