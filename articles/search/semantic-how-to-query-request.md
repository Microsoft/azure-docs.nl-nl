---
title: Een semantische query maken
titleSuffix: Azure Cognitive Search
description: Stel een semantisch query type in om de diepe leer modellen te koppelen aan het verwerken van query's, het uitstellen van intentie en context als onderdeel van de rang schikking en relevantie van de zoek opdracht.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c33739124092a17acf0590f00b2f9c3c09bf894e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104654659"
---
# <a name="create-a-query-for-semantic-captions-in-cognitive-search"></a>Een query maken voor semantische bijschriften in Cognitive Search

> [!IMPORTANT]
> Semantische zoek opdracht bevindt zich in de open bare preview, die beschikbaar is via de preview-REST API en Azure Portal. Preview-functies worden onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)aangeboden. Deze functies zijn Factureerbaar. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

In dit artikel leert u hoe u een zoek opdracht kunt formuleren die gebruikmaakt van semantische classificatie en die semantische bijschriften (en eventueel [semantische antwoorden](semantic-answers.md)) retourneert, met de nadruk op de meest relevante termen en zinsdelen. Bijschriften en antwoorden worden geretourneerd in query's die zijn geformuleerd met behulp van het ' semantische-query type '.

Bijschriften en antwoorden worden Verbatim opgehaald uit tekst in het zoek document. Het semantische subsysteem bepaalt welk deel van uw inhoud de kenmerken van een bijschrift of antwoord bevat, maar er worden geen nieuwe zinnen of zinsdelen samengesteld. Daarom werken inhoud met uitleg of definities het beste voor semantisch zoeken.

## <a name="prerequisites"></a>Vereisten

+ Een zoek service in een Standard-laag (S1, S2, S3), die zich in een van deze regio's bevindt: Noord-Centraal VS, VS-West, VS-West 2, VS-Oost 2, Europa-noord, Europa-west. Als u een bestaande S1-of meer-service in een van deze regio's hebt, kunt u toegang aanvragen zonder dat u een nieuwe service hoeft te maken.

+ Toegang tot semantische Zoek voorbeeld: [registreren](https://aka.ms/SemanticSearchPreviewSignup)

+ Een bestaande zoek index die Engelse inhoud bevat

+ Een Search-client voor het verzenden van query's

  De Search-client moet de preview REST-Api's voor de query aanvraag ondersteunen. U kunt [postman](search-get-started-rest.md), [Visual Studio code](search-get-started-vs-code.md)of code gebruiken voor het aanroepen van de preview-api's. U kunt ook [Search Explorer](search-explorer.md) in azure Portal gebruiken om een semantische query in te dienen.

+ Een [query aanvraag](/rest/api/searchservice/preview-api/search-documents) moet de semantische optie en andere para meters bevatten die in dit artikel worden beschreven.

## <a name="whats-a-semantic-query"></a>Wat is een semantische query?

In Cognitive Search is een query een aanvraag met para meters die de verwerking van query's en de vorm van de reactie bepaalt. Een *semantische query* voegt para meters toe die het semantische herstel model aanroepen dat de context en betekenis van overeenkomende resultaten kan beoordelen, meer relevante overeenkomsten voor het hoogste niveau moet verhogen en semantische antwoorden en bijschriften kan retour neren.

De volgende aanvraag is representatief voor een minimale semantische query (zonder antwoorden).

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Net als bij alle query's in Cognitive Search streeft de aanvraag naar de verzameling documenten van een enkele index. Bovendien gaat een semantische query dezelfde volg orde van parseren, analyses, scannen en scores als een niet-semantische query. 

Het verschil ligt in relevantie en score. Zoals gedefinieerd in deze preview-versie, is een semantische query waarvan de *resultaten* worden herstemd met behulp van een semantisch taal model, waardoor het mogelijk is om de overeenkomsten te laten lopen die het meest relevant zijn voor de semantische rangorde, in plaats van de scores die worden toegewezen door het standaard classificatie algoritme voor gelijkenis.

Alleen de Top 50 treffers van de oorspronkelijke resultaten kan semantisch worden geclassificeerd en alle ondertiteling in het antwoord worden meegenomen. Desgewenst kunt u een **`answer`** para meter opgeven voor de aanvraag om een mogelijk antwoord uit te pakken. Zie voor meer informatie [semantische antwoorden](semantic-answers.md).

## <a name="query-with-search-explorer"></a>Query uitvoeren met Search Explorer

[Search Explorer](search-explorer.md) is bijgewerkt met opties voor semantische query's. Deze opties worden weer gegeven in de portal na het volt ooien van de volgende stappen:

1. [Registreren](https://aka.ms/SemanticSearchPreviewSignup) en Admittance van uw zoek service in het preview-programma

1. Open de portal met de volgende syntaxis: `https://portal.azure.com/?feature.semanticSearch=true`

Query opties bevatten switches voor het inschakelen van semantische query's, searchFields en spelling correctie. U kunt ook de vereiste query parameters in de query teken reeks plakken.

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="Query opties in Search Explorer" border="true":::

## <a name="query-using-rest"></a>Query uitvoeren met REST

Gebruik de [Zoek documenten (rest preview)](/rest/api/searchservice/preview-api/search-documents) om de aanvraag via een programma te formuleren.

Een antwoord bevat bijschriften en wordt automatisch gemarkeerd. Als u wilt dat het antwoord spelling correctie of antwoorden bevat, voegt u een optionele **`speller`** of **`answers`** para meter toe aan de aanvraag.

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

De volgende tabel bevat een overzicht van de query parameters die in een semantische query worden gebruikt, zodat u ze op een holistische manier kunt zien. Zie [zoeken naar documenten (rest preview)](/rest/api/searchservice/preview-api/search-documents) voor een lijst met alle para meters.

| Parameter | Type | Beschrijving |
|-----------|-------|-------------|
| Type | Tekenreeks | Geldige waarden zijn simple, Full en semantisch. De waarde ' semantisch ' is vereist voor semantische query's. |
| queryLanguage | Tekenreeks | Vereist voor semantische query's. Momenteel wordt alleen "en-US" geïmplementeerd. |
| searchFields | Tekenreeks | Een door komma's gescheiden lijst met Doorzoek bare velden. Hiermee geeft u de velden op waarover de semantische rang schikking plaatsvindt, waaruit bijschriften en antwoorden worden geëxtraheerd. </br></br>In tegens telling tot eenvoudige en volledige query typen, bepaalt de volg orde waarin velden worden weer gegeven voor rang. Zie [stap 2: set searchFields](#searchfields)voor meer gebruiks instructies. |
| speller | Tekenreeks | Optionele para meter, niet specifiek voor semantische query's, waarmee verkeerd gespelde termen worden gecorrigeerd voordat de zoek machine wordt bereikt. Zie [spelling correctie toevoegen aan query's](speller-how-to-add.md)voor meer informatie. |
| beantwoordt |Tekenreeks | Optionele para meters die aangeven of semantische antwoorden worden opgenomen in het resultaat. Op dit moment wordt alleen ' extra heren ' geïmplementeerd. Antwoorden kunnen worden geconfigureerd om Maxi maal vijf te retour neren. De standaard waarde is één. Dit voor beeld toont een aantal van drie antwoorden: ' extractie \| count3 ' '. Zie voor meer informatie [semantische antwoorden retour neren](semantic-answers.md).|

### <a name="formulate-the-request"></a>De aanvraag formuleren

In deze sectie worden de query parameters beschreven die nodig zijn voor semantisch zoeken.

#### <a name="step-1-set-querytype-and-querylanguage"></a>Stap 1: set query type en queryLanguage

Voeg de volgende para meters aan de rest toe. Beide para meters zijn vereist.

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

De queryLanguage moet consistent zijn met alle [taal analyse](index-add-language-analyzers.md) functies die zijn toegewezen aan veld definities in het index schema. Als queryLanguage "en-US" is, moeten alle taal analysen ook een Engelse variant ("en. micro soft" of "en. lucene") zijn. Alle taal neutraal-analyse functies, zoals sleutel woord of eenvoudig, hebben geen conflict met queryLanguage-waarden.

Als u in een query aanvraag ook [spelling correctie](speller-how-to-add.md)gebruikt, gelden de queryLanguage die u hebt ingesteld gelijk aan de spelling, antwoorden en bijschriften. Er zijn geen onderdrukkingen voor afzonderlijke onderdelen. 

Terwijl inhoud in een zoek index in meerdere talen kan worden samengesteld, is de query-invoer waarschijnlijk in één taal. De zoek machine controleert niet op compatibiliteit van queryLanguage, language Analyzer en de taal waarin inhoud is samengesteld. Zorg er daarom voor dat u query's moet uitvoeren om te voor komen dat er onjuiste resultaten worden geproduceerd.

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>Stap 2: searchFields instellen

De para meter searchFields wordt gebruikt om de door gang te identificeren die moet worden geëvalueerd voor ' semantische gelijkenis ' met de query. Voor de preview-versie raden we u aan om searchFields leeg te laten, omdat het model een hint vereist om aan te geven welke velden het belangrijkst zijn voor het proces.

De volg orde van de searchFields is kritiek. Als u searchFields al gebruikt in bestaande code voor eenvoudige of volledige lucene-query's, moet u deze para meter opnieuw bezoeken om te controleren of er een veld volgorde is wanneer u overschakelt naar een semantisch query type.

Voor twee of meer searchFields:

+ Alleen teken reeks velden en teken reeks velden op het hoogste niveau in verzamelingen bevatten. Als u een niet-teken reeks velden of velden van het lagere niveau in een verzameling opneemt, is er geen fout, maar deze velden worden niet gebruikt in semantische volg orde.

+ Het eerste veld moet altijd beknopt zijn (zoals een titel of naam), in het ideale geval met 25 woorden.

+ Als de index een URL-veld heeft dat tekst bevat (leesbaar voor mensen zoals `www.domain.com/name-of-the-document-and-other-details` en niet op computer gericht `www.domain.com/?id=23463&param=eis` , zoals), plaatst u het in de lijst (of eerst als er geen beknopt titel veld is).

+ Volg deze velden op beschrijvende velden waarin het antwoord op semantische query's kan worden gevonden, zoals de hoofd inhoud van een document.

Als er slechts één veld is opgegeven, gebruikt u een beschrijvende veld waarin het antwoord op semantische query's kan worden gevonden, zoals de hoofd inhoud van een document. 

#### <a name="step-3-remove-orderby-clauses"></a>Stap 3: orderBy-componenten verwijderen

Verwijder alle orderBy-componenten, als deze in een bestaande aanvraag aanwezig zijn. De semantische score wordt gebruikt voor het best Ellen van resultaten en als u expliciete Sorteer logica opneemt, wordt een HTTP 400-fout geretourneerd.

#### <a name="step-4-add-answers"></a>Stap 4: antwoorden toevoegen

Voeg eventueel ' antwoorden ' toe als u aanvullende verwerking wilt opnemen die een antwoord geeft. Antwoorden (en bijschriften) worden geëxtraheerd uit passeren die zijn gevonden in de velden in searchFields. Zorg ervoor dat u in searchFields inhouds opgemaakte velden in kunt gebruiken om de beste antwoorden in een antwoord te krijgen. Zie voor meer informatie [semantische antwoorden retour neren](semantic-answers.md).

#### <a name="step-5-add-other-parameters"></a>Stap 5: andere para meters toevoegen

Stel alle andere para meters in die u in de aanvraag wilt. Para meters zoals [speller](speller-how-to-add.md), [Select](search-query-odata-select.md)en Count verbeteren de kwaliteit van de aanvraag en lees baarheid van het antwoord.

Desgewenst kunt u de markerings stijl aanpassen die wordt toegepast op bijschriften. Met bijschriften past u de markerings opmaak toe op de sleutel door gang in het document waarin het antwoord wordt samenvatten. De standaardwaarde is `<em>`. Als u het type opmaak (bijvoorbeeld gele achtergrond) wilt opgeven, kunt u de highlightPreTag en highlightPostTag instellen.

## <a name="evaluate-the-response"></a>Het antwoord evalueren

Net als bij alle query's bestaat een antwoord uit alle velden die zijn gemarkeerd als ophaalbaar, of alleen de velden die worden vermeld in de Select-para meter. Het bevat de oorspronkelijke relevantie score en kan ook een aantal of batch resultaten bevatten, afhankelijk van hoe u de aanvraag hebt opgesteld.

In een semantische query heeft het antwoord extra elementen: een nieuwe semantisch geclassificeerde relevantie score, bijschriften in tekst zonder opmaak en met hooglichten, en optioneel een antwoord.

In een client-app kunt u de zoek pagina zodanig structureren dat een bijschrift wordt toegevoegd als de beschrijving van de overeenkomst, in plaats van de volledige inhoud van een specifiek veld. Dit is handig als afzonderlijke velden te dicht bij de pagina met zoek resultaten zijn.

Het antwoord voor de bovenstaande voorbeeld query retourneert de volgende overeenkomst als de eerste verzameling. Bijschriften worden automatisch geretourneerd, met tekst zonder opmaak en gemarkeerde versies. Antwoorden worden uit het voor beeld wegge laten omdat er geen kan worden vastgesteld voor deze specifieke query en verzameling.

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>Volgende stappen

Stel in dat semantische classificatie en reacties worden gebaseerd op een eerste resultatenset. Elke logica waarmee de kwaliteit van de oorspronkelijke resultaten wordt verbeterd, wordt getransporteerd naar semantisch zoeken. Als volgende stap bekijkt u de functies die bijdragen aan de oorspronkelijke resultaten, waaronder analyserende onderdelen die van invloed zijn op hoe teken reeksen worden getokend, Score profielen die resultaten kunnen afstemmen en het standaard relevantie algoritme.

+ [Analyse functies voor tekst verwerking](search-analyzers.md)
+ [Classificatie algoritme voor gelijkenis](index-similarity-and-scoring.md)
+ [Scoreprofielen](index-add-scoring-profiles.md)
+ [Overzicht van semantisch zoeken](semantic-search-overview.md)
+ [Algoritme voor semantische classificatie](semantic-ranking.md)
