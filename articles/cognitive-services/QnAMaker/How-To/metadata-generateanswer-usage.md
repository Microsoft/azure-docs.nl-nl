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
ms.openlocfilehash: 7e8d1b13dfd802df820bea4015e411dbb85540ba
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011422"
---
# <a name="get-an-answer-with-the-generateanswer-api"></a>Een antwoord krijgen met de GenerateAnswer-API

Als u het voorspelde antwoord wilt ontvangen op de vraag van een gebruiker, gebruikt u de GenerateAnswer-API. Wanneer u een Knowledge Base publiceert, kunt u informatie bekijken over het gebruik van deze API op de pagina **publiceren** . U kunt de API ook configureren om antwoorden te filteren op basis van meta gegevenslabels en de Knowledge Base te testen op het eind punt met de teken reeks parameter test query.

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

## <a name="return-precise-answers"></a>Nauw keurige antwoorden retour neren

### <a name="generate-answer-api"></a>Antwoord-API genereren 

De gebruiker kan [nauw keurige antwoorden](../reference-precise-answering.md) inschakelen wanneer de door QnA Maker beheerde resource wordt gebruikt. De para meter answerSpanRequest moet worden bijgewerkt voor dezelfde.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3,
    "answerSpanRequest": {
        "enable": true,
        "topAnswersWithSpan": 1
    }
}
```

De gebruikers kunnen er ook voor kiezen om nauw keurige antwoorden uit te scha kelen door de para meter answerSpanRequest niet in te stellen.

```json
{
    "question": "How long it takes to charge surface pro 4?",
    "top": 3
}
```
### <a name="bot-settings"></a>Bot-instellingen

Als u nauw keurige antwoord instellingen voor uw bot-service wilt configureren, navigeert u naar de app service-resource voor uw bot. Vervolgens moet u de configuraties bijwerken door de volgende instelling toe te voegen.

- EnablePreciseAnswer
- DisplayPreciseAnswerOnly

|Weergaveconfiguratie|EnablePreciseAnswer|DisplayPreciseAnswerOnly|
|:--|--|--|
|Alleen nauw keurige antwoorden|true|true|
|Alleen lange antwoorden|onjuist|onjuist|
|Zowel lange als nauw keurige antwoorden|waar|onjuist|

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
> [!div class="nextstepaction"]
> [Betrouwbaarheids Score](../Concepts/confidence-score.md)
