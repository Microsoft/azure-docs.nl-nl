---
title: Een batchtest uitvoeren - LUIS
titleSuffix: Azure Cognitive Services
description: Gebruik Language Understanding batchtestsets (LUIS) om uitingen met onjuiste intenties en entiteiten te vinden.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 04/13/2021
ms.openlocfilehash: 9fe4f21a5c9e9e26a2f94b8a60cba47916842fe3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501787"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batchtests met een set voorbeeld-utterances

Batchtests valideren uw actief getrainde versie om de nauwkeurigheid van de voorspelling te meten. Met een batchtest kunt u de nauwkeurigheid van elke intentie en entiteit in uw actieve versie bekijken. Bekijk de resultaten van de batchtest om de juiste actie te ondernemen om de nauwkeurigheid te verbeteren, zoals het toevoegen van meer voorbeelduitingen aan een intentie als uw app vaak de juiste intentie niet kan identificeren of entiteiten binnen de utterance niet kan labelen.

## <a name="group-data-for-batch-test"></a>Gegevens groep voor batchtest

Het is belangrijk dat utterances die worden gebruikt voor batchtests nieuw zijn voor LUIS. Als u een gegevensset met utterances hebt, verdeelt u de utterances in drie sets: voorbeeld-utterances die zijn toegevoegd aan een intentie, uitingen die zijn ontvangen van het gepubliceerde eindpunt en utterances die worden gebruikt om LUIS in batch te testen nadat deze is getraind.

Het batch-JSON-bestand dat u gebruikt, moet utterances bevatten met machine learning-entiteiten op het hoogste niveau met het label start- en eindpositie. De uitingen mogen geen deel uitmaken van de voorbeelden die al in de app staan. Het moeten uitingen zijn die u positief wilt voorspellen voor de intentie en entiteiten.

U kunt afzonderlijke tests uitvoeren op intentie en/of entiteit of alle tests (maximaal 1000 uitingen) in hetzelfde bestand hebben. 

### <a name="common-errors-importing-a-batch"></a>Veelvoorkomende fouten bij het importeren van een batch

Als er fouten optreden bij het uploaden van uw batchbestand naar LUIS, controleert u op de volgende veelvoorkomende problemen:

* Meer dan 1000 utterances in een batchbestand
* Een JSON-object van een utterance dat geen eigenschap entities heeft. De eigenschap kan een lege matrix zijn.
* Word(s) gelabeld in meerdere entiteiten
* Entiteitslabels beginnen of eindigen op een spatie.

## <a name="fixing-batch-errors"></a>Batchfouten oplossen

Als er fouten zijn in de batchtests, kunt u meer utterances toevoegen aan een intentie en/of meer utterances labelen met de entiteit om LUIS te helpen de relatie tussen intenties te maken. Als u utterances hebt toegevoegd en ze hebt gelabeld en nog [](luis-concept-feature.md) steeds voorspellingsfouten krijgt in batchtests, kunt u overwegen een woordgroepenlijstfunctie met domeinspecifieke woordenlijst toe te voegen om LUIS te helpen sneller te leren.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Batchtests met behulp van de LUIS-portal 

### <a name="import-and-train-an-example-app"></a>Een voorbeeld-app importeren en trainen

Importeer een app voor het opnemen van een pizzabestelling, zoals `1 pepperoni pizza on thin crust`.

1.  Download het [JSON-bestand van de app](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) en sla het op.

1. Meld u aan bij de [LUIS-portal](https://www.luis.ai) en selecteer uw **abonnement** en **Ontwerpresource** om de apps weer te geven die aan die ontwerpresource zijn toegewezen.
1. Selecteer de pijl naast **Nieuwe app en** klik op Importeren als **JSON** om de JSON in een nieuwe app te importeren. Noem de app `Pizza app` .


1. Selecteer **Trainen** rechtsboven in de navigatiebalk om de app te trainen.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Batch-testbestand

Het JSON-voorbeeldbestand bevat een uiting met een gelabelde entiteit om u een idee te geven hoe een testbestand eruitziet. Uw eigen tests moeten veel uitingen met de juiste intentie en gelabelde machine learning-entiteit bevatten.

1. Maak `pizza-with-machine-learned-entity-test.json` in een teksteditor of [download](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) het.

2. Voeg aan het batchbestand in JSON-indeling een uiting toe met de **intentie** die moet worden voorspeld in de test.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>De batch uitvoeren

1. Selecteer **Test** in de bovenste navigatiebalk.

2. Selecteer het **paneel Batchgewijs testen** in het paneel aan de rechterkant.

    ![Koppeling voor batchtests](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Selecteer **Importeren**. Selecteer in het dialoogvenster dat  wordt weergegeven Bestand kiezen en zoek een JSON-bestand met de juiste JSON-indeling die niet meer dan *1000* uitingen bevat om te testen.

    Importfouten worden vermeld in een rode meldingsbalk boven aan de browser. Wanneer er fouten optreden bij het importeren, wordt er geen gegevensset gemaakt. Zie Veelvoorkomende [fouten voor meer informatie.](#common-errors-importing-a-batch)

4. Kies de bestandslocatie van het bestand `pizza-with-machine-learned-entity-test.json`.

5. Geef de gegevensset de naam `pizza test` en selecteer **Gereed**.

6. Selecteer de knop **Run** (Uitvoeren). Nadat de batchtest is uitgevoerd, selecteert u **Resultaten bekijken.** 

    > [!TIP]
    > * Als **u Downloaden** selecteert, wordt hetzelfde bestand gedownload dat u hebt geüpload.
    > * Als u ziet dat de batchtest is mislukt, komt ten minste één uitingsintentie niet overeen met de voorspelling.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Batchresultaten voor intenties evalueren

Als u de resultaten van de batchtest wilt bekijken, **selecteert u Resultaten bekijken.** In de testresultaten wordt grafisch weergegeven hoe de testuitingen zijn voorspeld voor de actieve versie.

In de batchgrafiek worden vier kwadranten met resultaten weergegeven. Rechts van de grafiek ziet u een filter. Het filter bevat intenties en entiteiten. Wanneer u een [sectie van de grafiek](#review-batch-results-for-intents) of een punt in de grafiek selecteert, worden de gekoppelde uitingen weergegeven onder de grafiek.

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

De entiteit ModifyOrder wordt als een machine-entiteit met subentiteiten weergegeven als de entiteit op het hoogste niveau overeenkomen en hoe de subentiteiten worden voorspeld.

1. Selecteer de entiteit **ModifyOrder** in de filterlijst en selecteer vervolgens de cirkel in het raster.

1. De voorspelling van de entiteit wordt onder de grafiek weergegeven. De weergave bevat ononderbroken lijnen voor voorspellingen die overeenkomen met de verwachting en stippellijnen voor voorspellingen die niet overeenkomen met de verwachting.

    > [!div class="mx-imgBorder"]
    > ![Bovenliggende entiteit is correct voorspeld in batchbestand](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Grafiekresultaten filteren

Als u de grafiek wilt filteren op een specifieke intentie of entiteit, selecteert u de intentie of entiteit in het filterpaneel aan de rechterkant. De gegevenspunten en hun distributie worden bijgewerkt in de grafiek op basis van uw selectie.

![Resultaat gevisualiseerde Batch-test](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Voorbeelden van grafiekresultaat

In de grafiek in de LUIS-portal kunt u de volgende acties uitvoeren:
 
#### <a name="view-single-point-utterance-data"></a>Uitingsgegevens met één punt weergeven

Beweeg in de grafiek de muisaanwijzer over een gegevenspunt om de zekerheidsscore van de voorspelling te zien. Selecteer een gegevenspunt om de bijbehorende utterance op te halen in de lijst met utterances onderaan de pagina.

![Geselecteerde utterance](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Sectiegegevens weergeven

Selecteer in het diagram met vier secties  de sectienaam, zoals Fout-positief rechtsboven in de grafiek. Onder de grafiek worden alle utterances in die sectie weergegeven onder de grafiek in een lijst.

![Geselecteerde utterances per sectie](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

In deze voorgaande afbeelding is de uiting gelabeld met de intentie TurnAllOn, maar de voorspelling `switch on` van de intentie None is ontvangen. Dit is een indicatie dat de intentie TurnAllOn meer voorbeeld-utterances nodig heeft om de verwachte voorspelling te maken.

De twee secties van de grafiek in het rood geven utterances aan die niet overeenkomen met de verwachte voorspelling. Deze geven utterances aan waarvoor LUIS meer training nodig heeft.

De twee secties van de grafiek in het groen komen overeen met de verwachte voorspelling.

## <a name="batch-testing-using-the-rest-api"></a>Batchtests met behulp van de REST API 

Met LUIS kunt u batchtests uitvoeren met behulp van de LUIS-portal en REST API. De eindpunten voor de REST API worden hieronder vermeld. Zie Zelfstudie: batchtestgegevenssets voor meer informatie over batchtests met behulp van de [LUIS-portal.]() Gebruik de onderstaande volledige URL's en vervang de waarden van de tijdelijke aanduiding door uw eigen LUIS-voorspellingssleutel en -eindpunt. 

Vergeet niet om uw LUIS-sleutel `Ocp-Apim-Subscription-Key` toe te voegen aan in de header en stel in op `Content-Type` `application/json` .

### <a name="start-a-batch-test"></a>Een batchtest starten

Start een batchtest met behulp van een app-versie-id of een publicatiesleuf. Verzend een **POST-aanvraag** naar een van de volgende eindpuntindelingen. Neem uw batchbestand op in de hoofdgrootte van de aanvraag.

Publicatiesleuf
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

App-versie-id
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Deze eindpunten retourneren een bewerkings-id die u gebruikt om de status te controleren en resultaten te krijgen. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>De status van een lopende batchtest op te halen

Gebruik de bewerkings-id uit de batchtest die u hebt gestart om de status op te halen uit de volgende eindpuntindelingen: 

Publicatiesleuf
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

App-versie-id
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>De resultaten van een batchtest op halen

Gebruik de bewerkings-id van de batchtest die u hebt gestart om de resultaten op te halen uit de volgende eindpuntindelingen: 

Publicatiesleuf
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

App-versie-id
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Batchbestand met utterances

Verzend een batchbestand met utterances, ook wel een *gegevensset genoemd,* voor batchtests. De gegevensset is een bestand met JSON-indeling dat maximaal 1000 gelabelde utterances bevat. U kunt maximaal 10 gegevenssets testen in een app. Als u meer wilt testen, verwijdert u een gegevensset en voegt u een nieuwe toe. Alle aangepaste entiteiten in het model worden weergegeven in het batchtestentiteitenfilter, zelfs als er geen overeenkomende entiteiten in de batchbestandsgegevens zijn.

Het batchbestand bestaat uit utterances. Elke uiting moet een verwachte intentievoorspelling hebben, samen met [eventuele machine learning-entiteiten](luis-concept-entity-types.md#machine-learned-ml-entity) die u verwacht te detecteren.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Batchsyntaxisjabloon voor intenties met entiteiten

Gebruik de volgende sjabloon om uw batchbestand te starten:

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

Het batchbestand maakt gebruik van **de eigenschappen startPos** en **endPos om** het begin en einde van een entiteit te noteren. De waarden zijn op nul gebaseerd en mogen niet beginnen of eindigen op een spatie. Dit wijs af van de querylogboeken, die de eigenschappen startIndex en endIndex gebruiken.

Als u geen entiteiten wilt testen, moet u de eigenschap opnemen en `entities` de waarde instellen als een lege matrix, `[]` .

### <a name="rest-api-batch-test-results"></a>REST API batchtestresultaten

Er worden verschillende objecten geretourneerd door de API:

* Informatie over de intenties en entiteitenmodellen, zoals precisie, relevante terugroepen en F-score.
* Informatie over de entiteitenmodellen, zoals precisie, relevante terugroepen en F-score) voor elke entiteit 
  * Met de `verbose` vlag kunt u meer informatie krijgen over de entiteit, zoals en `entityTextFScore` `entityTypeFScore` .
* Verstrekte utterances met de voorspelde en gelabelde intentienamen
* Een lijst met fout-positieve entiteiten en een lijst met fout-negatieve entiteiten.

## <a name="next-steps"></a>Volgende stappen

Als testen aangeeft dat uw LUIS-app niet de juiste intenties en entiteiten herkent, kunt u de prestaties van uw LUIS-app verbeteren door meer utterances te labelen of functies toe te voegen.

* [Voorgestelde utterances labelen met LUIS](luis-how-to-review-endpoint-utterances.md)
* [Functies gebruiken om de prestaties van uw LUIS-app te verbeteren](luis-how-to-add-features.md)
