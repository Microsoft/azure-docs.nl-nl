---
title: 'Quickstart: Text Analytics v3-clientbibliotheek voor Node.js | Microsoft Docs'
description: Ga aan de slag met de v3 Text Analytics-clientbibliotheek voor Node.js.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.custom: devx-track-js
ms.openlocfilehash: 72ca331546d53f85ca82f33ec6a02558d91f1c1e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765061"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

[v3-referentiedocumentatie](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-preview) | [broncode voor v3-bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3-pakket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

[v3-referentiedocumentatie](/javascript/api/overview/azure/ai-text-analytics-readme) | [broncode voor v3-bibliotheek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3-pakket (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De huidige versie van [Node.js](https://nodejs.org/).
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Een Text Analytics-resource maken"  target="_blank">maakt u een Text Analytics-resource </a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Text Analytics-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Als u de functie Analyseren wilt gebruiken, hebt u een Text Analytics-resource in de prijscategorie Standard (S) nodig.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp 

cd myapp
```

Voer de opdracht `npm init` uit om een knooppunttoepassing te maken met een `package.json`-bestand. 

```console
npm init
```
### <a name="install-the-client-library"></a>De clientbibliotheek installeren

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

De `@azure/ai-text-analytics` NPM-pakketten installeren:

```console
npm install --save @azure/ai-text-analytics@5.1.0-beta.5
```

> [!TIP]
> Wilt u het volledige quickstartcodebestand ineens weergeven? Ga dan naar [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), waar u de codevoorbeelden uit deze quickstart kunt vinden. 


# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

De `@azure/ai-text-analytics` NPM-pakketten installeren:

```console
npm install --save @azure/ai-text-analytics@5.0.0
```

> [!TIP]
> Wilt u het volledige quickstartcodebestand ineens weergeven? U kunt het vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js), dat de codevoorbeelden in deze quickstart bevat. 

---

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.
Maak een bestand met de naam `index.js` en voeg het volgende toe:

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
```

---

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

## <a name="object-model"></a>Objectmodel

De Text Analytics-client is een `TextAnalyticsClient`-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De client biedt verschillende methoden voor het analyseren van tekst, zoals één tekenreeks of een batch.

Tekst wordt naar de API verzonden als een lijst met `documents`. Dit zijn `dictionary`-objecten die een combinatie van de kenmerken `id`, `text` en `language` bevatten, afhankelijk van de gebruikte methode. Met het kenmerk `text` wordt de tekst die moet worden geanalyseerd, opgeslagen in de oorsprong `language`, en `id` kan elke waarde hebben. 

Het antwoordobject is een lijst met de analyse-informatie voor elk document. 

## <a name="code-examples"></a>Codevoorbeelden

* [Clientauthenticatie](#client-authentication)
* [Sentimentanalyse](#sentiment-analysis) 
* [Meninganalyse](#opinion-mining)
* [Taaldetectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner)
* [Entiteiten koppelen](#entity-linking)
* Persoonlijk identificeerbare informatie
* [Sleuteltermextractie](#key-phrase-extraction)

## <a name="client-authentication"></a>Clientauthenticatie

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een nieuw `TextAnalyticsClient`-object met uw sleutel en eindpunt als parameters.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een nieuw `TextAnalyticsClient`-object met uw sleutel en eindpunt als parameters.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));
```

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `analyzeSentiment()` van de client aan en haal het geretourneerde `SentimentBatchResult`-object op. Blader door de lijst met resultaten en druk voor elk document de id en het gevoel op documentniveau met betrouwbaarheidsscores af. Voor elk document bevat het resultaat gevoel op zinsniveau, samen met offset, lengte en betrouwbaarheidsscores.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

### <a name="opinion-mining"></a>Meninganalyse

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren als u een sentimentanalyse wilt uitvoeren met meninganalyse. Roep de methode `analyzeSentiment()` van de client aan waarbij u optie `includeOpinionMining: true` toevoegt en haal het geretourneerde `SentimentBatchResult`-object op. Blader door de lijst met resultaten en druk voor elk document de id en het gevoel op documentniveau met betrouwbaarheidsscores af. Voor elk document bevat het resultaat niet alleen sentiment op zinniveau zoals hierboven, maar ook sentiment op aspect- en meningniveau.

```javascript
async function sentimentAnalysisWithOpinionMining(client){

  const sentimentInput = [
    {
      text: "The food and service were unacceptable, but the concierge were nice",
      id: "0",
      language: "en"
    }
  ];
  const results = await client.analyzeSentiment(sentimentInput, { includeOpinionMining: true });

  for (let i = 0; i < results.length; i++) {
    const result = results[i];
    console.log(`- Document ${result.id}`);
    if (!result.error) {
      console.log(`\tDocument text: ${sentimentInput[i].text}`);
      console.log(`\tOverall Sentiment: ${result.sentiment}`);
      console.log("\tSentiment confidence scores:", result.confidenceScores);
      console.log("\tSentences");
      for (const { sentiment, confidenceScores, opinions } of result.sentences) {
        console.log(`\t- Sentence sentiment: ${sentiment}`);
        console.log("\t  Confidence scores:", confidenceScores);
        console.log("\t  Mined opinions");
        for (const { target, assessments } of opinions) {
          console.log(`\t\t- Target text: ${target.text}`);
          console.log(`\t\t  Target sentiment: ${target.sentiment}`);
          console.log("\t\t  Target confidence scores:", target.confidenceScores);
          console.log("\t\t  Target assessments");
          for (const { text, sentiment } of assessments) {
            console.log(`\t\t\t- Text: ${text}`);
            console.log(`\t\t\t  Sentiment: ${sentiment}`);
          }
        }
      }
    } else {
      console.error(`\tError: ${result.error}`);
    }
  }
}
sentimentAnalysisWithOpinionMining(textAnalyticsClient)
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
- Document 0
        Document text: The food and service were unacceptable, but the concierge were nice
        Overall Sentiment: positive
        Sentiment confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
        Sentences
        - Sentence sentiment: positive
          Confidence scores: { positive: 0.84, neutral: 0, negative: 0.16 }
          Mined opinions
                - Target text: food
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: service
                  Target sentiment: negative
                  Target confidence scores: { positive: 0.01, negative: 0.99 }
                  Target assessments
                        - Text: unacceptable
                          Sentiment: negative
                - Target text: concierge
                  Target sentiment: positive
                  Target confidence scores: { positive: 1, negative: 0 }
                  Target assessments
                        - Text: nice
                          Sentiment: positive
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `analyzeSentiment()` van de client aan en haal het geretourneerde `SentimentBatchResult`-object op. Blader door de lijst met resultaten en druk voor elk document de id en het gevoel op documentniveau met betrouwbaarheidsscores af. Voor elk document bevat het resultaat gevoel op zinsniveau, samen met offset, lengte en betrouwbaarheidsscores.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

---

## <a name="language-detection"></a>Taaldetectie

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `detectLanguage()` van de client aan en haal het geretourneerde `DetectLanguageResultCollection` op. Blader vervolgens door de resultaten en druk voor elk document de id af, met de bijbehorende primaire taal.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Primary Language French
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `detectLanguage()` van de client aan en haal het geretourneerde `DetectLanguageResultCollection` op. Blader vervolgens door de resultaten en druk voor elk document de id af, met de bijbehorende primaire taal.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Primary Language French
```

---

## <a name="named-entity-recognition-ner"></a>NER (Herkenning van benoemde entiteiten)

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

> [!NOTE]
> In versie `3.1`:
> * Entiteitskoppeling is een aanvraag die los staat van NER.

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `recognizeEntities()` van de client aan en haal het `RecognizeEntitiesResult`-object op. Blader door de lijst met resultaten, en druk naam, type, subtype, offset, lengte en score voor de entiteit af.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entiteiten koppelen

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `recognizeLinkedEntities()` van de client aan en haal het `RecognizeLinkedEntitiesResult`-object op. Blader door de lijst met resultaten, en druk naam, id, gegevensbron, URL en overeenkomsten voor de entiteit af. Elk object in de matrix `matches` bevat offset, lengte en score voor deze overeenkomst.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```

### <a name="personally-identifying-information-pii-recognition"></a>Herkenning van persoonlijke gegevens

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `recognizePiiEntities()` van de client aan en haal het `RecognizePIIEntitiesResult`-object op. Blader door de lijst met resultaten, en druk naam, type en score voor de entiteit af.

```javascript
async function piiRecognition(client) {

    const documents = [
        "The employee's phone number is (555) 555-5555."
    ];

    const results = await client.recognizePiiEntities(documents, "en");
    for (const result of results) {
        if (result.error === undefined) {
            console.log("Redacted Text: ", result.redactedText);
            console.log(" -- Recognized PII entities for input", result.id, "--");
            for (const entity of result.entities) {
                console.log(entity.text, ":", entity.category, "(Score:", entity.confidenceScore, ")");
            }
        } else {
            console.error("Encountered an error:", result.error);
        }
    }
}
piiRecognition(textAnalyticsClient)
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
Redacted Text:  The employee's phone number is **************.
 -- Recognized PII entities for input 0 --
(555) 555-5555 : Phone Number (Score: 0.8 )
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

> [!NOTE]
> In versie `3.0`:
> * Entiteitskoppeling is een aanvraag die los staat van NER.

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `recognizeEntities()` van de client aan en haal het `RecognizeEntitiesResult`-object op. Blader door de lijst met resultaten, en druk naam, type, subtype, offset, lengte en score voor de entiteit af.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```

### <a name="entity-linking"></a>Entiteiten koppelen

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `recognizeLinkedEntities()` van de client aan en haal het `RecognizeLinkedEntitiesResult`-object op. Blader door de lijst met resultaten, en druk naam, id, gegevensbron, URL en overeenkomsten voor de entiteit af. Elk object in de matrix `matches` bevat offset, lengte en score voor deze overeenkomst.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.confidenceScore.toFixed(2)}`);
        })
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.88
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.63
                Text: Gates     Score: 0.63
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.60
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.55
                Text: Microsoft         Score: 0.55
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.32
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.33
```


---

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `extractKeyPhrases()` van de client aan en haal het geretourneerde `ExtractKeyPhrasesResult`-object op. Blader door de resultaten en druk voor elk document de id en gedetecteerde sleuteltermen af.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een matrix met tekenreeksen die het document bevat dat u wilt analyseren. Roep de methode `extractKeyPhrases()` van de client aan en haal het geretourneerde `ExtractKeyPhrasesResult`-object op. Blader door de resultaten en druk voor elk document de id en gedetecteerde sleuteltermen af.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Voer de code met `node index.js` uit in het consolevenster.

### <a name="output"></a>Uitvoer

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>De API asynchroon gebruiken met de bewerking Analyseren

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

[!INCLUDE [Analyze Batch Action pricing](../analyze-operation-pricing-caution.md)]

Maak een nieuwe functie met de naam `analyze_example()`. Deze roept de functie `beginAnalyze()` aan. Het resultaat is een langdurige bewerking waarvan de resultaten worden gepolld.

```javascript
async function analyze_example(client) {
  const documents = [
    "Microsoft was founded by Bill Gates and Paul Allen.",
  ];

  const actions = {
    recognizeEntitiesActions: [{ modelVersion: "latest" }],
  };
  const poller = await client.beginAnalyzeBatchActions(documents, actions, "en");

  console.log(
    `The analyze batch actions operation was created on ${poller.getOperationState().createdOn}`
  );
  console.log(
    `The analyze batch actions operation results will expire on ${
      poller.getOperationState().expiresOn
    }`
  );
  const resultPages = await poller.pollUntilDone();
  for await (const page of resultPages) {
    const entitiesAction = page.recognizeEntitiesResults[0];
    if (!entitiesAction.error) {
      for (const doc of entitiesAction.results) {
        console.log(`- Document ${doc.id}`);
        if (!doc.error) {
          console.log("\tEntities:");
          for (const entity of doc.entities) {
            console.log(`\t- Entity ${entity.text} of type ${entity.category}`);
          }
        } else {
          console.error("\tError:", doc.error);
        }
      }
    }
  }
}

analyze_example(textAnalyticsClient);
```

### <a name="output"></a>Uitvoer

```console
The analyze batch actions operation was created on Fri Mar 12 2021 09:53:49 GMT-0800 (Pacific Standard Time)
The analyze batch actions operation results will expire on Sat Mar 13 2021 09:53:49 GMT-0800 (Pacific Standard Time)
- Document 0
        Entities:
        - Entity Microsoft of type Organization
        - Entity Bill Gates of type Person
        - Entity Paul Allen of type Person
```

U kunt ook de analysebewerking gebruiken om PII te detecteren, gekoppelde entiteiten en sleuteltermextractie te herkennen. Zie de analysevoorbeelden voor [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/javascript) en [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/v5/typescript/src) (beide Engelstalig) op GitHub.

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Deze functie is niet beschikbaar in versie 3.0.

---

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```
