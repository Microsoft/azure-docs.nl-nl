---
title: Zoeken in JSON-blobs
titleSuffix: Azure Cognitive Search
description: Verken Azure JSON-blobs voor tekst inhoud met behulp van de Azure Cognitive Search BLOB-indexer. Indexeer functies automatiseren gegevens opname voor geselecteerde gegevens bronnen, zoals Azure Blob-opslag.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: a3c44d667b6baaf16e109dfb88c22c16a1ea2ce1
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697200"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>JSON-blobs indexeren met behulp van een BLOB-Indexeer functie in azure Cognitive Search

In dit artikel wordt beschreven hoe u een Azure Cognitive Search BLOB- [Indexeer functie](search-indexer-overview.md) kunt configureren voor het extra heren van gestructureerde inhoud van JSON-documenten in Azure Blob-opslag en hoe u deze kunt doorzoeken in azure Cognitive Search. Deze werk stroom maakt een Azure Cognitive Search-index en laadt deze met bestaande tekst die is geëxtraheerd uit JSON-blobs. 

U kunt de [Portal](#json-indexer-portal), [rest-API'S](#json-indexer-rest)of [.NET SDK](#json-indexer-dotnet) gebruiken voor het indexeren van JSON-inhoud. Algemene voor alle benaderingen is dat JSON-documenten zich bevinden in een BLOB-container in een Azure Storage-account. Zie [gegevens importeren in Azure Cognitive Search](search-what-is-data-import.md)voor hulp bij het pushen van JSON-documenten van andere niet-Azure-platforms.

JSON-blobs in Azure Blob-opslag zijn meestal één JSON-document (de parserings modus is `json` ) of een verzameling van JSON-entiteiten. Voor verzamelingen kan de BLOB een **matrix** van goed opgemaakte JSON-elementen bevatten (de geparseerde modus is `jsonArray` ). Blobs kunnen ook bestaan uit meerdere afzonderlijke JSON-entiteiten, gescheiden door een nieuwe regel (parseren modus is `jsonLines` ). De **parsingMode** para meter voor de aanvraag bepaalt de uitvoer structuren.

> [!NOTE]
> Zie [een-op-veel-indexering](search-howto-index-one-to-many-blobs.md)voor meer informatie over het indexeren van meerdere zoek documenten uit één blob.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Gebruik de portal

De eenvoudigste methode voor het indexeren van JSON-documenten is het gebruik van een wizard in de [Azure Portal](https://portal.azure.com/). Door het parseren van meta gegevens in de Azure Blob-container, kunt u met de wizard [**gegevens importeren**](search-import-data-portal.md) een standaard index maken, bron velden toewijzen aan doel index velden en de index in één bewerking laden. Afhankelijk van de grootte en complexiteit van de bron gegevens, kunt u in slechts enkele minuten een werk index voor het doorzoeken van de volledige tekst hebben.

U kunt het beste dezelfde regio of locatie gebruiken voor zowel Azure Cognitive Search als Azure Storage voor lagere latentie en om bandbreedte kosten te voor komen.

### <a name="1---prepare-source-data"></a>1: Bron gegevens voorbereiden

[Meld u aan bij de Azure Portal](https://portal.azure.com/) en [Maak een BLOB-container](../storage/blobs/storage-quickstart-blobs-portal.md) om uw gegevens te bevatten. Het niveau van de open bare toegang kan worden ingesteld op een van de geldige waarden.

U hebt de naam van het opslag account, de container naam en een toegangs sleutel nodig om uw gegevens op te halen in de wizard **gegevens importeren** .

### <a name="2---start-import-data-wizard"></a>2-wizard gegevens importeren starten

Op de pagina overzicht van uw zoek service kunt u [de wizard starten](search-import-data-portal.md) vanaf de opdracht balk.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd2.png" alt-text="De opdracht gegevens importeren in de portal" border="false":::

### <a name="3---set-the-data-source"></a>3: de gegevens bron instellen

Op de pagina **gegevens bron** moet de bron **Azure Blob Storage** zijn, met de volgende specificaties:

+ **Te extra heren gegevens** moeten *inhoud en meta gegevens* zijn. Als u deze optie kiest, kan de wizard een index schema afleiden en de velden voor importeren toewijzen.
   
+ De **parserings modus** moet worden ingesteld op *JSON*, *JSON-matrix* of JSON- *lijnen*. 

  *JSON* -gelet elke BLOB als één Zoek document, die als een onafhankelijk item in de zoek resultaten wordt weer gegeven. 

  *JSON-matrix* is voor blobs die goed opgemaakte JSON-gegevens bevatten. de juist opgemaakte JSON komt overeen met een matrix met objecten, of heeft een eigenschap die een matrix met objecten is en u wilt dat elk element wordt gegeleeerd als een zelfstandig, onafhankelijk Zoek document. Als blobs complex zijn en u geen JSON- *matrix* kiest, wordt de hele BLOB opgenomen als één document.

  *JSON-lijnen* zijn de blobs die bestaan uit meerdere JSON-entiteiten, gescheiden door een nieuwe regel, waarbij u wilt dat elke entiteit wordt geleend als zelfstandig, onafhankelijk Zoek document. Als blobs complex zijn en u geen JSON- *lijnen* kiest, wordt de hele BLOB opgenomen als één document.
   
+ **Opslag container** moet uw opslag account en container opgeven, of een Connection String die wordt omgezet in de container. U kunt verbindings reeksen ophalen op de pagina Blob service portal.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-data-source.png" alt-text="Definitie van BLOB-gegevens bron" border="false":::

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-de pagina verrijkte inhoud in de wizard overs Laan

Het toevoegen van cognitieve vaardig heden (of verrijking) is geen import vereiste. Tenzij u een specifieke behoefte hebt om [AI-verrijking toe te voegen](cognitive-search-concept-intro.md) aan uw indexerings pijplijn, moet u deze stap overs Laan.

Als u de stap wilt overs Laan, klikt u op de blauwe knoppen onder aan de pagina voor ' volgende ' en ' overs Laan '.

### <a name="5---set-index-attributes"></a>5-index kenmerken instellen

Op de pagina **index** ziet u een lijst met velden met een gegevens type en een reeks selectie vakjes voor het instellen van index kenmerken. Met de wizard kunt u een lijst met velden genereren op basis van meta gegevens en door de bron gegevens te bemonsteren. 

U kunt kenmerken bulksgewijs selecteren door te klikken op het selectie vakje boven aan een kenmerk kolom. Kies **ophalen** en **doorzoekbaar** voor elk veld dat moet worden geretourneerd naar een client-app en waarop de volledige Zoek verwerking van de tekst van toepassing is. U zult merken dat gehele getallen geen volledige tekst of fuzzy Doorzoek bare waarden zijn (getallen worden geëvalueerd Verbatim en zijn vaak nuttig in filters).

Lees de beschrijving van [index kenmerken](/rest/api/searchservice/create-index#bkmk_indexAttrib) en [taal analyse](/rest/api/searchservice/language-support) functies voor meer informatie. 

Neem even de tijd om uw selecties te controleren. Nadat u de wizard hebt uitgevoerd, worden er fysieke gegevens structuren gemaakt en kunt u deze velden niet bewerken zonder alle objecten te verwijderen en opnieuw te maken.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-index.png" alt-text="Definitie van BLOB-index" border="false":::

### <a name="6---create-indexer"></a>6-Indexeer functie maken

Volledig opgegeven, maakt de wizard drie afzonderlijke objecten in uw zoek service. Een gegevens bron object en Index object worden opgeslagen als benoemde resources in uw Azure Cognitive Search-service. Met de laatste stap maakt u een Indexeer functie-object. Als u de Indexeer functie een naam geeft, kan deze bestaan als een zelfstandige resource, die u onafhankelijk van het index-en gegevens bron object kunt plannen en beheren, gemaakt in dezelfde wizard reeks.

Als u niet bekend bent met Indexeer functies, is een *Indexeer functie* een resource in azure Cognitive Search die een externe gegevens bron verkent voor Doorzoek bare inhoud. De uitvoer van de wizard **gegevens importeren** is een Indexeer functie waarmee de JSON-gegevens bron wordt verkend, Doorzoek bare inhoud wordt geëxtraheerd en geïmporteerd in een index op Azure Cognitive Search.

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-indexer.png" alt-text="Definitie van BLOB-Indexer" border="false":::

Klik op **OK** om de wizard uit te voeren en alle objecten te maken. Indexering begint onmiddellijk.

U kunt het importeren van gegevens in de portal pagina's bewaken. Voortgangs meldingen geven de indexerings status aan en hoeveel documenten er worden geüpload. 

Wanneer het indexeren is voltooid, kunt u [Search Explorer](search-explorer.md) gebruiken om een query uit te voeren op de index.

> [!NOTE]
> Als de verwachte gegevens niet worden weer gegeven, moet u mogelijk meer kenmerken instellen voor meer velden. Verwijder de index en Indexeer functie die u zojuist hebt gemaakt, en stap de wizard opnieuw uit en wijzig uw selecties voor index kenmerken in stap 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST-API's gebruiken

U kunt de REST API gebruiken voor het indexeren van JSON-blobs, het volgen van een werk stroom met drie delen die wordt gebruikt voor alle Indexeer functies in azure Cognitive Search: een gegevens bron maken, een index maken, een Indexeer functie maken. Gegevens extractie uit Blob Storage vindt plaats wanneer u de aanvraag voor het maken van een index verzendt. Nadat deze aanvraag is voltooid, hebt u een query die kan worden geïndexeerd. 

Aan het einde van deze sectie kunt u de code van de [rest-voorbeeld](#rest-example) weergave bekijken, waarin wordt uitgelegd hoe u alle drie de objecten maakt. Deze sectie bevat ook informatie over [JSON-parserings modi](#parsing-modes), [één BLOB](#parsing-single-blobs), [JSON-matrices](#parsing-arrays)en [geneste matrices](#nested-json-arrays).

Voor op code gebaseerde JSON-indexering gebruikt u [postman of Visual Studio code](search-get-started-rest.md) en de rest API om deze objecten te maken:

+ [TabIndex](/rest/api/searchservice/create-index)
+ [gegevens bron](/rest/api/searchservice/create-data-source)
+ [Indexeer functie](/rest/api/searchservice/create-indexer)

Voor de volg orde van bewerkingen moet u objecten in deze volg orde maken en aanroepen. In tegens telling tot de werk stroom van de portal, is voor een code benadering een beschik bare index vereist om de JSON-documenten te accepteren die via de aanvraag voor het maken van een **Indexeer functie**

JSON-blobs in Azure Blob-opslag zijn meestal één JSON-document of een JSON-matrix. De BLOB-Indexeer functie in azure Cognitive Search kan worden geparseerd op basis van de manier waarop u de para meter **parsingMode** op de aanvraag hebt ingesteld.

| JSON-document | parsingMode | Beschrijving | Beschikbaarheid |
|--------------|-------------|--------------|--------------|
| Eén per BLOB | `json` | JSON-blobs worden als één tekst segment geparseerd. Elke JSON-BLOB wordt één Azure Cognitive Search-document. | Algemeen beschikbaar in de [rest](/rest/api/searchservice/indexer-operations) -API en [.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK. |
| Meerdere per BLOB | `jsonArray` | Parseert een JSON-matrix in de blob, waarbij elk element van de matrix een afzonderlijk Azure Cognitive Search-document wordt.  | Algemeen beschikbaar in de [rest](/rest/api/searchservice/indexer-operations) -API en [.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK. |
| Meerdere per BLOB | `jsonLines` | Parseert een blob die meerdere JSON-entiteiten (een matrix) bevat, gescheiden door een nieuwe regel, waarbij elke entiteit een afzonderlijk Azure Cognitive Search-document wordt. | Algemeen beschikbaar in de [rest](/rest/api/searchservice/indexer-operations) -API en [.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1-invoer voor de aanvraag samen stellen

Voor elke aanvraag moet u de service naam en de beheerders sleutel voor Azure Cognitive Search (in de kop POST) en de naam en sleutel van het opslag account voor de Blob-opslag opgeven. U kunt een [Web-API-test programma](search-get-started-rest.md) gebruiken om HTTP-aanvragen te verzenden naar Azure Cognitive Search.

Kopieer de volgende vier waarden naar Klad blok, zodat u ze in een aanvraag kunt plakken:

+ Naam van Azure Cognitive Search-service
+ Azure Cognitive Search-beheer sleutel
+ Naam van het Microsoft Azure Storage-account
+ Sleutel voor het Azure-opslag account

U kunt deze waarden vinden in de portal:

1. Kopieer de URL van de zoek service op de pagina overzicht van de portal pagina's voor Azure Cognitive Search.

2. Klik in het navigatie deel venster links op **sleutels** en kopieer vervolgens de primaire of secundaire sleutel (ze zijn equivalent).

3. Ga naar de portal pagina's voor uw opslag account. Klik in het navigatie deel venster links onder **instellingen** op **toegangs toetsen**. Deze pagina bevat de account naam en-sleutel. Kopieer de naam van het opslag account en een van de sleutels naar Klad blok.

### <a name="2---create-a-data-source"></a>2: een gegevens bron maken

Deze stap bevat verbindings gegevens van gegevens bronnen die worden gebruikt door de Indexeer functie. De gegevens bron is een benoemd object in azure Cognitive Search dat de verbindings gegevens persistent maakt. Het gegevens bron type, `azureblob` , bepaalt welk gedrag voor gegevens extractie door de Indexeer functie wordt aangeroepen. 

Vervang geldige waarden door de service naam, de beheerders sleutel, het opslag account en de tijdelijke aanduidingen van de account sleutel.

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   
```

### <a name="3---create-a-target-search-index"></a>3-een doel index voor zoeken maken 

Indexeer functies zijn gekoppeld aan een index schema. Als u de API gebruikt (in plaats van de portal), moet u een index vooraf voorbereiden zodat u deze kunt opgeven voor de indexerings bewerking.

De index slaat Doorzoek bare inhoud op in azure Cognitive Search. Als u een index wilt maken, geeft u een schema op waarmee de velden in een document, kenmerken en andere constructies worden opgegeven die de zoek ervaring vormen. Als u een index met dezelfde veld namen en gegevens typen als de bron maakt, komt de Indexeer functie overeen met de bron-en doel velden en bespaart u het werk van het expliciet toewijzen van de velden.

In het volgende voor beeld ziet u een aanvraag voor het maken van een [index](/rest/api/searchservice/create-index) . De index bevat een doorzoekbaar `content` veld voor het opslaan van de tekst die uit blobs is geëxtraheerd:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```


### <a name="4---configure-and-run-the-indexer"></a>4: de Indexeer functie configureren en uitvoeren

Net als bij een index en een gegevens bron, en Indexeer functie is ook een benoemd object dat u maakt en hergebruikt in een Azure Cognitive Search-service. Een volledig opgegeven aanvraag voor het maken van een Indexeer functie kan er als volgt uitzien:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }
```

De configuratie van de Indexeer functie bevindt zich in de hoofd tekst van de aanvraag. Hiervoor is een gegevens bron en een lege doel index vereist die al bestaan in azure Cognitive Search. 

Schema en para meters zijn optioneel. Als u deze weglaat, wordt de Indexeer functie onmiddellijk uitgevoerd, met `json` als de parseringsfout.

Deze specifieke Indexeer functie bevat geen veld toewijzingen. In de definitie van de Indexeer functie kunt u **veld Toewijzingen** laten staan als de eigenschappen van het JSON-bron document overeenkomen met de velden van de doel-zoek index. 


### <a name="rest-example"></a>REST-voor beeld

Deze sectie is een samen vatting van alle aanvragen die worden gebruikt voor het maken van objecten. Zie de vorige secties in dit artikel voor een bespreking van onderdeel onderdelen.

### <a name="data-source-request"></a>Gegevens bron aanvraag

Voor alle Indexeer functies is een gegevens bron object vereist dat verbindings gegevens levert aan bestaande gegevens. 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  
```

### <a name="index-request"></a>Index aanvraag

Voor alle Indexeer functies is een doel index vereist die de gegevens ontvangt. De hoofd tekst van de aanvraag definieert het index schema, bestaande uit velden, met het kenmerk dat het gewenste gedrag in een Doorzoek bare index kan ondersteunen. Deze index moet leeg zijn wanneer u de Indexeer functie uitvoert. 

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

### <a name="indexer-request"></a>Indexer-aanvraag

In deze aanvraag wordt een volledig opgegeven Indexeer functie weer gegeven. Het bevat veld toewijzingen, die in eerdere voor beelden zijn wegge laten. U kunt ' schema ', ' para meters ' en ' fieldMappings ' optioneel maken zolang er een beschik bare standaard waarde is. Als u ' schema ' weglaat, wordt de Indexeer functie onmiddellijk uitgevoerd. Als u ' parsingMode ' weglaat, wordt de index de standaard waarde ' JSON ' gebruikt.

Het maken van de Indexeer functie op Azure Cognitive Search triggers voor gegevens import. Het wordt onmiddellijk uitgevoerd en daarna volgens een schema als u er een hebt gegeven.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }
```

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>.NET SDK gebruiken

De .NET SDK heeft volledige pariteit met de REST API. We raden u aan de sectie voor gaande REST API te bekijken voor meer informatie over concepten, werk stromen en vereisten. U kunt vervolgens de volgende .NET API-referentie documentatie raadplegen voor het implementeren van een JSON-indexer in beheerde code.

+ [azure.search.documents. Indexes. Models. searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents. Indexes. Models. searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) 
+ [azure.search.documents. Indexes. Models. searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 
+ [azure.search.documents. Indexes. Models. SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Parserings modi

JSON-blobs kunnen aannemen dat er meerdere formulieren zijn. De para meter **parsingMode** op de JSON-indexer bepaalt hoe JSON-blob-inhoud wordt geparseerd en gestructureerd in een Azure Cognitive search-index:

| parsingMode | Beschrijving |
|-------------|-------------|
| `json`  | Indexeer elke BLOB als één document. Dit is de standaardinstelling. |
| `jsonArray` | Kies deze modus als uw blobs bestaan uit JSON-matrices en u elk element van de matrix nodig hebt om een afzonderlijk document in azure Cognitive Search te worden. |
|`jsonLines` | Kies deze modus als uw blobs bestaan uit meerdere JSON-entiteiten die door een nieuwe regel worden gescheiden en u wilt dat elke entiteit een afzonderlijk document in azure Cognitive Search wordt. |

U kunt een document beschouwen als één item in de zoek resultaten. Als u wilt dat elk element in de matrix wordt weer gegeven in Zoek resultaten als een onafhankelijk item, gebruikt u `jsonArray` de `jsonLines` optie of waar nodig.

In de definitie van de Indexeer functie kunt u eventueel [veld Toewijzingen](search-indexer-field-mappings.md) gebruiken om te kiezen welke eigenschappen van het JSON-bron document worden gebruikt om uw doel zoek index te vullen. `jsonArray`Als de matrix als een eigenschap met een lager niveau bestaat, kunt u voor de Parseerfout een documenthoofdmap instellen die aangeeft waar de matrix binnen de blob is geplaatst.

> [!IMPORTANT]
> Wanneer u `json` , `jsonArray` of `jsonLines` de parserings modus gebruikt Cognitive Search, wordt ervan uitgegaan dat alle blobs in uw gegevens bron JSON bevatten. Als u een combi natie van JSON-en niet-JSON-blobs in dezelfde gegevens bron wilt ondersteunen, laat het ons dan weten op [onze UserVoice-site](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Enkele JSON-blobs parseren

Standaard worden JSON-blobs door [Azure Cognitive Search BLOB-indexer](search-howto-indexing-azure-blob-storage.md) als één tekst segment geparseerd. Vaak wilt u de structuur van uw JSON-documenten behouden. Stel dat u het volgende JSON-document in Azure Blob-opslag hebt:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

De BLOB-indexer parseert het JSON-document naar één Azure Cognitive Search-document. De Indexeer functie laadt een index door "text", "datePublished" en "Tags" te zoeken in de bron op basis van identieke benoemde en getypte doel index velden.

Zoals aangegeven, zijn veld toewijzingen niet vereist. Op basis van een index met de velden Text, datePublished en tags, kan de BLOB-indexer de juiste toewijzing afleiden zonder dat er een veld toewijzing in de aanvraag aanwezig is.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON-matrices parseren

U kunt ook de JSON-matrix optie gebruiken. Deze optie is handig wanneer Blobs een *matrix van goed opgemaakte JSON-objecten* bevatten en u wilt dat elk element een afzonderlijk Azure Cognitive Search-document wordt. Als u bijvoorbeeld de volgende JSON-BLOB hebt opgegeven, kunt u uw Azure Cognitive Search index vullen met drie afzonderlijke documenten, elk met de velden id en text.  

```text
    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]
```

Voor een JSON-matrix moet de definitie van de Indexeer functie er ongeveer uitzien als in het volgende voor beeld. U ziet dat de parsingMode-para meter de `jsonArray` parser opgeeft. Als u de juiste parser opgeeft en de juiste gegevens invoer hebt, zijn de enige twee matrix-specifieke vereisten voor het indexeren van JSON-blobs.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
```

Ook hier ziet u dat veld Toewijzingen kunnen worden wegge laten. Uitgaande van een index met identieke velden met de naam ' id ' en ' text ', kan de BLOB-indexer de juiste toewijzing afleiden zonder een expliciete veld toewijzings lijst.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Geneste matrices parseren
Voor JSON-matrices die geneste elementen bevatten, kunt u een een `documentRoot` structuur met meerdere niveaus opgeven. Als uw blobs er bijvoorbeeld als volgt uitzien:

```http
    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }
```

Gebruik deze configuratie om de matrix te indexeren die is opgenomen in de `level2` eigenschap:

```http
    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }
```

## <a name="parse-blobs-separated-by-newlines"></a>Parseren van blobs gescheiden door nieuwen

Als uw BLOB meerdere JSON-entiteiten bevat, gescheiden door een nieuwe regel, en u wilt dat elk element een afzonderlijk Azure Cognitive Search-document wordt, kunt u kiezen voor de optie JSON-lijnen. Als u bijvoorbeeld de volgende BLOB (als er drie verschillende JSON-entiteiten zijn) hebt, kunt u uw Azure Cognitive Search index vullen met drie afzonderlijke documenten, elk met de velden id en text.

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Voor JSON-lijnen moet de definitie van de Indexeer functie er ongeveer uitzien als in het volgende voor beeld. U ziet dat de parsingMode-para meter de `jsonLines` parser opgeeft. 

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }
```

Ook hier ziet u dat veld Toewijzingen kunnen worden wegge laten, vergelijkbaar met de `jsonArray` parserings modus.

## <a name="add-field-mappings"></a>Veld toewijzingen toevoegen

Als de bron-en doel velden niet perfect zijn uitgelijnd, kunt u een sectie voor veld toewijzing definiëren in de hoofd tekst van de aanvraag voor expliciete koppelingen tussen velden.

Op dit moment kunnen Azure-Cognitive Search geen wille keurige JSON-documenten indexeren, omdat alleen primitieve gegevens typen, teken reeks matrices en geojson-punten worden ondersteund. U kunt echter **veld Toewijzingen** gebruiken om onderdelen van het JSON-document te kiezen en ze te ' liften ' in velden op het hoogste niveau van het zoek document. Zie [veld toewijzingen in Azure Cognitive Search-Indexeer](search-indexer-field-mappings.md)functies voor meer informatie over de basis principes van veld toewijzingen.

Bekijk ons voor beeld van een JSON-document:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

Stel dat er een zoek index met de volgende velden: van het type type en `text` `Edm.String` van het `date` `Edm.DateTimeOffset` `tags` type is `Collection(Edm.String)` . Let op de discrepantie tussen ' datePublished ' in de bron en `date` het veld in de index. Als u uw JSON wilt toewijzen aan de gewenste vorm, gebruikt u de volgende veld toewijzingen:

```http
    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
```

De bron veldnaam in de toewijzingen worden opgegeven met behulp van de [JSON-aanwijzer](https://tools.ietf.org/html/rfc6901) notatie. U begint met een slash om te verwijzen naar de hoofdmap van het JSON-document. vervolgens kiest u de gewenste eigenschap (op wille keurig niveau nesten) met behulp van door sturen gescheiden pad.

U kunt ook verwijzen naar afzonderlijke matrix elementen met behulp van een index op basis van nul. Als u bijvoorbeeld het eerste element van de matrix Tags in het bovenstaande voor beeld wilt kiezen, gebruikt u een veld toewijzing als volgt:

```http
    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Als de naam van een bron veld in een pad naar een veld toewijzing verwijst naar een eigenschap die niet voor komt in JSON, wordt die toewijzing overgeslagen zonder een fout. Dit wordt gedaan, zodat we documenten met een ander schema kunnen ondersteunen (dit is een veelgebruikte use-case). Omdat er geen validatie is, moet u er rekening mee houden om type fouten in de specificatie van de veld toewijzing te voor komen.
>

## <a name="help-us-make-azure-cognitive-search-better"></a>Help ons Azure Cognitive Search beter te maken
Als u een functie verzoek of ideeën voor verbeteringen hebt, geeft u uw invoer op [UserVoice](https://feedback.azure.com/forums/263029-azure-search/)op. Als u hulp nodig hebt bij het gebruik van de bestaande functie, plaatst u uw vraag op [stack overflow](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="see-also"></a>Zie ook

+ [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
+ [Azure Blob Storage indexeren met Azure Cognitive Search](search-howto-index-json-blobs.md)
+ [Indexeren van CSV-blobs met Azure Cognitive Search BLOB-Indexer](search-howto-index-csv-blobs.md)
+ [Zelf studie: semi-gestructureerde gegevens zoeken vanuit Azure Blob-opslag](search-semi-structured-data.md)