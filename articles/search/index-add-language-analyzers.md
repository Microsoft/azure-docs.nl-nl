---
title: Taal analyse functies toevoegen aan teken reeks velden
titleSuffix: Azure Cognitive Search
description: Meertalige lexicale analyse voor niet-Engelse query's en indexen in azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: ac11b7bc7e53c214f872d400565d50009479afcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604420"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Taal analyse functies toevoegen aan teken reeks velden in een Azure Cognitive Search-index

Een *taal analyse* is een specifiek type [tekst analyse](search-analyzers.md) dat lexicale analyses uitvoert met behulp van de taal kundige regels van de doel taal. Elk doorzoekbaar veld heeft een eigenschap **Analyzer** . Als uw inhoud bestaat uit vertaalde teken reeksen, zoals afzonderlijke velden voor Engelse en Chinese tekst, kunt u taal analysen opgeven voor elk veld om toegang te krijgen tot de uitgebreide taal mogelijkheden van die analyse functies.

## <a name="when-to-use-a-language-analyzer"></a>Wanneer moet u een taal analyse gebruiken?

U moet een taal analyse overwegen wanneer u weet dat de structuur van een woord of zin een waarde toevoegt aan het parseren van tekst. Een voor beeld hiervan is het koppelen van onregelmatige woord vormen (' meebrengen ' en ' ingebrachte ') of meervouden (muizen ' en ' muis '). Zonder taal kundig bewustzijn worden deze teken reeksen alleen op fysieke kenmerken geparseerd, waardoor de verbinding niet kan worden onderschept. Omdat grote stukken tekst waarschijnlijk deze inhoud bevatten, zijn de velden met beschrijvingen, beoordelingen of samen vattingen goede kandidaten voor een taal analyse.

U moet ook taal analyse functies beschouwen als de inhoud uit niet-westerse teken reeksen bestaat. Hoewel de [standaard analyse functie](search-analyzers.md#default-analyzer) language-neutraal is, is het concept van het gebruik van spaties en speciale tekens (afbreek streepjes en slashes) voor het scheiden van teken reeksen meer van toepassing op westerse talen dan andere Westerse versies. 

Bijvoorbeeld, in het Chinees, Japans, Koreaans (CJK) en andere Aziatische talen, is een spatie niet noodzakelijkerwijs een woord scheidings teken. Bekijk de volgende Japanse teken reeks. Omdat het geen spaties bevat, zal een taal-neutraal Analyzer waarschijnlijk de hele teken reeks als één token analyseren, wanneer de teken reeks in feite een zin is.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

In het bovenstaande voor beeld zou een geslaagde query het volledige token moeten bevatten, of een gedeeltelijk token met behulp van een achtervoegsel Joker teken, wat resulteert in een onnatuurlijke en beperkende Zoek ervaring.

Een betere ervaring is het zoeken naar afzonderlijke woorden: 明るい (helder), 私たちの (onze), 銀河系 (Galaxy). Het gebruik van een van de Japanse analyse functies die beschikbaar zijn in Cognitive Search is waarschijnlijker om dit gedrag te ontgrendelen omdat deze analyse functies beter zijn ingericht bij het splitsen van het tekst segment in betekenis volle woorden in de doel taal.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Lucene en micro soft-analyse functies vergelijken

Azure Cognitive Search ondersteunt 35 taal analysen die worden ondersteund door Lucene, en 50 taal analyse functies die worden ondersteund door een eigen micro soft-technologie voor natuurlijke taal verwerking die wordt gebruikt in Office en Bing.

Sommige ontwikkel aars hebben mogelijk de voor keur aan een bekendere, eenvoudige, open-source oplossing van Lucene. De taal analysen van Lucene zijn sneller, maar de micro soft-analyse functies hebben geavanceerde mogelijkheden, zoals lemmatisering, het ontsamenen van woorden (in talen zoals Duits, Deens, Nederlands, Zweeds, Noors, Estland, finish, Hong aars, Slowaaks) en entiteits herkenning (Url's, e-mails, datums, cijfers). Voer, indien mogelijk, vergelijkingen uit van de micro soft-en lucene-analyse functies om te bepalen welke een beter aansluit. 

Indexeren met micro soft-analyse functies is gemiddeld twee tot drie keer langzamer dan hun bijbehorende Lucene, afhankelijk van de taal. Zoek prestaties moeten niet significant worden beïnvloed voor de gemiddelde grootte van query's. 

### <a name="english-analyzers"></a>Engelse analyse functies

De standaard-Analyzer is standaard-Lucene, die goed werkt voor het Engels, maar mogelijk niet ook en de Engelse analyse functie van Lucene of het Engelse analyse beleid van micro soft.

+ De Engelse Analyzer van Lucene breidt de Standard Analyzer uit. Hiermee verwijdert u de possessives (van het begin) van woorden, wordt de functie voor het verbreken van de splitsing van het schema en de Engelse stop woorden verwijderd.  

+ De Engelse Analyzer van micro soft voert lemmatisering uit in plaats van de ontleding. Dit betekent dat het verbogen en onregelmatige woord vormen aanzienlijk beter kan worden verwerkt, wat resulteert in meer relevante zoek resultaten 

## <a name="how-to-specify-a-language-analyzer"></a>Een taal analyse opgeven

Stel een taal analyse in op velden die doorzoekbaar zijn van het type EDM. String tijdens veld definitie.

Hoewel veld definities verschillende eigenschappen die betrekking hebben op de Analyzer, kan alleen de eigenschap ' Analyzer ' worden gebruikt voor taal analysen. De waarde van ' Analyzer ' moet een van de taal analyse functies van de lijst met ondersteunings analyses zijn.

```json
{
  "name": "hotels-sample-index",
  "fields": [
    {
      "name": "Description",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "en.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
    {
      "name": "Description_fr",
      "type": "Edm.String",
      "retrievable": true,
      "searchable": true,
      "analyzer": "fr.microsoft",
      "indexAnalyzer": null,
      "searchAnalyzer": null
    },
```

Zie [Create Index (rest) (Engelstalig)](/rest/api/searchservice/create-index)voor meer informatie over het maken van een index en het instellen van veld eigenschappen. Zie voor meer informatie over tekst analyse [analyse functies in Azure Cognitive Search](search-analyzers.md).

<a name="language-analyzer-list"></a>

## <a name="supported-language-analyzers"></a>Ondersteunde taal analysen

 Hieronder ziet u een lijst met ondersteunde talen, met de namen van Lucene en micro soft Analyzer.  

| Taal | Naam van micro soft Analyzer | Naam van Lucene Analyzer |
|----------|-------------------------|----------------------|
| Arabisch   | AR. micro soft | AR. lucene |
| Armeens |           | HY. lucene |
| Bengaals   | bn. micro soft |  |
| Baskisch   |  | EU. lucene |
| Bulgaars | bg. micro soft | bg. lucene |
| Catalaans  | ca. micro soft | ca. lucene |
| Chinees (vereenvoudigd) | zh-Hans. micro soft | zh-Hans. lucene |
| Chinees (traditioneel) | zh-hant. micro soft | zh-hant. lucene |
| Kroatisch | HR. micro soft |  |
| Tsjechisch | CS. micro soft | CS. lucene |
| Deens | da. micro soft | da. lucene |
| Nederlands | nl. micro soft | nl. lucene |
| Engels | en. micro soft | en. lucene |
| Ests | et. micro soft |  |
| Fins | fi. micro soft | fi. lucene |
| Frans | fr. micro soft | fr. lucene |
| Galicisch |  | gl. lucene |
| Duits | de. micro soft | de. lucene |
| Grieks | El. micro soft | El. lucene |
| Gujarati | Gu. micro soft |  |
| Hebreeuws | he. micro soft |  |
| Hindi | Hallo. micro soft | Hallo. lucene |
| Hongaars | hu. micro soft | hu. lucene |
| IJslands | is. micro soft |  |
| Indonesisch (Bahasa) | id. micro soft | id. lucene |
| Iers |  | Ga. lucene |
| Italiaans | it. micro soft | it. lucene |
| Japans | Ja. micro soft | Ja. lucene |
| Kannada | kn. micro soft |  |
| Koreaans | ko. micro soft | ko. lucene |
| Lets | LV. micro soft | LV. lucene |
| Litouws | lt. micro soft |  |
| Malayalam | ml. micro soft |  |
| Maleis (Latijns) | MS. micro soft |  |
| Mahrati | Mr. micro soft |  |
| Noors | NB. micro soft | No. lucene |
| Perzisch |  | FA. lucene |
| Pools | pl. micro soft | pl. lucene |
| Portugees (Brazilië) | pt-br. micro soft | pt-br. lucene |
| Portugees (Portugal) | pt-pt. micro soft | pt-pt. lucene |
| Punjabi | pa. micro soft |  |
| Roemeens | ro. micro soft | ro. lucene |
| Russisch | ru. micro soft | ru. lucene |
| Servisch (Cyrillisch) | SR-Cyrillisch. micro soft |  |
| Servisch (Latijns) | SR-Latijn. micro soft |  |
| Slowaaks | SK. micro soft |  |
| Sloveens | SL. micro soft |  |
| Spaans | es. micro soft | es. lucene |
| Zweeds | SV. micro soft | SV. lucene |
| Tamil | ta. micro soft |  |
| Telugu | te. micro soft |  |
| Thai | th. micro soft | th. lucene |
| Turks | tr. micro soft | tr. lucene |
| Oekraïens | UK. micro soft |  |
| Urdu | uw. micro soft |  |
| Vietnamees | VI. micro soft |  |

 Alle analyse functies met namen die zijn gekoppeld aan **lucene** , worden aangedreven door [de taal Analyseers van Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Zie ook  

+ [Een index maken](search-what-is-an-index.md)
+ [Een index voor meerdere talen maken](search-language-support.md)
+ [Index maken (REST API)](/rest/api/searchservice/create-index)  
+ [LexicalAnalyzerName-klasse (Azure SDK voor .NET)](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)