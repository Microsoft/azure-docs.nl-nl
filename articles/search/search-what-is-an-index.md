---
title: Een zoekindex maken
titleSuffix: Azure Cognitive Search
description: Hierin worden de concepten en hulpprogram ma's voor indexering in azure Cognitive Search beschreven, met inbegrip van schema definities en de fysieke gegevens structuur.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/15/2020
ms.openlocfilehash: 3d5663177bb087e936a49dd7289659b684d85860
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116191"
---
# <a name="create-a-basic-search-index-in-azure-cognitive-search"></a>Een eenvoudige zoek index maken in azure Cognitive Search

In azure Cognitive Search slaat een *zoek index* Zoek bare inhoud op die wordt gebruikt voor volledige tekst en gefilterde query's. Een index wordt gedefinieerd door een schema en opgeslagen in de service, met het importeren van gegevens na een tweede stap. 

Indexen bevatten *documenten*. Een document is conceptueel gezien een enkele eenheid van Doorzoek bare gegevens in uw index. Een detail handelaar kan voor elk product een document hebben, een nieuws organisatie heeft mogelijk een document voor elk artikel, enzovoort. Deze concepten toewijzen aan meer vertrouwde database equivalenten: een *zoek index* is gelijk aan een *tabel* en *documenten* zijn ongeveer gelijk aan *rijen* in een tabel.

De fysieke structuur van een index wordt bepaald door het schema, met velden die zijn gemarkeerd als doorzoekbaar, wat resulteert in een omgekeerde index die voor dat veld is gemaakt. 

U kunt een index maken met de volgende hulpprogram ma's en Api's:

* Gebruik de wizard **index toevoegen** of **gegevens importeren** in de Azure Portal
* De [Create Index gebruiken (rest API)](/rest/api/searchservice/create-index)
* De [.NET-SDK](./search-get-started-dotnet.md) gebruiken

Het is eenvoudiger om te leren werken met een portal-hulp programma. De portal dwingt vereisten en schema regels af voor specifieke gegevens typen, zoals het niet toestaan van zoek mogelijkheden in volledige tekst in numerieke velden. Zodra u een stapsgewijze index hebt, kunt u overstappen op code door de JSON-definitie op te halen van de service met [Get index (rest API)](/rest/api/searchservice/get-index) en deze toe te voegen aan uw oplossing.

## <a name="recommended-workflow"></a>Aanbevolen werk stroom

Het aankomen van een laatste index ontwerp is een iteratief proces. Het is gebruikelijk om te beginnen met de portal om de eerste index te maken en vervolgens te scha kelen naar code om de index onder broncode beheer te plaatsen.

1. Bepaal of u [**import gegevens**](search-import-data-portal.md)kunt gebruiken. De wizard voert alles-in-één indexering op basis van Indexeer functie uit als de bron gegevens afkomstig zijn uit een [ondersteund gegevens bron type in azure](search-indexer-overview.md#supported-data-sources).

1. Als u geen **import gegevens** kunt gebruiken, begint u met **index toevoegen** om het schema te definiëren.

   ![Opdracht index toevoegen](media/search-what-is-an-index/add-index.png "Opdracht index toevoegen")

1. Geef een naam en sleutel op waarmee elk zoek document in de index uniek wordt geïdentificeerd. De sleutel is verplicht en moet van het type EDM. String zijn. Bij het importeren moet u een uniek veld in de bron gegevens aan dit veld toewijzen. 

   De portal geeft u een `id` veld voor de sleutel. Als u de standaard instelling wilt overschrijven `id` , maakt u een nieuw veld (bijvoorbeeld een nieuwe veld definitie genaamd `HotelId` ) en selecteert u dit in de **sleutel**.

   ![De vereiste eigenschappen invullen](media/search-what-is-an-index//field-attributes.png "De vereiste eigenschappen invullen")

1. Voeg meer velden toe. In de portal ziet u welke [veld kenmerken](#index-attributes) beschikbaar zijn voor verschillende gegevens typen. Als u niet bekend bent met het ontwerp van de index, is dit nuttig.

   Als inkomende gegevens hiërarchisch zijn, wijst u het gegevens type [complex type](search-howto-complex-data-types.md) toe om de geneste structuren aan te duiden. De ingebouwde voor beeld-gegevensset, hotels, illustreert complexe typen met behulp van een adres (bevat meerdere subvelden) die een een-op-een-relatie hebben met elk Hotel, en een verzameling van ruimten die complexe verzamelingen bevatten, waarbij meerdere kamers aan elk hotel zijn gekoppeld. 

1. Wijs [analyse](#analyzers) functies toe aan teken reeks velden voordat de index wordt gemaakt. Doe hetzelfde voor [suggesties](#suggesters) als u automatisch aanvullen wilt inschakelen voor specifieke velden.

1. Klik op **maken** om de fysieke structuren in uw zoek service te maken.

1. Nadat een index is gemaakt, gebruikt u aanvullende opdrachten om definities te controleren of meer elementen toe te voegen.

   ![Index pagina met kenmerken op gegevens type toevoegen](media/search-what-is-an-index//field-definitions.png "Index pagina met kenmerken op gegevens type toevoegen")

1. Down load het index schema met [Get index (rest API)](/rest/api/searchservice/get-index) en een hulp programma voor het testen van webtoepassingen zoals in het [bericht](search-get-started-rest.md). U hebt nu een JSON-weer gave van de index die u kunt aanpassen voor code.

1. [Laad uw index met gegevens](search-what-is-data-import.md). Azure Cognitive Search accepteert JSON-documenten. Als u gegevens wilt laden via een programma, kunt u postman gebruiken met JSON-documenten in de aanvraag lading. Als uw gegevens niet eenvoudig kunnen worden uitgedrukt als JSON, is deze stap het meest arbeids intensief. 

    Als een index met gegevens is geladen, moeten de meeste bewerkingen in bestaande velden de index verwijderen en opnieuw samen stellen.

1. Zoek uw index op, Bekijk de resultaten en herhaal verder naar het index schema totdat u begint met de resultaten die u verwacht. U kunt [**Search Explorer**](search-explorer.md) of postman gebruiken om de index op te vragen.

Plan tijdens de ontwikkeling regel matig opnieuw samen stellen. Omdat fysieke structuren in de service zijn gemaakt, is het [verwijderen en opnieuw maken van indexen](search-howto-reindex.md) nood zakelijk voor de meeste wijzigingen in een bestaande veld definitie. U kunt ook met een subset van uw gegevens werken om het opnieuw opbouwen sneller te laten verlopen. 

> [!Tip]
> Code in plaats van een portal-benadering wordt aanbevolen voor het samen werken aan index ontwerp en het importeren van gegevens. Als alternatief kunt u hulpprogram ma's zoals [postman](search-get-started-rest.md) of [Visual Studio code](search-get-started-vs-code.md) nuttig vinden bij het testen van de test omgeving wanneer ontwikkel projecten nog steeds in vroege fasen zijn. U kunt incrementele wijzigingen aanbrengen in een index definitie in een aanvraag tekst en vervolgens de aanvraag verzenden naar uw service om een index opnieuw te maken met behulp van een bijgewerkt schema.

## <a name="index-schema"></a>Index schema

Een index moet een naam hebben en een aangewezen sleutel veld (van EDM. String) in de verzameling velden. De [*verzameling velden*](#fields-collection) is doorgaans het grootste deel van een index, waarbij elk veld een naam heeft, wordt getypt en is voorzien van een toegestaan gedrag dat bepaalt hoe het wordt gebruikt. 

Andere elementen omvatten [suggesties](#suggesters), [Score profielen](#scoringprofiles), [analyse](#analyzers) functies die worden gebruikt voor het verwerken van teken reeksen in tokens volgens linguïstische regels of andere kenmerken die worden ondersteund door de Analyzer en de instellingen voor [externe script verwerking (CORS) voor cross-Origin](#corsoptions) .

```json
{
  "name": (optional on PUT; required on POST) "name_of_index",
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
  "suggesters": [
    {
      "name": "name of suggester",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["field1", "field2", ...]
    }
  ],
  "scoringProfiles": [
    {
      "name": "name of scoring profile",
      "text": (optional, only applies to searchable fields) {
        "weights": {
          "searchable_field_name": relative_weight_value (positive #'s),
          ...
        }
      },
      "functions": (optional) [
        {
          "type": "magnitude | freshness | distance | tag",
          "boost": # (positive number used as multiplier for raw score != 1),
          "fieldName": "...",
          "interpolation": "constant | linear (default) | quadratic | logarithmic",
          "magnitude": {
            "boostingRangeStart": #,
            "boostingRangeEnd": #,
            "constantBoostBeyondRange": true | false (default)
          },
          "freshness": {
            "boostingDuration": "..." (value representing timespan leading to now over which boosting occurs)
          },
          "distance": {
            "referencePointParameter": "...", (parameter to be passed in queries to use as reference location)
            "boostingDistance": # (the distance in kilometers from the reference location where the boosting range ends)
          },
          "tag": {
            "tagsParameter": "..." (parameter to be passed in queries to specify a list of tags to compare against target fields)
          }
        }
      ],
      "functionAggregation": (optional, applies only when functions are specified) 
        "sum (default) | average | minimum | maximum | firstMatching"
    }
  ],
  "analyzers":(optional)[ ... ],
  "charFilters":(optional)[ ... ],
  "tokenizers":(optional)[ ... ],
  "tokenFilters":(optional)[ ... ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) {
    "allowedOrigins": ["*"] | ["origin_1", "origin_2", ...],
    "maxAgeInSeconds": (optional) max_age_in_seconds (non-negative integer)
  },
  "encryptionKey":(optional){
    "keyVaultUri": "azure_key_vault_uri",
    "keyVaultKeyName": "name_of_azure_key_vault_key",
    "keyVaultKeyVersion": "version_of_azure_key_vault_key",
    "accessCredentials":(optional){
      "applicationId": "azure_active_directory_application_id",
      "applicationSecret": "azure_active_directory_application_authentication_key"
    }
  }
}
```

<a name="fields-collection"></a>

## <a name="fields-collection-and-field-attributes"></a>Velden verzameling en veld kenmerken

Velden hebben een naam, een type dat de opgeslagen gegevens classificeert en kenmerken die bepalen hoe het veld wordt gebruikt.

### <a name="data-types"></a>Gegevenstypen

| Type | Beschrijving |
|------|-------------|
| Edm.String |Tekst die eventueel kan worden getokend voor Zoek opdrachten in volledige tekst (woord afbreking, stam bestand, enzovoort). |
| Collection(EDM.String) |Een lijst met tekenreeksen die van tokens kan worden voorzien om te zoeken in de volledige tekst. Er is geen theoretische bovengrens voor het aantal items in een verzameling, maar de bovengrens van 16 MB voor de nettolading geldt voor alle verzamelingen. |
| Edm.Boolean |Bevat de waarden waar/niet waar. |
| Edm.Int32 |32-bits waarden van een heel getal. |
| Edm.Int64 |64-bits waarden van een heel getal. |
| Edm.Double |Numerieke gegevens met dubbele precisie. |
| Edm.DateTimeOffset |Datum en tijd waarden die worden weer gegeven in de OData v4-indeling (bijvoorbeeld `yyyy-MM-ddTHH:mm:ss.fffZ` of `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm` ). |
| Edm.GeographyPoint |Een punt voor een geografische locatie op de wereld. |

Zie [ondersteunde gegevens typen](/rest/api/searchservice/Supported-data-types)voor meer informatie.

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

## `analyzers`

Het element analyse functies stelt de naam in van de taal analyse die moet worden gebruikt voor het veld. Zie [analyse functies toevoegen aan een Azure Cognitive search-index](search-analyzers.md)voor meer informatie over het aanbod dat voor u beschikbaar is. Analyseerers kunnen alleen worden gebruikt met Doorzoek bare velden. Zodra de analyse functie is toegewezen aan een veld, kan deze niet meer worden gewijzigd, tenzij u de index opnieuw opbouwt.

## `suggesters`

Een suggestie is een sectie van het schema waarmee wordt gedefinieerd welke velden in een index worden gebruikt voor het ondersteunen van automatisch aanvullen of het type-ahead query's in Zoek opdrachten. Normaal gesp roken worden gedeeltelijke Zoek reeksen naar de [suggesties (rest API)](/rest/api/searchservice/suggestions) verzonden terwijl de gebruiker een zoek opdracht typt. de API retourneert een aantal voorgestelde documenten of zinsdelen. 

Velden die zijn toegevoegd aan een suggestie, worden gebruikt voor het bouwen van type-ahead zoek termen. Alle zoek termen worden tijdens het indexeren gemaakt en afzonderlijk opgeslagen. Zie Voorst [Ellen toevoegen](index-add-suggesters.md)voor meer informatie over het maken van een boom structuur.

## `corsOptions`

Java script aan de client zijde kan standaard geen Api's aanroepen omdat de browser alle cross-Origin-aanvragen voor komt. Als u cross-Origin query's naar uw index wilt toestaan, schakelt u CORS (cross-Origin Resource Sharing) in door het kenmerk **corsOptions** in te stellen. Uit veiligheids overwegingen wordt alleen voor de query-Api's CORS ondersteund. 

De volgende opties kunnen worden ingesteld voor CORS:

+ **allowedOrigins** (vereist): dit is een lijst met oorsprongen waaraan toegang tot uw index wordt verleend. Dit betekent dat elke Java script-code die door deze oorsprongen wordt bediend, een query kan uitvoeren op uw index (ervan uitgaande dat deze de juiste API-sleutel bevat). Elke oorsprong is doorgaans van het formulier `protocol://<fully-qualified-domain-name>:<port>` Hoewel deze `<port>` vaak wordt wegge laten. Zie [Cross-Origin Resource Sharing (Wikipedia)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) voor meer informatie.

  Als u toegang tot alle oorsprongen wilt toestaan, neemt u op `*` als één item in de **allowedOrigins** -matrix. *Dit wordt niet aanbevolen voor productie Zoek Services* , maar het is vaak handig voor het ontwikkelen en opsporen van fouten.

+ **maxAgeInSeconds** (optioneel): browsers gebruiken deze waarde om de duur (in seconden) te bepalen voor het cachen van CORS-Preflight-reacties. Dit moet een niet-negatief geheel getal zijn. Hoe groter deze waarde is, hoe beter de prestaties, maar hoe langer het duurt om de wijzigingen van het CORS-beleid van kracht te laten worden. Als deze niet is ingesteld, wordt een standaard duur van 5 minuten gebruikt.

## `scoringProfiles`

Een [Score profiel](index-add-scoring-profiles.md) is een sectie van het schema waarmee aangepaste Score gedragingen worden gedefinieerd waarmee u kunt beïnvloeden welke items hoger worden weer gegeven in de zoek resultaten. Score profielen bestaan uit veld gewichten en-functies. Als u deze wilt gebruiken, geeft u een profiel op naam op voor de query teken reeks.

Een standaard Score profiel werkt achter de schermen om een zoek score te berekenen voor elk item in een resultatenset. U kunt het interne, niet-genaamde Score profiel gebruiken. U kunt ook **defaultScoringProfile** instellen om een aangepast profiel te gebruiken als de standaard instelling, wanneer er geen aangepast profiel is opgegeven in de query reeks.

<a name="index-size"></a>

## <a name="attributes-and-index-size-storage-implications"></a>Kenmerken en index grootte (opslag implicaties)

De grootte van een index wordt bepaald door de grootte van de documenten die u uploadt, plus de index configuratie, zoals of u Voorst Ellen opneemt en hoe u kenmerken instelt voor afzonderlijke velden. 

In de volgende scherm afbeelding ziet u de index opslag patronen die voortkomen uit verschillende combi Naties van kenmerken. De index is gebaseerd op de voor **beeld-index** van onroerend goed, die u eenvoudig kunt maken met behulp van de wizard gegevens importeren. Hoewel de index schema's niet worden weer gegeven, kunt u de kenmerken afleiden op basis van de naam van de index. *Realestate-Doorzoek bare* index kan bijvoorbeeld het kenmerk doorzoekbaar hebben geselecteerd en niets anders, de index die *realestate* kan worden opgehaald, is geselecteerd en niets anders, enzovoort.

![Index grootte op basis van kenmerk selectie](./media/search-what-is-an-index/realestate-index-size.png "Index grootte op basis van kenmerk selectie")

Hoewel deze index varianten kunst matig zijn, kunnen we ernaar verwijzen naar een uitgebreidere vergelijking van de manier waarop kenmerken van opslag worden beïnvloed. Verhoogt u de index grootte met de instelling ' ophaalbaar '? Nee. Voegt velden toe aan een **suggestie** voor het verg Roten van index grootte? Ja.

Indexen die ondersteuning bieden voor filteren en sorteren, zijn proportioneel groter dan indexen die alleen zoeken in volledige tekst ondersteunen. Dit komt doordat filter-en sorteer bewerkingen zoeken naar exacte overeenkomsten, waarbij Verbatim-tekst teken reeksen moeten worden uitgevoerd. In tegens telling tot Doorzoek bare velden die volledige-tekst query's ondersteunen, wordt gebruikgemaakt van omgekeerde indexen, die worden gevuld met tokens die minder ruimte in beslag nemen dan voor hele documenten. 

> [!Note]
> Opslag architectuur wordt beschouwd als een implementatie details van Azure Cognitive Search en kan zonder kennisgeving worden gewijzigd. Er is geen garantie dat het huidige gedrag in de toekomst blijft behouden.

## <a name="next-steps"></a>Volgende stappen

Met een goed idee van index samenstelling kunt u door gaan in de portal om uw eerste index te maken. U kunt het beste beginnen met de wizard **gegevens importeren** , de gehoste gegevens bronnen *realestate-US-sample* of *hotels-voor beeld* kiezen.

> [!div class="nextstepaction"]
> [Wizard gegevens importeren (Portal)](search-get-started-portal.md)

Voor beide gegevens sets kan de wizard een index schema afleiden, de gegevens importeren en een Doorzoek bare index uitvoeren waarmee u een query kunt uitvoeren met behulp van Search Explorer. Deze gegevens bronnen vindt u op de pagina **verbinding maken met uw gegevens** van de wizard **gegevens importeren** .

   ![Een voor beeld-index maken](media/search-what-is-an-index//import-wizard-sample-data.png "Een voor beeld-index maken")