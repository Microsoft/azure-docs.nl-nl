---
title: Verkeerd gespelde woorden corrigeren-LUIS
titleSuffix: Azure Cognitive Services
description: Corrigeer verkeerd gespelde woorden in uitingen door Bing Spellingcontrole-API V7 toe te voegen aan LUIS-eindpunt query's.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/19/2019
ms.openlocfilehash: 9d2a1702ea131e9b1b4bf5e586f4290db3aff7ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018766"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Verkeerd gespelde woorden corrigeren met Bing Spellingcontrole

U kunt uw LUIS-app integreren met [Bing spellingcontrole-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) om verkeerd gespelde woorden in uitingen te corrigeren voordat Luis de score en entiteiten van de utterance voor spelt.

[!INCLUDE [Not supported in V3 API prediction endpoint](./includes/v2-support-only.md)]

## <a name="create-endpoint-key"></a>Eindpunt sleutel maken

Volg de volgende instructies om een Bing Spellingcontrole resource in de Azure Portal te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **een resource maken** in de linkerbovenhoek.

3. Typ `Bing Spell Check API V7` in het zoekvak.

    ![Zoeken naar Bing Spellingcontrole-API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Selecteer de service.

5. Er wordt rechts een informatie venster weer gegeven met daarin informatie, inclusief de juridische kennisgeving. Selecteer **maken** om het proces voor het maken van het abonnement te starten.

6. Geef in het volgende deel venster uw service-instellingen op. Wacht totdat het proces voor het maken van de service is voltooid.

    ![Service-instellingen invoeren](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Selecteer **alle resources** onder de titel **Favorieten** in de navigatie aan de linkerkant.

8. Selecteer de nieuwe service. Het type is **Cognitive Services** en de locatie is **globaal**.

9. Selecteer in het hoofd paneel **sleutels** om uw nieuwe sleutels weer te geven.

    ![Sleutels ophalen](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopieer de eerste sleutel. U hebt slechts een van de twee sleutels nodig.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="adding-the-key-to-the-endpoint-url"></a>De sleutel toevoegen aan de eind punt-URL
Voor de eindpunt query moet de sleutel worden door gegeven in de query teken reeks parameters voor elke query waarop u de spelling correctie wilt Toep assen. Mogelijk hebt u een chatbot die LUIS aanroept of u kunt de LUIS endpoint API rechtstreeks aanroepen. Ongeacht hoe het eind punt wordt aangeroepen, moeten elke aanroep de vereiste informatie bevatten die de spelling correcties goed kunnen uitvoeren.

De eind punt-URL heeft verschillende waarden die correct moeten worden door gegeven. De Bing Spellingcontrole-API V7-sleutel is slechts een van deze. U moet de para meter **Spelling** controle instellen op True en u moet de waarde van **Bing-spelling check-Subscription-Key** instellen op de sleutel waarde:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=true&bing-spell-check-subscription-key={bingKey}&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Verkeerd gespeld utterance naar LUIS verzenden
1. Kopieer in een webbrowser de voor gaande teken reeks en vervang de,, en door `region` `appId` `luisKey` `bingKey` uw eigen waarden. Zorg ervoor dat u de regio van het eind punt gebruikt als deze niet overeenkomt met de publicatie [regio](luis-reference-regions.md).

2. Voeg een verkeerd gespelde utterance toe, zoals ' hoe ver is de mountainn? '. In het Engels `mountain` is, met een `n` , de juiste spelling.

3. Selecteer ENTER om de query naar LUIS te verzenden.

4. LUIS reageert met een JSON-resultaat voor `How far is the mountain?` . Als Bing Spellingcontrole-API V7 een verkeerde spelling detecteert, `query` bevat het veld in het JSON-antwoord van de Luis-app de oorspronkelijke query en `alteredQuery` bevat het veld de gecorrigeerde query die wordt verzonden naar Luis.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Spel fouten negeren

Als u de Bing Spellingcontrole-API V7-service niet wilt gebruiken, moet u de juiste en onjuiste spelling toevoegen.

Twee oplossingen zijn:

* Voor beeld van een label uitingen die de verschillende spellingen hebben, zodat LUIS de juiste spelling en type fouten kan ontdekken. Voor deze optie is meer inspanning vereist dan het gebruik van een spelling controle.
* Een woordgroepen lijst maken met alle variaties van het woord. Met deze oplossing hoeft u geen label te maken voor de woord variaties in het voor beeld uitingen.

## <a name="publishing-page"></a>Publicatie pagina
Op de [publicatie](luis-how-to-publish-app.md) pagina is het selectie vakje **Bing spelling controle inschakelen ingeschakeld** . Dit is een handige manier om de sleutel te maken en te begrijpen hoe de eind punt-URL verandert. U moet nog steeds de juiste eindpunt parameters gebruiken om de spelling voor elke utterance te corrigeren.

> [!div class="nextstepaction"]
> [Meer informatie over voorbeeld uitingen](./luis-how-to-add-entities.md)