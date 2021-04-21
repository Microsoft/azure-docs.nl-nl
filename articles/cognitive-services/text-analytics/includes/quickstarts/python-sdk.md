---
author: aahill
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/19/2021
ms.author: aahi
ms.openlocfilehash: cee201c11d0415e1f63e7e6a9157b96a059503ba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765075"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

[v3.1-referentiedocumentatie](/python/api/azure-ai-textanalytics/azure.ai.textanalytics) | [Broncode voor v3.1-bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics) | [v3.1-pakket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3.1-voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

[v3-referentiedocumentatie](/python/api/azure-ai-textanalytics/azure.ai.textanalytics) | [Broncode voor v3-bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics) | [v3-pakket (PiPy)](https://pypi.org/project/azure-ai-textanalytics/) | [v3-voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Een Text Analytics-resource maken"  target="_blank">maakt u een Text Analytics-resource </a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Text Analytics-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Als u de functie Analyseren wilt gebruiken, hebt u een Text Analytics-resource in de prijscategorie Standard (S) nodig.

## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Na de installatie van Python kunt u de clientbibliotheek installeren met:

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

```console
pip install azure-ai-textanalytics --pre
```

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Ga dan naar [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), waar u de codevoorbeelden uit deze quickstart kunt vinden. 

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

```console
pip install --upgrade azure-ai-textanalytics
```

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Ga dan naar [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/TextAnalytics/python-v3-client-library.py), waar u de codevoorbeelden uit deze quickstart kunt vinden. 


---

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuw Python-bestand en maak variabelen voor het eindpunt en de abonnementssleutel in Azure van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```python
key = "<paste-your-text-analytics-key-here>"
endpoint = "<paste-your-text-analytics-endpoint-here>"
```


## <a name="object-model"></a>Objectmodel

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

De Text Analytics-client is een `TextAnalyticsClient`-object dat wordt geverifieerd bij Azure. De client biedt verschillende methoden voor het analyseren van tekst. 

Bij verwerking wordt de tekst naar de API verzonden als een lijst met `documents`, die een lijst met tekenreeksen, een lijst met een dicteerachtige weergave of een lijst met `TextDocumentInput/DetectLanguageInput` is. Een `dict-like`-object bevat een combinatie van `id`, `text` en `language/country_hint`. Met het kenmerk `text` wordt de tekst die moet worden geanalyseerd, opgeslagen in de oorsprong `country_hint`, en `id` kan elke waarde hebben. 

Het antwoordobject is een lijst met de analyse-informatie voor elk document. 

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

De Text Analytics-client is een `TextAnalyticsClient`-object dat met behulp van uw sleutel wordt geverifieerd bij Azure. De client biedt verschillende methoden voor het analyseren van tekst als een batch. 

Bij batchverwerking wordt tekst als een lijst met `documents` naar de API verzonden. De lijst bestaat uit `dictionary`-objecten die een combinatie van de kenmerken `id`, `text` en `language` bevatten, afhankelijk van de gebruikte methode. Met het kenmerk `text` wordt de tekst die moet worden geanalyseerd, opgeslagen in de oorsprong `language`, en `id` kan elke waarde hebben. 

Het antwoordobject is een lijst met de geanalyseerde informatie voor elk document. 

---

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Text Analytics-clientbibliotheek voor Python:

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Meninganalyse](#opinion-mining)
* [Taaldetectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner) 
* [Herkenning van persoonlijke gegevens](#personally-identifiable-information-recognition) 
* [Entiteiten koppelen](#entity-linking)
* [Sleuteltermextractie](#key-phrase-extraction)


# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

* [De client verifiëren](#authenticate-the-client)
* [Sentimentanalyse](#sentiment-analysis)
* [Taaldetectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner) 
* [Entiteiten koppelen](#entity-linking)
* [Sleuteltermextractie](#key-phrase-extraction)

---

## <a name="authenticate-the-client"></a>De client verifiëren

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een functie om het `TextAnalyticsClient`-object te instantiëren met uw `key` EN `endpoint` die hierboven zijn gemaakt. Maak vervolgens een nieuwe client. 

```python
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een functie om het `TextAnalyticsClient`-object te instantiëren met uw `key` EN `endpoint` die hierboven zijn gemaakt. Maak vervolgens een nieuwe client. Let op: `api_version=TextAnalyticsApiVersion.V3_0` moet worden gedefinieerd voor het gebruik van versie 3.0.

```python
# use this code if you're using SDK version is 5.0.0
from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential) 
    return text_analytics_client

client = authenticate_client()
```

Als u v5.1.0 van de clientbibliotheek hebt geïnstalleerd met behulp van `pip install azure-ai-textanalytics --pre`, kunt u v3.0 van de Text Analytics-API opgeven met de parameter `api_version` van de client. Gebruik de volgende methode `authenticate_client()` alleen als u client v5.1.0 of hoger hebt.

```python
# Only use the following code sample if you're using v5.1.0 of the client library, 
# and are looking to specify v3.0 of the Text Analytics API for your client
from azure.ai.textanalytics import TextAnalyticsClient, TextAnalyticsApiVersion
from azure.core.credentials import AzureKeyCredential
def authenticate_client():
   ta_credential = AzureKeyCredential(key)
   text_analytics_client = TextAnalyticsClient(
     endpoint=endpoint,
     credential=ta_credential,
     api_version=TextAnalyticsApiVersion.V3_0
   )
   
client = authenticate_client()
```

--- 

## <a name="sentiment-analysis"></a>Sentimentanalyse

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een nieuwe functie met de naam `sentiment_analysis_example()` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `analyze_sentiment()` aanroept. Het geretourneerde responsobject bevat het sentimentlabel en de score van het volledige invoerdocument, evenals een sentimentanalyse voor elke zin.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents=documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Uitvoer

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

## <a name="opinion-mining"></a>Meninganalyse

Als u een sentimentanalyse wilt uitvoeren met meninganalyse, maakt u een nieuwe functie met de naam `sentiment_analysis_with_opinion_mining_example()` die de client als argument gebruikt. Vervolgens wordt de functie `analyze_sentiment()` aangeroepen met de optie `show_opinion_mining=True`. Het geretourneerde antwoordobject bevat niet alleen het sentimentlabel en de score van het volledige invoerdocument een sentimentanalyse voor elke zin, maar ook sentimentanalyse op aspect- en meningniveau.


```python
def sentiment_analysis_with_opinion_mining_example(client):

    documents = [
        "The food and service were unacceptable, but the concierge were nice"
    ]

    result = client.analyze_sentiment(documents, show_opinion_mining=True)
    doc_result = [doc for doc in result if not doc.is_error]

    positive_reviews = [doc for doc in doc_result if doc.sentiment == "positive"]
    negative_reviews = [doc for doc in doc_result if doc.sentiment == "negative"]

    positive_mined_opinions = []
    mixed_mined_opinions = []
    negative_mined_opinions = []

    for document in doc_result:
        print("Document Sentiment: {}".format(document.sentiment))
        print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
            document.confidence_scores.positive,
            document.confidence_scores.neutral,
            document.confidence_scores.negative,
        ))
        for sentence in document.sentences:
            print("Sentence: {}".format(sentence.text))
            print("Sentence sentiment: {}".format(sentence.sentiment))
            print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
                sentence.confidence_scores.positive,
                sentence.confidence_scores.neutral,
                sentence.confidence_scores.negative,
            ))
            for mined_opinion in sentence.mined_opinions:
                target = mined_opinion.target
                print("......'{}' target '{}'".format(target.sentiment, target.text))
                print("......Target score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                    target.confidence_scores.positive,
                    target.confidence_scores.negative,
                ))
                for assessment in mined_opinion.assessments:
                    print("......'{}' assessment '{}'".format(assessment.sentiment, assessment.text))
                    print("......Assessment score:\n......Positive={0:.2f}\n......Negative={1:.2f}\n".format(
                        assessment.confidence_scores.positive,
                        assessment.confidence_scores.negative,
                    ))
            print("\n")
        print("\n")
          
sentiment_analysis_with_opinion_mining_example(client)
```

### <a name="output"></a>Uitvoer

```console
Document Sentiment: positive
Overall scores: positive=0.84; neutral=0.00; negative=0.16

Sentence: The food and service were unacceptable, but the concierge were nice
Sentence sentiment: positive
Sentence score:
Positive=0.84
Neutral=0.00
Negative=0.16

......'negative' target 'food'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'negative' target 'service'
......Target score:
......Positive=0.01
......Negative=0.99

......'negative' assessment 'unacceptable'
......Assessment score:
......Positive=0.01
......Negative=0.99

......'positive' target 'concierge'
......Target score:
......Positive=1.00
......Negative=0.00

......'positive' assessment 'nice'
......Assessment score:
......Positive=1.00
......Negative=0.00





Press any key to continue . . .

```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een nieuwe functie met de naam `sentiment_analysis_example()` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `analyze_sentiment()` aanroept. Het geretourneerde responsobject bevat het sentimentlabel en de score van het volledige invoerdocument, evenals een sentimentanalyse voor elke zin.


```python
def sentiment_analysis_example(client):

    documents = ["I had the best day of my life. I wish you were there with me."]
    response = client.analyze_sentiment(documents = documents)[0]
    print("Document Sentiment: {}".format(response.sentiment))
    print("Overall scores: positive={0:.2f}; neutral={1:.2f}; negative={2:.2f} \n".format(
        response.confidence_scores.positive,
        response.confidence_scores.neutral,
        response.confidence_scores.negative,
    ))
    for idx, sentence in enumerate(response.sentences):
        print("Sentence: {}".format(sentence.text))
        print("Sentence {} sentiment: {}".format(idx+1, sentence.sentiment))
        print("Sentence score:\nPositive={0:.2f}\nNeutral={1:.2f}\nNegative={2:.2f}\n".format(
            sentence.confidence_scores.positive,
            sentence.confidence_scores.neutral,
            sentence.confidence_scores.negative,
        ))
          
sentiment_analysis_example(client)
```

### <a name="output"></a>Uitvoer

```console
Document Sentiment: positive
Overall scores: positive=1.00; neutral=0.00; negative=0.00 

Sentence: I had the best day of my life.
Sentence 1 sentiment: positive
Sentence score:
Positive=1.00
Neutral=0.00
Negative=0.00

Sentence: I wish you were there with me.
Sentence 2 sentiment: neutral
Sentence score:
Positive=0.21
Neutral=0.77
Negative=0.02
```

---

## <a name="language-detection"></a>Taaldetectie

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een nieuwe functie met de naam `language_detection_example()` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `detect_language()` aanroept. Het geretourneerde responsobject bevat de gedetecteerde taal in `primary_language` als de aanroep is gelukt en een `error` als dat niet het geval is.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt de parameter `country_hint` gebruiken om een landnummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `country_hint : ""`. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Uitvoer

```console
Language:  French
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een nieuwe functie met de naam `language_detection_example()` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `detect_language()` aanroept. Het geretourneerde responsobject bevat de gedetecteerde taal in `primary_language` als de aanroep is gelukt en een `error` als dat niet het geval is.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt de parameter `country_hint` gebruiken om een landnummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `country_hint : ""`. 

```python
def language_detection_example(client):
    try:
        documents = ["Ce document est rédigé en Français."]
        response = client.detect_language(documents = documents, country_hint = 'us')[0]
        print("Language: ", response.primary_language.name)

    except Exception as err:
        print("Encountered exception. {}".format(err))
language_detection_example(client)
```


### <a name="output"></a>Uitvoer

```console
Language:  French
```


---

## <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

> [!NOTE]
> In versie `3.1`: 
> * Entiteitskoppeling is een aanvraag die los staat van NER.

Maak een nieuwe functie met de naam `entity_recognition_example` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `recognize_entities()` aanroept en de resultaten herhaalt. Het geretourneerde responsobject bevat de lijst met gedetecteerde entiteiten in `entity` als de aanroep is gelukt en een `error` als dat niet het geval is. Geef voor elke gedetecteerde entiteit de categorie en subcategorie weer, indien aanwezig.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\tLength: \t", entity.length, "\tOffset: \t", entity.offset, "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Uitvoer

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61   Length:          4      Offset:          18

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82   Length:          7      Offset:          26

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8    Length:          9      Offset:          34
```

### <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe functie met de naam `entity_linking_example()` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `recognize_linked_entities()` aanroept en de resultaten herhaalt. Het geretourneerde responsobject bevat de lijst met gedetecteerde entiteiten in `entities` als de aanroep is gelukt en een `error` als dat niet het geval is. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `entity`-object als een lijst met `match`-objecten.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
                print("\t\tOffset: {}".format(match.offset))
                print("\t\tLength: {}".format(match.length))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:

        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 0
                Length: 9
                Text: Microsoft
                Confidence Score: 0.55
                Offset: 168
                Length: 9
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Offset: 25
                Length: 10
                Text: Gates
                Confidence Score: 0.63
                Offset: 179
                Length: 5
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
                Offset: 40
                Length: 10
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
                Offset: 54
                Length: 7
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
                Offset: 98
                Length: 5
        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
                Offset: 125
                Length: 11
```

### <a name="personally-identifiable-information-recognition"></a>Herkenning van persoonlijke gegevens

Maak een nieuwe functie met de naam `pii_recognition_example` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `recognize_pii_entities()` aanroept en de resultaten herhaalt. Het geretourneerde responsobject bevat de lijst met gedetecteerde entiteiten in `entity` als de aanroep is gelukt en een `error` als dat niet het geval is. Geef voor elke gedetecteerde entiteit de categorie en subcategorie weer, indien aanwezig.

```python
def pii_recognition_example(client):
    documents = [
        "The employee's SSN is 859-98-0987.",
        "The employee's phone number is 555-555-5555."
    ]
    response = client.recognize_pii_entities(documents, language="en")
    result = [doc for doc in response if not doc.is_error]
    for doc in result:
        print("Redacted Text: {}".format(doc.redacted_text))
        for entity in doc.entities:
            print("Entity: {}".format(entity.text))
            print("\tCategory: {}".format(entity.category))
            print("\tConfidence Score: {}".format(entity.confidence_score))
            print("\tOffset: {}".format(entity.offset))
            print("\tLength: {}".format(entity.length))
pii_recognition_example(client)
```

### <a name="output"></a>Uitvoer

```console
Redacted Text: The employee's SSN is ***********.
Entity: 859-98-0987
        Category: U.S. Social Security Number (SSN)
        Confidence Score: 0.65
        Offset: 22
        Length: 11
Redacted Text: The employee's phone number is ************.
Entity: 555-555-5555
        Category: Phone Number
        Confidence Score: 0.8
        Offset: 31
        Length: 12
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

> [!NOTE]
> In versie `3.0`: 
> * Entiteitskoppeling is een aanvraag die los staat van NER.

Maak een nieuwe functie met de naam `entity_recognition_example` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `recognize_entities()` aanroept en de resultaten herhaalt. Het geretourneerde responsobject bevat de lijst met gedetecteerde entiteiten in `entity` als de aanroep is gelukt en een `error` als dat niet het geval is. Geef voor elke gedetecteerde entiteit de categorie en subcategorie weer, indien aanwezig.

```python
def entity_recognition_example(client):

    try:
        documents = ["I had a wonderful trip to Seattle last week."]
        result = client.recognize_entities(documents = documents)[0]

        print("Named Entities:\n")
        for entity in result.entities:
            print("\tText: \t", entity.text, "\tCategory: \t", entity.category, "\tSubCategory: \t", entity.subcategory,
                    "\n\tConfidence Score: \t", round(entity.confidence_score, 2), "\n")

    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_recognition_example(client)
```

### <a name="output"></a>Uitvoer

```console
Named Entities:

        Text:    trip   Category:        Event  SubCategory:     None
        Confidence Score:        0.61

        Text:    Seattle        Category:        Location       SubCategory:     GPE
        Confidence Score:        0.82

        Text:    last week      Category:        DateTime       SubCategory:     DateRange
        Confidence Score:        0.8
```

### <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe functie met de naam `entity_linking_example()` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `recognize_linked_entities()` aanroept en de resultaten herhaalt. Het geretourneerde responsobject bevat de lijst met gedetecteerde entiteiten in `entities` als de aanroep is gelukt en een `error` als dat niet het geval is. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `entity`-object als een lijst met `match`-objecten.

```python
def entity_linking_example(client):

    try:
        documents = ["""Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, 
        to develop and sell BASIC interpreters for the Altair 8800. 
        During his career at Microsoft, Gates held the positions of chairman,
        chief executive officer, president and chief software architect, 
        while also being the largest individual shareholder until May 2014."""]
        result = client.recognize_linked_entities(documents = documents)[0]

        print("Linked Entities:\n")
        for entity in result.entities:
            print("\tName: ", entity.name, "\tId: ", entity.data_source_entity_id, "\tUrl: ", entity.url,
            "\n\tData Source: ", entity.data_source)
            print("\tMatches:")
            for match in entity.matches:
                print("\t\tText:", match.text)
                print("\t\tConfidence Score: {0:.2f}".format(match.confidence_score))
            
    except Exception as err:
        print("Encountered exception. {}".format(err))
entity_linking_example(client)
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:

        Name:  Altair 8800      Id:  Altair 8800        Url:  https://en.wikipedia.org/wiki/Altair_8800
        Data Source:  Wikipedia
        Matches:
                Text: Altair 8800
                Confidence Score: 0.88
        Name:  Bill Gates       Id:  Bill Gates         Url:  https://en.wikipedia.org/wiki/Bill_Gates
        Data Source:  Wikipedia
        Matches:
                Text: Bill Gates
                Confidence Score: 0.63
                Text: Gates
                Confidence Score: 0.63
        Name:  Paul Allen       Id:  Paul Allen         Url:  https://en.wikipedia.org/wiki/Paul_Allen
        Data Source:  Wikipedia
        Matches:
                Text: Paul Allen
                Confidence Score: 0.60
        Name:  Microsoft        Id:  Microsoft  Url:  https://en.wikipedia.org/wiki/Microsoft
        Data Source:  Wikipedia
        Matches:
                Text: Microsoft
                Confidence Score: 0.55
                Text: Microsoft
                Confidence Score: 0.55
        Name:  April 4  Id:  April 4    Url:  https://en.wikipedia.org/wiki/April_4
        Data Source:  Wikipedia
        Matches:
                Text: April 4
                Confidence Score: 0.32
        Name:  BASIC    Id:  BASIC      Url:  https://en.wikipedia.org/wiki/BASIC
        Data Source:  Wikipedia
        Matches:
                Text: BASIC
                Confidence Score: 0.33
```

---

### <a name="key-phrase-extraction"></a>Sleuteltermextractie

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een nieuwe functie met de naam `key_phrase_extraction_example()` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `extract_key_phrases()` aanroept. Het resultaat bevat de lijst met gedetecteerde sleuteltermen in `key_phrases` als de aanroep is gelukt en een `error` als dat niet het geval is. Geef alle gedetecteerde sleuteltermen weer.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Uitvoer

```console
    Key Phrases:
         cat
         veterinarian
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een nieuwe functie met de naam `key_phrase_extraction_example()` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `extract_key_phrases()` aanroept. Het resultaat bevat de lijst met gedetecteerde sleuteltermen in `key_phrases` als de aanroep is gelukt en een `error` als dat niet het geval is. Geef alle gedetecteerde sleuteltermen weer.

```python
def key_phrase_extraction_example(client):

    try:
        documents = ["My cat might need to see a veterinarian."]

        response = client.extract_key_phrases(documents = documents)[0]

        if not response.is_error:
            print("\tKey Phrases:")
            for phrase in response.key_phrases:
                print("\t\t", phrase)
        else:
            print(response.id, response.error)

    except Exception as err:
        print("Encountered exception. {}".format(err))
        
key_phrase_extraction_example(client)
```


### <a name="output"></a>Uitvoer

```console
    Key Phrases:
         cat
         veterinarian
```


---

## <a name="use-the-api-asynchronously-with-the-batch-analyze-operation"></a>De API asynchroon gebruiken met de batchanalysebewerking

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Maak een nieuwe functie met de naam `analyze_batch_actions_example()` waaraan de client via een argument wordt doorgegeven en die vervolgens de functie `begin_analyze_batch_actions()` aanroept. Het resultaat is een langdurige bewerking waarvan de resultaten worden gepolld.

```python
from azure.ai.textanalytics import (
    RecognizeEntitiesAction
)

def analyze_batch_example(client):
        documents = [
            "Microsoft was founded by Bill Gates and Paul Allen."
        ]

        poller = client.begin_analyze_batch_actions(
            documents,
            display_name="Sample Text Analysis",
            actions=[RecognizeEntitiesAction()]
        )

        result = poller.result()
        action_results = [action_result for action_result in list(result) if not action_result.is_error]

        entities_recognition_task_result = action_results[0]
        print("Results of Entities Recognition action:")
        docs = [doc for doc in entities_recognition_task_result.document_results if not doc.is_error]

        for idx, doc in enumerate(docs):
            print("\nDocument text: {}".format(documents[idx]))
            for entity in doc.entities:
                print("Entity: {}".format(entity.text))
                print("...Category: {}".format(entity.category))
                print("...Confidence Score: {}".format(entity.confidence_score))
                print("...Offset: {}".format(entity.offset))
            print("------------------------------------------")

analyze_batch_example(client)
```

### <a name="output"></a>Uitvoer

```console
Results of Entities Recognition task:
Document text: Microsoft was founded by Bill Gates and Paul Allen.
Entity: Microsoft
...Category: Organization
...Confidence Score: 0.83
...Offset: 0
Entity: Bill Gates
...Category: Person
...Confidence Score: 0.85
...Offset: 25
Entity: Paul Allen
...Category: Person
...Confidence Score: 0.9
...Offset: 40
------------------------------------------
```

U kunt ook de batchanalysebewerking gebruiken om PII te detecteren en sleuteltermextractie uit te voeren. Zie het [batch-analysevoorbeeld](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/samples/sample_analyze_batch_actions.py) op GitHub.

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Deze functie is niet beschikbaar in versie 3.0.

---
