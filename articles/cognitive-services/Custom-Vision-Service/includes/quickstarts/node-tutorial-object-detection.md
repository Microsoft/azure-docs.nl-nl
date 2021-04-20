---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: de8ca0a9410479b4d166a47e5c56742955b7853f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725163"
---
Deze handleiding biedt informatie en voorbeeldcode om u op weg te helpen met de Custom Vision-clientbibliotheek voor Node.js om een objectdetectiemodel te maken. U maakt een project, voegt tags toe, traint het project en gebruikt de voorspellingseindpunt-URL van het project om het programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen beeldherkennings-app te maken.

> [!NOTE]
> Als u een objectdetectiemodel wilt bouwen en trainen _zonder_ code te schrijven, raadpleegt u de [handleiding voor browsers](../../get-started-build-detector.md).

Gebruik de Custom Vision-clientbibliotheek voor .NET voor het volgende:

* Een nieuw project maken in de Custom Vision-service
* Label aan het project toevoegen
* Afbeeldingen uploaden en labelen
* Het project trainen
* De huidige herhaling publiceren
* Voorspellingseindpunt testen

Referentiedocumentatie [(training)](/javascript/api/@azure/cognitiveservices-customvision-training/) [(voorspelling)](/javascript/api/@azure/cognitiveservices-customvision-prediction/) | Bibliotheekbroncode [(training)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(voorspelling)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | Pakket (npm) [(training)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(voorspelling)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction) | [Voorbeelden](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van [Node.js](https://nodejs.org/)
* Zodra u uw Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="een Custom Vision-resource maken"  target="_blank"> maakt u een Custom Vision-resource </a> in de Azure-portal om een trainings- en voorspellingsresource te maken en uw sleutels en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
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

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak variabelen voor het Azure-eindpunt en de Azure-sleutels voor uw resource. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Custom Vision-resources die u in de sectie **Vereisten** hebt gemaakt, zijn geïmplementeerd, klikt u onder **Volgende stappen** op de knop **Ga naar resource**. U vindt de sleutels en het eindpunt op de pagina's over **sleutel en eindpunt** van de resources, onder **Resourcebeheer**. U moet de sleutels voor zowel uw trainings- als voorspellingsresources ophalen, samen met het API-eindpunt voor uw trainingsresource.
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel over [Cognitive Services-beveiliging voor meer](../../../cognitive-services-security.md) informatie.

Voeg ook velden toe voor de projectnaam en een time-outparameter voor asynchrone aanroepen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>Objectmodel

|Naam|Beschrijving|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) | Deze klasse behandelt het maken, trainen en publiceren van uw modellen. |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)| Deze klasse verwerkt de query op uw modellen voor objectdetectievoorspellingen.|
|[Voorspelling](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction)| Deze interface definieert één voorspelling van één afbeelding. De klasse bevat eigenschappen voor de id en de naam van het object en een betrouwbaarheidsscore.|

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

Instantieer clientobjecten met uw eindpunt en sleutel. Maak een **ApiKeyCredentials**-object met uw sleutel en gebruik het met uw eindpunt om een [TrainingAPIClient-](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient) en [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient)-object te maken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>Hulpfuncties toevoegen

Voeg de volgende functie toe om meerdere asynchrone aanroepen te maken. U kunt dit later gebruiken.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Een nieuw project maken in de Custom Vision-service

Start een nieuwe functie die al uw Custom Vision-functieaanroepen omvat. Als u een nieuw Custom Vision Service-project wilt maken, voegt u de volgende code toe.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Download eerst de voorbeeldafbeeldingen voor dit project. Sla de inhoud van de [map Voorbeeldafbeeldingen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) op uw lokale apparaat op.

> [!NOTE]
> Hebt u een bredere set afbeeldingen nodig om uw training te voltooien? Met Trove, een Microsoft Garage-project, kunt u sets afbeeldingen verzamelen en aanschaffen voor trainingsdoeleinden. Wanneer u uw afbeeldingen hebt verzameld kunt u ze downloaden en importeren naar uw Custom Vision-project. Ga naar de pagina [Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) voor meer informatie.

Als u de voorbeeldafbeeldingen aan het project wilt toevoegen, voegt u de volgende code in nadat u de tag hebt gemaakt. Met deze code wordt elke afbeelding met de bijbehorende tag geüpload. Als u afbeeldingen labelt in objectdetectieprojecten, dient u de regio van elk gelabeld object op te geven met behulp van genormaliseerde coördinaten. In deze zelfstudie zijn de regio's inline vastgelegd in de code zelf. Door de regio's wordt het begrenzingsvak opgegeven in genormaliseerde coördinaten. De coördinaten worden gegeven in de volgorde links, boven, breedte, hoogte. U kunt maximaal 64 afbeeldingen uploaden in één batch.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> U moet het pad naar de afbeeldingen (`sampleDataRoot`) wijzigen, gebaseerd op waar u de opslagplaats Cognitive Services Python-SDK-voorbeelden hebt gedownload.

> [!NOTE]
> Als u geen hulpprogramma voor klikken en slepen hebt om de coördinaten van regio's te markeren, kunt u de Web-UI op [Customvision.ai](https://www.customvision.ai/) gebruiken. In dit voorbeeld zijn de coördinaten al opgenomen.


## <a name="train-the-project"></a>Het project trainen

Met deze code wordt de eerste iteratie van het voorspellingsmodel gemaakt. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>De huidige herhaling publiceren

Met deze code wordt de getrainde iteratie gepubliceerd naar het voorspellingseindpunt. De naam die is opgegeven voor de gepubliceerde iteratie, kan worden gebruikt voor het verzenden van voorspellingsaanvragen. Er is pas na publicatie een iteratie beschikbaar in het voorspellingseindpunt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Voorspellingseindpunt testen

Als u een afbeelding naar het voorspellingseindpunt wilt verzenden en de voorspelling wilt ophalen, voegt u de volgende code toe aan uw functie. 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

Sluit vervolgens uw Custom Vision-functie en roep deze aan.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```shell
node index.js
```

De uitvoer van de toepassing moet in de console worden weergegeven. Vervolgens kunt u controleren of de testafbeelding (gevonden in **<sampleDataRoot>/Test/** ) op de juiste wijze wordt gelabeld en of de detectieregio juist is. U kunt altijd teruggaan naar de [Custom Vision-website](https://customvision.ai) en de huidige status bekijken van het nieuwe project dat u hebt gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Volgende stappen

U hebt nu elke stap van het objectdetectieproces in code uitgevoerd. Met dit voorbeeld wordt één trainingsiteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken. In de volgende handleiding wordt classificatie van afbeeldingen behandeld. De principes zijn soortgelijk aan die van objectdetectie.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)

* Wat is Custom Vision?
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)
* [SDK-referentiedocumentatie (training)](/javascript/api/@azure/cognitiveservices-customvision-training/)
* [SDK-referentiedocumentatie (voorspelling)](/javascript/api/@azure/cognitiveservices-customvision-prediction/)
