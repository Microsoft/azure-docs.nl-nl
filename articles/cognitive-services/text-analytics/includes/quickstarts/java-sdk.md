---
title: 'Quickstart: Text Analytics v3-clientbibliotheek voor Java | Microsoft Docs'
description: Aan de slag met de v3 Text Analytics-clientbibliotheek voor Java.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.custom: devx-track-java
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: c90dc8fdee0e7b44386c5e97a0cc31665ee0e238
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445006"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Preview van versie 3.1](#tab/version-3-1)

[Referentiedocumentatie](/java/api/overview/azure/ai-textanalytics-readme?preserve-view=true&view=azure-java-preview) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.1.0-beta.3/sdk/textanalytics/azure-ai-textanalytics) | [Pakket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.3) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.1.0-beta.3/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

[Referentiedocumentatie](/java/api/overview/azure/ai-textanalytics-readme) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-java/blob/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics) | [Pakket](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/azure-ai-textanalytics_5.0.0/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) met versie 8 of hoger
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Een Text Analytics-resource maken"  target="_blank">maakt u een Text Analytics-resource </a> in de Azure-portal om uw sleutel en eindpunt op te halen.  Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Text Analytics-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Als u de functie Analyseren wilt gebruiken, hebt u een Text Analytics-resource in de prijscategorie Standard (S) nodig.

## <a name="setting-up"></a>Instellen

### <a name="add-the-client-library"></a>De clientbibliotheek toevoegen

# <a name="version-31-preview"></a>[Preview van versie 3.1](#tab/version-3-1)

Maak een Maven-project in uw favoriete IDE of ontwikkelomgeving. Voeg vervolgens de volgende afhankelijkheid toe aan het *pom.xml*-bestand van uw project. U kunt de implementatiesyntaxis [voor andere compilatieprogramma's](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.1.0-beta.1) online vinden.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.1.0-beta.3</version>
    </dependency>
</dependencies>
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een Maven-project in uw favoriete IDE of ontwikkelomgeving. Voeg vervolgens de volgende afhankelijkheid toe aan het *pom.xml*-bestand van uw project. U kunt de implementatiesyntaxis [voor andere compilatieprogramma's](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.0.0) online vinden.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>5.0.0</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Wilt u het volledige quickstartcodebestand ineens weergeven? U kunt het vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), dat de codevoorbeelden in deze quickstart bevat. 

---

Maak een Java-bestand met de naam `TextAnalyticsSamples.java`. Open het bestand en voeg de volgende `import`-instructies toe:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

Voeg in het Java-bestand een nieuwe klasse toe en voeg de sleutel en het eindpunt van uw Azure-resource toe, zoals hieronder wordt weergegeven.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Voeg de volgende hoofdmethode aan de klasse toe. U definieert de methoden die hier later worden aangeroepen.

# <a name="version-31-preview"></a>[Versie 3.1 (preview)](#tab/version-3-1)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisWithOpinionMiningExample(client)
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    recognizePiiEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
        AnalyzeOperationExample(client)
}
```

---


## <a name="object-model"></a>Objectmodel

De Text Analytics-client is een `TextAnalyticsClient`-object dat wordt geverifieerd bij Azure met behulp van uw sleutel. De client biedt functies om tekst als afzonderlijke tekenreeksen of als een batch te accepteren. U kunt tekst synchroon of asynchroon naar de API verzenden. Het antwoordobject bevat de analyse-informatie voor elk document dat u verzendt. 

## <a name="code-examples"></a>Codevoorbeelden

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis) 
* [Meninganalyse](#opinion-mining)
* [Taaldetectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner)
* [Entiteiten koppelen](#entity-linking)
* [Sleuteltermextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

Maak een methode voor het instantiëren van het `TextAnalyticsClient`-object met de sleutel en het eindpunt voor uw Text Analytics-resource. Dit voorbeeld is hetzelfde voor versie 3.0 en 3.1 van de API.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .credential(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```


In de `main()`-methode van uw programma roept u de verificatiemethode op om de client te instantiëren.

## <a name="sentiment-analysis"></a>Sentimentanalyse

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

> [!NOTE]
> In versie `3.1`:
> * Sentimentanalyse bevat meninganalyse, wat een optionele markering is. 
> * Meninganalyse bevat sentimenten op het niveau van aspecten en meningen. 

Maak een nieuwe functie met de naam `sentimentAnalysisExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `analyzeSentiment()`-functie aan. Het geretourneerde `AnalyzeSentimentResult`-object bevat `documentSentiment` en `sentenceSentiments` indien succesvol, of een `errorMessage` als dat niet het geval is. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

### <a name="opinion-mining"></a>Meninganalyse

Als u sentimentanalyse wilt uitvoeren met meninganalyse, maakt u een nieuwe functie met de naam `sentimentAnalysisWithOpinionMiningExample()` die de client gaat gebruiken die u eerder hebt gemaakt, en roept u de functie `analyzeSentiment()` aan met de instelling `AnalyzeSentimentOptions` voor het optieobject. Het geretourneerde `AnalyzeSentimentResult`-object bevat `documentSentiment` en `sentenceSentiments` indien succesvol, of een `errorMessage` als dat niet het geval is. 


```java
static void sentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    // The document that needs be analyzed.
    String document = "Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.";

    System.out.printf("Document = %s%n", document);

    AnalyzeSentimentOptions options = new AnalyzeSentimentOptions().setIncludeOpinionMining(true);
    final DocumentSentiment documentSentiment = client.analyzeSentiment(document, "en", options);
    SentimentConfidenceScores scores = documentSentiment.getConfidenceScores();
    System.out.printf(
            "Recognized document sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
            documentSentiment.getSentiment(), scores.getPositive(), scores.getNeutral(), scores.getNegative());

    documentSentiment.getSentences().forEach(sentenceSentiment -> {
        SentimentConfidenceScores sentenceScores = sentenceSentiment.getConfidenceScores();
        System.out.printf("\tSentence sentiment: %s, positive score: %f, neutral score: %f, negative score: %f.%n",
                sentenceSentiment.getSentiment(), sentenceScores.getPositive(), sentenceScores.getNeutral(), sentenceScores.getNegative());
        sentenceSentiment.getMinedOpinions().forEach(minedOpinions -> {
            AspectSentiment aspectSentiment = minedOpinions.getAspect();
            System.out.printf("\t\tAspect sentiment: %s, aspect text: %s%n", aspectSentiment.getSentiment(),
                    aspectSentiment.getText());
            SentimentConfidenceScores aspectScores = aspectSentiment.getConfidenceScores();
            System.out.printf("\t\tAspect positive score: %f, negative score: %f.%n",
                    aspectScores.getPositive(), aspectScores.getNegative());
            for (OpinionSentiment opinionSentiment : minedOpinions.getOpinions()) {
                System.out.printf("\t\t\t'%s' opinion sentiment because of \"%s\". Is the opinion negated: %s.%n",
                        opinionSentiment.getSentiment(), opinionSentiment.getText(), opinionSentiment.isNegated());
                SentimentConfidenceScores opinionScores = opinionSentiment.getConfidenceScores();
                System.out.printf("\t\t\tOpinion positive score: %f, negative score: %f.%n",
                        opinionScores.getPositive(), opinionScores.getNegative());
            }
        });
    });
}
```

### <a name="output"></a>Uitvoer

```console
Document = Bad atmosphere. Not close to plenty of restaurants, hotels, and transit! Staff are not friendly and helpful.
Recognized document sentiment: negative, positive score: 0.010000, neutral score: 0.140000, negative score: 0.850000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Aspect sentiment: negative, aspect text: atmosphere
        Aspect positive score: 0.010000, negative score: 0.990000.
            'negative' opinion sentiment because of "bad". Is the opinion negated: false.
            Opinion positive score: 0.010000, negative score: 0.990000.
    Sentence sentiment: negative, positive score: 0.020000, neutral score: 0.440000, negative score: 0.540000.
    Sentence sentiment: negative, positive score: 0.000000, neutral score: 0.000000, negative score: 1.000000.
        Aspect sentiment: negative, aspect text: Staff
        Aspect positive score: 0.000000, negative score: 1.000000.
            'negative' opinion sentiment because of "friendly". Is the opinion negated: true.
            Opinion positive score: 0.000000, negative score: 1.000000.
            'negative' opinion sentiment because of "helpful". Is the opinion negated: true.
            Opinion positive score: 0.000000, negative score: 1.000000.

Process finished with exit code 0
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een nieuwe functie met de naam `sentimentAnalysisExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `analyzeSentiment()`-functie aan. Het geretourneerde `AnalyzeSentimentResult`-object bevat `documentSentiment` en `sentenceSentiments` indien succesvol, of een `errorMessage` als dat niet het geval is. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
        }
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

---

## <a name="language-detection"></a>Taaldetectie

Maak een nieuwe functie met de naam `detectLanguageExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `detectLanguage()`-functie aan. Het geretourneerde `DetectLanguageResult`-object bevat een primaire taal, een lijst met andere talen die zijn gedetecteerd, indien succesvol, of een `errorMessage` als dat niet het geval is. Dit voorbeeld is hetzelfde voor versie 3.0 en 3.1 van de API.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt de parameter `countryHint` gebruiken om een landnummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `countryHint = ""`. Als u een andere standaardwaarde wilt instellen, stelt u eigenschap `TextAnalyticsClientOptions.DefaultCountryHint` in en geeft u deze door tijdens de initialisatie van de client.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getConfidenceScore());
}
```

### <a name="output"></a>Uitvoer

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```

## <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

> [!NOTE]
> In versie `3.1`:
> * NER omvat afzonderlijke methoden voor het detecteren van persoonlijke gegevens. 
> * Entiteitskoppeling is een aanvraag die los staat van NER.

Maak een nieuwe functie met de naam `recognizeEntitiesExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `recognizeEntities()`-functie aan. Het geretourneerde `CategorizedEntityCollection`-object bevat een lijst met `CategorizedEntity` indien succesvol, of een `errorMessage` als dat niet het geval is.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s, offset: %s, length: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore(),
            entity.getOffset(),
            entity.getLength());
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61, offset: 8, length: 4.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82, offset: 16, length: 7.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8, offset: 24, length: 9.
```

### <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe functie met de naam `recognizeLinkedEntitiesExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `recognizeLinkedEntities()`-functie aan. Het geretourneerde `LinkedEntityCollection`-object bevat een lijst met `LinkedEntity` indien succesvol, of een `errorMessage` als dat niet het geval is. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity`-object als een lijst met `LinkedEntityMatch`-objecten.


```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f, Offset: %s, Length: %s%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore(),
            linkedEntityMatch.getOffset(),
            linkedEntityMatch.getLength());
        }
    }
}
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55, Offset: 9, Length: 0
Text: Microsoft, Score: 0.55, Offset: 9, Length: 150
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63, Offset: 10, Length: 25
Text: Gates, Score: 0.63, Offset: 5, Length: 161
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60, Offset: 10, Length: 40
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32, Offset: 7, Length: 54
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33, Offset: 5, Length: 89
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88, Offset: 11, Length: 116
```


### <a name="personally-identifiable-information-recognition"></a>Herkenning van herkenbare, persoonlijke gegevens

Maak een nieuwe functie met de naam `recognizePiiEntitiesExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `recognizePiiEntities()`-functie aan. Het geretourneerde `PiiEntityCollection`-object bevat een lijst met `PiiEntity` indien succesvol, of een `errorMessage` als dat niet het geval is. Het bevat ook de geredigeerde tekst, die bestaat uit de invoertekst waarbij alle herkenbare entiteiten worden vervangen door `*****`.

```java
static void recognizePiiEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String document = "My SSN is 859-98-0987";
    PiiEntityCollection piiEntityCollection = client.recognizePiiEntities(document);
    System.out.printf("Redacted Text: %s%n", piiEntityCollection.getRedactedText());
    piiEntityCollection.forEach(entity -> System.out.printf(
        "Recognized Personally Identifiable Information entity: %s, entity category: %s, entity subcategory: %s,"
            + " confidence score: %f.%n",
        entity.getText(), entity.getCategory(), entity.getSubcategory(), entity.getConfidenceScore()));
}
```

### <a name="output"></a>Uitvoer

```console
Redacted Text: My SSN is ***********
Recognized Personally Identifiable Information entity: 859-98-0987, entity category: U.S. Social Security Number (SSN), entity subcategory: null, confidence score: 0.650000.
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

> [!NOTE]
> In versie `3.0`:
> * NER omvat afzonderlijke methoden voor het detecteren van persoonlijke gegevens. 
> * Entiteitskoppeling is een aanvraag die los staat van NER.

Maak een nieuwe functie met de naam `recognizeEntitiesExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `recognizeEntities()`-functie aan. Het geretourneerde `CategorizedEntityCollection`-object bevat een lijst met `CategorizedEntity` indien succesvol, of een `errorMessage` als dat niet het geval is.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubcategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized entity: trip, entity category: Event, entity sub-category: null, score: 0.61.
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.82.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

### <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe functie met de naam `recognizeLinkedEntitiesExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `recognizeLinkedEntities()`-functie aan. Het geretourneerde `LinkedEntityCollection`-object bevat een lijst met `LinkedEntity` indien succesvol, of een `errorMessage` als dat niet het geval is. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity`-object als een lijst met `LinkedEntityMatch`-objecten.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
            linkedEntity.getName(),
            linkedEntity.getDataSourceEntityId(),
            linkedEntity.getUrl(),
            linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
            linkedEntityMatch.getText(),
            linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.88
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.63
Text: Gates, Score: 0.63
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.60
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.55
Text: Microsoft, Score: 0.55
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.32
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.33
```

---

## <a name="key-phrase-extraction"></a>Sleuteltermextractie

Maak een nieuwe functie met de naam `extractKeyPhrasesExample()`, waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende `extractKeyPhrases()`-functie aan. Het geretourneerde `ExtractKeyPhraseResult`-object bevat een lijst met sleuteltermen indien succesvol, of een `errorMessage` als dat niet het geval is. Dit voorbeeld is hetzelfde voor versie 3.0 en 3.1 van de API.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Uitvoer

```console
Recognized phrases: 
cat
veterinarian
```
---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>De API asynchroon gebruiken met de bewerking Analyseren

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Maak een nieuwe functie met de naam `analyzeOperationExample()`. Deze roept de functie `beginAnalyzeTasks()` aan. Het resultaat is een langdurige bewerking waarvan de resultaten worden gepolld.

```java
static void analyzeOperationExample(TextAnalyticsClient client)
{
        List<TextDocumentInput> documents = Arrays.asList(
                        new TextDocumentInput("0", "Microsoft was founded by Bill Gates and Paul Allen.")
                        );

        SyncPoller<TextAnalyticsOperationResult, PagedIterable<AnalyzeTasksResult>> syncPoller =
                        client.beginAnalyzeTasks(documents,
                                        new AnalyzeTasksOptions().setDisplayName("{tasks_display_name}")
                                                        .setEntitiesRecognitionTasks(Arrays.asList(new EntitiesTask())),
                                        Context.NONE);

        syncPoller.waitForCompletion();
        PagedIterable<AnalyzeTasksResult> result = syncPoller.getFinalResult();

        result.forEach(analyzeJobState -> {
                System.out.printf("Job Display Name: %s, Job ID: %s.%n", analyzeJobState.getDisplayName(),
                                analyzeJobState.getJobId());
                System.out.printf("Total tasks: %s, completed: %s, failed: %s, in progress: %s.%n",
                                analyzeJobState.getTotal(), analyzeJobState.getCompleted(), analyzeJobState.getFailed(),
                                analyzeJobState.getInProgress());

                List<RecognizeEntitiesResultCollection> entityRecognitionTasks =
                                analyzeJobState.getEntityRecognitionTasks();
                if (entityRecognitionTasks != null) {
                        entityRecognitionTasks.forEach(taskResult -> {
                                // Recognized entities for each of documents from a batch of documents
                                AtomicInteger counter = new AtomicInteger();
                                for (RecognizeEntitiesResult entitiesResult : taskResult) {
                                        System.out.printf("%n%s%n", documents.get(counter.getAndIncrement()));
                                        if (entitiesResult.isError()) {
                                                // Erroneous document
                                                System.out.printf("Cannot recognize entities. Error: %s%n",
                                                                entitiesResult.getError().getMessage());
                                        } else {
                                                // Valid document
                                                entitiesResult.getEntities().forEach(entity -> System.out.printf(
                                                                "Recognized entity: %s, entity category: %s, entity subcategory: %s, "
                                                                                + "confidence score: %f.%n",
                                                                entity.getText(), entity.getCategory(), entity.getSubcategory(),
                                                                entity.getConfidenceScore()));
                                        }
                                }
                        });
                }
        });
    }
```

Nadat u dit voorbeeld aan uw toepassing hebt toegevoegd, roept u deze aan in de methode `main()`.

```java
analyzeOperationExample(client);
```

### <a name="output"></a>Uitvoer

```console
Job Display Name: {tasks_display_name}, Job ID: 84fd4db4-0734-47ec-b263-ac5451e83f2a_637432416000000000.
Total tasks: 1, completed: 1, failed: 0, in progress: 0.

Text = Microsoft was founded by Bill Gates and Paul Allen., Id = 0, Language = null
Recognized entity: Microsoft, entity category: Organization, entity subcategory: null, confidence score: 0.960000.
Recognized entity: Bill Gates, entity category: Person, entity subcategory: null, confidence score: 1.000000.
Recognized entity: Paul Allen, entity category: Person, entity subcategory: null, confidence score: 0.990000.
```

U kunt ook de bewerking Analyseren ook gebruiken om persoonsgegevens en extractie van sleutelwoorden te detecteren. Zie het [analysevoorbeeld](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro) (Engelstalig) op GitHub.

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Deze functie is niet beschikbaar in versie 3.0.

---
