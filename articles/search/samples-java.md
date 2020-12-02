---
title: Java-voorbeelden
titleSuffix: Azure Cognitive Search
description: Zoek Azure Cognitive Search Demo Java-code voorbeelden die gebruikmaken van de Azure .NET SDK voor Java.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 10dff18f7b9db7273fcd6ec92bcca5970bb83b08
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510366"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Java-code voorbeelden voor Azure Cognitive Search

Meer informatie over de Java-code voorbeelden die de functies en functionaliteit van Azure Cognitive Search demonstreren. De primaire opslag plaatsen zijn als volgt:

| Opslagplaats | Beschrijving |
|------------|-------------|
| [Azure-SDK-voor-Java/tree/master/SDK/Search/Azure-Search-documenten/src/samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Voor beelden die worden geproduceerd door het team van de Azure SDK die worden geleverd bij de uments-client bibliotheek van Azure.Search.Docin de SDK. U kunt ook de [eenheids tests](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test) voor de client bibliotheek bekijken om te zien hoe verschillende api's worden aangeroepen. |
| [Azure-samples/Azure-Search-java-voor beelden](https://github.com/Azure-Samples/azure-search-java-samples) | Code voorbeelden die betrekking hebben op procedures. **Voor beelden in deze opslag plaats zijn nog niet bijgewerkt met de Azure SDK voor Java**. Op dit moment roepen deze steek proeven REST-Api's aan in Java-code.|

> [!Tip]
> Gebruik de voor beelden van de [browser](/samples/browse/?languages=java&products=azure-cognitive-search) om te zoeken naar micro soft-code voorbeelden in github, gefilterd op product, service en taal.

## <a name="java-sdk-samples"></a>Java SDK-voorbeelden

De Azure SDK voor Java bevat talloze voor beelden en een [pagina aan](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) de slag die de pakket installatie omvat. De pagina bevat ook een groot aantal voor beelden. Enkele van de meest voorkomende bewerkingen worden hieronder weer gegeven voor uw gemak.

| Voorbeelden | Beschrijving |
|---------|-------------|
| [Zoek index maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Laat zien hoe u [zoek indexen](search-what-is-an-index.md)maakt. |
| [Synoniemen maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Laat zien hoe u [synoniemen kaarten](search-synonyms.md)maakt.  |
| [Zoek opdracht index maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Laat zien hoe u [Indexeer functies](search-indexer-overview.md)kunt maken. |
| [Zoek opdracht indexer gegevens bron maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Demonstreert hoe u Indexeer functie gegevens bronnen maakt, vereist voor indexering op basis van een indexer van [ondersteunde Azure-gegevens bronnen](search-indexer-overview.md#supported-data-sources). |
| [Vaardig heden maken](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Demonstreert hoe u [vaardig heden](cognitive-search-working-with-skillsets.md) maakt die gekoppelde Indexeer functies zijn en die op AI gebaseerde verrijking uitvoeren tijdens het indexeren. |
| [Documenten laden](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Laat zien hoe u documenten uploadt of samenvoegt in een index in een [gegevens import](search-what-is-data-import.md) bewerking. |
| [Query syntaxis](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Laat zien hoe u een [eenvoudige query](search-query-overview.md)kunt instellen. |

## <a name="documentation-samples"></a>Documentatievoorbeelden

De volgende voor beelden hebben een bijbehorend artikel in [Azure Cognitive Search-documentatie](./index.yml).

| Voorbeelden | Beschrijving | 
|---------|-------------|
| [Snelstartgids](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | Bron code voor [Quick Start: een zoek index maken in Java](search-get-started-java.md). In dit voor beeld worden de REST-Api's aangeroepen. |
| [Search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Toont een Azure Cosmos DB Indexeer functie in Java. In dit voor beeld worden de REST-Api's aangeroepen. |