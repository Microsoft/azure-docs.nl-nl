---
title: 'Zelfstudie: Intenties voorspellen - LUIS'
description: Maak in deze zelfstudie een aangepaste app waarmee de intentie van een gebruiker wordt voorspeld op basis van de uiting (tekst).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 398324099f923a5141b172d61f9fd41a69dbf069
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025290"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Zelfstudie: Een LUIS-app bouwen om gebruikersintenties te bepalen

In deze zelfstudie maakt u een aangepaste app waarmee de intentie van een gebruiker wordt voorspeld op basis van de uiting (tekst).

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een nieuwe app maken
> * Intents maken
> * Voorbeelden van utterances toevoegen
> * App trainen
> * App publiceren
> * Intentvoorspelling ophalen vanaf eindpunt

## <a name="user-intentions-as-intents"></a>Gebruikersintenties als intents

Het doel van de app is om de intentie van natuurlijke omgangstaal in een tekst te bepalen:

`I'd like to order a veggie pizza with a salad on the side.`

Deze intenties worden onderverdeeld in **Intents**.

|Intentie|Doel|
|--|--|
|`ModifyOrder`|De pizzabestelling van een gebruiker bepalen.|
|`Greeting`|Begin gesprek met bot.|
|`ConfirmOrder`|Bevestig pizza-bestelling.|
|`None`|Bepaal of de gebruiker een vraag stelt die de LUIS-app niet kan beantwoorden. Deze intentie wordt geleverd als onderdeel van het maken van de app en kan niet worden verwijderd. |

## <a name="create-a-new-app"></a>Een nieuwe app maken

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Een nieuwe intent maken

Een intent wordt gebruikt om uitingen van gebruikers in te delen volgens de gebruikersintentie, die blijkt uit de natuurlijke taal in een tekst.

Om een uiting te classificeren heeft de intent voorbeelden nodig van gebruikersuitingen die onder deze intent geclassificeerd moeten worden.

1. Selecteer **+ Maken** in het onderdeel **Build** op de pagina **Intents** om een nieuwe intent te maken. Voer de naam van de nieuwe intent, `OrderPizza`, in en selecteer vervolgens **Gereed**.

    De `OrderPizza`-intent wordt voorspeld wanneer een gebruiker een pizza wil bestellen.

1. Voeg verschillende voorbeelden van utterances toe aan deze intent waarnaar een gebruiker waarschijnlijk zal vragen:

    |`OrderPizza` voorbeelduitingen|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    > [!div class="mx-imgBorder"]
    > ![Schermopname waarop voorbeelduitingen worden toegevoegd in het LUIS-portaal op de pagina Intent](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Door _voorbeelden van uitingen_ op te geven traint u LUIS in het soort uitingen dat voor deze intent kan worden voorspeld. Dit zijn positieve voorbeelden. De uitingen in alle andere intents worden behandeld als negatieve voorbeelden voor deze intent.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Resterende intenties maken

1. Maak de volgende `Greeting`-intent en voeg de volgende voorbeelduitingen toe. Dit is de intent om te bepalen of een gebruiker een gesprek begint om een nieuwe pizza te bestellen.

    |`Greeting` voorbeelduiting|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Maak de volgende `Confirm`-intent en voeg de volgende voorbeelduitingen toe. Dit is de intent om te bepalen of een gebruiker klaar is met bestellen en de details van de bestelling accepteert.

    |`Confirm` voorbeelduitingen|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Geen voorbeelduitingen intent

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>De app trainen

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>De app publiceren

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Intentvoorspelling ophalen

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ga naar het einde van de URL in de adresbalk en voer in:

    `get a medium vegetarian pizza for delivery`

  
    De parameter van de queryreeks is `query`, de utterance **query** wordt doorgegeven in de URI. Deze utterance is niet hetzelfde als een van de voorbeeldutterances. Dit is een goede test om te controleren of LUIS de `OrderPizza`-intent leert en voorspelt als de hoogst scorende intent.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    De entiteitenmatrix is leeg omdat deze app momenteel geen entiteiten bevat (gegevenseenheden om uit de utterance te halen).

    Het JSON-resultaat identificeert de hoogst scorende intent als **`prediction.topIntent`** -eigenschap. Alle scores liggen tussen 1 en 0, met de betere score dichterbij 1.

1. Wijzig de URL-**query** parameter om zodat deze zich richt op de intent **Begroeting**:

    `Howdy`

    Dit is niet precies hetzelfde als een voorbeeld van een utterance, dus vormt het een goede test om te zien of LUIS kan leren wat voorspeld moet worden met deze intent.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    Deze voorspelling heeft een betrouwbaarheidsscore van 44%. Als u een hogere betrouwbaarheidsscore wilt, voegt u tussen 15 en 30 voorbeelden van utterances toe.

## <a name="client-application-next-steps"></a>Volgende stappen voor clienttoepassing

In deze zelfstudie hebt u een LUIS-app gemaakt, hebt u intents gemaakt, voorbeeldutterances aan elke intent toegevoegd, voorbeeldutterances aan de None-intent toegevoegd, de app getraind, gepubliceerd en getest op het eindpunt. Dit zijn de basisstappen voor het compileren van een LUIS-model.

Nadat LUIS de JSON-reactie heeft geretourneerd, is LUIS klaar met deze aanvraag. LUIS geeft geen antwoord op deze utterances van gebruikers, maar stelt alleen vast om wat voor soort informatie er wordt gevraagd in natuurlijke taal. De gespreksopvolging wordt uitgevoerd met de clienttoepassing, bijvoorbeeld een Azure-bot.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Gerelateerde informatie

* [Typen entiteiten](luis-concept-entity-types.md)
* [Trainen](luis-how-to-train.md)
* [Hoe u kunt publiceren](luis-how-to-publish-app.md)
* [Testen in de LUIS-portal](luis-interactive-test.md)
* [Azure Bot](/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een afbreekbare entiteit toevoegen aan deze app](tutorial-machine-learned-entity.md)