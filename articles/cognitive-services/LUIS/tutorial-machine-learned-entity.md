---
title: 'Zelfstudie: gestructureerde gegevens extraheren met machine learning-entiteit - LUIS'
description: Gestructureerde gegevens uit een uiting extraheren met behulp van de machine learning-entiteit. Voeg subentiteiten met functies toe om de nauwkeurigheid van de extractie te vergroten.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: aa2783abe86b90a907510dec8d7eb40162820ad5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025256"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Zelfstudie: Gestructureerde gegevens uit de uiting van de gebruiker extraheren met machine learning-entiteiten in Language Understanding (LUIS)

In deze zelfstudie extraheert u gestructureerde gegevens uit een uiting met behulp van de machine learning-entiteit.

De machine learning-entiteit ondersteunt het [modelontledingsconcept](luis-concept-model.md#v3-authoring-model-decomposition) door subentiteitentiteiten met [functies](luis-concept-feature.md) te bieden.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Voorbeeld-app importeren
> * Machine learning-entiteit toevoegen
> * Subentiteit en functie toevoegen
> * App trainen, testen en publiceren
> * Voorspelling van entiteit uit eindpunt ophalen

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Waarom een machine learning-entiteit gebruiken?

In deze zelfstudie voegt u een machine learning-entiteit toe om gegevens te extraheren uit de uiting van een gebruiker.

De entiteit definieert de gegevens die moeten worden geëxtraheerd uit de uiting. Dit omvat het geven van een naam aan de gegevens, een type (indien mogelijk), een oplossing van de gegevens als er sprake is van dubbelzinnigheid en de exacte tekst die de gegevens oplevert.

Als u de gegevens wilt definiëren, moet u het volgende doen:
* De entiteit maken
* Label de tekst, binnen voorbeelduitingen, die de entiteit vertegenwoordigt. Deze gelabelde voorbeelden leren LUIS wat de entiteit is en waar deze kan worden gevonden in een uiting.

## <a name="entity-decomposability-is-important"></a>Afbreekbaarheid van entiteiten is belangrijk

Afbreekbaarheid van entiteiten is belangrijk voor zowel intentievoorspelling als voor gegevensextractie met de entiteit.

Begin met een machine learning-entiteit, die het begin en de entiteit op het hoogste niveau is voor het extraheren van gegevens. Breek de entiteit vervolgens af in subentiteiten.

Hoewel u mogelijk niet weet hoe nauwkeurig uw entiteit moet zijn wanneer u uw app start, wordt aangeraden om te beginnen met een machine learning-entiteit en vervolgens af te breken met subentiteiten als uw app meer vorm krijgt.

In deze zelfstudie maakt u een machine learning-entiteit die een bestelling voor een pizza-app vertegenwoordigt. De entiteit extraheert tekst die betrekking heeft op de bestelling en haalt de grootte en hoeveelheid eruit.

Een uiting van `Please deliver one large cheese pizza to me` moet `one large cheese pizza` als de bestelling extraheren en vervolgens ook `1` extraheren voor hoeveelheid en `large` voor grootte.

## <a name="download-json-file-for-app"></a>JSON-bestand voor de app downloaden

Download het [JSON-bestand van de app](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json) en sla het op.

## <a name="import-json-file-for-app"></a>JSON-bestand voor de app importeren

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Door machine geleerde entiteit maken

Als u details over een pizzabestelling wilt extraheren, maakt u een `Order` entiteit voor machine learning op hoofdniveau.

1. Selecteer op de pagina **Intenties** de intentie **OrderPizza**.

1. Selecteer de volgende uiting in de lijst met voorbeelduitingen.

    |Voorbeelduiting van een bestelling|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Klik en sleep de muisaanwijzer over de tekst voor de eerste voorbeeldintentie. Voer in het menu dat wordt weergegeven, de naam van de entiteit in als `Order`. Selecteer vervolgens `Order Create new entity` in de lijst.

    ![Label het begin en eind van de tekst voor de volledige bestelling](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Een entiteit is niet altijd de hele uiting. In dit specifieke geval geeft `pickup` aan hoe de bestelling moet worden ontvangen. Vanuit een conceptueel perspectief moet `pickup` deel uitmaken van de gelabelde entiteit voor de bestelling.

1. Selecteer **Structuur toevoegen** en selecteer dan **Volgende** in het vak **Een entiteitstype kiezen**. Structuur is nodig om subentiteiten, zoals grootte en hoeveelheid, toe te voegen.

    ![Schermopname van het venster Een entiteitstype kiezen met de optie Structuur ingeschakeld.](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Selecteer in het vak **Subentiteiten toevoegen (optioneel)** de optie **+** op de rij `Order` en voeg vervolgens `Size` en `Quantity` toe als subentiteiten. Selecteer vervolgens **Maken**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van het venster Subentiteiten toevoegen (optioneel) met subentiteiten gemarkeerd.](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Subentiteiten bewerken om extractie te verbeteren

In de vorige stappen hebt u de entiteit en de subentiteit gemaakt. Voeg functies toe aan de subentiteiten om extractie te verbeteren.

### <a name="improve-size-extraction-with-phrase-list"></a>Grootte-extractie verbeteren met frasenlijst

1. Selecteer **Entiteiten** in het menu links en selecteer vervolgens de entiteit **Bestelling**.

1. Selecteer op het tabblad **Schema en functies** de subentiteit **Grootte** en selecteer vervolgens **+ Functie toevoegen**.

1. Selecteer **Nieuwe frasenlijst maken** in de vervolgkeuzelijst.

1. Voer in het vak **Nieuwe frasenlijst maken** de naam `SizePhraselist` in en voer vervolgens de waarden `small`, `medium` en `large` in. Wanneer het vak **Suggesties** wordt gevuld, selecteert u `extra large`en `xl`. Selecteer **Maken** om de nieuwe frasenlijst te maken.

    Deze frasenlijstfunctie helpt de subentiteit `Size` om woorden te vinden die gerelateerd zijn aan de grootte door er voorbeeldwoorden aan te geven. Deze frasenlijst hoeft niet elke grootte van een woord te bevatten, maar moet wel woorden bevatten waarvan kan worden verwacht dat ze grootte aanduiden.

### <a name="add-sizelist-entity"></a>Entiteit SizeList toevoegen

Door een lijst met bekende grootten toe te voegen die door de clienttoepassing worden herkend, kunt u ook extraheren.

1. Selecteer **Entiteiten** in het menu links en selecteer vervolgens **+ Maken**.

1. Stel de naam van de entiteit in als `SizeListentity` en stel het type in als **Lijst**, zodat het gemakkelijk te identificeren is in vergelijking met de `SizePhraselist` die in de vorige sectie is gemaakt.

1. Voeg de grootten toe die de clienttoepassing verwacht: `Small`, `Medium`, `Large`en `XLarge`. Voeg vervolgens synoniemen toe voor elk. De synoniemen moeten de termen zijn die een gebruiker invoert in de chatbot. De entiteit wordt geëxtraheerd met een lijstentiteit wanneer deze precies overeenkomt met de genormaliseerde waarde of synoniemen.

    |Genormaliseerde waarde|Synoniemen|
    |--|--|
    |Klein|sm, sml, zeer klein, kleinste|
    |Middelgroot|md, mdm, normaal, gemiddeld, midden|
    |Groot|lg, lrg, groot|
    |XLarge|xl, grootste, gigantisch|


    > [!div class="mx-imgBorder"]
    > ![Schermopname van het venster SizeList en Lijstitems met XLarge geselecteerd.](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Functie van SizeList-entiteit toevoegen

1. Selecteer **Entiteiten** in het menu links om terug te keren naar de lijst met entiteiten.

1. Selecteer **Bestelling** uit de lijst met entiteiten.

1. Selecteer op het tabblad **Schema en functies** de entiteit **Grootte** en selecteer vervolgens **+ Functie toevoegen**.

1. Selecteer **@ SizeListentity** in de vervolgkeuzelijst.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Vooraf gedefinieerde cijferentiteit toevoegen

Het toevoegen van een vooraf gedefinieerde cijferentiteit helpt ook bij extractie.

1. Selecteer **Entiteiten** in het menu links en selecteer vervolgens **Vooraf gemaakte entiteit toevoegen**.

1. Selecteer **Cijfer** in de lijst en selecteer vervolgens **Gereed**.

1. Selecteer **Entiteiten** in het menu links om terug te keren naar de lijst met entiteiten.

### <a name="add-feature-of-prebuilt-number-entity"></a>Functie van vooraf gebouwde cijferentiteit toevoegen

1. Selecteer **Bestelling** uit de lijst met entiteiten.

1. Selecteer op het tabblad **Schema en functies** de entiteit **Aantal** en selecteer vervolgens **+ Functie toevoegen**.

1. Selecteer **@ number** in de vervolgkeuzelijst.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Vereiste functies configureren

Selecteer op de pagina Details van entiteit voor entiteit **Bestelling** het sterretje, `*`, voor zowel de functie **@ SizeList** als de functie **@ number**. Het sterretje wordt weergegeven in hetzelfde label als de functienaam.

> [!div class="mx-imgBorder"]
> ![Schermopname van de functie @SizeList met het sterretje en de waarschuwing Vereisen.](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Voorbeelden van uitingen labelen

De door de machine geleerde entiteit wordt gemaakt en de subentiteiten beschikken over functies. Voor het voltooien van de extractieverbetering moeten de voorbeelduitingen worden gelabeld met de subentiteiten.

1. Selecteer **Intenties** in de navigatiebalk links en selecteer vervolgens de intentie **OrderPizza**.

1. Als u het **Entiteitpalet** wilt openen, selecteert u het symbool **@** in de contextuele werkbalk.

1. Selecteer elke entiteitrij in het palet en gebruik vervolgens de paletcursor om de entiteit te selecteren in elke voorbeelduiting. Wanneer u klaar bent, ziet de lijst met entiteiten eruit als in de volgende afbeelding.

    > [!div class="mx-imgBorder"]
    > ![Gedeeltelijke schermopname van de configuratie van de vereiste functie](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>De app trainen

Als u de app wilt trainen, selecteert u **Trainen**. Door te trainen worden de wijzigingen, zoals de nieuwe entiteiten en de gelabelde uitingen, toegepast op het actieve model.

## <a name="add-a-new-example-utterance"></a>Een nieuwe voorbeelduiting toevoegen

1. Voeg na de training een nieuwe voorbeelduiting toe aan de intentie `OrderPizza` om te zien hoe goed LUIS de machine learning-entiteit begrijpt.

    |Voorbeelduiting van een bestelling|
    |--|
    |`I need a large pepperoni pizza`|

    De algemene bovenste entiteit, `Order` is gelabeld en de subentiteit `Size` is ook gelabeld met stippellijnen.

    > [!div class="mx-imgBorder"]
    > ![Gedeeltelijke schermopname van nieuwe voorbeelduiting voorspeld met entiteit](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    De stippellijn geeft de voorspelling aan op basis van de huidige getrainde app.

1. Als u de voorspelling in een entiteit met een label wilt wijzigen, selecteert u het vinkje op dezelfde rij.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van een voorbeelduiting waarbij het vinkje is gemarkeerd.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    Op dit moment werkt de machine learning-entiteit, omdat deze de entiteit kan vinden binnen een nieuwe voorbeelduiting. Wanneer u voorbeelduitingen toevoegt en de entiteit wordt niet correct voorspeld, labelt u de entiteit en de subentiteiten. Als de entiteit correct wordt voorspeld, moet u de voorspellingen bevestigen.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>De app trainen om de entiteitswijzigingen toe te passen op de app

Selecteer **Trainen** om de app te trainen met deze nieuwe uiting.

Op dit moment bevat de bestelling enkele details die kunnen worden geëxtraheerd (grootte, hoeveelheid en tekst totale bestelling). De entiteit `Order` wordt verder verfijnd, zoals pizzabeleg, type bodem en bijgerechten, wordt verder verfijnd. Elk van deze moet worden gemaakt als subentiteiten van de entiteit `Order`.

## <a name="test-the-app-to-validate-the-changes"></a>De app testen om de wijzigingen te valideren

Test de app met het interactieve paneel **Test**. Met dit proces kunt u een nieuwe uiting invoeren en vervolgens de resultaten van de voorspelling weergeven om te zien hoe goed de actieve, getrainde app werkt. De intentievoorspelling moet redelijk zeker zijn (meer dan 60%) en de extractie van de entiteit moet ten minste de entiteit `Order` ophalen. De details van de bestellingentiteit ontbreken mogelijk, omdat deze paar uitingen niet voldoende zijn om elk geval af te handelen.

1. Selecteer **Test** in de bovenste navigatiebalk.
1. Voer de uiting `2 small cheese pizzas for pickup` in en selecteer Enter. Het actieve model heeft de juiste intentie voorspeld met meer dan 60% waarschijnlijkheid.


1. Selecteer **Inspecteren** om de voorspellingen van de entiteit weer te geven.

    > [!div class="mx-imgBorder"]
    > ![Gedeeltelijke schermopname van de entiteit voorspellingen in het interactieve testpaneel.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>De app publiceren voor toegang vanaf het HTTP-eindpunt

Om een voorspelling van LUIS te ontvangen in een chatbot of een andere clienttoepassing, moet u de app publiceren naar het eindpunt.

1. Selecteer **Publiceren** in de navigatiebalk rechtsboven.

    ![Schermopname van knop om vanuit LUIS te publiceren naar eindpunt in menu rechtsboven](./media/howto-publish/publish-button.png)

1. Selecteer de sleuf **Productie**, selecteer **Instellingen wijzigen**, selecteer **Sentimentanalyse** en selecteer **Gereed**.

    > [!div class="mx-imgBorder"]
    > ![Schermopname van publiceren naar eindpunt vanuit LUIS](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Selecteer de koppeling **Uw eindpunt-URL's openen** in de melding om naar de pagina **Azure-resources** te gaan. De eindpunt-URL's worden onderaan vermeld als de **Voorbeeldquery**.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Voorspelling van intenties en entiteiten ophalen van HTTP-eindpunt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het eind van de URL in de adresbalk en vervang _YOUR_QUERY_HERE_ door dezelfde query die u ook hebt ingevoerd in het interactieve testpaneel.

    `2 small cheese pizzas for pickup`

    De laatste parameter van de queryreeks is `query`, de utterance **query**.

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
       }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Zelfstudie - Intenties](./tutorial-intents-only.md)
* [Concept - Entiteiten](luis-concept-entity-types.md) - conceptuele informatie
* [Concept - Functies](luis-concept-feature.md) - conceptuele informatie
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie gebruikt de app een machine learning-entiteit om de intentie van de uiting van een gebruiker te vinden en details uit die uiting te extraheren. Door de machine learning-entiteit te gebruiken, kunt u de details van de entiteit afbreken.

> [!div class="nextstepaction"]
> [Een vooraf gemaakte entiteit KeyPhrase toevoegen](./luis-reference-prebuilt-keyphrase.md)