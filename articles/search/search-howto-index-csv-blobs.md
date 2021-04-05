---
title: Zoeken in CSV-blobs
titleSuffix: Azure Cognitive Search
description: Pak CSV-bestand uit vanuit Azure Blob-opslag en importeer dit met behulp van de delimitedText-parserings modus.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: d9633031ca8358ab0498c2e806b22e6c4ddd3eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99430476"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>Het indexeren van CSV-blobs met behulp van de delimitedText-verwerkings modus en BLOB-Indexeer functies in azure Cognitive Search

De [Indexeer functie](search-howto-indexing-azure-blob-storage.md) van Azure Cognitive Search BLOB biedt een `delimitedText` parseringsfout voor CSV-bestanden die elke regel in het CSV-bestand als een afzonderlijk Zoek document behandelt. Met de volgende door komma's gescheiden tekst worden bijvoorbeeld `delimitedText` twee documenten in de zoek index als resultaat gegeven: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Zonder de `delimitedText` parserings modus wordt de volledige inhoud van het CSV-bestand beschouwd als één Zoek document.

Wanneer u meerdere zoek documenten van één BLOB maakt, moet u de [indexeringen indexeren om meerdere zoek documenten te maken](search-howto-index-one-to-many-blobs.md) om inzicht te krijgen in de werking van document toetstoewijzingen. De BLOB-indexer kan waarden zoeken of genereren waarmee elk nieuw document op unieke wijze wordt gedefinieerd. Met name een tijdelijke oplossing die wordt `AzureSearch_DocumentKey` gegenereerd wanneer een BLOB wordt geparseerd in kleinere delen, waarbij de waarde vervolgens wordt gebruikt als de sleutel van het zoek document in de index.

## <a name="setting-up-csv-indexing"></a>CSV-indexering instellen

Als u CSV-blobs wilt indexeren, maakt of werkt u een Indexeer functie definitie met de `delimitedText` parserings modus op een aanvraag voor het maken van een [Indexeer](/rest/api/searchservice/create-indexer) functie:

```http
{
  "name" : "my-csv-indexer",
  ... other indexer properties
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
}
```

`firstLineContainsHeaders` geeft aan dat de eerste (niet-lege) regel van elke BLOB kopteksten bevat.
Als blobs geen oorspronkelijke header regel bevatten, moeten de headers worden opgegeven in de Indexeer functie: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

U kunt het scheidings teken aanpassen met behulp van de `delimitedTextDelimiter` configuratie-instelling. Bijvoorbeeld:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Op dit moment wordt alleen de UTF-8-code ring ondersteund. Als u ondersteuning voor andere code ringen nodig hebt, stem u dan op [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Wanneer u de modus voor het parseren van tekst met scheidings tekens gebruikt Cognitive Search, wordt ervan uitgegaan dat alle blobs in uw gegevens bron CSV zijn. Als u een combi natie van CSV-en niet-CSV-blobs in dezelfde gegevens bron wilt ondersteunen, moet u deze op [UserVoice](https://feedback.azure.com/forums/263029-azure-search)stemmen.
>

## <a name="request-examples"></a>Aanvraag voorbeelden

Als u dit alles gebruikt, zijn hier de volledige voor beelden van payload. 

Bron 

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
}   
```

Indexeer functie

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-csv-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```


