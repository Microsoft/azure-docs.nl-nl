---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-js, cog-serv-seo-aug-2020
ms.openlocfilehash: f4b9c84480940889b0278129952bcf2918d9c835
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947705"
---
Gebruik de LUIS-clienbibliotheken (Language Understanding) voor Node.js voor het volgende:

* Een app maken
* Voeg een intentie toe, een entiteit die via machine learning verkregen is, met een voorbeelduiting
* App trainen en publiceren
* Runtime queryvoorspelling

[Referentiedocumentatie](/javascript/api/@azure/cognitiveservices-luis-authoring/) |  [Creatie](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-authoring) en [Voorspelling](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) Broncode van bibliotheek | [Creatie](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring) en [Voorspelling](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) NPM | [Voorbeelden](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/sdk-3x/index.js)

## <a name="prerequisites"></a>Vereisten

* [Node.js](https://nodejs.org)
* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* Zodra u een Azure-abonnement hebt, [maakt u een Language Understanding-creatieresource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u [maakt](../luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) om de toepassing te verbinden met Language Understanding-creatie. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code. U kunt de gratis prijscategorie (`F0`) gebruiken om de service te proberen.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-javascript-application"></a>Een nieuwe JavaScript-toepassing maken

1. Maak in een consolevenster een nieuwe map voor uw toepassing en ga naar die map.

    ```console
    mkdir quickstart-sdk && cd quickstart-sdk
    ```

1. Initialiseer de map als een JavaScript-toepassing door een `package.json`-bestand te maken.

    ```console
    npm init -y
    ```

1. Maak een bestand met de naam `index.js` voor uw JavaScript-code.

    ```console
    touch index.js
    ```

### <a name="install-the-npm-libraries"></a>De NPM-bibliotheken installeren

Installeer de afhankelijkheden in de toepassingsmap met de volgende opdrachten, waarbij één regel tegelijk wordt uitgevoerd:

```console
npm install @azure/ms-rest-js
npm install @azure/cognitiveservices-luis-authoring
npm install @azure/cognitiveservices-luis-runtime
```

Uw `package.json` moet er als volgt uitzien:

```json
{
  "name": "quickstart-sdk",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/cognitiveservices-luis-authoring": "^4.0.0-preview.3",
    "@azure/cognitiveservices-luis-runtime": "^5.0.0",
    "@azure/ms-rest-js": "^2.0.8"
  }
}
```

## <a name="authoring-object-model"></a>Objectmodel Creatie

De creatieclient van Language Understanding (LUIS) is een [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient)-object dat wordt geverifieerd bij Azure, dat de creatiesleutel bevat.

## <a name="code-examples-for-authoring"></a>Codevoorbeelden voor creatie

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* Apps: [toevoegen](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#add-applicationcreateobject--msrest-requestoptionsbase-), [verwijderen](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#deletemethod-string--models-appsdeletemethodoptionalparams-), [publiceren](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#publish-string--applicationpublishobject--msrest-requestoptionsbase-)
* Voorbeelden van uitingen: [toevoegen per batch](/javascript/api/@azure/cognitiveservices-luis-authoring/examples#batch-string--string--examplelabelobject----msrest-requestoptionsbase-), [verwijderen per id](/javascript/api/@azure/cognitiveservices-luis-authoring/examples#deletemethod-string--string--number--msrest-requestoptionsbase-)
* Kenmerken: [lijsten met woordgroepen](/javascript/api/@azure/cognitiveservices-luis-authoring/features#addphraselist-string--string--phraselistcreateobject--msrest-requestoptionsbase-) beheren
* Model: [intenties](/javascript/api/@azure/cognitiveservices-luis-authoring/model#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) en entiteiten beheren
* Patroon: [patronen](/javascript/api/@azure/cognitiveservices-luis-authoring/pattern#addpattern-string--string--patternrulecreateobject--msrest-requestoptionsbase-) beheren
* Trainen: de app [trainen](/javascript/api/@azure/cognitiveservices-luis-authoring/train#trainversion-string--string--msrest-requestoptionsbase-) en de [trainingsstatus](/javascript/api/@azure/cognitiveservices-luis-authoring/train#getstatus-string--string--msrest-requestoptionsbase-) uitvragen
* [Versies](/javascript/api/@azure/cognitiveservices-luis-authoring/versions): beheren door middel van klonen, exporteren en verwijderen

## <a name="prediction-object-model"></a>Objectmodel Voorspelling

De creatieclient van Language Understanding (LUIS) is een [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient)-object dat wordt geverifieerd bij Azure, dat de creatiesleutel bevat.

## <a name="code-examples-for-prediction-runtime"></a>Codevoorbeelden voor voorspellingsruntime

Als de client is gemaakt, gebruikt u deze client voor toegang tot de functionaliteit, waaronder:

* [Voorspellings](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) per `staging` of `production`-sleuf
* [Voorspelling per versie](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>De afhankelijkheden toevoegen

Open het `index.js`-bestand in uw voorkeurseditor of IDE-naam en voeg de volgende afhankelijkheden toe.

[!code-javascript[Add NPM libraries to code file](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=Dependencies)]

## <a name="add-boilerplate-code"></a>Standaardcode toevoegen

1. Voeg de `quickstart`-methode en de aanroep toe. Deze methode bevat de meeste resterende code. Deze methode wordt op het einde van het bestand aangeroepen.

    ```javascript
    const quickstart = async () => {

        // add calls here


    }
    quickstart()
        .then(result => console.log("Done"))
        .catch(err => {
            console.log(`Error: ${err}`)
            })
    ```

1. Voeg de resterende code toe in de quickstart-methode tenzij die anders opgegeven.

## <a name="create-variables-for-the-app"></a>Variabelen maken voor de app

Maak twee sets variabelen: de eerste set wijzigt u, de tweede set laat u zoals wordt weergegeven in het codevoorbeeld. 

1. Maak variabelen om uw ontwerpsleutel en resourcenamen op te slaan.

    [!code-javascript[Variables you need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouChange)]

1. Maak variabelen om uw eindpunten, naam van de app, versie en naam van de intenties op te slaan.

    [!code-javascript[Variables you don't need to change](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een [CognitiveServicesCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials)-object met uw sleutel en gebruik het met uw eindpunt om een [LUISAuthoringClient](/javascript/api/@azure/cognitiveservices-luis-authoring/luisauthoringclient)-object te maken.

[!code-javascript[Authenticate the client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>Een LUIS-app maken

Een LUIS-app bevat de intenties, entiteiten en voorbeelduitingen van het model voor natuurlijke taalverwerking.

Maak een [add](/javascript/api/@azure/cognitiveservices-luis-authoring/apps)-methode van het [AppsOperation](/javascript/api/@azure/cognitiveservices-luis-authoring/apps)-object om de app te maken. De naam en taalcultuur zijn vereiste eigenschappen.

[!code-javascript[Create a LUIS app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringCreateApplication)]


## <a name="create-intent-for-the-app"></a>Intentie voor de app maken
Het primaire object in het model van een LUIS-app is de intentie. De intentie wordt in overeenstemming gebracht met een groepering _intenties_ van uitingen van gebruikers. Een gebruiker kan een vraag stellen of een instructie maken op zoek naar een bepaalde _beoogde_ respons van een bot (of een andere clienttoepassing). Voorbeelden van intenties zijn het reserveren van een vlucht, vragen naar het weer op de plaats van bestemming en vragen naar de contactgegevens van de klantenservice.

Gebruik de methode [model. add_intent](/javascript/api/@azure/cognitiveservices-luis-authoring/model#addintent-string--string--modelcreateobject--msrest-requestoptionsbase-) met de naam van de unieke intentie, en voer de app-id, de versie-id en de nieuwe naam van de intentie door.

De waarde `intentName` wordt in code vastgelegd in `OrderPizzaIntent` als onderdeel van de variabelen in de sectie [Variabelen maken voor de app](#create-variables-for-the-app).

[!code-javascript[Create intent for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>Entiteiten voor de app maken

Hoewel entiteiten niet vereist zijn, worden ze in de meeste apps aangetroffen. De entiteit extraheert informatie uit de uiting van de gebruiker, wat nodig is om aan de bedoeling van de gebruiker tegemoet te komen. Er zijn verschillende soorten [vooraf ontwikkelde](/javascript/api/@azure/cognitiveservices-luis-authoring/model#addcustomprebuiltentity-string--string--prebuiltdomainmodelcreateobject--msrest-requestoptionsbase-) en aangepaste entiteiten, elk met hun eigen gegevenstransformatieobject.  Veelvoorkomende vooraf ontwikkelde entiteiten die u aan uw app kunt toevoegen, zijn [number](../luis-reference-prebuilt-number.md), [datetimeV2](../luis-reference-prebuilt-datetimev2.md), [geographyV2](../luis-reference-prebuilt-geographyv2.md) en [ordinal](../luis-reference-prebuilt-ordinal.md).

Het is belangrijk te weten dat entiteiten niet met een intentie zijn gemarkeerd. Ze kunnen op veel intenties worden toegepast en dit gebeurt ook vaak. Alleen voorbeelden van uitingen van gebruikers zijn gemarkeerd voor een specifieke, enkelvoudige intentie.

Methoden voor het maken van entiteiten maken deel uit van de klasse [Model](/javascript/api/@azure/cognitiveservices-luis-authoring/model). Elk entiteitstype heeft een eigen gegevenstransformatieobjectmodel.

De code voor het maken van een entiteit maakt een machine learning-entiteit met subentiteiten en functies toegepast op de `Quantity`-subentiteiten.

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="Gedeeltelijke schermopname van de portal met de gemaakte entiteit, een machine learning-entiteit met subentiteiten en functies toegepast op de 'Quantity'-subentiteiten.":::

[!code-javascript[Create entities for the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddEntities)]

Plaats de volgende methode boven de `quickstart`-methode om het id van de 'Quantity'-subentiteit te vinden en de functies toe te wijzen aan die subentiteit.

[!code-javascript[Find subentity id](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>Voorbeeld van uiting toevoegen aan intentie

De app heeft voorbeelden van uitingen nodig om de intentie van een uiting te kunnen vaststellen en entiteiten te kunnen ophalen. De voorbeelden moeten gericht zijn op een specifieke, afzonderlijke intentie. Alle aangepaste entiteiten moeten zijn gemarkeerd. Vooraf gemaakte entiteiten hoeven niet te worden gemarkeerd.

Voeg voorbeelduitingen toe door een lijst met [ExampleLabelObject](/javascript/api/@azure/cognitiveservices-luis-authoring/examplelabelobject)-objecten te maken, één object voor elke voorbeelduiting. Elk voorbeeld moet alle entiteiten markeren met een woordenlijst met naam-/waardeparen met de naam en de waarde van de entiteit. De waarde van de entiteit moet exact zo zijn als deze wordt weergegeven in de tekst van de voorbeelduiting.

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="Gedeeltelijke schermopname van de gelabelde voorbeelduiting in de portal. ":::

Roep [examples.add](/javascript/api/@azure/cognitiveservices-luis-authoring/examples) aan met de app-id, versie-id en het voorbeeld.

[!code-javascript[Add example utterance to intent](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>De app trainen

Zodra het model is gemaakt, moet de LUIS-app worden getraind voor deze versie van het model. Een getraind model kan worden gebruikt in een [container](../luis-container-howto.md) of [worden gepubliceerd](../luis-how-to-publish-app.md) naar de fasering of productsleuven.

Voor de methode [train.trainVersion](/javascript/api/@azure/cognitiveservices-luis-authoring/train#trainversion-string--string--msrest-requestoptionsbase-) is de app-id en de versie-id vereist.

Een heel klein model, kan heel snel worden getraind, zoals deze quickstart laat zien. Voor toepassingen op productieniveau moet het trainen van de app een pollingaanroep naar de methode [get_status](/javascript/api/@azure/cognitiveservices-luis-authoring/train#getstatus-string--string--msrest-requestoptionsbase-) omvatten om te bepalen of de training is geslaagd. De respons is een lijst met [ModelTrainingInfo](/javascript/api/@azure/cognitiveservices-luis-authoring/modeltraininginfo)-objecten met een afzonderlijke status voor elk object. De training is pas voltooid als alle objecten zijn geslaagd.

[!code-javascript[Train the app](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>App publiceren naar productiesite

Publiceer de LUIS-app met behulp van de methode [app.publish](/javascript/api/@azure/cognitiveservices-luis-authoring/apps#publish-string--applicationpublishobject--msrest-requestoptionsbase-). Hiermee wordt de huidige getrainde versie gepubliceerd naar de opgegeven sleuf op het eindpunt. Uw clienttoepassing gebruikt dit eindpunt om uitingen van gebruikers te verzenden voor het voorspellen van intenties en het extraheren van entiteiten.

[!code-javascript[Publish app to production slot](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PublishVersion)]


## <a name="authenticate-the-prediction-runtime-client"></a>De voorspellingsruntime-client verifiëren

Gebruik een msRest.ApiKeyCredentials-object met uw sleutel en gebruik het met uw eindpunt om een [LUIS.LUISRuntimeClient](/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient)-object te maken.

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-javascript [Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=PredictionCreateClient)]

## <a name="get-prediction-from-runtime"></a>Voorspelling uit runtime ophalen

Voeg de volgende code toe om de aanvraag aan de voorspellingsruntime te maken. De uiting van de gebruiker maakt deel uit van het [predictionRequest](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest)-object.

De **[luisRuntimeClient.prediction.getSlotPrediction](/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** -methode heeft verschillende parameters nodig, zoals de app-id, de naam van de sleuf en het voorspellingsaanvraagobject, om aan de aanvraag te voldoen. De andere opties, zoals verbose, show all intents en log, zijn optioneel.

[!code-javascript [Get prediction from runtime](~/cognitive-services-quickstart-code/javascript/LUIS/sdk-3x/index.js?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node index.js` in uw quickstart-bestand.

```console
node index.js
```
