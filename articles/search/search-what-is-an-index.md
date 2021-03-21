---
title: Een index maken
titleSuffix: Azure Cognitive Search
description: Hierin worden de concepten en hulpprogram ma's voor indexering in azure Cognitive Search beschreven, met inbegrip van schema definities en de fysieke gegevens structuur.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 96594d573c308727217f537e5421dcb79f02c2ff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433791"
---
# <a name="creating-search-indexes-in-azure-cognitive-search"></a>Zoek indexen maken in azure Cognitive Search

Cognitive Search Zoek bare inhoud die wordt gebruikt voor volledige tekst en gefilterde query's in een *zoek index* worden opgeslagen. Een index wordt gedefinieerd door een schema en opgeslagen in de service, met het importeren van gegevens na een tweede stap. 

Indexen bevatten *Zoek documenten*. Een document is conceptueel gezien een enkele eenheid van Doorzoek bare gegevens in uw index. Een detail handelaar kan voor elk product een document hebben, een nieuws organisatie heeft mogelijk een document voor elk artikel, enzovoort. Deze concepten toewijzen aan meer vertrouwde database equivalenten: een *zoek index* is gelijk aan een *tabel* en *documenten* zijn ongeveer gelijk aan *rijen* in een tabel.

## <a name="whats-an-index-schema"></a>Wat is een index schema?

De fysieke structuur van een index wordt bepaald door het schema. De verzameling velden is doorgaans het grootste deel van een index, waarbij elk veld een naam heeft, een [gegevens type](/rest/api/searchservice/Supported-data-types)heeft toegewezen en is voorzien van een toegestaan gedrag dat bepaalt hoe het wordt gebruikt.

```json
{
  "name": "name_of_index, unique across the service",
  "fields": [
    {
      "name": "name_of_field",
      "type": "Edm.String | Collection(Edm.String) | Edm.Int32 | Edm.Int64 | Edm.Double | Edm.Boolean | Edm.DateTimeOffset | Edm.GeographyPoint",
      "searchable": true (default where applicable) | false (only Edm.String and Collection(Edm.String) fields can be searchable),
      "filterable": true (default) | false,
      "sortable": true (default where applicable) | false (Collection(Edm.String) fields cannot be sortable),
      "facetable": true (default where applicable) | false (Edm.GeographyPoint fields cannot be facetable),
      "key": true | false (default, only Edm.String fields can be keys),
      "retrievable": true (default) | false,
      "analyzer": "name_of_analyzer_for_search_and_indexing", (only if 'searchAnalyzer' and 'indexAnalyzer' are not set)
      "searchAnalyzer": "name_of_search_analyzer", (only if 'indexAnalyzer' is set and 'analyzer' is not set)
      "indexAnalyzer": "name_of_indexing_analyzer", (only if 'searchAnalyzer' is set and 'analyzer' is not set)
      "synonymMaps": [ "name_of_synonym_map" ] (optional, only one synonym map per field is currently supported)
    }
  ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "encryptionKey":(optional){ }
  }
}
```

Andere elementen worden samengevouwen voor het boog gebruik, maar de volgende koppelingen kunnen de details bieden: [suggesties](index-add-suggesters.md), [Score profielen](index-add-scoring-profiles.md), [analyse](search-analyzers.md) functies die worden gebruikt voor het verwerken van teken reeksen in tokens volgens taal kundige regels of andere kenmerken die worden ondersteund door de Analyzer, en CORS-instellingen [(cross-origin remote scripting)](#corsoptions) .

## <a name="choose-a-client"></a>Kies een client

Er zijn verschillende manieren om een zoek index te maken. U wordt aangeraden de Azure Portal of Sdk's voor vroegtijdige ontwikkeling en haalbaarheids tests te testen.

Plan tijdens de ontwikkeling regel matig opnieuw samen stellen. Omdat fysieke structuren in de service zijn gemaakt, is het [verwijderen en opnieuw maken van indexen](search-howto-reindex.md) nood zakelijk voor de meeste wijzigingen in een bestaande veld definitie. U kunt ook met een subset van uw gegevens werken om het opnieuw opbouwen sneller te laten verlopen.

### <a name="permissions"></a>Machtigingen

Voor alle bewerkingen met betrekking tot een zoek index, met inbegrip van de definitie GET-aanvragen, is een [admin API-sleutel](search-security-api-keys.md) voor de aanvraag vereist.

### <a name="limits"></a>Limieten

Alle [service lagen beperken](search-limits-quotas-capacity.md#index-limits) het aantal objecten dat u kunt maken. Als u op de laag gratis experimenteert, kunt u op elk gewenst moment drie indexen hebben.

### <a name="use-azure-portal-to-create-a-search-index"></a>Azure Portal gebruiken om een zoek index te maken

De portal biedt twee opties voor het maken van een zoek index: [**wizard gegevens importeren**](search-import-data-portal.md) en **index toevoegen** waarmee velden worden opgegeven voor het opgeven van een index schema. De wizard pakt extra bewerkingen uit door ook een Indexeer functie, gegevens bron en het laden van gegevens te maken. Als dit meer is dan u wilt, kunt u gewoon **index toevoegen** of een andere benadering gebruiken.

De volgende scherm afbeelding laat zien waar u de **index toevoegen** kunt vinden in de portal. **Gegevens importeren** vindt u in de rechter deur.

  :::image type="content" source="media/search-what-is-an-index/add-index.png" alt-text="Opdracht index toevoegen" border="true":::

> [!Tip]
> Het ontwerp van de index via de portal dwingt vereisten en schema regels af voor specifieke gegevens typen, zoals het niet toestaan van zoek mogelijkheden in volledige tekst in numerieke velden. Zodra u een mogelijke index hebt, kunt u de JSON uit de portal kopiëren en toevoegen aan uw oplossing.

### <a name="use-a-rest-client"></a>Een REST-client gebruiken

Postman en Visual Studio code (met een extensie voor Azure Cognitive Search) kunnen worden gebruikt als een client voor zoek index. Met behulp van beide hulpprogram ma's kunt u verbinding maken met uw zoek service en aanvragen voor [Create Index (rest)](/rest/api/searchservice/create-index) verzenden. Er zijn talloze zelf studies en voor beelden waarin REST-clients voor het maken van objecten worden gedemonstreerd. 

Begin met een van deze artikelen voor meer informatie over elke client:

+ [Een zoek index maken met behulp van REST en postman](search-get-started-rest.md)
+ [Aan de slag met Visual Studio Code en Azure Cognitive Search](search-get-started-vs-code.md)

Raadpleeg de [index bewerkingen (rest)](/rest/api/searchservice/index-operations) voor hulp bij het formuleren van index aanvragen.

### <a name="use-an-sdk"></a>Een SDK gebruiken

Voor Cognitive Search implementeren de Azure-Sdk's algemeen beschik bare functies. Zo kunt u een van de Sdk's gebruiken om een zoek index te maken. Allemaal bieden een **SearchIndexClient** met methoden voor het maken en bijwerken van indexen.

| Azure SDK | Client | Voorbeelden |
|-----------|--------|----------|
| .NET | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [Azure-Search-DotNet-samples/Quick Start/V11/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) |
| Java | [SearchIndexClient](/java/api/com.azure.search.documents.indexes.searchindexclient) | [CreateIndexExample. java](https://github.com/Azure/azure-sdk-for-java/blob/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) |
| Javascript | [SearchIndexClient](/javascript/api/@azure/search-documents/searchindexclient) | [Indexen](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/indexes) |
| Python | [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) | [sample_index_crud_operations. py](https://github.com/Azure/azure-sdk-for-python/blob/7cd31ac01fed9c790cec71de438af9c45cb45821/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) |

## <a name="define-fields"></a>Velden definiëren

Er wordt een zoek document gedefinieerd door de `fields` verzameling. U hebt velden nodig voor query's en sleutels. U hebt waarschijnlijk ook velden nodig om filters, facetten en sorteer bewerkingen te ondersteunen. Mogelijk hebt u ook velden nodig voor gegevens die een gebruiker nooit ziet, bijvoorbeeld velden voor winst marges of marketing promoties die u kunt gebruiken om de zoek positie te wijzigen.

Eén veld van het type EDM. string moet worden opgegeven als de document sleutel. Het wordt gebruikt om elk zoek document uniek te identificeren en is hoofdletter gevoelig. U kunt een document ophalen op basis van de sleutel om een detail pagina in te vullen.

Als inkomende gegevens hiërarchisch zijn, wijst u het gegevens type [complex type](search-howto-complex-data-types.md) toe om de geneste structuren aan te duiden. De ingebouwde voor beeld-gegevensset, hotels, illustreert complexe typen met behulp van een adres (bevat meerdere subvelden) die een een-op-een-relatie hebben met elk Hotel, en een verzameling van ruimten die complexe verzamelingen bevatten, waarbij meerdere kamers aan elk hotel zijn gekoppeld. 

Wijs analyse functies toe aan teken reeks velden voordat de index wordt gemaakt. Doe hetzelfde voor suggesties als u automatisch aanvullen wilt inschakelen voor specifieke velden.

<a name="index-attributes"></a>

### <a name="attributes"></a>Kenmerken

Veldkenmerken bepalen hoe een veld wordt gebruikt, bijvoorbeeld of het wordt gebruikt voor zoeken in volledige tekst, facetnavigatie, sorteerbewerkingen, enzovoort. 

Teken reeks velden worden vaak aangeduid als doorzoekbaar en kunnen worden opgehaald. Velden die worden gebruikt om de zoek resultaten te beperken, zijn "sorteerbaar", "filterable" en "facetable".

|Kenmerk|Beschrijving|  
|---------------|-----------------|  
|doorzoekbaar |Zoeken in volledige tekst mogelijk, onderworpen aan lexicale analyse, zoals het afbreken van woorden tijdens het indexeren. Als u een doorzoekbaar veld instelt op een waarde als 'zonnige dag', wordt de waarde intern gesplitst in de afzonderlijke tokens 'zonnige' en 'dag'. Zie [Hoe zoeken in de volledige tekst werkt](search-lucene-query-architecture.md) voor meer informatie.|  
|filterbaar |Hier wordt naar verwezen in $filter-query's. Bij filterbare velden van het type `Edm.String` of `Collection(Edm.String)` worden woorden niet afgebroken, dus vergelijkingen gelden alleen voor exacte overeenkomsten. Als u zo'n veld bijvoorbeeld instelt op 'zonnige dag', worden er met `$filter=f eq 'sunny'` geen overeenkomsten gevonden, maar met `$filter=f eq 'sunny day'` wel. |  
|sorteerbaar |Het systeem sorteert de resultaten standaard op score, maar u kunt het sorteren configureren op basis van velden in de documenten. Velden van het type `Collection(Edm.String)` kunnen niet ' sorteerbaar ' zijn. |  
|facetten |Wordt doorgaans gebruikt bij een weergave van zoekresultaten met een treffertelling per categorie (bijvoorbeeld hotels in een specifieke stad). Deze optie kan niet worden gebruikt bij velden van het type `Edm.GeographyPoint`. Velden van `Edm.String` het type dat kan worden gefilterd, ' sorteerbaar ' of ' facetable ' kunnen Maxi maal 32 kilo bytes lang zijn. Zie [Index maken (REST API)](/rest/api/searchservice/create-index) voor meer informatie.|  
|prestatie |Unieke id voor documenten binnen de index. Er moet precies één veld worden uitgekozen als sleutelveld. Dit veld moet van het type `Edm.String` zijn.|  
|ophalen mogelijk |Hiermee bepaalt u of het veld in een zoekresultaat kan worden geretourneerd. Dit is handig als u een veld (zoals *winstmarge*) wilt gebruiken als filter-, sorteer- of scoremechanisme, maar niet wilt dat het veld zichtbaar is voor de eindgebruiker. Dit kenmerk moet `true` zijn voor `key`-velden.|  

Hoewel u op elk gewenst moment nieuwe velden kunt toevoegen, worden bestaande velddefinities voor de hele levensduur van de index vergrendeld. Daarom gebruiken ontwikkelaars de portal doorgaans om eenvoudige indexen te maken, om ideeën uit te testen of om een instelling op te zoeken met behulp van de portalpagina's. Een frequente iteratie van een index-ontwerp is efficiënter als u een op code gebaseerde benadering hanteert, zodat u uw index eenvoudig kunt herbouwen.

> [!NOTE]
> De Api's die u gebruikt om een index te maken, hebben verschillende standaard gedragingen. Voor de [rest-api's](/rest/api/searchservice/Create-Index)zijn de meeste kenmerken standaard ingeschakeld (bijvoorbeeld ' Doorzoek bare ' en ' ophaalbaar ' zijn waar voor teken reeks velden). u hoeft ze vaak alleen in te stellen als u ze wilt uitschakelen. Voor de .NET SDK is het tegenovergestelde waar. Op elke eigenschap die u niet expliciet instelt, is de standaard instelling om het bijbehorende Zoek gedrag uit te scha kelen, tenzij u dit specifiek inschakelt.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Kenmerken en index grootte (opslag implicaties)

De grootte van een index wordt bepaald door de grootte van de documenten die u uploadt, plus de index configuratie, zoals of u Voorst Ellen opneemt en hoe u kenmerken instelt voor afzonderlijke velden. 

In de volgende scherm afbeelding ziet u de index opslag patronen die voortkomen uit verschillende combi Naties van kenmerken. De index is gebaseerd op de voor **beeld-index** van onroerend goed, die u eenvoudig kunt maken met behulp van de wizard gegevens importeren. Hoewel de index schema's niet worden weer gegeven, kunt u de kenmerken afleiden op basis van de naam van de index. *Realestate-Doorzoek bare* index kan bijvoorbeeld het kenmerk doorzoekbaar hebben geselecteerd en niets anders, de index die *realestate* kan worden opgehaald, is geselecteerd en niets anders, enzovoort.

![Index grootte op basis van kenmerk selectie](./media/search-what-is-an-index/realestate-index-size.png "Index grootte op basis van kenmerk selectie")

Hoewel deze index varianten kunst matig zijn, kunnen we ernaar verwijzen naar een uitgebreidere vergelijking van de manier waarop kenmerken van opslag worden beïnvloed. Verhoogt u de index grootte met de instelling ' ophaalbaar '? Nee. Voegt velden toe aan een **suggestie** voor het verg Roten van index grootte? Ja. 

Het is ook mogelijk om een veld filterbaar of sorteerbaar toe te voegen aan het opslag verbruik omdat gefilterde en gesorteerde velden niet worden getokend zodat teken reeksen kunnen worden vergeleken met Verbatim.

Ook niet gereflecteerd in de bovenstaande tabel is de impact van [analyse](search-analyzers.md)functies. Als u de edgeNgram-tokenizer gebruikt om Verbatim reeksen van tekens (a, AB, ABC, ABCD) op te slaan, is de grootte van de index groter dan wanneer u een Standard Analyzer hebt gebruikt.

> [!Note]
> Opslag architectuur wordt beschouwd als een implementatie details van Azure Cognitive Search en kan zonder kennisgeving worden gewijzigd. Er is geen garantie dat het huidige gedrag in de toekomst blijft behouden.

<a name="corsoptions"></a>

## <a name="about-corsoptions"></a>Wilt `corsOptions`

Index schema's bevatten een sectie voor de instelling `corsOptions` . Java script aan de client zijde kan standaard geen Api's aanroepen omdat de browser alle cross-Origin-aanvragen voor komt. Als u cross-Origin query's naar uw index wilt toestaan, schakelt u CORS (cross-Origin Resource Sharing) in door het kenmerk **corsOptions** in te stellen. Uit veiligheids overwegingen wordt alleen voor de query-Api's CORS ondersteund. 

De volgende opties kunnen worden ingesteld voor CORS:

+ **allowedOrigins** (vereist): dit is een lijst met oorsprongen waaraan toegang tot uw index wordt verleend. Dit betekent dat elke Java script-code die door deze oorsprongen wordt bediend, een query kan uitvoeren op uw index (ervan uitgaande dat deze de juiste API-sleutel bevat). Elke oorsprong is doorgaans van het formulier `protocol://<fully-qualified-domain-name>:<port>` Hoewel deze `<port>` vaak wordt wegge laten. Zie [Cross-Origin Resource Sharing (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) voor meer informatie.

  Als u toegang tot alle oorsprongen wilt toestaan, neemt u op `*` als één item in de **allowedOrigins** -matrix. *Dit wordt niet aanbevolen voor productie Zoek Services* , maar het is vaak handig voor het ontwikkelen en opsporen van fouten.

+ **maxAgeInSeconds** (optioneel): browsers gebruiken deze waarde om de duur (in seconden) te bepalen voor het cachen van CORS-Preflight-reacties. Dit moet een niet-negatief geheel getal zijn. Hoe groter deze waarde is, hoe beter de prestaties, maar hoe langer het duurt om de wijzigingen van het CORS-beleid van kracht te laten worden. Als deze niet is ingesteld, wordt een standaard duur van 5 minuten gebruikt.

## <a name="next-steps"></a>Volgende stappen

U kunt meteen aan de slag gaan met het maken van een index met behulp van nagenoeg elk voor beeld of overzicht van Cognitive Search. Voor starters kunt u een van de Snelstartgids kiezen uit de inhouds opgave.

Maar u wilt ook vertrouwd raken met methoden voor het laden van een index met gegevens. Definities van index definitie en gegevens import worden samen gedefinieerd. De volgende artikelen bevatten meer informatie over het laden van een index.

+ [Overzicht van gegevensimport](search-what-is-data-import.md)

+ [Documenten toevoegen, bijwerken of verwijderen (REST)](/rest/api/searchservice/addupdate-or-delete-documents) 