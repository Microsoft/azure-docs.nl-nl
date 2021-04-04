---
title: Tekst vertaling cognitieve vaardigheid
titleSuffix: Azure Cognitive Search
description: Evalueert tekst en retourneert voor elke record tekst die wordt vertaald naar de opgegeven doel taal in een AI-verrijkings pijplijn in azure Cognitive Search.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0953d750ee8b59e9889512bb64cfd276a0bbeb53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654861"
---
#   <a name="text-translation-cognitive-skill"></a>Tekst vertaling cognitieve vaardigheid

De **tekst Vertaal** vaardigheid evalueert tekst en retourneert voor elke record de tekst die wordt vertaald naar de opgegeven doel taal. Deze vaardigheid maakt gebruik van de [Translator text-API v 3.0](../cognitive-services/translator/reference/v3-0-translate.md) beschikbaar in cognitive Services.

Deze mogelijkheid is handig als u verwacht dat uw documenten niet allemaal in één taal zijn opgenomen. in dat geval kunt u de tekst voor het indexeren in één taal normaliseren voordat u de zoek opdracht vertaalt.  Het is ook handig voor lokalisatie van gebruiks voorbeelden, waar u mogelijk kopieën wilt hebben van dezelfde tekst die beschikbaar is in meerdere talen.

De [Translator text-API v 3.0](../cognitive-services/translator/reference/v3-0-reference.md) is een niet-regionale cognitieve service, wat betekent dat uw gegevens niet worden gegarandeerd in dezelfde regio als uw Azure Cognitive Search of Cognitive Services resource zijn gekoppeld.

> [!NOTE]
> Als u het bereik uitbreidt door de verwerkings frequentie te verhogen, meer documenten toe te voegen of meer AI-algoritmen toe te voegen, moet u [een factureer bare Cognitive Services resource koppelen](cognitive-search-attach-cognitive-services.md). Er worden kosten in rekening gebracht bij het aanroepen van Api's in Cognitive Services en voor het ophalen van afbeeldingen als onderdeel van de fase voor het kraken van documenten in azure Cognitive Search. Er worden geen kosten in rekening gebracht voor het ophalen van tekst uit documenten.
>
> De uitvoering van ingebouwde vaardig heden wordt in rekening gebracht op basis van de bestaande [Cognitive Services betalen naar](https://azure.microsoft.com/pricing/details/cognitive-services/)gebruik-prijs. Prijzen voor Image extractie worden beschreven op de [pagina met prijzen voor Azure Cognitive Search](https://azure.microsoft.com/pricing/details/search/).

## <a name="odatatype"></a>@odata.type  
Micro soft. skills. Text. TranslationSkill

## <a name="data-limits"></a>Gegevenslimieten
De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten door [`String.Length`](/dotnet/api/system.string.length) . Als u uw gegevens wilt opsplitsen voordat u deze naar de tekst Vertaal vaardigheid verzendt, kunt u overwegen de [Kwalificatie tekst splitsen](cognitive-search-skill-textsplit.md)te gebruiken.

## <a name="skill-parameters"></a>Vaardigheids parameters

Parameters zijn hoofdlettergevoelig.

| Invoerwaarden | Description |
|---------------------|-------------|
| defaultToLanguageCode | Lang De taal code voor het vertalen van documenten in voor documenten die de naar-taal niet expliciet opgeven. <br/> Bekijk de [volledige lijst met ondersteunde talen](../cognitive-services/translator/language-support.md). |
| defaultFromLanguageCode | Beschrijving De taal code voor het vertalen van documenten uit voor documenten die niet expliciet zijn opgegeven in de taal van.  Als de defaultFromLanguageCode niet is opgegeven, wordt de automatische taal detectie die is opgegeven door de Translator Text-API, gebruikt om de van-taal te bepalen. <br/> Bekijk de [volledige lijst met ondersteunde talen](../cognitive-services/translator/language-support.md). |
| suggestedFrom | Beschrijving De taal code voor het vertalen van documenten vanaf wanneer noch de fromLanguageCode-invoer noch de defaultFromLanguageCode-para meter worden opgegeven, en de automatische taal detectie is mislukt.  Als de taal suggestedFrom niet is opgegeven, wordt Engels (en) gebruikt als de suggestedFrom taal. <br/> Bekijk de [volledige lijst met ondersteunde talen](../cognitive-services/translator/language-support.md). |

## <a name="skill-inputs"></a>Vaardigheids invoer

| Invoer naam     | Description |
|--------------------|-------------|
| tekst | De tekst die moet worden vertaald.|
| toLanguageCode    | Een teken reeks die de taal aangeeft waarnaar de tekst moet worden vertaald. Als deze invoer niet is opgegeven, wordt de defaultToLanguageCode gebruikt om de tekst te vertalen. <br/>[Volledige lijst met ondersteunde talen](../cognitive-services/translator/language-support.md) weer geven|
| fromLanguageCode  | Een teken reeks die de huidige taal van de tekst aangeeft. Als deze para meter niet is opgegeven, wordt de defaultFromLanguageCode (of automatische taal detectie als de defaultFromLanguageCode niet is opgegeven) gebruikt voor het vertalen van de tekst. <br/>[Volledige lijst met ondersteunde talen](../cognitive-services/translator/language-support.md) weer geven|

## <a name="skill-outputs"></a>Vaardigheids uitvoer

| Uitvoer naam    | Description |
|--------------------|-------------|
| translatedText | Het teken reeks resultaat van de tekst omzetting van de translatedFromLanguageCode naar de translatedToLanguageCode.|
| translatedToLanguageCode  | Een teken reeks die de taal code aangeeft waarnaar de tekst is vertaald. Dit is handig als u vertaalt naar meerdere talen en u wilt bijhouden welke tekst de taal is.|
| translatedFromLanguageCode    | Een teken reeks die de taal code aangeeft waarin de tekst is vertaald. Het is handig als u hebt gekozen voor de optie automatische taal detectie, omdat deze uitvoer het resultaat van die detectie oplevert.|

##  <a name="sample-definition"></a>Voorbeeld definitie

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>Voorbeeldinvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Fouten en waarschuwingen
Als u een niet-ondersteunde taal code opgeeft voor de van-of-naar-taal, wordt er een fout gegenereerd en wordt tekst niet vertaald.
Als uw tekst leeg is, wordt er een waarschuwing gegenereerd.
Als uw tekst groter is dan 50.000 tekens, worden alleen de eerste 50.000 tekens vertaald en wordt er een waarschuwing gegeven.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)