---
title: Zoeken in blobs voor tekst zonder opmaak
titleSuffix: Azure Cognitive Search
description: Een zoek Indexeer functie configureren om platte tekst uit Azure-blobs te halen voor Zoek opdrachten in volledige tekst in azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: b8881d3fa7ade08da103c5af4b828a12e74cc355
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99509449"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Blobs met tekst zonder opmaak indexeren in azure Cognitive Search

Wanneer u een [BLOB Indexeer functie](search-howto-indexing-azure-blob-storage.md) gebruikt om Doorzoek bare BLOB-tekst te extra heren voor zoeken in volledige tekst, kunt u een parseringsfout toewijzen om betere index resultaten te krijgen. De Indexeer functie parseert de blob-inhoud standaard als één tekst segment. Als alle blobs echter onbewerkte tekst in dezelfde code ring bevatten, kunt u de indexerings prestaties aanzienlijk verbeteren met behulp van de verwerkings `text` modus.

De aanbevelingen voor `text` het gebruik van parsering zijn onder andere:

+ Bestands type is. txt
+ Bestanden zijn van elk type, maar de inhoud zelf is tekst (bijvoorbeeld programma bron code, HTML, XML enzovoort). Voor bestanden in een markerings taal worden de syntaxis tekens weer geven als statische tekst.

U kunt alle Indexeer functies serialiseren naar JSON. Standaard wordt de inhoud van het hele tekst bestand geïndexeerd in één groot veld als `"content": "<file-contents>"` . Nieuwe regel-en retour instructies zijn Inge sloten in het veld inhoud en worden weer gegeven als `\r\n\` .

Als u een meer granulair resultaat wilt hebben en als het bestands type compatibel is, kunt u de volgende oplossingen overwegen:

+ [`delimitedText`](search-howto-index-csv-blobs.md) de geparseerde modus, als de bron CSV is
+ [ `jsonArray` of `jsonLines` ](search-howto-index-json-blobs.md), als de bron JSON is

Een derde optie voor het afbreken van inhoud in meerdere delen vereist geavanceerde functies in de vorm van [AI-verrijking](cognitive-search-concept-intro.md). Er wordt een analyse toegevoegd waarmee segmenten van het bestand worden geïdentificeerd en toegewezen aan verschillende Zoek velden. U kunt een volledige of gedeeltelijke oplossing vinden via [ingebouwde vaardig heden](cognitive-search-predefined-skills.md), maar een meer waarschijnlijke oplossing is het leer model dat uw inhoud begrijpt, gegeled in een aangepast leer model, verpakt in een [aangepaste vaardigheid](cognitive-search-custom-skill-interface.md).

## <a name="set-up-plain-text-indexing"></a>Indexering voor tekst zonder opmaak instellen

Als u de blobs voor tekst zonder opmaak wilt indexeren, maakt of werkt u een Indexeer functie definitie met de `parsingMode` configuratie-eigenschap in `text` op een aanvraag voor het maken van een [Indexeer functie](/rest/api/searchservice/create-indexer) :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text" } }
}
```

De `UTF-8` code ring wordt standaard gebruikt. Als u een andere code ring wilt opgeven, gebruikt u de `encoding` configuratie-eigenschap: 

```http
{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
}
```

## <a name="request-example"></a>Voorbeeld van aanvraag

De parserings modi worden opgegeven in de definitie van de Indexeer functie.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "name" : "my-plaintext-indexer",
  "dataSourceName" : "my-blob-datasource",
  "targetIndexName" : "my-target-index",
  "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
}
```

## <a name="next-steps"></a>Volgende stappen

+ [Indexeerfuncties in Azure Cognitive Search](search-indexer-overview.md)
+ [Een BLOB-Indexeer functie configureren](search-howto-indexing-azure-blob-storage.md)
+ [Overzicht van BLOB-indexering](search-blob-storage-integration.md)