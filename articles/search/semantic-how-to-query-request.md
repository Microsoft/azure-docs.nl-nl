---
title: Een semantische query maken
titleSuffix: Azure Cognitive Search
description: Stel een semantisch query type in om de diepe leer modellen te koppelen aan het verwerken van query's, het uitstellen van intentie en context als onderdeel van de rang schikking en relevantie van de zoek opdracht.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 7551ef88c2251b64cf6f6db1de4fed22db2c69e2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693642"
---
# <a name="create-a-semantic-query-in-cognitive-search"></a>Een semantische query maken in Cognitive Search

> [!IMPORTANT]
> Semantisch query type bevindt zich in de open bare preview-versie, die beschikbaar is via de preview-REST API en Azure Portal. Preview-functies worden onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)aangeboden. Tijdens de eerste preview-versie worden er geen kosten in rekening gebracht voor semantisch zoeken. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

In dit artikel leert u hoe u een zoek opdracht kunt formuleren die gebruikmaakt van semantische classificatie en dat er semantische bijschriften en antwoorden worden gegenereerd.

## <a name="prerequisites"></a>Vereisten

+ Een zoek service in een Standard-laag (S1, S2, S3), die zich in een van deze regio's bevindt: Noord-Centraal VS, VS-West, VS-West 2, VS-Oost 2, Europa-noord, Europa-west. Als u een bestaande S1-of meer-service in een van deze regio's hebt, kunt u toegang aanvragen zonder dat u een nieuwe service hoeft te maken.

+ Toegang tot semantische Zoek voorbeeld: [registreren](https://aka.ms/SemanticSearchPreviewSignup)

+ Een bestaande zoek index met Engelse inhoud

+ Een Search-client voor het verzenden van query's

  De Search-client moet de preview REST-Api's voor de query aanvraag ondersteunen. U kunt [postman](search-get-started-rest.md), [Visual Studio code](search-get-started-vs-code.md)of code gebruiken die u hebt gewijzigd om rest-aanroepen naar de preview-api's te maken. U kunt ook [Search Explorer](search-explorer.md) in azure Portal gebruiken om een semantische query in te dienen.

+ Een aanvraag voor [zoeken naar documenten](/rest/api/searchservice/preview-api/search-documents) met de semantische optie en andere para meters die in dit artikel worden beschreven.

## <a name="whats-a-semantic-query"></a>Wat is een semantische query?

In Cognitive Search is een query een aanvraag met para meters die de verwerking van query's en de vorm van de reactie bepaalt. Een *semantische query* voegt para meters toe die het semantische herstelbeleid aanroepen, waarmee de context en de betekenis van overeenkomende resultaten kan worden geëvalueerd en waarmee meer relevante overeenkomsten naar boven worden bevorderd.

De volgende aanvraag is representatief voor een algemene semantische query (zonder antwoorden).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us",  
}
```

Net als bij alle query's in Cognitive Search streeft de aanvraag naar de verzameling documenten van een enkele index. Bovendien gaat een semantische query dezelfde volg orde van parseren, analyses en scannen als een niet-semantische query. Het verschil ligt in de manier waarop relevantie wordt berekend. Zoals gedefinieerd in deze preview-versie, is een semantische query waarvan de *resultaten* opnieuw worden verwerkt met behulp van geavanceerde algoritmen, waardoor het mogelijk is om de overeenkomsten te laten Opper lopen die het meest relevant zijn voor de semantische rangorde, in plaats van de scores die worden toegewezen door het standaard classificatie algoritme voor gelijkenis. 

Alleen de Top 50 treffers van de oorspronkelijke resultaten kan semantisch worden geclassificeerd en alle ondertiteling in het antwoord worden meegenomen. Desgewenst kunt u een **`answer`** para meter opgeven voor de aanvraag om een mogelijk antwoord uit te pakken. In dit model worden Maxi maal vijf mogelijke antwoorden op de query geformuleerd, die u kunt weer geven boven aan de zoek pagina.

## <a name="query-using-rest-apis"></a>Query's uitvoeren met REST-Api's

De volledige specificatie van de REST API kan worden gevonden in [Zoek documenten (rest preview)](/rest/api/searchservice/preview-api/search-documents).

Semantische query's zijn bedoeld voor open vragen, zoals ' wat is de beste fabriek voor bestuivers ' of ' How to Fry a ei '. Als u wilt dat het antwoord antwoorden bevat, kunt u een optionele **`answer`** para meter toevoegen aan de aanvraag.

In het volgende voor beeld wordt gebruikgemaakt van de hotels-voor beeld-index voor het maken van een semantisch query verzoek met semantische antwoorden en bijschriften:

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

### <a name="formulate-the-request"></a>De aanvraag formuleren

1. Stel **`"queryType"`** in op ' semantisch ' en **`"queryLanguage"`** ' en-us '. Beide para meters zijn vereist.

   De queryLanguage moet consistent zijn met alle [taal analyse](index-add-language-analyzers.md) functies die zijn toegewezen aan veld definities in het index schema. Als queryLanguage "en-US" is, moeten alle taal analysen ook een Engelse variant ("en. micro soft" of "en. lucene") zijn. Alle taal neutraal-analyse functies, zoals sleutel woord of eenvoudig, hebben geen conflict met queryLanguage-waarden.

   Als u in een query aanvraag ook [spelling correctie](speller-how-to-add.md)gebruikt, gelden de queryLanguage die u hebt ingesteld gelijk aan de spelling, antwoorden en bijschriften. Er zijn geen onderdrukkingen voor afzonderlijke onderdelen. 

   Terwijl inhoud in een zoek index in meerdere talen kan worden samengesteld, is de query-invoer waarschijnlijk in één taal. De zoek machine controleert niet op compatibiliteit van queryLanguage, language Analyzer en de taal waarin inhoud is samengesteld. Zorg er daarom voor dat u query's moet uitvoeren om te voor komen dat er onjuiste resultaten worden geproduceerd.

<a name="searchfields"></a>

1. Set **`"searchFields"`** (optioneel, maar wordt aanbevolen).

   In een semantische query weerspiegelt de volg orde van de velden in ' searchFields ' de prioriteit of het relatieve belang van het veld in semantische classificaties. Alleen teken reeks velden op het hoogste niveau (zelfstandig of in een verzameling) worden gebruikt. Omdat searchFields andere gedragingen heeft in eenvoudige en volledige lucene-query's (waarbij er geen geïmpliceerde prioriteits volgorde is), hebben niet-teken reeks velden en subvelden geen fout, maar worden ze ook niet gebruikt in semantische volg orde.

   Volg de volgende richt lijnen bij het opgeven van searchFields:

   + Beknopte velden, zoals naam van hotels of een titel, moeten voorafgaan aan uitgebreide velden als beschrijving.

   + Als uw index een URL-veld heeft dat tekst is (leesbaar voor mensen zoals `www.domain.com/name-of-the-document-and-other-details` en niet op computer gericht `www.domain.com/?id=23463&param=eis` , zoals), plaatst u het in de lijst. (plaats het eerst als er geen beknopt titel veld is).

   + Als er slechts één veld is opgegeven, wordt dit beschouwd als een beschrijvende veld voor de semantische rang orde van documenten.  

   + Als er geen velden zijn opgegeven, worden alle Doorzoek bare velden gezien als semantische rang schikking van documenten. Dit wordt echter niet aanbevolen, omdat het mogelijk niet de meest optimale resultaten van uw zoek index oplevert.

1. Verwijder **`"orderBy"`** componenten als deze in een bestaande aanvraag aanwezig zijn. De semantische score wordt gebruikt voor het best Ellen van resultaten en als u expliciete Sorteer logica opneemt, wordt een HTTP 400-fout geretourneerd.

1. Voeg eventueel **`"answers"`** ingesteld op ' extra heren ' toe en geef het aantal antwoorden op als u meer dan 1 wilt.

1. Desgewenst kunt u de markerings stijl aanpassen die wordt toegepast op bijschriften. Met bijschriften past u de markerings opmaak toe op de sleutel door gang in het document waarin het antwoord wordt samenvatten. De standaardwaarde is `<em>`. Als u het type opmaak (bijvoorbeeld gele achtergrond) wilt opgeven, kunt u de highlightPreTag en highlightPostTag instellen.

1. Geef alle andere para meters op die u in de aanvraag wilt. Para meters zoals [speller](speller-how-to-add.md), [Select](search-query-odata-select.md)en Count verbeteren de kwaliteit van de aanvraag en lees baarheid van het antwoord.

### <a name="review-the-response"></a>Het antwoord controleren

Antwoord voor de bovenstaande query retourneert de volgende overeenkomst als de eerste verzameling. Bijschriften worden automatisch geretourneerd, met tekst zonder opmaak en gemarkeerde versies. Zie voor meer informatie over semantische antwoorden [semantische rang schikking en reacties](semantic-how-to-query-response.md).

```json
"@odata.count": 29,
"value": [
    {
        "@search.score": 1.8920634,
        "@search.rerankerScore": 1.1091284966096282,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Budget. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Budget. New Luxury Hotel. Be the first to stay.<strong> Bay views</strong> from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelId": "18",
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel.  Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Budget"
    },
```

### <a name="parameters-used-in-a-semantic-query"></a>Para meters die worden gebruikt in een semantische query

De volgende tabel bevat een overzicht van de query parameters die in een semantische query worden gebruikt, zodat u ze op een holistische manier kunt zien. Zie [zoeken naar documenten (rest preview)](/rest/api/searchservice/preview-api/search-documents) voor een lijst met alle para meters.

| Parameter | Type | Beschrijving |
|-----------|-------|-------------|
| Type | Tekenreeks | Geldige waarden zijn simple, Full en semantisch. De waarde ' semantisch ' is vereist voor semantische query's. |
| queryLanguage | Tekenreeks | Vereist voor semantische query's. Momenteel wordt alleen "en-US" geïmplementeerd. |
| searchFields | Tekenreeks | Een door komma's gescheiden lijst met Doorzoek bare velden. Optioneel, maar aanbevolen. Hiermee geeft u de velden op waarover de semantische classificatie plaatsvindt. </br></br>In tegens telling tot eenvoudige en volledige query typen, bepaalt de volg orde waarin velden worden weer gegeven voor rang.|
| beantwoordt |Tekenreeks | Optioneel veld om op te geven of semantische antwoorden worden opgenomen in het resultaat. Op dit moment wordt alleen ' extra heren ' geïmplementeerd. Antwoorden kunnen worden geconfigureerd om Maxi maal vijf te retour neren. Dit voor beeld "extra heren|count3 ' ' bevat een aantal van drie antwoorden. De standaardwaarde is 1.|

## <a name="query-with-search-explorer"></a>Query uitvoeren met Search Explorer

De volgende query streeft naar de ingebouwde hotels-voor beeld-index, met API-versie 2020-06-30-preview, en wordt uitgevoerd in Search Explorer. De `$select` component beperkt de resultaten tot slechts een paar velden, waardoor het eenvoudiger is om te scannen in de uitgebreide json in Search Explorer.

### <a name="with-querytypesemantic"></a>Met query type = semantisch

```json
search=I want a nice hotel on the water with a great restaurant&$select=HotelId,HotelName,Description,Tags&queryType=semantic&queryLanguage=english&searchFields=Description,Tags
```

De eerste paar resultaten zijn als volgt.

```json
{
    "@search.score": 0.38330218,
    "@search.rerankerScore": 0.9754053303040564,
    "HotelId": "18",
    "HotelName": "Oceanside Resort",
    "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
    "Tags": [
        "view",
        "laundry service",
        "air conditioning"
    ]
},
{
    "@search.score": 1.8920634,
    "@search.rerankerScore": 0.8829904259182513,
    "HotelId": "36",
    "HotelName": "Pelham Hotel",
    "Description": "Stunning Downtown Hotel with indoor Pool. Ideally located close to theatres, museums and the convention center. Indoor Pool and Sauna and fitness centre. Popular Bar & Restaurant",
    "Tags": [
        "view",
        "pool",
        "24-hour front desk service"
    ]
},
{
    "@search.score": 0.95706713,
    "@search.rerankerScore": 0.8538530203513801,
    "HotelId": "22",
    "HotelName": "Stone Lion Inn",
    "Description": "Full breakfast buffet for 2 for only $1.  Excited to show off our room upgrades, faster high speed WiFi, updated corridors & meeting space. Come relax and enjoy your stay.",
    "Tags": [
        "laundry service",
        "air conditioning",
        "restaurant"
    ]
},
```

### <a name="with-querytype-default"></a>Met query type (standaard)

Voer voor vergelijking dezelfde query uit als hierboven, zodat u deze kunt verwijderen `&queryType=semantic&queryLanguage=english&searchFields=Description,Tags` . U ziet dat er geen `"@search.rerankerScore"` resultaten zijn en dat verschillende hotels op de drie belangrijkste posities worden weer gegeven.

```json
{
    "@search.score": 8.633856,
    "HotelId": "3",
    "HotelName": "Triple Landscape Hotel",
    "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
    "Tags": [
        "air conditioning",
        "bar",
        "continental breakfast"
    ]
},
{
    "@search.score": 6.407289,
    "HotelId": "40",
    "HotelName": "Trails End Motel",
    "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport.",
    "Tags": [
        "continental breakfast",
        "view",
        "view"
    ]
},
{
    "@search.score": 5.843788,
    "HotelId": "14",
    "HotelName": "Twin Vertex Hotel",
    "Description": "New experience in the Making.  Be the first to experience the luxury of the Twin Vertex. Reserve one of our newly-renovated guest rooms today.",
    "Tags": [
        "bar",
        "restaurant",
        "air conditioning"
    ]
},
```

## <a name="next-steps"></a>Volgende stappen

Stel in dat semantische classificatie en reacties worden gebaseerd op een eerste resultatenset. Elke logica waarmee de kwaliteit van de oorspronkelijke resultaten wordt verbeterd, wordt getransporteerd naar semantisch zoeken. Als volgende stap bekijkt u de functies die bijdragen aan de oorspronkelijke resultaten, waaronder analyserende onderdelen die van invloed zijn op hoe teken reeksen worden getokend, Score profielen die resultaten kunnen afstemmen en het standaard relevantie algoritme.

+ [Analyse functies voor tekst verwerking](search-analyzers.md)
+ [Gelijkenis en score in Cognitive Search](index-similarity-and-scoring.md)
+ [Scoringprofielen toevoegen](index-add-scoring-profiles.md)
+ [Overzicht van semantisch zoeken](semantic-search-overview.md)
+ [Spelling controle toevoegen aan query termen](speller-how-to-add.md)
