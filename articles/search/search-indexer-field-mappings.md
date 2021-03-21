---
title: Veldtoewijzingen in indexeerfuncties
titleSuffix: Azure Cognitive Search
description: Configureer veld toewijzingen in een Indexeer functie om te zien wat de verschillen zijn in veld namen en gegevens representaties.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: fb3a77291d8b24d5774094533f8c214f1527d771
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99430442"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Veld Toewijzingen en trans formaties met Azure Cognitive Search Indexeer functies

![Indexerings fasen](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "indexerings fasen")

Wanneer u Azure Cognitive Search-Indexeer functies gebruikt, merkt u soms dat de invoer gegevens niet echt overeenkomen met het schema van uw doel index. In dergelijke gevallen kunt u **veld Toewijzingen** gebruiken om de vorm van uw gegevens tijdens het indexerings proces te wijzigen.

Een aantal situaties waarin veld Toewijzingen handig zijn:

* De gegevens bron bevat een veld met de naam `_id` , maar in Azure Cognitive Search zijn veld namen die beginnen met een onderstrepings teken niet toegestaan. Met een veld toewijzing kunt u effectief de naam wijzigen van een veld.
* U wilt verschillende velden in de index vullen op basis van dezelfde gegevens bron gegevens. Het is bijvoorbeeld mogelijk dat u verschillende analyse functies op deze velden wilt Toep assen.
* U wilt een index veld vullen met gegevens uit meer dan één gegevens bron en de gegevens bronnen gebruiken verschillende veld namen.
* U moet uw gegevens met Base64 coderen of decoderen. Veld Toewijzingen ondersteunen verschillende **toewijzings functies**, waaronder functies voor Base64-code ring en-decodering.

> [!NOTE]
> Veld toewijzingen in Indexeer functies zijn een eenvoudige manier om gegevens velden toe te wijzen aan index velden, met een zekere mogelijkheid voor gegevens conversie met licht gewicht. Complexere gegevens kunnen vooraf worden verwerkt om deze te wijzigen in een formulier dat kan worden geïndexeerd. Een van de opties die u kunt overwegen, is [Azure Data Factory](../data-factory/index.yml).

## <a name="set-up-field-mappings"></a>Veld toewijzingen instellen

Een veld toewijzing bestaat uit drie delen:

1. A `sourceFieldName` , waarmee een veld in de gegevens bron wordt aangeduid. Deze eigenschap is vereist.
2. Een optioneel `targetFieldName` , waarmee een veld in uw zoek index wordt aangeduid. Als u dit weglaat, wordt dezelfde naam gebruikt als voor de gegevens bron.
3. Een optioneel `mappingFunction` , waarmee u uw gegevens kunt transformeren met behulp van een van de vooraf gedefinieerde functies. Dit kan worden toegepast op de veld Toewijzingen invoer en uitvoer. [Hieronder](#mappingFunctions)vindt u de volledige lijst met functies.

Veld toewijzingen worden toegevoegd aan de `fieldMappings` matrix van de definitie van de Indexeer functie.

> [!NOTE]
> Als er geen veld toewijzingen worden toegevoegd, nemen Indexeer functies aan dat gegevens bron velden moeten worden toegewezen aan index velden met dezelfde naam. Als u een veld toewijzing toevoegt, worden deze standaard veld toewijzingen voor het bron-en doel veld verwijderd. Sommige Indexeer functies, zoals de [Indexeer functie van Blob Storage](search-howto-indexing-azure-blob-storage.md), voegen standaard veld toewijzingen toe voor het veld index sleutel.

## <a name="map-fields-using-rest"></a>Velden toewijzen met behulp van REST

U kunt veld toewijzingen toevoegen wanneer u een nieuwe Indexeer functie maakt met behulp van de API-aanvraag voor het maken van de [Indexeer functie](/rest/api/searchservice/create-Indexer) . U kunt de veld toewijzingen van een bestaande Indexeer functie beheren met de API-aanvraag [Update indexer](/rest/api/searchservice/update-indexer) .

Hier volgt een voor beeld van het toewijzen van een bron veld aan een doel veld met een andere naam:

```JSON
PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

In meerdere veld toewijzingen kan naar een bron veld worden verwezen. In het volgende voor beeld ziet u hoe u een veld kunt ' fork ', waarbij u hetzelfde bron veld kopieert naar twee verschillende index velden:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Cognitive Search maakt gebruik van niet-hoofdletter gevoelige vergelijking om de veld-en functie namen in veld toewijzingen op te lossen. Dit is handig (het is niet nodig om alle hoofd letters op te halen), maar het betekent wel dat uw gegevens bron of index geen velden kan bevatten die alleen per geval verschillen.  
>

## <a name="map-fields-using-net"></a>Velden toewijzen met .NET

U definieert veld toewijzingen in de .NET SDK met behulp van de klasse [FieldMapping](/dotnet/api/azure.search.documents.indexes.models.fieldmapping) , die de eigenschappen `SourceFieldName` en bevat `TargetFieldName` en een optionele `MappingFunction` verwijzing.

U kunt veld Toewijzingen opgeven wanneer u de Indexeer functie bouwt of later door de eigenschap rechtstreeks in te stellen `Indexer.FieldMappings` .

In het volgende C#-voor beeld worden de veld Toewijzingen ingesteld bij het maken van een Indexeer functie.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Functie voor veld toewijzing

Met een veld toewijzings functie wordt de inhoud van een veld getransformeerd voordat deze in de index wordt opgeslagen. De volgende toewijzings functies worden momenteel ondersteund:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>de functie base64Encode

Voert *URL-veilige base64-* code ring van de invoer teken reeks uit. Er wordt van uitgegaan dat de invoer UTF-8-code ring is.

#### <a name="example---document-key-lookup"></a>Voor beeld-zoeken naar document sleutels

Alleen URL-veilige tekens kunnen worden weer gegeven in een Azure Cognitive Search-document sleutel (omdat klanten het document moeten kunnen adresseren met de [lookup-API](/rest/api/searchservice/lookup-document) ). Als het Bron veld voor uw sleutel URL-onveilige tekens bevat, kunt u de `base64Encode` functie gebruiken om deze te converteren naar de indexerings tijd. Een document sleutel (zowel vóór als na de conversie) mag echter niet langer zijn dan 1.024 tekens.

Wanneer u de gecodeerde sleutel ophaalt tijdens de zoek tijd, kunt u de `base64Decode` functie gebruiken om de oorspronkelijke sleutel waarde op te halen en gebruiken om het bron document op te halen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

#### <a name="example---preserve-original-values"></a>Voor beeld: oorspronkelijke waarden behouden

De [Blob Storage Indexeer functie](search-howto-indexing-azure-blob-storage.md) voegt automatisch een veld toewijzing van `metadata_storage_path` , de URI van de blob, toe aan het veld index sleutel als er geen veld toewijzing is opgegeven. Deze waarde is base64-gecodeerd zodat deze veilig kan worden gebruikt als een Azure Cognitive Search-document sleutel. In het volgende voor beeld ziet u hoe u tegelijkertijd een *URL-veilige base64-* gecodeerde versie van `metadata_storage_path` aan een veld kunt toewijzen `index_key` en de oorspronkelijke waarde in een veld wilt behouden `metadata_storage_path` :

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

Als u geen eigenschap para meters voor uw toewijzings functie opgeeft, wordt standaard de waarde gebruikt `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Azure Cognitive Search ondersteunt twee verschillende base64-code ringen. U moet dezelfde para meters gebruiken bij het coderen en decoderen van hetzelfde veld. Zie voor meer informatie [Base64-coderings opties](#base64details) om te bepalen welke para meters u moet gebruiken.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>de functie base64Decode

Voert base64-code ring uit van de invoer teken reeks. Er wordt van uitgegaan dat de invoer een *URL-veilige* base64-gecodeerde teken reeks is.

#### <a name="example---decode-blob-metadata-or-urls"></a>Voor beeld: BLOB-meta gegevens of Url's decoderen

Uw bron gegevens kunnen bestaan uit base64-gecodeerde teken reeksen, zoals teken reeksen voor BLOB-meta gegevens of Web-Url's, die u doorzoekbaar wilt maken als tekst zonder opmaak. U kunt de `base64Decode` functie gebruiken om de gecodeerde gegevens weer in te scha kelen in reguliere teken reeksen bij het invullen van uw zoek index.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Als u geen eigenschap para meters opneemt, wordt standaard de waarde gebruikt `{"useHttpServerUtilityUrlTokenEncode" : true}` .

Azure Cognitive Search ondersteunt twee verschillende base64-code ringen. U moet dezelfde para meters gebruiken bij het coderen en decoderen van hetzelfde veld. Zie voor meer informatie [Base64-coderings opties](#base64details) om te bepalen welke para meters u moet gebruiken.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opties voor Base64-code ring

Azure Cognitive Search ondersteunt URL-veilige base64-code ring en normale base64-code ring. Een teken reeks die met base64 is gecodeerd tijdens het indexeren, moet later worden gedecodeerd met dezelfde coderings opties, anders wordt het resultaat niet met de oorspronkelijke waarde overeenkomen.

Als de `useHttpServerUtilityUrlTokenEncode` or `useHttpServerUtilityUrlTokenDecode` -para meters voor respectievelijk coderen en decoderen zijn ingesteld op `true` , wordt `base64Encode` gereageerd als [HttpServerUtility. UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode) en `base64Decode` gedraagt zich als [HttpServerUtility. UrlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode).

> [!WARNING]
> Als `base64Encode` wordt gebruikt voor het produceren van sleutel waarden, `useHttpServerUtilityUrlTokenEncode` moet worden ingesteld op True. Alleen URL-veilige base64-code ring kan worden gebruikt voor sleutel waarden. Zie [naamgevings regels &#40;Azure Cognitive Search&#41;](/rest/api/searchservice/naming-rules) voor de volledige set beperkingen voor tekens in sleutel waarden.

De .NET-bibliotheken in azure Cognitive Search uitgaan van de volledige .NET Framework, dat ingebouwde code ring biedt. De `useHttpServerUtilityUrlTokenEncode` Opties en maken `useHttpServerUtilityUrlTokenDecode` gebruik van deze ingebouwde functionaliteit. Als u .NET core of een ander Framework gebruikt, raden we u aan om deze opties in te stellen `false` en de functies code ring en decodering van uw Framework rechtstreeks aan te roepen.

De volgende tabel vergelijkt verschillende base64-code ringen van de teken reeks `00>00?00` . Als u de vereiste aanvullende verwerking (indien van toepassing) voor uw base64-functies wilt bepalen, past u de functie voor het coderen van de bibliotheek toe op de teken reeks `00>00?00` en vergelijkt u de uitvoer met de verwachte uitvoer `MDA-MDA_MDA` .

| Encoding | Uitvoer van base64-code ring | Aanvullende verwerking na bibliotheek codering | Aanvullende verwerking voordat de tape wisselaar wordt gedecodeerd |
| --- | --- | --- | --- |
| Base64 met opvulling | `MDA+MDA/MDA=` | URL-veilige tekens gebruiken en opvulling verwijderen | Gebruik standaard base64-tekens en opvulling toevoegen |
| Base64 zonder opvulling | `MDA+MDA/MDA` | Gebruik URL-veilige tekens | Gebruik standaard base64-tekens |
| URL-veilig base64 met opvulling | `MDA-MDA_MDA=` | Opvulling verwijderen | Opvulling toevoegen |
| URL-veilig base64 zonder opvulling | `MDA-MDA_MDA` | Geen | Geen |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>de functie extractTokenAtPosition

Hiermee wordt een teken reeks veld gesplitst met het opgegeven scheidings teken en wordt het token op de opgegeven positie in de resulterende splitsing gepickt.

Deze functie maakt gebruik van de volgende para meters:

* `delimiter`: een teken reeks die als scheidings teken moet worden gebruikt bij het splitsen van de invoer teken reeks.
* `position`: een op nul gebaseerde positie van het token dat moet worden gekozen nadat de invoer reeks is gesplitst.

Als de invoer bijvoorbeeld `Jane Doe` , de `delimiter` is `" "` (spatie) en de `position` 0, is het resultaat `Jane` ; als de `position` waarde 1 is, is het resultaat `Doe` . Als de positie verwijst naar een token dat niet bestaat, wordt er een fout geretourneerd.

#### <a name="example---extract-a-name"></a>Voor beeld: een naam ophalen

De gegevens bron bevat een `PersonName` veld en u wilt het indexeren als twee afzonderlijke `FirstName` `LastName` velden. U kunt deze functie gebruiken om de invoer te splitsen met behulp van de spatie als scheidings teken.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>de functie jsonArrayToStringCollection

Transformeert een teken reeks die is opgemaakt als een JSON-matrix met teken reeksen in een teken reeks matrix die kan worden gebruikt om een `Collection(Edm.String)` veld in de index te vullen.

Als de invoer teken reeks bijvoorbeeld is `["red", "white", "blue"]` , wordt het doel veld van het type `Collection(Edm.String)` gevuld met de drie waarden `red` , `white` en `blue` . Voor invoer waarden die niet kunnen worden geparseerd als JSON-teken reeks matrices, wordt een fout geretourneerd.

#### <a name="example---populate-collection-from-relational-data"></a>Voor beeld: verzameling van relationele gegevens invullen

Azure SQL Database heeft geen ingebouwd gegevens type dat op natuurlijke wijze is gekoppeld aan `Collection(Edm.String)` velden in Azure Cognitive Search. Als u velden voor teken reeks verzameling wilt vullen, kunt u de bron gegevens vooraf verwerken als een JSON-teken reeks matrix en vervolgens de `jsonArrayToStringCollection` functie mapping gebruiken.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>de functie urlEncode

Deze functie kan worden gebruikt om een teken reeks te coderen zodat deze ' URL safe ' is. Wanneer deze functie wordt gebruikt in combi natie met een teken reeks die tekens bevat die niet zijn toegestaan in een URL, worden deze ' onveilige ' tekens geconverteerd naar equivalente teken eenheden. Deze functie maakt gebruik van de UTF-8-indeling voor versleuteling.

#### <a name="example---document-key-lookup"></a>Voor beeld-zoeken naar document sleutels

`urlEncode` de functie kan worden gebruikt als alternatief voor de `base64Encode` functie als alleen onveilige URL-tekens moeten worden geconverteerd, terwijl andere tekens ongewijzigd blijven.

De invoer teken reeks is `<hello>` -en vervolgens wordt het doel veld van `(Edm.String)` het type ingevuld met de waarde `%3chello%3e`

Wanneer u de gecodeerde sleutel ophaalt tijdens de zoek tijd, kunt u de `urlDecode` functie gebruiken om de oorspronkelijke sleutel waarde op te halen en gebruiken om het bron document op te halen.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>de functie urlDecode

 Met deze functie wordt een URL-gecodeerde teken reeks geconverteerd naar een gedecodeerde teken reeks met UTF-8-code ring.

 ### <a name="example---decode-blob-metadata"></a>Voor beeld: BLOB-meta gegevens decoderen

 Sommige Azure Storage-clients coderen BLOB-meta gegevens automatisch als deze niet-ASCII-tekens bevatten. Als u bijvoorbeeld wilt dat meta gegevens kunnen worden doorzocht (als tekst zonder opmaak), kunt u de `urlDecode` functie gebruiken om de gecodeerde gegevens weer in te stellen op reguliere teken reeksen wanneer u uw zoek index vult.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>de functie fixedLengthEncode
 
 Deze functie zet een teken reeks van elke lengte om in een teken reeks met een vaste lengte.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>Voor beeld: document sleutels toewijzen die te lang zijn
 
Deze functie kan worden toegepast om de lengte van de document sleutel te beperken wanneer er fouten optreden bij een klacht over een document sleutel die langer is dan 1024 tekens.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```