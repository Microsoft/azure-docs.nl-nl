---
title: Meta gegevens met GenerateAnswer-API-QnA Maker
titleSuffix: Azure Cognitive Services
description: Met QnA Maker kunt u meta gegevens, in de vorm van sleutel/waarde-paren, toevoegen aan uw vraag/antwoord-paren. U kunt de resultaten filteren op gebruikers query's en aanvullende informatie opslaan die kan worden gebruikt bij opvolgings gesprekken.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f92b8c4835e42e758085e0434ee926fbabce5a38
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98936232"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Een antwoord krijgen met de GenerateAnswer-API en meta gegevens

Als u het voorspelde antwoord wilt ontvangen op de vraag van een gebruiker, gebruikt u de GenerateAnswer-API. Wanneer u een Knowledge Base publiceert, kunt u informatie bekijken over het gebruik van deze API op de pagina **publiceren** . U kunt de API ook configureren om antwoorden te filteren op basis van meta gegevenslabels en de Knowledge Base te testen op het eind punt met de teken reeks parameter test query.

Met QnA Maker kunt u meta gegevens, in de vorm van sleutel-en waardeparen, toevoegen aan uw paren van vragen en antwoorden. U kunt deze informatie vervolgens gebruiken om de resultaten te filteren op gebruikers query's en om aanvullende informatie op te slaan die kan worden gebruikt bij opvolgings gesprekken. Zie de [Knowledge Base](../index.yml)voor meer informatie.

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Vragen en antwoorden opslaan met een QnA-entiteit

Het is belang rijk om te begrijpen hoe QnA Maker de vraag-en antwoord gegevens opslaat. In de volgende afbeelding ziet u een QnA-entiteit:

![Afbeelding van een QnA-entiteit](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Elke QnA-entiteit heeft een unieke en permanente ID. U kunt de ID gebruiken om updates te maken voor een bepaalde QnA-entiteit.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Vraag voor spellingen ophalen met de GenerateAnswer-API

U gebruikt de [GenerateAnswer-API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) in uw bot of toepassing om een query uit te geven op uw Knowledge Base met een gebruikers vraag, om het beste resultaat te krijgen van de vraag-en antwoord paren.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publiceren om GenerateAnswer-eind punt op te halen

Nadat u uw Knowledge Base hebt gepubliceerd vanuit de [QnA Maker Portal](https://www.qnamaker.ai)of met behulp van de [API](/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), kunt u de details van uw GenerateAnswer-eind punt ophalen.

U kunt als volgt uw eindpunt Details ophalen:
1. Meld u aan bij [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. Selecteer in **mijn Knowledge** bases de optie **code weer geven** voor uw Knowledge Base.
    ![Scherm opname van mijn Knowledge bases](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Haal de details van uw GenerateAnswer-eind punt op.

    # <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

    ![Scherm opname van eindpunt Details](../media/qnamaker-how-to-metadata-usage/view-code.png)

    # <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

    ![Scherm afbeelding van beheerde eindpunt gegevens](../media/qnamaker-how-to-metadata-usage/view-code-managed.png)

    ---

U kunt ook uw eindpunt gegevens ophalen via het tabblad **instellingen** van de Knowledge Base.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuratie van GenerateAnswer-aanvraag

U roept GenerateAnswer aan met een HTTP POST-aanvraag. Raadpleeg de [Quick](../quickstarts/quickstart-sdk.md#generate-an-answer-from-the-knowledge-base)starts voor voorbeeld code die laat zien hoe u GenerateAnswer aanroept.

De POST-aanvraag gebruikt:

* Vereiste [URI-para meters](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters)
* Vereiste header-eigenschap, `Authorization` ,,, voor beveiliging
* Vereiste [Eigenschappen van de hoofd tekst](/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto).

De GenerateAnswer-URL heeft de volgende indeling:

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Vergeet niet om de eigenschap HTTP-header van `Authorization` met een waarde van de teken reeks in te stellen `EndpointKey` met een spatie en vervolgens de eindpunt sleutel op de pagina **instellingen** .

Een voor beeld van een JSON-bericht ziet er als volgt uit:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 30,
    "rankerType": "" // values: QuestionOnly
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

Meer informatie over [rankerType](../concepts/best-practices.md#choosing-ranker-type).

De vorige JSON heeft alleen antwoorden aangevraagd die 30% of hoger zijn dan de drempel waarde.

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>GenerateAnswer-antwoord eigenschappen

Het [antwoord](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) is een JSON-object met alle informatie die u nodig hebt om het antwoord weer te geven en de volgende stap in het gesprek, indien beschikbaar, in te scha kelen.

```json
{
    "answers": [
        {
            "score": 38.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

De vorige JSON heeft gereageerd met een antwoord met een Score van 38,5%.

## <a name="use-qna-maker-with-a-bot-in-c"></a>QnA Maker gebruiken met een bot in C #

Het bot-Framework biedt toegang tot de eigenschappen van de QnA Maker met de [getAnswer-API](/dotnet/api/microsoft.bot.builder.ai.qna.qnamaker.getanswersasync#Microsoft_Bot_Builder_AI_QnA_QnAMaker_GetAnswersAsync_Microsoft_Bot_Builder_ITurnContext_Microsoft_Bot_Builder_AI_QnA_QnAMakerOptions_System_Collections_Generic_Dictionary_System_String_System_String__System_Collections_Generic_Dictionary_System_String_System_Double__):

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

metadata.Name = Constants.MetadataName.Intent;
metadata.Value = topIntent;
qnaOptions.StrictFilters = new Microsoft.Bot.Builder.AI.QnA.Metadata[] { metadata };
qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

De vorige JSON heeft alleen antwoorden aangevraagd die 30% of hoger zijn dan de drempel waarde.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>QnA Maker gebruiken met een bot in Node.js

Het bot-Framework biedt toegang tot de eigenschappen van de QnA Maker met de [getAnswer-API](/javascript/api/botbuilder-ai/qnamaker?preserve-view=true&view=botbuilder-ts-latest#generateanswer-string---undefined--number--number-):

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.30,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

De vorige JSON heeft alleen antwoorden aangevraagd die 30% of hoger zijn dan de drempel waarde.

<a name="metadata-example"></a>

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

## <a name="match-questions-only-by-text"></a>Alleen vragen vergelijken, op tekst

QnA Maker zoekt standaard naar vragen en antwoorden. Als u alleen vragen wilt doorzoeken, gebruikt u de `RankerType=QuestionOnly` in de hoofd tekst van de GenerateAnswer-aanvraag om een antwoord te genereren.

U kunt zoeken in de gepubliceerde KB met `isTest=false` of in de test KB met `isTest=true` .

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Veelvoorkomende HTTP-fouten

|Code|Uitleg|
|:--|--|
|2xx|Geslaagd|
|400|De para meters van de aanvraag zijn onjuist, wat betekent dat de vereiste para meters ontbreken, ongeldig of te groot zijn|
|400|De hoofd tekst van de aanvraag is onjuist, wat betekent dat de JSON ontbreekt, ongeldig is of te groot is|
|401|Ongeldige sleutel|
|403|Verboden: u hebt niet de juiste machtigingen|
|404|KB bestaat niet|
|410|Deze API is afgeschaft en is niet meer beschikbaar|

## <a name="next-steps"></a>Volgende stappen

De pagina **publiceren** bevat ook informatie over het [genereren van een antwoord](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md) met postman of krul.

> [!div class="nextstepaction"]
> [Analytische gegevens verkrijgen voor uw knowledge base](../how-to/get-analytics-knowledge-base.md)
