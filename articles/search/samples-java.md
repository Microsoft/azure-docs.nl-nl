---
title: Java-voorbeelden
titleSuffix: Azure Cognitive Search
description: Zoek Azure Cognitive Search Demo Java-code voorbeelden die gebruikmaken van de Azure .NET SDK voor Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955034"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Java-code voorbeelden voor Azure Cognitive Search

Meer informatie over de Java-code voorbeelden die de functionaliteit en werk stroom van een Azure Cognitive Search-oplossing demonstreren. In deze voor beelden wordt de [**azure Cognitive Search-client bibliotheek**](/java/api/overview/azure/search-documents-readme) voor de [**Azure SDK voor Java**](/azure/developer/java/sdk)gebruikt, die u via de volgende koppelingen kunt verkennen.

| Doel | Koppeling |
|--------|------|
| Pakket downloaden | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API-verwijzing | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| API-test cases | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Broncode | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK steekproeven

Code voorbeelden uit het ontwikkel team van Azure SDK illustreren het gebruik van de API. U kunt deze voor beelden vinden in [**Azure/Azure-SDK-voor-Java/tree/master/SDK/Search/Azure-Search-documenten/src/samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) op github.

| Voorbeelden | Description |
|---------|-------------|
| [Zoek index maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Laat zien hoe u [zoek indexen](search-what-is-an-index.md)maakt. |
| [Synoniemen maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Laat zien hoe u [synoniemen kaarten](search-synonyms.md)maakt.  |
| [Zoek opdracht index maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Laat zien hoe u [Indexeer functies](search-indexer-overview.md)kunt maken. |
| [Zoek opdracht indexer gegevens bron maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Demonstreert hoe u Indexeer functie gegevens bronnen maakt, vereist voor indexering op basis van een indexer van [ondersteunde Azure-gegevens bronnen](search-indexer-overview.md#supported-data-sources). |
| [Vaardig heden maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Demonstreert hoe u [vaardig heden](cognitive-search-working-with-skillsets.md) maakt die gekoppelde Indexeer functies zijn en die op AI gebaseerde verrijking uitvoeren tijdens het indexeren. |
| [Documenten laden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Laat zien hoe u documenten uploadt of samenvoegt in een index in een [gegevens import](search-what-is-data-import.md) bewerking. |
| [Query syntaxis](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Laat zien hoe u een [eenvoudige query](search-query-overview.md)kunt instellen. |

## <a name="doc-samples"></a>Doc-voor beelden

Code voorbeelden van het Cognitive Search team illustreren de functies en werk stromen. In veel van deze voor beelden wordt verwezen naar zelf studies, Quick starts en artikelen met procedures. U kunt deze voor beelden vinden in [**Azure-samples/Azure-Search-java-samples**](https://github.com/Azure-Samples/azure-search-java-samples) op github.

| Voorbeelden | Artikel | 
|---------|-------------|
| [Snelstartgids](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Bron code voor [Quick Start: Maak een zoek index in Java en rest](search-get-started-java.md). Dit voor beeld is niet bijgewerkt voor de Java-SDK. Het aanroepen van de REST-Api's. |

> [!Tip]
> Gebruik de voor beelden van de [browser](/samples/browse/?languages=java&products=azure-cognitive-search) om te zoeken naar micro soft-code voorbeelden in github, gefilterd op product, service en taal.

## <a name="other-samples"></a>Andere voor beelden

De volgende voor beelden worden ook gepubliceerd door het Cognitive Search-team, maar er wordt niet naar verwezen in de documentatie. Gekoppelde Leesmij-bestanden bieden gebruiks instructies.

| Voorbeelden | Description |
|---------|-------------|
| [zoeken-Java-aan de slag](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | Maakt gebruik van de Java SDK-client bibliotheek om een zoek index te maken, te laden en op te vragen. Dit voor beeld is momenteel zelfstandig. |
| [Search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Toont een Azure Cosmos DB Indexeer functie in Java. Dit voor beeld is niet bijgewerkt voor de Java-SDK. Het aanroepen van de REST-Api's.|