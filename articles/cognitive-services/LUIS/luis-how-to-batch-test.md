---
title: Een batch test uitvoeren-LUIS
titleSuffix: Azure Cognitive Services
description: Gebruik Language Understanding (LUIS) batch test sets om uitingen te vinden met onjuiste doel stellingen en entiteiten.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: 2668f969076fd2b9960995fec44350d61b405740
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809413"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batch tests met een set voor beeld-uitingen

Met batch tests wordt uw actieve getrainde versie gevalideerd om de nauw keurigheid van de voor spelling te meten. Met een batch test kunt u de nauw keurigheid van elke intentie en entiteit in uw actieve versie bekijken. Bekijk de resultaten van de batch test om passende maat regelen te nemen om de nauw keurigheid te verbeteren, zoals het toevoegen van meer voor beeld-uitingen aan een intentie als uw app regel matig de juiste intentie of label entiteiten in de utterance niet kan identificeren.

## <a name="group-data-for-batch-test"></a>Groeps gegevens voor batch test

Het is belang rijk dat uitingen dat wordt gebruikt voor het uitvoeren van batch tests, nieuw zijn in LUIS. Als u een gegevensset van uitingen hebt, moet u de uitingen in drie sets delen: voor beeld-uitingen die zijn toegevoegd aan een intentie, uitingen ontvangen van het gepubliceerde eind punt, en uitingen gebruikt voor batch test-LUIS nadat deze is getraind.

Het batch-JSON-bestand dat u gebruikt, moet uitingen bevatten met machine learning-entiteiten op het hoogste niveau, met inbegrip van de begin-en eind positie. De uitingen mogen geen deel uitmaken van de voorbeelden die al in de app staan. Het moeten uitingen zijn die u positief wilt voorspellen voor de intentie en entiteiten.

U kunt afzonderlijke tests uitvoeren op intentie en/of entiteit of alle tests (maximaal 1000 uitingen) in hetzelfde bestand hebben. 

### <a name="common-errors-importing-a-batch"></a>Veelvoorkomende fouten bij het importeren van een batch

Als u fouten ondervindt bij het uploaden van uw batch bestand naar LUIS, controleert u de volgende algemene problemen:

* Meer dan 1.000 uitingen in een batch-bestand
* Een utterance JSON-object dat geen eigenschap entities heeft. De eigenschap kan een lege matrix zijn.
* Woord (en) gemarkeerd in meerdere entiteiten
* Labels van entiteiten beginnen of eindigen op een spatie.

## <a name="fixing-batch-errors"></a>Batch fouten corrigeren

Als er fouten zijn opgetreden tijdens het testen van de batch, kunt u meer uitingen toevoegen aan een intentie en/of een label meer uitingen met de entiteit om LUIS de discriminatie tussen de intenties te maken. Als u uitingen hebt toegevoegd en deze een label krijgt en nog steeds Voorspellings fouten in batch tests ontvangt, kunt u een [woordgroepen lijst](luis-concept-feature.md) functie toevoegen met een Documentspecifieke vocabulaire om Luis sneller te leren.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Batch testen met behulp van de LUIS-Portal 

### <a name="import-and-train-an-example-app"></a>Een voor beeld-App importeren en trainen

Importeer een app voor het opnemen van een pizzabestelling, zoals `1 pepperoni pizza on thin crust`.

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) en sla het op.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Selecteer de pijl naast **nieuwe app** en klik op **importeren als JSON** om de JSON te importeren in een nieuwe app. Geef de app een naam `Pizza app` .


1. Selecteer **Trainen** rechtsboven in de navigatiebalk om de app te trainen.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Batch test bestand

Het JSON-voorbeeldbestand bevat een uiting met een gelabelde entiteit om u een idee te geven hoe een testbestand eruitziet. Uw eigen tests moeten veel uitingen met de juiste intentie en gelabelde machine learning-entiteit bevatten.

1. Maak `pizza-with-machine-learned-entity-test.json` in een teksteditor of [download](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) het.

2. Voeg aan het batchbestand in JSON-indeling een uiting toe met de **intentie** die moet worden voorspeld in de test.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>De batch uitvoeren

1. Selecteer **Test** in de bovenste navigatiebalk.

2. Selecteer het **paneel Batchgewijs testen** in het paneel aan de rechterkant.

    ![Koppeling voor batch testen](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Selecteer **Importeren**. Selecteer in het dialoog venster dat wordt weer gegeven, de optie **bestand kiezen** en zoek een JSON-bestand met de juiste JSON-indeling die *niet meer dan 1.000* uitingen bevat om te testen.

    Import fouten worden gerapporteerd in een rode meldingen balk boven aan de browser. Wanneer een import fouten bevat, wordt er geen gegevensset gemaakt. Zie [common Errors](#common-errors-importing-a-batch)(Engelstalig) voor meer informatie.

4. Kies de bestandslocatie van het bestand `pizza-with-machine-learned-entity-test.json`.

5. Geef de gegevensset de naam `pizza test` en selecteer **Gereed**.

6. Selecteer de knop **Run** (Uitvoeren). Nadat de batch test is uitgevoerd, selecteert u **resultaten weer geven**. 

    > [!TIP]
    > * Als u **downloaden** selecteert, wordt hetzelfde bestand gedownload dat u hebt geüpload.
    > * Als u de batch test mislukt, komt ten minste één utterance-intentie niet overeen met de voor spelling.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Batchresultaten voor intenties evalueren

Selecteer **resultaten weer geven** om de resultaten van de batch test te controleren. In de testresultaten wordt grafisch weergegeven hoe de testuitingen zijn voorspeld voor de actieve versie.

In de batchgrafiek worden vier kwadranten met resultaten weergegeven. Rechts van de grafiek ziet u een filter. Het filter bevat intenties en entiteiten. Wanneer u een [sectie van de grafiek](luis-concept-batch-test.md#batch-test-results) of een punt in de grafiek selecteert, worden de gekoppelde uitingen weergegeven onder de grafiek.

Bij het aanwijzen van de grafiek met de muisaanwijzer kunt u de weergave in de grafiek vergroten of verkleinen met een muiswiel. Dit is handig wanneer veel punten in het diagram zich dicht op elkaar bevinden.

De grafiek is verdeeld in vier kwadranten, waarbij twee van de secties rood worden weergegeven.

1. Selecteer de intentie **ModifyOrder** in de filterlijst. De uiting wordt voorspeld als een **terecht-positief**, wat betekent dat de uiting correct is gematcht met de positieve voorspelling die in het batchbestand is vermeld.

    > [!div class="mx-imgBorder"]
    > ![Uiting is correct gematcht met de betreffende positieve voorspelling](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Het groene vinkje in de filterlijst geeft ook aan dat de test voor elke intentie is geslaagd. Alle andere intenties worden weergegeven met een positieve score van 1/1, omdat de uiting is getest op basis van elke intentie, als een negatieve test voor alle intenties die niet worden vermeld in de batchtest.

1. Selecteer de intentie **Bevestiging**. Deze intentie wordt niet vermeld in de batchtest. Dit is dus een negatieve test van de uiting die wordt vermeld in de batchtest.

    > [!div class="mx-imgBorder"]
    > ![Uiting is correct negatief voorspeld voor niet-vermelde intentie in batchbestand](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    De negatieve test is geslaagd, zoals aangegeven met de groene tekst in het filter, en het raster.

### <a name="review-batch-test-results-for-entities"></a>Batchtestresultaten voor entiteiten evalueren

De ModifyOrder-entiteit, als een machine-entiteit met subentiteiten, geeft weer of de entiteit op het hoogste niveau overeenkomt en hoe de subentiteiten worden voor speld.

1. Selecteer de entiteit **ModifyOrder** in de filterlijst en selecteer vervolgens de cirkel in het raster.

1. De voorspelling van de entiteit wordt onder de grafiek weergegeven. De weergave bevat ononderbroken lijnen voor voorspellingen die overeenkomen met de verwachting en stippellijnen voor voorspellingen die niet overeenkomen met de verwachting.

    > [!div class="mx-imgBorder"]
    > ![Bovenliggende entiteit is correct voorspeld in batchbestand](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Grafiek resultaten filteren

Als u de grafiek wilt filteren op een specifieke intentie of entiteit, selecteert u de intentie of entiteit in het deel venster filter aan de rechter kant. De gegevens punten en hun distributie-update in de grafiek volgens uw selectie.

![Gevisualiseerd batch test resultaat](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Voor beelden van grafiek resultaten

De grafiek in de LUIS-Portal kunt u de volgende acties uitvoeren:
 
#### <a name="view-single-point-utterance-data"></a>Utterance gegevens met één punt weer geven

Beweeg de muis aanwijzer over een gegevens punt in de grafiek om de zekerheids Score van de voor spelling te zien. Selecteer een gegevens punt om de bijbehorende utterance op te halen in de lijst met uitingen aan de onderkant van de pagina.

![Geselecteerde utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Sectie gegevens weer geven

Selecteer in het diagram met vier secties de naam van de sectie, zoals **Onwaar positief** in de rechter bovenhoek van de grafiek. Onder de grafiek worden alle uitingen in dat gedeelte onder de grafiek in een lijst weer gegeven.

![Geselecteerde uitingen per sectie](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

In deze voor gaande afbeelding wordt de utterance `switch on` aangeduid met de TurnAllOn intentie, maar is de voor spelling van geen intentie ontvangen. Dit is een indicatie dat de TurnAllOn-intentie meer voorbeeld uitingen nodig heeft om de verwachte voor spelling te maken.

De twee secties van de grafiek worden rood aangeduid met uitingen die niet overeenkomen met de verwachte voor spelling. Deze geven aan uitingen welke LUIS meer training nodig heeft.

De twee secties van de grafiek in het groen komen overeen met de verwachte voor spelling.

## <a name="batch-testing-using-the-rest-api"></a>Batch testen met behulp van de REST API 

Met LUIS kunt u batch testen met behulp van de LUIS-Portal en REST API. De eind punten voor de REST API worden hieronder weer gegeven. Zie [zelf studie: gegevens sets batch testen](luis-tutorial-batch-testing.md)voor meer informatie over batch tests met behulp van de Luis-Portal. Gebruik de volledige Url's hieronder en vervang de waarden van de tijdelijke aanduiding door uw eigen LUIS-Voorspellings sleutel en-eind punt. 

Vergeet niet om uw LUIS-sleutel toe te voegen aan `Apim-Subscription-Id` in de kop en stel in `Content-Type` op `application/json` .

### <a name="start-a-batch-test"></a>Een batch test starten

Een batch test starten met behulp van een app-versie-ID of een publicatie sleuf. Verzend een **post** -aanvraag naar een van de volgende eindpunt notaties. Neem uw batch-bestand op in de hoofd tekst van de aanvraag.

Publicatie sleuf
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

App-versie-ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Deze eind punten retour neren een bewerkings-ID die u gebruikt om de status te controleren en krijgt resultaten te zien. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>De status van een lopende batch test ophalen

Gebruik de bewerkings-ID van de batch test die u hebt gestart om de status van de volgende eindpunt indelingen op te halen: 

Publicatie sleuf
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

App-versie-ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>De resultaten van een batch test ophalen

Gebruik de bewerkings-ID van de batch test die u hebt gestart om de resultaten van de volgende eindpunt indelingen te verkrijgen: 

Publicatie sleuf
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

App-versie-ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Batch-bestand van uitingen

Verzend een batch-bestand van uitingen, ook wel een *gegevensset* genoemd, voor batch tests. De gegevensset is een bestand met JSON-indeling met een maximum van 1.000 gelabelde uitingen. U kunt Maxi maal 10 gegevens sets testen in een app. Als u meer wilt testen, verwijdert u een gegevensset en voegt u een nieuwe toe. Alle aangepaste entiteiten in het model worden weer gegeven in het filter voor batch test entiteiten, zelfs als er geen overeenkomende entiteiten in de batch bestands gegevens zijn.

Het batch bestand bestaat uit uitingen. Elke utterance moet een verwachte intentie voorspelling hebben, samen met alle [machine learning-entiteiten](luis-concept-entity-types.md#types-of-entities) die u verwacht te detecteren.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Sjabloon voor batch-syntaxis voor intentiesen met entiteiten

Gebruik de volgende sjabloon om uw batch-bestand te starten:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

Het batch-bestand maakt gebruik van de eigenschappen **startPos** en **endPos** om het begin en het einde van een entiteit te noteren. De waarden zijn op nul gebaseerd en mogen niet beginnen of eindigen op een spatie. Dit wijkt af van de query logboeken die eigenschappen start index en endIndex gebruiken.

Als u geen entiteiten wilt testen, neemt u de `entities` eigenschap op en stelt u de waarde in als een lege matrix `[]` .

### <a name="rest-api-batch-test-results"></a>Resultaten van REST API batch testen

De API retourneert verschillende objecten:

* Informatie over de modellen intenties en entiteiten, zoals precisie, intrekken en F-Score.
* Informatie over de entiteiten modellen, zoals precisie, intrekken en F-Score, voor elke entiteit 
  * Met de `verbose` vlag kunt u meer informatie krijgen over de entiteit, zoals `entityTextFScore` en `entityTypeFScore` .
* De uitingen met de voorspelde en gelabelde namen van doel woorden
* Een lijst met valse, positieve entiteiten en een lijst met onjuiste negatieve entiteiten.

## <a name="next-steps"></a>Volgende stappen

Als met testen wordt aangegeven dat uw LUIS-app de juiste intenties en entiteiten niet herkent, kunt u werken om de prestaties van uw LUIS-app te verbeteren door meer uitingen te labelen of functies toe te voegen.

* [Aanbevolen uitingen label met LUIS](luis-how-to-review-endpoint-utterances.md)
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md)
* [Meer informatie over batch tests met deze zelf studie](luis-tutorial-batch-testing.md)
* [Meer informatie over het testen van batches](luis-concept-batch-test.md).
