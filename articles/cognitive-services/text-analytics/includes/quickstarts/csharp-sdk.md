---
title: 'Quickstart: Text Analytics-clientbibliotheek voor C# | Microsoft Docs'
description: Aan de slag met de Text Analytics-clientbibliotheek voor C#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/20/2021
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 6e71d9b4006d0353b094306424ba0fe99c581279
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090707"
---
<a name="HOLTop"></a>

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

[v3.1-referentiedocumentatie](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet-preview) | [Broncode voor v3.1-bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [v3.1-pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0-beta.3) | [v3.1-voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

[v3-referentiedocumentatie](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet) | [Broncode voor v3-bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics) | [v3-pakket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3-voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.TextAnalytics_5.0.0/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

# <a name="version-21"></a>[Versie 2.1](#tab/version-2)

[v2-referentiedocumentatie](/dotnet/api/overview/azure/cognitiveservices/client) | [Broncode voor v2-bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2-pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2-voorbeelden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Zodra u een Azure-abonnement hebt, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Een Text Analytics-resource maken"  target="_blank">maakt u een Text Analytics-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen.  Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Text Analytics-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* Als u de functie Analyseren wilt gebruiken, hebt u een Text Analytics-resource in de prijscategorie Standard (S) nodig.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-net-core-application"></a>Een nieuwe .NET Core-app maken

Maak een nieuwe console-app in .NET Core met behulp van de Visual Studio IDE. Hiermee wordt een Hello World-project gemaakt met één bronbestand van C#: *program.cs*.

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Installeer de clientbibliotheek door met de rechtermuisknop op de oplossing te klikken in **Solution Explorer** en **NuGet-pakketten beheren** te selecteren. Selecteer in Package Manager dat wordt geopend de optie **Bladeren** en zoek naar `Azure.AI.TextAnalytics`. Schakel het vakje **prerelease toevoegen** in, selecteer versie `5.1.0-beta.3` en selecteer **Installeren**. U kunt ook de [Package Manager-console](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) gebruiken.

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Installeer de clientbibliotheek door met de rechtermuisknop op de oplossing te klikken in **Solution Explorer** en **NuGet-pakketten beheren** te selecteren. Selecteer in Package Manager dat wordt geopend de optie **Bladeren** en zoek naar `Azure.AI.TextAnalytics`. Selecteer versie `5.0.0` en vervolgens **Installeren**. U kunt ook de [Package Manager-console](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) gebruiken.


> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Ga dan naar [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), waar u de codevoorbeelden uit deze quickstart kunt vinden. 

# <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Installeer de clientbibliotheek door met de rechtermuisknop op de oplossing te klikken in **Solution Explorer** en **NuGet-pakketten beheren** te selecteren. Selecteer in de package manager die wordt geopend de optie **Bladeren** en zoek naar `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Klik erop en vervolgens op **Install**. U kunt ook de [Package Manager-console](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) gebruiken.

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? U kunt het vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), dat de codevoorbeelden in deze quickstart bevat. 

---

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Open het bestand *program.cs* en voeg de volgende `using`-instructies toe:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Maak in de klasse `Program` van de toepassing variabelen voor de sleutel en het eindpunt van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Vervang de methode `Main` van de toepassing. U definieert later de methoden die hier worden aangeroepen.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    SentimentAnalysisWithOpinionMiningExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    RecognizePIIExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Open het bestand *program.cs* en voeg de volgende `using`-instructies toe:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Maak in de klasse `Program` van de toepassing variabelen voor de sleutel en het eindpunt van uw resource.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Vervang de methode `Main` van de toepassing. U definieert later de methoden die hier worden aangeroepen.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

# <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Open het bestand *program.cs* en voeg de volgende `using`-instructies toe:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Maak in de klasse `Program` van de toepassing variabelen voor de sleutel en het eindpunt van uw resource. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Vervang de methode `Main` van de toepassing. U definieert later de methoden die hier worden aangeroepen.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objectmodel

De Text Analytics-client is een `TextAnalyticsClient`-object dat wordt geverifieerd bij Azure met behulp van uw sleutel. De client biedt functies om tekst als afzonderlijke tekenreeksen of als een batch te accepteren. U kunt tekst synchroon of asynchroon naar de API verzenden. Het responsobject bevat de analyse-informatie voor elk document dat u verzendt. 

Als u versie `3.x` van de service gebruikt, kunt u een optioneel exemplaar van `TextAnalyticsClientOptions` gebruiken om de client te initialiseren met verschillende standaardinstellingen (zoals een standaardtaal voor het land of de regio). U kunt ook verifiëren met een Azure Active Directory-token. 

## <a name="code-examples"></a>Codevoorbeelden

* [Sentimentanalyse](#sentiment-analysis)
* [Meninganalyse](#opinion-mining)
* [Taaldetectie](#language-detection)
* [Herkenning van benoemde entiteiten](#named-entity-recognition-ner)
* [Entiteiten koppelen](#entity-linking)
* [Sleuteltermextractie](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>De client verifiëren

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Zorg ervoor dat met uw eerdere main-methode een nieuw clientobject wordt gemaakt met uw eindpunt en referenties.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Zorg ervoor dat met uw eerdere main-methode een nieuw clientobject wordt gemaakt met uw eindpunt en referenties.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

# <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuwe klasse `ApiKeyServiceClientCredentials` om de referenties op te slaan en deze toe te voegen aan de aanvragen van de client. Maak binnen de klasse een overschrijving voor `ProcessHttpRequestAsync()` waarmee uw sleutel wordt toegevoegd aan de header `Ocp-Apim-Subscription-Key`.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Maak een methode voor het instantiëren van het [TextAnalyticsClient](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient)-object met uw eindpunt en een `ApiKeyServiceClientCredentials`-object dat uw sleutel bevat.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Sentimentanalyse

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `AnalyzeSentiment()` aan. Het geretourneerde `Response<DocumentSentiment>`-object bevat het sentimentlabel en de score van het volledige invoerdocument, evenals een sentimentanalyse voor elke zin als dit is gelukt. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

### <a name="opinion-mining"></a>Meninganalyse

Maak een nieuwe functie met de naam `SentimentAnalysisWithOpinionMiningExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `AnalyzeSentimentBatch()` aan met optie `AdditionalSentimentAnalyses.OpinionMining`. Het geretourneerde `AnalyzeSentimentResultCollection`-object bevat de verzameling `AnalyzeSentimentResult` waarin `Response<DocumentSentiment>` voorkomt. Het verschil tussen `SentimentAnalysis()` en `SentimentAnalysisWithOpinionMiningExample()` is dat de laatste `MinedOpinion` in elke zin bevat, waarin een geanalyseerd aspect en de gerelateerde opinie (s) worden weergegeven. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void SentimentAnalysisWithOpinionMiningExample(TextAnalyticsClient client)
{
    var documents = new List<string>
    {
        "The food and service were unacceptable, but the concierge were nice."
    };

    AnalyzeSentimentResultCollection reviews = client.AnalyzeSentimentBatch(documents, options: new AnalyzeSentimentOptions()
    {
        IncludeOpinionMining = true
    });

    foreach (AnalyzeSentimentResult review in reviews)
    {
        Console.WriteLine($"Document sentiment: {review.DocumentSentiment.Sentiment}\n");
        Console.WriteLine($"\tPositive score: {review.DocumentSentiment.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {review.DocumentSentiment.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {review.DocumentSentiment.ConfidenceScores.Neutral:0.00}\n");
        foreach (SentenceSentiment sentence in review.DocumentSentiment.Sentences)
        {
            Console.WriteLine($"\tText: \"{sentence.Text}\"");
            Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
            Console.WriteLine($"\tSentence positive score: {sentence.ConfidenceScores.Positive:0.00}");
            Console.WriteLine($"\tSentence negative score: {sentence.ConfidenceScores.Negative:0.00}");
            Console.WriteLine($"\tSentence neutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");

            foreach (MinedOpinion minedOpinion in sentence.MinedOpinions)
            {
                Console.WriteLine($"\tAspect: {minedOpinion.Aspect.Text}, Value: {minedOpinion.Aspect.Sentiment}");
                Console.WriteLine($"\tAspect positive score: {minedOpinion.Aspect.ConfidenceScores.Positive:0.00}");
                Console.WriteLine($"\tAspect negative score: {minedOpinion.Aspect.ConfidenceScores.Negative:0.00}");
                foreach (OpinionSentiment opinion in minedOpinion.Opinions)
                {
                    Console.WriteLine($"\t\tRelated Opinion: {opinion.Text}, Value: {opinion.Sentiment}");
                    Console.WriteLine($"\t\tRelated Opinion positive score: {opinion.ConfidenceScores.Positive:0.00}");
                    Console.WriteLine($"\t\tRelated Opinion negative score: {opinion.ConfidenceScores.Negative:0.00}");
                }
            }
        }
        Console.WriteLine($"\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Document sentiment: Positive

        Positive score: 0.84
        Negative score: 0.16
        Neutral score: 0.00

        Text: "The food and service were unacceptable, but the concierge were nice."
        Sentence sentiment: Positive
        Sentence positive score: 0.84
        Sentence negative score: 0.16
        Sentence neutral score: 0.00

        Aspect: food, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: service, Value: Negative
        Aspect positive score: 0.01
        Aspect negative score: 0.99
                Related Opinion: unacceptable, Value: Negative
                Related Opinion positive score: 0.01
                Related Opinion negative score: 0.99
        Aspect: concierge, Value: Positive
        Aspect positive score: 1.00
        Aspect negative score: 0.00
                Related Opinion: nice, Value: Positive
                Related Opinion positive score: 1.00
                Related Opinion negative score: 0.00


Press any key to exit.
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `AnalyzeSentiment()` aan. Het geretourneerde `Response<DocumentSentiment>`-object bevat het sentimentlabel en de score van het volledige invoerdocument, evenals een sentimentanalyse voor elke zin als dit is gelukt. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tText: \"{sentence.Text}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Document sentiment: Positive

        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

# <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuwe functie met de naam `SentimentAnalysisExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de functie [Sentiment()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment) van de nieuwe functie aan. Het geretourneerde [SentimentResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult)-object bevat het sentiment `Score` als de aanroep is gelukten een `errorMessage` als dat niet het geval is. 

Een score dichter bij 0 wijst op een negatief gevoel, terwijl een score dichter bij 1 op een positief gevoel wijst.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Taaldetectie

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)


Maak een nieuwe functie met de naam `LanguageDetectionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `DetectLanguage()` aan. Het geretourneerde `Response<DetectedLanguage>`-object bevat de gedetecteerde taal, samen met de naam en ISO 6391-code van de taal. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt parameter `countryHint` gebruiken om een land- of regionummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `countryHint = ""`. Als u een andere standaardwaarde wilt instellen, stelt u eigenschap `TextAnalyticsClientOptions.DefaultCountryHint` in en geeft u deze door tijdens de initialisatie van de client.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Uitvoer

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)


Maak een nieuwe functie met de naam `LanguageDetectionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `DetectLanguage()` aan. Het geretourneerde `Response<DetectedLanguage>`-object bevat de gedetecteerde taal, samen met de naam en ISO 6391-code van de taal. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt parameter `countryHint` gebruiken om een land- of regionummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `countryHint = ""`. Als u een andere standaardwaarde wilt instellen, stelt u eigenschap `TextAnalyticsClientOptions.DefaultCountryHint` in en geeft u deze door tijdens de initialisatie van de client.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Uitvoer

```console
Language:
        French, ISO-6391: fr
```

# <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuwe functie met de naam `languageDetectionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de functie [DetectLanguage()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan van de nieuwe functie. Het geretourneerde [LanguageResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult)-object bevat de lijst met gedetecteerde talen in `DetectedLanguages` als de aanroep is gelukt en een `errorMessage` als dat niet het geval is. Gebruik print met de eerste geretourneerde taal.

> [!Tip]
> In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt parameter `countryHint` gebruiken om een land- of regionummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `countryHint = ""`.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Uitvoer

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>NER (Herkenning van benoemde entiteiten)

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)


Maak een nieuwe functie met de naam `EntityRecognitionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt, roep de functie `RecognizeEntities()` aan van de nieuwe functie en herhaal de resultaten. Het geretourneerde `Response<CategorizedEntityCollection>`-object bevat de verzameling met gedetecteerde entiteiten `CategorizedEntity`. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2},\tLength: {entity.Length},\tOffset: {entity.Offset}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61,    Length: 4,      Offset: 18

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82,    Length: 7,      Offset: 26

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80,    Length: 9,      Offset: 34
```

### <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe functie met de naam `EntityLinkingExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt, roep de functie `RecognizeLinkedEntities()` aan van de nieuwe functie en herhaal de resultaten. Het geretourneerde `Response<LinkedEntityCollection>`-object bevat de verzameling met gedetecteerde entiteiten `LinkedEntity`. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity`-object als een lijst met `LinkedEntityMatch`-objecten.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}");
            Console.WriteLine($"\t\tLength: {match.Length}");
            Console.WriteLine($"\t\tOffset: {match.Offset}\n");
        }
    }
}
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:
        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 0

                Text: Microsoft
                Score: 0.55
                Length: 9
                Offset: 150

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63
                Length: 10
                Offset: 25

                Text: Gates
                Score: 0.63
                Length: 5
                Offset: 161

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60
                Length: 10
                Offset: 40

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32
                Length: 7
                Offset: 54

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
                Length: 5
                Offset: 89

        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88
                Length: 11
                Offset: 116
```

### <a name="personally-identifiable-information-recognition"></a>Herkenning van persoonlijke gegevens

Maak een nieuwe functie met de naam `RecognizePIIExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt, roep de functie `RecognizePiiEntities()` aan van de nieuwe functie en herhaal de resultaten. Het geretourneerde `PiiEntityCollection`-object vertegenwoordigt de lijst met gedetecteerde PII-entiteiten. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void RecognizePIIExample(TextAnalyticsClient client)
{
    string document = "A developer with SSN 859-98-0987 whose phone number is 800-102-1100 is building tools with our APIs.";

    PiiEntityCollection entities = client.RecognizePiiEntities(document).Value;

    Console.WriteLine($"Redacted Text: {entities.RedactedText}");
    if (entities.Count > 0)
    {
        Console.WriteLine($"Recognized {entities.Count} PII entit{(entities.Count > 1 ? "ies" : "y")}:");
        foreach (PiiEntity entity in entities)
        {
            Console.WriteLine($"Text: {entity.Text}, Category: {entity.Category}, SubCategory: {entity.SubCategory}, Confidence score: {entity.ConfidenceScore}");
        }
    }
    else
    {
        Console.WriteLine("No entities were found.");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Redacted Text: A developer with SSN *********** whose phone number is ************ is building tools with our APIs.
Recognized 2 PII entities:
Text: 859-98-0987, Category: U.S. Social Security Number (SSN), SubCategory: , Confidence score: 0.65
Text: 800-102-1100, Category: Phone Number, SubCategory: , Confidence score: 0.8
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)


> [!NOTE]
> Nieuw in versie `3.0`:
> * Entiteitskoppeling is nu gescheiden van entiteitsherkenning.


Maak een nieuwe functie met de naam `EntityRecognitionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt, roep de functie `RecognizeEntities()` aan van de nieuwe functie en herhaal de resultaten. Het geretourneerde `Response<IReadOnlyCollection<CategorizedEntity>>`-object bevat de lijst met gedetecteerde entiteiten. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Named Entities:
        Text: trip,     Category: Event,        Sub-Category:
                Score: 0.61

        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Score: 0.82

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Score: 0.80
```

### <a name="entity-linking"></a>Entiteiten koppelen

Maak een nieuwe functie met de naam `EntityLinkingExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt, roep de functie `RecognizeLinkedEntities()` aan van de nieuwe functie en herhaal de resultaten. Het geretourneerde object `Response<IReadOnlyCollection<LinkedEntity>>` vertegenwoordigt de lijst met gedetecteerde entiteiten. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd. Aangezien gekoppelde entiteiten uniek worden geïdentificeerd, worden exemplaren van dezelfde entiteit gegroepeerd onder een `LinkedEntity`-object als een lijst met `LinkedEntityMatch`-objecten.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Uitvoer

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Score: 0.88

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Score: 0.63

                Text: Gates
                Score: 0.63

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Score: 0.60

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Score: 0.55

                Text: Microsoft
                Score: 0.55

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Score: 0.32

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Score: 0.33
```

# <a name="version-21"></a>[Versie 2.1](#tab/version-2)

> [!NOTE]
> In versie 2.1 wordt entiteitskoppeling opgenomen in het NER-antwoord.

Maak een nieuwe functie met de naam `RecognizeEntitiesExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie [Entities()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Herhaal de resultaten. Het geretourneerde object [EntitiesResult](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult) bevat de lijst met gedetecteerde entiteiten in `Entities` als de aanroep is gelukt en een `errorMessage` als dat niet het geval is. Voor elke gedetecteerde entiteit wordt het type, subtype en de Wikipedia-naam (indien deze bestaan) weergegeven, evenals de locaties in de oorspronkelijke tekst.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


### <a name="key-phrase-extraction"></a>Sleuteltermextractie

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `ExtractKeyPhrases()` aan. Het geretourneerde object `<Response<KeyPhraseCollection>` bevat de lijst met gedetecteerde sleuteltermen. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `ExtractKeyPhrases()` aan. Het geretourneerde object `<Response<IReadOnlyCollection<string>>` bevat de lijst met gedetecteerde sleuteltermen. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

# <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Maak een nieuwe functie met de naam `KeyPhraseExtractionExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie [KeyPhrases()](/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) aan. Het resultaat bevat de lijst met gedetecteerde sleuteltermen in `KeyPhrases` als de aanroep is gelukt en een `errorMessage` als dat niet het geval is. Geef alle gedetecteerde sleuteltermen weer.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Uitvoer

```console
Key phrases:
    cat
    veterinarian
```

---

## <a name="use-the-api-asynchronously-with-the-analyze-operation"></a>De API asynchroon gebruiken met de bewerking Analyseren

# <a name="version-31-preview"></a>[Versie 3.1: preview](#tab/version-3-1)

[!INCLUDE [Analyze operation pricing](../analyze-operation-pricing-caution.md)]

Maak een nieuwe functie met de naam `AnalyzeOperationExample()` waarvoor de client wordt gebruikt die u eerder hebt gemaakt en roep de bijbehorende functie `StartAnalyzeOperationBatch()` aan. Het geretourneerde object `AnalyzeOperation` bevat het interfaceobject `Operation` voor `AnalyzeOperationResult`. Aangezien het een langdurige bewerking is, moet `await` op `operation.WaitForCompletionAsync()` voor de waarde worden bijgewerkt. Zodra `WaitForCompletionAsync()` is voltooid, moet de verzameling worden bijgewerkt in `operation.Value`. Als er een fout optreedt, wordt er een `RequestFailedException` gegenereerd.


```csharp
static async Task AnalyzeOperationExample(TextAnalyticsClient client)
{
    string inputText = "Microsoft was founded by Bill Gates and Paul Allen.";

    var batchDocuments = new List<string> { inputText };

    AnalyzeOperationOptions operationOptions = new AnalyzeOperationOptions()
    {
        EntitiesTaskParameters = new EntitiesTaskParameters(),
        DisplayName = "Analyze Operation Quick Start Example"
    };

    AnalyzeOperation operation = client.StartAnalyzeOperationBatch(batchDocuments, operationOptions, "en");

    await operation.WaitForCompletionAsync();

    AnalyzeOperationResult resultCollection = operation.Value;

    RecognizeEntitiesResultCollection entitiesResult = resultCollection.Tasks.EntityRecognitionTasks[0].Results;

    Console.WriteLine("Analyze Operation Request Details");
    Console.WriteLine($"    Status: {resultCollection.Status}");
    Console.WriteLine($"    DisplayName: {resultCollection.DisplayName}");
    Console.WriteLine("");

    Console.WriteLine("Recognized Entities");

    foreach (RecognizeEntitiesResult result in entitiesResult)
    {
        Console.WriteLine($"    Recognized the following {result.Entities.Count} entities:");

        foreach (CategorizedEntity entity in result.Entities)
        {
            Console.WriteLine($"    Entity: {entity.Text}");
            Console.WriteLine($"    Category: {entity.Category}");
            Console.WriteLine($"    Offset: {entity.Offset}");
            Console.WriteLine($"    ConfidenceScore: {entity.ConfidenceScore}");
            Console.WriteLine($"    SubCategory: {entity.SubCategory}");
        }
        Console.WriteLine("");
    }
}
```

Nadat u dit voorbeeld aan uw toepassing hebt toegevoegd, roept u de methode `main()` aan met behulp van `await`.

```csharp
await AnalyzeOperationExample(client).ConfigureAwait(false);
```
### <a name="output"></a>Uitvoer

```console
Analyze Operation Request Details
    Status: succeeded
    DisplayName: Analyze Operation Quick Start Example

Recognized Entities
    Recognized the following 3 entities:
    Entity: Microsoft
    Category: Organization
    Offset: 0
    ConfidenceScore: 0.83
    SubCategory: 
    Entity: Bill Gates
    Category: Person
    Offset: 25
    ConfidenceScore: 0.85
    SubCategory: 
    Entity: Paul Allen
    Category: Person
    Offset: 40
    ConfidenceScore: 0.9
    SubCategory: 
```

U kunt ook de bewerking Analyseren ook gebruiken om persoonsgegevens en extractie van sleutelwoorden te detecteren. Zie het [analysevoorbeeld](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples/Sample_AnalyzeOperation.md) (Engelstalig) op GitHub.

# <a name="version-30"></a>[Versie 3.0](#tab/version-3)

Deze functie is niet beschikbaar in versie 3.0.

# <a name="version-21"></a>[Versie 2.1](#tab/version-2)

Deze functie is niet beschikbaar in versie 2.1.

---
