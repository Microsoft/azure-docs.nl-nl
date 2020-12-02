---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, cog-serv-seo-aug-2020
ms.openlocfilehash: f5c7508cdb6151b61263fd8daf533906ba9c42a1
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303652"
---
Gebruik de LUIS-clienbibliotheken (Language Understanding) voor Python om:

* Een app maken
* Voeg een intentie toe, een entiteit die via machine learning verkregen is, met een voorbeelduiting
* App trainen en publiceren
* Runtime queryvoorspelling

[Referentiedocumentatie](/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [Creatie](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/authoring) en [Voorspelling](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis/runtime)  Broncode van bibliotheek | [Pakket (Pypi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/sdk-3x/authoring_and_predict.py)

## <a name="prerequisites"></a>Vereisten

* De huidige versie van [Python 3.x](https://www.python.org/).
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Zodra u een Azure-abonnement hebt, [maakt u een Language Understanding-creatieresource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u [maakt](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) om de toepassing te verbinden met Language Understanding-creatie. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code. U kunt de gratis prijscategorie (`F0`) gebruiken om de service te proberen.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

1. Maak in een consolevenster een nieuwe map voor uw toepassing en ga naar die map.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Maak een bestand met de naam `authoring_and_predict.py` voor uw Python-code.

    ```console
    touch authoring_and_predict.py
    ```

### <a name="install-the-client-library-with-pip"></a>De clientbibliotheek installeren met Pip

Installeer in de toepassingsmap de creatie-clientbibliotheek van Language Understanding (LUIS) voor Python met de volgende opdracht:

```console
pip install azure-cognitiveservices-language-luis
```

## <a name="authoring-object-model"></a>Objectmodel Creatie

De creatieclient van Language Understanding (LUIS) is een [LUISAuthoringClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python)-object dat wordt geverifieerd bij Azure, dat de creatiesleutel bevat.

## <a name="code-examples-for-authoring"></a>Codevoorbeelden voor creatie

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Apps: [maken](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-), [verwijderen](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#delete-app-id--force-false--custom-headers-none--raw-false----operation-config-), [publiceren](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-)
* Voorbeelden van uitingen: [toevoegen per batch](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#batch-app-id--version-id--example-label-object-array--custom-headers-none--raw-false----operation-config-), [verwijderen per id](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python#delete-app-id--version-id--example-id--custom-headers-none--raw-false----operation-config-)
* Kenmerken: [lijsten met woordgroepen](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.featuresoperations?view=azure-python#add-phrase-list-app-id--version-id--phraselist-create-object--custom-headers-none--raw-false----operation-config-) beheren
* Model: [intenties](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) en entiteiten beheren
* Patroon: [patronen](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.patternoperations?view=azure-python#add-pattern-app-id--version-id--pattern-none--intent-none--custom-headers-none--raw-false----operation-config-) beheren
* Trainen: de app [trainen](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) en de [trainingsstatus](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) uitvragen
* [Versies](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.versionsoperations?view=azure-python): beheren door middel van klonen, exporteren en verwijderen


## <a name="prediction-object-model"></a>Objectmodel Voorspelling

De voorspellingsruntime-client van Language Understanding (LUIS) is een [LUISRuntimeClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python)-object dat wordt geverifieerd bij Azure, dat de bronsleutel bevat.

## <a name="code-examples-for-prediction-runtime"></a>Codevoorbeelden voor voorspellingsruntime

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Voorspelling per [fasering of productiesleuf](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Voorspelling per [versie](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Voeg de clientbibliotheken toe aan het Python-bestand.

[!code-python[Add python libraries to code file](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=Dependencies)]


## <a name="add-boilerplate-code"></a>Standaardcode toevoegen

1. Voeg de `quickstart`-methode en de aanroep toe. Deze methode bevat de meeste resterende code. Deze methode wordt op het einde van het bestand aangeroepen.

    ```python
    def quickstart():

        # add calls here, remember to indent properly

    quickstart()
    ```

1. Voeg de resterende code toe in de quickstart-methode tenzij die anders opgegeven.

## <a name="create-variables-for-the-app"></a>Variabelen maken voor de app

Maak twee sets variabelen: de eerste set wijzigt u, de tweede set laat u zoals wordt weergegeven in het codevoorbeeld. 

1. Maak variabelen om uw ontwerpsleutel en resourcenamen op te slaan.

    [!code-python[Variables you need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouChange)]

1. Maak variabelen om uw eindpunten, naam van de app, versie en naam van de intenties op te slaan.

    [!code-python[Variables you don't need to change](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een [CognitiveServicesCredentials](/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python)-object met uw sleutel en gebruik het met uw eindpunt om een [LUISAuthoringClient](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-python)-object te maken.

[!code-python[Authenticate the client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

Een LUIS-app bevat de intenties, entiteiten en voorbeelduitingen van het model voor natuurlijke taalverwerking.

Maak een [add](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#add-application-create-object--custom-headers-none--raw-false----operation-config-)-methode van het [AppsOperation](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python)-object om de app te maken. De naam en taalcultuur zijn vereiste eigenschappen.

[!code-python[Create a LUIS app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Intentie voor de app maken
Het primaire object in het model van een LUIS-app is de intentie. De intentie wordt in overeenstemming gebracht met een groepering _intenties_ van uitingen van gebruikers. Een gebruiker kan een vraag stellen of een instructie maken op zoek naar een bepaalde _beoogde_ respons van een bot (of een andere clienttoepassing). Voorbeelden van intenties zijn het reserveren van een vlucht, vragen naar het weer op de plaats van bestemming en vragen naar de contactgegevens van de klantenservice.

Gebruik de methode [model. add_intent](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-intent-app-id--version-id--name-none--custom-headers-none--raw-false----operation-config-) met de naam van de unieke intentie, en voer de app-id, de versie-id en de nieuwe naam van de intentie door.

De waarde `intentName` wordt in code vastgelegd in `OrderPizzaIntent` als onderdeel van de variabelen in de sectie [Variabelen maken voor de app](#create-variables-for-the-app).

[!code-python[Create intent for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps aangetroffen. De entiteit extraheert informatie uit de uiting van de gebruiker, wat nodig is om aan de bedoeling van de gebruiker tegemoet te komen. Er zijn verschillende soorten [vooraf ontwikkelde](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python#add-prebuilt-app-id--version-id--prebuilt-extractor-names--custom-headers-none--raw-false----operation-config-) en aangepaste entiteiten, elk met hun eigen gegevenstransformatieobject.  Veelvoorkomende vooraf ontwikkelde entiteiten die u aan uw app kunt toevoegen, zijn [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) en [ordinal](../luis-reference-prebuilt-ordinal.md).

Het is belangrijk te weten dat entiteiten niet met een intentie zijn gemarkeerd. Ze kunnen op veel intenties worden toegepast en dit gebeurt ook vaak. Alleen voorbeelden van uitingen van gebruikers zijn gemarkeerd voor een specifieke, enkelvoudige intentie.

Methoden voor het maken van entiteiten maken deel uit van de klasse [ModelOperations](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.modeloperations?view=azure-python). Elk entiteitstype heeft een eigen gegevenstransformatieobjectmodel.

De code voor het maken van een entiteit maakt een machine learning-entiteit met subentiteiten en functies toegepast op de `Quantity`-subentiteiten.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Gedeeltelijke schermopname van de portal met de gemaakte entiteit, een machine learning-entiteit met subentiteiten en functies toegepast op de 'Quantity'-subentiteiten.":::

[!code-python[Create entities for the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddEntities)]

Plaats de volgende methode boven de `quickstart`-methode om het id van de 'Quantity'-subentiteit te vinden en de functies toe te wijzen aan die subentiteit.

[!code-python[Find subentity id](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Voorbeeld van uiting toevoegen aan intentie

De app heeft voorbeelden van uitingen nodig om de intentie van een uiting te kunnen vaststellen en entiteiten te kunnen ophalen. De voorbeelden moeten gericht zijn op een specifieke, afzonderlijke intentie. Alle aangepaste entiteiten moeten zijn gemarkeerd. Vooraf gemaakte entiteiten hoeven niet te worden gemarkeerd.

Voeg voorbeelduitingen toe door een lijst met [ExampleLabelObject](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-python)-objecten te maken, één object voor elke voorbeelduiting. Elk voorbeeld moet alle entiteiten markeren met een woordenlijst met naam-/waardeparen met de naam en de waarde van de entiteit. De waarde van de entiteit moet exact zo zijn als deze wordt weergegeven in de tekst van de voorbeelduiting.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Gedeeltelijke schermopname van de gelabelde voorbeelduiting in de portal. ":::

Roep [examples.add](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.examplesoperations?view=azure-python) aan met de app-id, versie-id en het voorbeeld.

[!code-python[Add example utterance to intent](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container](../luis-container-howto.md) of [worden gepubliceerd](../luis-how-to-publish-app.md) naar de fasering of productsleuven.

Voor de methode [train.train_version](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#train-version-app-id--version-id--custom-headers-none--raw-false----operation-config-) is de app-id en de versie-id vereist.

Een heel klein model, kan heel snel worden getraind, zoals deze quickstart laat zien. Voor toepassingen op productieniveau moet het trainen van de app een pollingaanroep naar de methode [get_status](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.trainoperations?view=azure-python#get-status-app-id--version-id--custom-headers-none--raw-false----operation-config-) omvatten om te bepalen of de training is geslaagd. De respons is een lijst met [ModelTrainingInfo](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-python)-objecten met een afzonderlijke status voor elk object. De training is pas voltooid als alle objecten zijn geslaagd.

[!code-python[Train the app](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>App publiceren naar productiesleuf

Publiceer de LUIS-app met behulp van de methode [app.publish](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.authoring.operations.appsoperations?view=azure-python#publish-app-id--version-id-none--is-staging-false--custom-headers-none--raw-false----operation-config-). Hiermee wordt de huidige getrainde versie gepubliceerd naar de opgegeven sleuf op het eindpunt. Uw clienttoepassing gebruikt dit eindpunt om uitingen van gebruikers te verzenden voor het voorspellen van intenties en het extraheren van entiteiten.

[!code-python[Publish app to production slot](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>De voorspellingsruntime-client verifiëren

Gebruik de referenties-object met uw sleutel en gebruik dit met uw eindpunt om een [LUISRuntimeClientConfiguration](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python)-object te maken.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-python[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Voorspelling uit runtime ophalen

Voeg de volgende code toe om de aanvraag aan de voorspellingsruntime te maken.

De uiting van de gebruiker maakt deel uit van het [prediction_request](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python)-object.

De **[get_slot_prediction](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** -methode heeft verschillende parameters nodig, zoals de app-id, de naam van de sleuf en het voorspellingsaanvraagobject, om aan de aanvraag te voldoen. De andere opties, zoals verbose, show all intents en log, zijn optioneel. De aanvraag retourneert een [PredictionResponse](/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python)-object.

[!code-python[Get prediction from runtime](~/cognitive-services-quickstart-code/python/LUIS/sdk-3x/authoring_and_predict.py?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `python` in uw quickstart-bestand.

```console
python authoring_and_predict.py
```