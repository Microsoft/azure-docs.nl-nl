---
title: Python-voorbeelden
titleSuffix: Azure Cognitive Search
description: Zoek de code voorbeelden van Azure Cognitive Search demo python die gebruikmaken van de Azure .NET SDK voor python of REST.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955119"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Python-code voorbeelden voor Azure Cognitive Search

Meer informatie over de python-code voorbeelden die de functionaliteit en werk stroom van een Azure Cognitive Search-oplossing demonstreren. In deze voor beelden wordt de [**azure Cognitive Search-client bibliotheek**](/python/api/overview/azure/search-documents-readme) voor de [**Azure SDK voor python**](/azure/developer/python/)gebruikt, die u via de volgende koppelingen kunt verkennen.

| Doel | Koppeling |
|--------|------|
| Pakket downloaden | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API-verwijzing | [Azure-zoeken-documenten](/python/api/azure-search-documents)  |
| API-test cases | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Broncode | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK steekproeven

Code voorbeelden uit het ontwikkel team van Azure SDK illustreren het gebruik van de API. U kunt deze voor beelden vinden in [**Azure-SDK-voor python/tree/master/SDK/Search/Azure-Search-documenten/samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) op github.

| Voorbeelden | Beschrijving |
|---------|-------------|
| [Verifieer](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Laat zien hoe u een client configureert en verificatie voor de service uitvoert. | 
| [Index Create-Read-update-delete-bewerkingen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Laat zien hoe [zoek indexen](search-what-is-an-index.md)maken, bijwerken, ophalen, weer geven en verwijderen. |
| [Indexeer functie Create-Read-update-delete-bewerkingen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Demonstreert hoe [Indexeer functies](search-indexer-overview.md)maken, bijwerken, ophalen, weer geven, opnieuw instellen en verwijderen. |
| [Gegevens bronnen van Indexeer functie zoeken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Demonstreert hoe u Indexeer functie gegevens bronnen kunt maken, bijwerken, ophalen, weer geven en verwijderen. Dit is vereist voor indexering op basis van een indexer van [ondersteunde Azure-gegevens bronnen](search-indexer-overview.md#supported-data-sources). |
| [Synoniemen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Laat zien hoe u [synoniemen kaarten](search-synonyms.md)kunt maken, bijwerken, ophalen, weer geven en verwijderen.  |
| [Documenten laden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Laat zien hoe u documenten uploadt of samenvoegt in een index in een [gegevens import](search-what-is-data-import.md) bewerking. |
| [Eenvoudige query](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Laat zien hoe u een [eenvoudige query](search-query-overview.md)kunt instellen. |
| [Query filteren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Laat zien hoe u een [filter expressie](search-filters.md)instelt. |
| [Facet query](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Illustreert het werken met [facetten](search-filters-facets.md). |

## <a name="doc-samples"></a>Doc-voor beelden

Code voorbeelden van het Cognitive Search team illustreren de functies en werk stromen. In veel van deze voor beelden wordt verwezen naar zelf studies, Quick starts en artikelen met procedures. U kunt deze voor beelden vinden in [**Azure-samples/Azure-Search-python-samples**](https://github.com/Azure-Samples/azure-search-python-samples) op github.

| Voorbeelden | Artikel |
|---------|---------|
| [Snelstartgids](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Bron code voor [Quick Start: Maak een zoek index in python](search-get-started-python.md). In dit artikel wordt de basis werk stroom beschreven voor het maken, laden en doorzoeken van een zoek index met voorbeeld gegevens. |
| [zelf studie-AI-verrijking](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | Bron code voor [zelf studie: gebruik python en AI voor het genereren van Doorzoek bare inhoud van Azure-blobs](cognitive-search-tutorial-blob-python.md). In dit artikel wordt beschreven hoe u een BLOB-Indexeer functie maakt met een cognitieve vaardig heden, waarbij onbewerkte inhoud door de vaardig heden wordt gemaakt en getransformeerd, zodat deze kan worden doorzocht of verbruikt. |
| [AzureML-aangepast-vaardigheid](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Bron code [bijvoorbeeld: Maak een aangepaste vaardigheid met behulp van python](cognitive-search-custom-skill-python.md). In dit artikel ziet u de integratie van Indexeer functie en vaardig heden met diepe Learning-modellen in Azure Machine Learning. |

> [!Tip]
> Gebruik de voor beelden van de [browser](/samples/browse/?languages=python&products=azure-cognitive-search) om te zoeken naar micro soft-code voorbeelden in github, gefilterd op product, service en taal.