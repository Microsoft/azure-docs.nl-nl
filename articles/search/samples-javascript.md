---
title: JavaScript-voorbeelden
titleSuffix: Azure Cognitive Search
description: Zoek Azure Cognitive Search Demo Java script-code voorbeelden die gebruikmaken van de Azure .NET SDK voor Java script.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 6cd696bf0853b1e6bafc06f2e99b2808970fed25
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686724"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Java script-code voorbeelden voor Azure Cognitive Search

Meer informatie over de Java script-code voorbeelden die de functies en functionaliteit van Azure Cognitive Search demonstreren. De primaire opslag plaatsen zijn als volgt:

| Opslagplaats | Beschrijving |
|------------|-------------|
| [Azure-SDK-voor-JS/tree/master/SDK/Search/Search-documenten](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Voor beelden die worden geproduceerd door het team van de Azure SDK die worden geleverd bij de uments-client bibliotheek van Azure.Search.Docin de SDK. U kunt ook de [eenheids tests](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) voor de client bibliotheek bekijken om te zien hoe verschillende api's worden aangeroepen. |
| [Azure-samples/Azure-Search-java script-voor beelden](https://github.com/Azure-Samples/azure-search-javascript-samples) | Code voorbeelden die betrekking hebben op procedures, waaronder [Quick Start: een zoek index maken in Java script](search-get-started-javascript.md).|

> [!Tip]
> Gebruik de voor beelden van de [browser](/samples/browse/?languages=csharp&products=azure-cognitive-search) om te zoeken naar micro soft-code voorbeelden in github, gefilterd op product, service en taal.

## <a name="javascript-sdk-samples"></a>Java script SDK-voor beelden

De Azure SDK voor Java bevat talloze voor beelden en een aan de slag- [pagina](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started) voor het installeren van pakketten, het instellen van clients en het oplossen van problemen. Op de pagina worden ook de volgende voorbeeld categorieën beschreven, die hier worden weer gegeven voor uw gemak.

| Voorbeelden | Beschrijving |
|---------|-------------|
| [indexen](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Laat zien hoe [zoek indexen](search-what-is-an-index.md)maken, bijwerken, ophalen, weer geven en verwijderen. Deze voorbeeld categorie bevat ook een voor beeld van een service statistiek. |
| [dataSourceConnections (voor Indexeer functies)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Demonstreert hoe u Indexeer functie gegevens bronnen kunt maken, bijwerken, ophalen, weer geven en verwijderen. Dit is vereist voor indexering op basis van een indexer van [ondersteunde Azure-gegevens bronnen](search-indexer-overview.md#supported-data-sources). |
| [Indexeer functies](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Demonstreert hoe [Indexeer functies](search-indexer-overview.md)maken, bijwerken, ophalen, weer geven, opnieuw instellen en verwijderen.|
| [Vaardig heden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Demonstreert hoe u [vaardig heden](cognitive-search-working-with-skillsets.md) kunt maken, bijwerken, ophalen, weer geven en verwijderen die zijn gekoppeld aan Indexeer functies en die op AI gebaseerde verrijking uitvoeren tijdens het indexeren. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Laat zien hoe u [synoniemen kaarten](search-synonyms.md)kunt maken, bijwerken, ophalen, weer geven en verwijderen.  |
| [Query's](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Query's zijn alleen-lezen. Deze voorbeeld query wordt uitgevoerd op een open bare index die wordt gehost door micro soft.  |

## <a name="typescript-samples"></a>TypeScript-voorbeelden

De SDK biedt ook type script-voor beelden, die hier worden weer gegeven voor uw gemak.

| Voorbeelden | Beschrijving |
|---------|-------------|
| [indexen](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Laat zien hoe [zoek indexen](search-what-is-an-index.md)maken, bijwerken, ophalen, weer geven en verwijderen. Deze voorbeeld categorie bevat ook een voor beeld van een service statistiek. |
| [dataSourceConnections (voor Indexeer functies)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Demonstreert hoe u Indexeer functie gegevens bronnen kunt maken, bijwerken, ophalen, weer geven en verwijderen. Dit is vereist voor indexering op basis van een indexer van [ondersteunde Azure-gegevens bronnen](search-indexer-overview.md#supported-data-sources). |
| [Indexeer functies](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Demonstreert hoe [Indexeer functies](search-indexer-overview.md)maken, bijwerken, ophalen, weer geven, opnieuw instellen en verwijderen.|
| [Vaardig heden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Demonstreert hoe u [vaardig heden](cognitive-search-working-with-skillsets.md) kunt maken, bijwerken, ophalen, weer geven en verwijderen die zijn gekoppeld aan Indexeer functies en die op AI gebaseerde verrijking uitvoeren tijdens het indexeren. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Laat zien hoe u [synoniemen kaarten](search-synonyms.md)kunt maken, bijwerken, ophalen, weer geven en verwijderen.  |
| [Query's](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.js) | Demonstreert de uitvoering van query's op een open bare alleen-lezen index die wordt gehost door micro soft.  |

## <a name="documentation-samples"></a>Documentatievoorbeelden

De volgende voor beelden hebben een bijbehorend artikel in [Azure Cognitive Search-documentatie](https://docs.microsoft.com/azure/search/).

| Voorbeelden | Beschrijving | 
|---------|-------------|
| [Snelstartgids](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/Quickstart) | Bron code voor [Quick Start: een zoek index maken in Java script](search-get-started-javascript.md).  |

## <a name="standalone-samples"></a>Zelfstandige voor beelden

| Voorbeelden | Beschrijving |
|---------|-------------|
| [Azure-zoeken-reageren-sjabloon](https://github.com/dereklegenzoff/azure-search-react-template) | Sjabloon reageren voor Azure Cognitive Search (github.com) |