---
title: Zoeken in JSON-blobs
titleSuffix: Azure Cognitive Search
description: Verken Azure JSON-blobs voor tekst inhoud met behulp van de Azure Cognitive Search BLOB-indexer. Indexeer functies automatiseren gegevens opname voor geselecteerde gegevens bronnen, zoals Azure Blob-opslag.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: 8156966e9a1c000701a5cc1c68a70c4ee048c738
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259047"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>JSON-blobs indexeren met behulp van een BLOB-Indexeer functie in azure Cognitive Search

Dit artikel laat u zien hoe u [een BLOB-Indexeer functie kunt configureren](search-howto-indexing-azure-blob-storage.md) voor blobs die bestaan uit JSON-documenten. JSON-blobs in Azure Blob-opslag aannemen meestal een van deze formulieren:

+ Eén JSON-document
+ Een JSON-document met een matrix met goed opgemaakte JSON-elementen
+ Een JSON-document met meerdere entiteiten, gescheiden door een nieuwe regel

De BLOB-Indexeer functie biedt een **`parsingMode`** para meter voor het optimaliseren van de uitvoer van het zoek document op basis van de structuur voor het parseren van de modus bestaat uit de volgende opties:

| parsingMode | JSON-document | Beschrijving |
|--------------|-------------|--------------|
| **`json`** | Eén per BLOB | prijs JSON-blobs worden als één tekst segment geparseerd. Elke JSON-BLOB wordt één Zoek document. |
| **`jsonArray`** | Meerdere per BLOB | Parseert een JSON-matrix in de blob, waarbij elk element van de matrix een afzonderlijk Zoek document wordt.  |
| **`jsonLines`** | Meerdere per BLOB | Parseert een blob die meerdere JSON-entiteiten (ook een matrix) bevat, waarbij afzonderlijke elementen worden gescheiden door een nieuwe regel. De Indexeer functie start een nieuw Zoek document na elke nieuwe regel. |

Voor beide **`jsonArray`** en **`jsonLines`** moet u het [indexeren van één BLOB voor het maken van een groot aantal Zoek documenten](search-howto-index-one-to-many-blobs.md) door nemen om te begrijpen hoe de BLOB-Indexeer functie de ondubbelzinnigheid van de document sleutel afhandelt voor meerdere zoek documenten die zijn gemaakt op basis van dezelfde blob.

In de definitie van de Indexeer functie kunt u optioneel [veld Toewijzingen](search-indexer-field-mappings.md) instellen om te kiezen welke eigenschappen van het JSON-bron document worden gebruikt om uw doel zoek index te vullen. **`jsonArray`** Als de matrix bijvoorbeeld als een eigenschap met een lager niveau bestaat, kunt u in de verwerkings modus een eigenschap instellen **`document root`** die aangeeft waar de matrix binnen de blob is geplaatst.

In de volgende secties wordt elke modus uitvoeriger beschreven. Als u niet bekend bent met Indexeer functie-clients en-concepten, raadpleegt u [een Indexeer functie voor Zoek opdrachten maken](search-howto-create-indexers.md). U moet ook bekend zijn met de details van de [basis configuratie](search-howto-indexing-azure-blob-storage.md)van de BLOB-indexer, die hier niet wordt herhaald.

<a name="parsing-single-blobs"></a>

## <a name="index-single-json-documents-one-per-blob"></a>Eén JSON-document indexeren (één per blob)

Standaard parseren BLOB-indexen JSON-blobs als één tekst segment, één Zoek document voor elke Blob in een container. Als de JSON is gestructureerd, kan het zoek document de structuur weer geven, waarbij afzonderlijke elementen als afzonderlijke velden worden weer gegeven. Stel dat u het volgende JSON-document in Azure Blob-opslag hebt:

```http
{
    "article" : {
        "text" : "A hopefully useful article explaining how to parse JSON blobs",
        "datePublished" : "2020-04-13",
        "tags" : [ "search", "storage", "howto" ]    
    }
}
```

De BLOB-Indexeer functie parseert het JSON-document in één Zoek document, waarbij een index wordt geladen door ' text ', ' datePublished ' en ' Tags ' van de bron te vergelijken met identieke en getypte doel index velden. Op basis van een index met de velden Text, datePublished en tags, kan de BLOB-indexer de juiste toewijzing afleiden zonder dat er een veld toewijzing in de aanvraag aanwezig is.

Hoewel het standaard gedrag bestaat uit één Zoek document per JSON-blob, wordt door het instellen van de modus voor het parseren van JSON de interne veld toewijzingen voor inhoud gewijzigd, waarbij velden worden bevorderd in `content` werkelijke velden in de zoek index. Een voor beeld van een indexerings definitie voor de verwerkings **`json`** modus kan er als volgt uitzien:

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "json" } }
}
```

> [!NOTE]
> Net als bij alle Indexeer functies, als velden niet duidelijk overeenkomen, moet u waarschijnlijk expliciet afzonderlijke [veld Toewijzingen](search-indexer-field-mappings.md) opgeven, tenzij u de impliciete velden toewijzingen gebruikt die beschikbaar zijn voor blob-inhoud en meta gegevens, zoals beschreven in de [basis configuratie van BLOB-Indexeer functie](search-howto-indexing-azure-blob-storage.md).

### <a name="json-example-single-hotel-json-files"></a>JSON-voor beeld (JSON-bestanden met één hotel)

De [gegevens sets](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotel-json-documents) van het github JSON-document van het hotel zijn handig voor het testen van JSON-parsering, waarbij elke Blob een Structured JSON-bestand vertegenwoordigt. U kunt de gegevens bestanden uploaden naar Blob-opslag en de wizard **gegevens importeren** gebruiken om snel te evalueren hoe deze inhoud in afzonderlijke Zoek documenten wordt geparseerd. 

De gegevensset bestaat uit vijf blobs, elk met een hotel document met een adres verzameling en een verzameling ruimtes. De BLOB-indexer detecteert beide verzamelingen en weerspiegelt de structuur van de invoer documenten in het index schema.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON-matrices parseren

U kunt ook de JSON-matrix optie gebruiken. Deze optie is handig wanneer Blobs een matrix van goed opgemaakte JSON-objecten bevatten en u wilt dat elk element een afzonderlijk Zoek document wordt. Met behulp van **`jsonArrays`** de volgende JSON-BLOB worden drie afzonderlijke documenten gemaakt, elk met `"id"` en `"text"` velden.  

```text
[
    { "id" : "1", "text" : "example 1" },
    { "id" : "2", "text" : "example 2" },
    { "id" : "3", "text" : "example 3" }
]
```

De **`parameters`** eigenschap van de Indexeer functie bevat waarden voor het parseren van de modus. Voor een JSON-matrix moet de definitie van de Indexeer functie er ongeveer uitzien als in het volgende voor beeld.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

### <a name="jsonarrays-example-clinical-trials-sample-data"></a>jsonArrays-voor beeld (voorbeeld gegevens voor klinisch testen)

De [JSON-gegevens van klinisch experimenten](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/clinical-trials-json) die zijn ingesteld op github, zijn handig voor het testen van JSON-matrix parsering. U kunt de gegevens bestanden uploaden naar Blob-opslag en de wizard **gegevens importeren** gebruiken om snel te evalueren hoe deze inhoud in afzonderlijke Zoek documenten wordt geparseerd. 

De gegevensset bestaat uit acht blobs, elk met een JSON-matrix van entiteiten, voor een totaal van 100 entiteiten. De entiteiten zijn afhankelijk van de velden die worden ingevuld, maar het eind resultaat is één Zoek document per entiteit, van alle matrices, in alle blobs.

<a name="nested-json-arrays"></a>

### <a name="parsing-nested-json-arrays"></a>Geneste JSON-matrices parseren

Voor JSON-matrices die geneste elementen bevatten, kunt u een een **`documentRoot`** structuur met meerdere niveaus opgeven. Als uw blobs er bijvoorbeeld als volgt uitzien:

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

## <a name="parse-json-entities-separated-by-newlines"></a>JSON-entiteiten parseren die zijn gescheiden door nieuwen

Als uw BLOB meerdere JSON-entiteiten bevat, gescheiden door een nieuwe regel, en u wilt dat elk element een afzonderlijk Zoek document wordt, gebruikt u **`jsonLines`** .

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

Voor JSON-lijnen moet de definitie van de Indexeer functie er ongeveer uitzien als in het volgende voor beeld.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-json-indexer",
    "dataSourceName" : "my-blob-datasource",
    "targetIndexName" : "my-target-index",
    "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
}
```

### <a name="jsonlines-example-caselaw-sample-data"></a>jsonLines-voor beeld (caselaw-voorbeeld gegevens)

De [CASELAW JSON data set](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/caselaw) op github is handig voor het testen van JSON nieuwe regel parsering. Net als bij andere voor beelden kunt u deze gegevens uploaden naar Blob-opslag en kunt u de wizard **gegevens importeren** gebruiken om snel de impact van de parserings modus op afzonderlijke blobs te evalueren.

De gegevensset bestaat uit één blob met tien JSON-entiteiten gescheiden door een nieuwe regel, waarbij elke entiteit één juridische case beschrijft. Het eind resultaat is één Zoek document per entiteit.

## <a name="map-json-fields-to-search-fields"></a>JSON-velden toewijzen aan Zoek velden

Veld toewijzingen worden gebruikt voor het koppelen van een bron veld met een doel veld in situaties waarin de veld namen en typen niet identiek zijn. Veld Toewijzingen kunnen ook worden gebruikt om onderdelen van een JSON-document te koppelen en ze te ' til ' in velden op het hoogste niveau van het zoek document.

In het volgende voor beeld ziet u dit scenario. Zie [veld Toewijzingen](search-indexer-field-mappings.md)voor meer informatie over veld toewijzingen in het algemeen.

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

Bron velden worden opgegeven met behulp van de [JSON-wijzer](https://tools.ietf.org/html/rfc6901) notatie. U begint met een slash om te verwijzen naar de hoofdmap van het JSON-document. vervolgens kiest u de gewenste eigenschap (op wille keurig niveau nesten) met behulp van door sturen gescheiden pad.

U kunt ook verwijzen naar afzonderlijke matrix elementen met behulp van een index op basis van nul. Als u bijvoorbeeld het eerste element van de matrix Tags in het bovenstaande voor beeld wilt kiezen, gebruikt u een veld toewijzing als volgt:

```http
{ "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Als **`sourceFieldName`** verwijst naar een eigenschap die niet voor komt in de JSON-blob, wordt die toewijzing overgeslagen zonder een fout. Met dit gedrag kan indexering worden voortgezet voor JSON-blobs met een ander schema (dit is een veelgebruikte use-case). Omdat er geen validatie controle is, controleert u de toewijzingen zorgvuldig voor type fouten, zodat u niet de juiste reden hebt om documenten te verliezen.
>

## <a name="next-steps"></a>Volgende stappen

+ [BLOB-Indexeer functies configureren](search-howto-indexing-azure-blob-storage.md)
+ [Veld toewijzingen definiëren](search-indexer-field-mappings.md)
+ [Overzicht van indexeerfuncties](search-indexer-overview.md)
+ [CSV-blobs indexeren met een BLOB-Indexer](search-howto-index-csv-blobs.md)
+ [Zelf studie: semi-gestructureerde gegevens zoeken vanuit Azure Blob-opslag](search-semi-structured-data.md)