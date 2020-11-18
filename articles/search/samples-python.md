---
title: Python-voorbeelden
titleSuffix: Azure Cognitive Search
description: Zoek de code voorbeelden van Azure Cognitive Search demo python die gebruikmaken van de Azure .NET SDK voor python of REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 3de630552f7ad2cc941fe23369398c10ffce5870
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686721"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Python-code voorbeelden voor Azure Cognitive Search

Meer informatie over de python-code voorbeelden die de functies en functionaliteit van Azure Cognitive Search demonstreren. De primaire opslag plaatsen zijn als volgt:

| Opslagplaats | Beschrijving |
|------------|-------------|
| [Azure-SDK-for-python/tree/master/SDK/Search/Azure-Search-documenten/samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Voor beelden die worden geproduceerd door het team van de Azure SDK die worden geleverd bij de uments-client bibliotheek van Azure.Search.Docin de SDK. U kunt ook de [eenheids tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) voor de client bibliotheek bekijken om te zien hoe verschillende api's worden aangeroepen. |
| [Azure-samples/Azure-Search-python-voor beelden](https://github.com/Azure-Samples/azure-search-python-samples) | Code voorbeelden die betrekking hebben op procedures, waaronder [Quick Start: een zoek index in python maken](search-get-started-python.md).|

> [!Tip]
> Gebruik de voor beelden van de [browser](/samples/browse/?languages=csharp&products=azure-cognitive-search) om te zoeken naar micro soft-code voorbeelden in github, gefilterd op product, service en taal.

## <a name="python-sdk-samples"></a>Python SDK-voorbeelden

De Azure SDK voor python bevat talloze voor beelden en een aan de slag- [pagina](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) die vereisten en pakket installaties bevat. De pagina bevat ook koppelingen naar de volgende voor beelden, die hier worden weer gegeven voor uw gemak.

| Voorbeelden | Beschrijving |
|---------|-------------|
| [Verifiëren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Laat zien hoe u een client configureert en verificatie voor de service uitvoert. | 
| [Index Create-Read-update-delete-bewerkingen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Laat zien hoe [zoek indexen](search-what-is-an-index.md)maken, bijwerken, ophalen, weer geven en verwijderen. |
| [Indexeer functie Create-Read-update-delete-bewerkingen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Demonstreert hoe [Indexeer functies](search-indexer-overview.md)maken, bijwerken, ophalen, weer geven, opnieuw instellen en verwijderen. |
| [Gegevens bronnen van Indexeer functie zoeken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Demonstreert hoe u Indexeer functie gegevens bronnen kunt maken, bijwerken, ophalen, weer geven en verwijderen. Dit is vereist voor indexering op basis van een indexer van [ondersteunde Azure-gegevens bronnen](search-indexer-overview.md#supported-data-sources). |
| [Synoniemen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Laat zien hoe u [synoniemen kaarten](search-synonyms.md)kunt maken, bijwerken, ophalen, weer geven en verwijderen.  |
| [Documenten laden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Laat zien hoe u documenten uploadt of samenvoegt in een index in een [gegevens import](search-what-is-data-import.md) bewerking. |
| [Eenvoudige query](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Laat zien hoe u een [eenvoudige query](search-query-overview.md)kunt instellen. |
| [Query filteren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Laat zien hoe u een [filter expressie](search-filters.md)instelt. |
| [Facet query](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Illustreert het werken met [facetten](search-filters-facets.md). |

## <a name="documentation-samples"></a>Documentatievoorbeelden

De volgende voor beelden hebben een bijbehorend artikel in [Azure Cognitive Search-documentatie](https://docs.microsoft.com/azure/search/).

| Voorbeelden | Beschrijving | 
|---------|-------------|
| [Snelstartgids](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Bron code voor [Quick Start: Maak een zoek index in python](search-get-started-python.md).  |
| [zelf studie-AI-verrijking](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Bron code voor [zelf studie: gebruik python en AI voor het genereren van Doorzoek bare inhoud van Azure-blobs](cognitive-search-tutorial-blob-python.md).  |
| [AzureML-aangepast-vaardigheid](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Bron code [bijvoorbeeld: Maak een aangepaste vaardigheid met behulp van python](cognitive-search-custom-skill-python.md).  |