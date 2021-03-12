---
title: Context filter met behulp van meta gegevens
titleSuffix: Azure Cognitive Services
description: QnA Maker QnA-paren worden gefilterd op meta gegevens.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8f65ca9386963824f0cb740f587de83c9dec7f78
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103022100"
---
# <a name="filter-responses-with-metadata"></a>Antwoorden filteren met meta gegevens

Met QnA Maker kunt u meta gegevens, in de vorm van sleutel-en waardeparen, toevoegen aan uw paren van vragen en antwoorden. U kunt deze informatie vervolgens gebruiken om de resultaten te filteren op gebruikers query's en om aanvullende informatie op te slaan die kan worden gebruikt bij opvolgings gesprekken.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Vragen en antwoorden opslaan met een QnA-entiteit

Het is belang rijk om te begrijpen hoe QnA Maker de vraag-en antwoord gegevens opslaat. In de volgende afbeelding ziet u een QnA-entiteit:

![Afbeelding van een QnA-entiteit](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Elke QnA-entiteit heeft een unieke en permanente ID. U kunt de ID gebruiken om updates te maken voor een bepaalde QnA-entiteit.

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Meta gegevens gebruiken voor het filteren van antwoorden op aangepaste labels voor meta gegevens

Door meta gegevens toe te voegen, kunt u de antwoorden filteren op deze meta gegevenslabels. Voeg de kolom meta gegevens toe vanuit het menu **weergave opties** . Voeg Meta gegevens aan uw Knowledge Base toe door het **+** pictogram meta gegevens te selecteren om een meta gegevens paar toe te voegen. Dit paar bestaat uit één sleutel en één waarde.

![Scherm afbeelding van het toevoegen van meta gegevens](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Resultaten filteren met strictFilters voor labels van meta gegevens

Houd rekening met de vraag van de gebruiker ' wanneer is dit hotel gesloten? ', waarbij de intentie wordt geïmpliceerd voor het restaurant ' Paradise '.

Omdat de resultaten alleen vereist zijn voor het restaurant "Paradise", kunt u een filter instellen in de GenerateAnswer-aanroep voor de meta gegevens "naam restaurant". In het volgende voor beeld ziet u dit:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [ { "name": "restaurant", "value": "paradise"}]
}
```

### <a name="logical-and-by-default"></a>Logische en standaard

Als u verschillende filters voor meta gegevens wilt combi neren in de query, voegt u de extra filters voor meta gegevens toe aan de matrix van de `strictFilters` eigenschap. Standaard worden de waarden logisch gecombineerd (en). Een logische combi natie vereist dat alle filters overeenkomen met de QnA-paren zodat het paar in het antwoord wordt geretourneerd.

Dit komt overeen met het gebruik `strictFiltersCompoundOperationType` van de eigenschap met de waarde van `AND` .

### <a name="logical-or-using-strictfilterscompoundoperationtype-property"></a>Logische of met behulp van de eigenschap strictFiltersCompoundOperationType

Bij het combi neren van verschillende filters voor meta gegevens kunt u, als u alleen een of meer bevindt met een van de filteringen, de `strictFiltersCompoundOperationType` eigenschap gebruiken met de waarde van `OR` .

Op deze manier kan uw Knowledge Base antwoorden retour neren als er filters overeenkomen, maar geen antwoorden zonder meta gegevens retour neren.

```json
{
    "question": "When do facilities in this hotel close?",
    "top": 1,
    "strictFilters": [
      { "name": "type","value": "restaurant"},
      { "name": "type", "value": "bar"},
      { "name": "type", "value": "poolbar"}
    ],
    "strictFiltersCompoundOperationType": "OR"
}
```

### <a name="metadata-examples-in-quickstarts"></a>Voor beelden van meta gegevens in Quick starts

Meer informatie over meta gegevens vindt u in de QnA Maker portal voor meta gegevens:
* [Creatie: metagegevens toevoegen aan QnA-paar](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Queryvoorspelling: antwoorden filteren op metagegevens](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Vraag-en antwoord resultaten gebruiken om de discussie context te blijven

Het antwoord op de GenerateAnswer bevat de bijbehorende meta gegevens van de overeenkomende vraag en het antwoord paar. U kunt deze informatie in uw client toepassing gebruiken om de context van de vorige conversatie op te slaan voor gebruik in latere conversaties.

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw Knowledge Base analyseren](../How-to/get-analytics-knowledge-base.md)
