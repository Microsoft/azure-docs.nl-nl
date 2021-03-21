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
ms.date: 01/12/2021
ms.openlocfilehash: 509d1dc0b94bdfa9be5185df0bad793f7702eb26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731031"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Verkeerd gespelde woorden corrigeren met Bing-resource

De V3 API voor voor spellingen ondersteunt nu de [Bing-spelling-API](/bing/search-apis/bing-spell-check/overview). Voeg de spelling controle toe aan uw toepassing door de sleutel van de Bing zoeken-resource op te nemen in de koptekst van uw aanvragen. U kunt een bestaande Bing-Resource gebruiken als u er al een hebt of [een nieuwe bron maakt](https://portal.azure.com/#create/Microsoft.BingSearch) om deze functie te gebruiken. 

Voor beeld van voor spelling uitvoer voor een verkeerd gespelde query:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

Correcties voor de spelling worden uitgevoerd voordat de LUIS-gebruiker utterance voor spelling. U kunt alle wijzigingen in de oorspronkelijke utterance zien, inclusief de spelling, in het antwoord.

## <a name="create-bing-search-resource"></a>Bing Search resource maken

Volg de volgende instructies om een Bing Search resource in de Azure Portal te maken:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **een resource maken** in de linkerbovenhoek.

3. In het zoekvak voert `Bing Search V7` u de service in en selecteert u deze.

4. Er wordt rechts een informatie venster weer gegeven met daarin informatie, inclusief de juridische kennisgeving. Selecteer **maken** om het proces voor het maken van het abonnement te starten.

> [!div class="mx-imgBorder"]
> ![Bing Spellingcontrole-API V7-resource](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. Geef in het volgende deel venster uw service-instellingen op. Wacht totdat het proces voor het maken van de service is voltooid.

6. Nadat de resource is gemaakt, gaat u naar de Blade **sleutels en eind punt** aan de linkerkant. 

7. Kopieer een van de sleutels die moeten worden toegevoegd aan de koptekst van uw Voorspellings aanvraag. U hebt slechts een van de twee sleutels nodig.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>Spelling controle van de gebruikers interface inschakelen 
U kunt de spelling controle voor uw voorbeeld query inschakelen met behulp van de [Luis-Portal](https://www.luis.ai). Selecteer **beheren** boven aan het scherm en **Azure-resources** in de linkernavigatiebalk. Nadat u een Voorspellings bron aan uw toepassing hebt gekoppeld, kunt u **query parameters wijzigen** selecteren onder aan de pagina en de resource sleutel plakken in het veld **spelling controle inschakelen** .
    
   > [!div class="mx-imgBorder"]
   > ![Spelling controle inschakelen](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>De sleutel toevoegen aan de eind punt-URL
Voor elke query waarop u een spelling correctie wilt Toep assen, moet de bron sleutel voor de Bing-spelling controle worden door gegeven aan de query header-para meter. Mogelijk hebt u een chatbot die LUIS aanroept of u kunt de LUIS endpoint API rechtstreeks aanroepen. Ongeacht hoe het eind punt wordt aangeroepen, moeten elke aanroep de vereiste informatie bevatten in de aanvraag van de koptekst voor het goed functioneren van spelling correcties. U moet de waarde met **MKT-Bing-spelling controle** op de sleutel waarde instellen.

|Header sleutel|Header waarde|
|--|--|
|`mkt-bing-spell-check-key`|Sleutels gevonden in de sleutels en de Blade van het **eind punt** van uw resource|

## <a name="send-misspelled-utterance-to-luis"></a>Verkeerd gespeld utterance naar LUIS verzenden
1. Voeg een foutief gespeld utterance toe aan de Voorspellings query die u verzendt, zoals ' hoe ver is de mountaine? '. In het Engels `mountain` is, met een `n` , de juiste spelling.

2. LUIS reageert met een JSON-resultaat voor `How far is the mountain?` . Als Bing Spellingcontrole-API V7 een verkeerde spelling detecteert, `query` bevat het veld in het JSON-antwoord van de Luis-app de oorspronkelijke query en `alteredQuery` bevat het veld de gecorrigeerde query die wordt verzonden naar Luis.

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

Als u de Bing Search API V7-service niet wilt gebruiken, moet u de juiste en onjuiste spelling toevoegen.

Twee oplossingen zijn:

* Voor beeld van een label uitingen die de verschillende spellingen hebben, zodat LUIS de juiste spelling en type fouten kan ontdekken. Voor deze optie is meer inspanning vereist dan het gebruik van een spelling controle.
* Een woordgroepen lijst maken met alle variaties van het woord. Met deze oplossing hoeft u geen label te maken voor de woord variaties in het voor beeld uitingen.


> [!div class="nextstepaction"]
> [Meer informatie over voorbeeld uitingen](./luis-how-to-add-entities.md)