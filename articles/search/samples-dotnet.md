---
title: .NET-voorbeelden
titleSuffix: Azure Cognitive Search
description: Zoek Azure Cognitive Search demo C#-code voorbeelden die gebruikmaken van de .NET-client bibliotheken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 5567cf3bf606b08ce430f9189467d796498ae691
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953900"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Code voorbeelden van .NET (C#) voor Azure Cognitive Search

Meer informatie over de C#-code voorbeelden die de functionaliteit en werk stroom van een Azure Cognitive Search-oplossing demonstreren. Deze voor beelden gebruiken de [**azure Cognitive Search-client bibliotheek**](/dotnet/api/overview/azure/search) voor de [**Azure SDK voor .net**](/dotnet/azure/), die u via de volgende koppelingen kunt verkennen.

| Doel | Koppeling |
|--------|------|
| Pakket downloaden | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API-verwijzing | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API-test cases | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Broncode | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK steekproeven

Code voorbeelden uit het ontwikkel team van Azure SDK illustreren het gebruik van de API. U kunt deze voor beelden vinden in [**Azure/Azure-SDK-voor-net/tree/master/SDK/Search/Azure.Search.Documents/samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) op github.

| Voorbeelden | Description |
|---------|-------------|
| ["Hallo wereld", synchroon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Demonstreert hoe u een client maakt, de verificatie uitvoert en fouten afhandelt met behulp van synchrone methoden.|
| ["Hallo wereld" asynchroon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Demonstreert hoe u een client maakt, een verificatie uitvoert en fouten afhandelt met behulp van asynchrone methoden.  |
| [Bewerkingen op service niveau](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Demonstreert hoe u indexen, Indexeer functies, gegevens bronnen, vaardig heden en synoniemen maakt. Dit voor beeld laat ook zien hoe u service statistieken kunt ophalen en query's kunt uitvoeren op een index.  |
| [Index bewerkingen](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Demonstreert hoe u een actie uitvoert op een bestaande index. in dit geval wordt het aantal documenten opgehaald dat is opgeslagen in de index.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Toont een techniek voor het werken met niet-ondersteunde gegevens typen.  |
| [Documenten indexeren (push model)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Model voor het indexeren van ' push ', waarbij u een JSON-nettolading naar een index van een service verzendt.   |
| [Voor beeld van versleutelings sleutel](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Laat zien hoe u een door de klant beheerde versleutelings sleutel gebruikt om een extra beveiligingslaag via gevoelige inhoud toe te voegen.  |

## <a name="doc-samples"></a>Doc-voor beelden

Code voorbeelden van het Cognitive Search team illustreren de functies en werk stromen. In veel van deze voor beelden wordt verwezen naar zelf studies, Quick starts en artikelen met procedures. U kunt deze voor beelden vinden in [**Azure-samples/Azure-Search-DotNet-samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) en in [**Azure-samples/Search-DotNet-Getting-Started**](https://github.com/Azure-Samples/search-dotnet-getting-started/) op github.

| Voorbeelden | Artikel  |
|---------|-------------|
| [Snelstartgids](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Bron code voor [Quick Start: Maak een zoek index ](search-get-started-dotnet.md). In dit artikel wordt de basis werk stroom beschreven voor het maken, laden en doorzoeken van een zoek index met voorbeeld gegevens. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Bron code voor [het gebruik van de .net-client bibliotheek](search-howto-dotnet-sdk.md). In dit artikel wordt stapsgewijs beschreven hoe u de basis werk stroom gebruikt, maar in detail en bespreking van het API-gebruik.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Bron code [bijvoorbeeld: synoniemen toevoegen in C#](search-synonyms-tutorial-sdk.md). Synoniemen lijsten worden gebruikt voor het uitbreiden van query's en bieden een afwijkende term die extern is ten opzichte van een index. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Bron code voor [zelf studie: Indexeer Azure SQL-gegevens met behulp van de .NET SDK](search-indexer-tutorial.md). In dit artikel wordt beschreven hoe u een Azure SQL-Indexeer functie configureert met een planning, veld Toewijzingen en para meters.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Bron code voor [het configureren van door de klant beheerde sleutels voor gegevens versleuteling](search-security-manage-encryption-keys.md). |
| [Uw eerste app maken in C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Bron code voor [zelf studie: uw eerste Zoek-app maken](tutorial-csharp-create-first-app.md). Hoewel de meeste voor beelden console toepassingen zijn, gebruikt dit MVC-voor beeld een webpagina voor de index van de voor beeld-hotels, met een demonstratie van basis zoeken, paginering, automatisch aanvullen en voorgestelde query's, facetten en filters. |
| [meerdere gegevens bronnen](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Bron code voor [zelf studie: index uit meerdere gegevens bronnen](tutorial-multiple-data-sources.md). |
|  [optimaliseren-gegevens indexeren](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Bron code voor [zelf studie: de indexering optimaliseren met de Push-API](tutorial-optimize-indexing-push-api.md).  |
| [zelf studie-AI-verrijking](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Bron code voor [zelf studie: door AI gegenereerde Doorzoek bare inhoud van Azure-blobs met behulp van de .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Gebruik de voor beelden van de [browser](/samples/browse/?languages=csharp&products=azure-cognitive-search) om te zoeken naar micro soft-code voorbeelden in github, gefilterd op product, service en taal.

## <a name="other-samples"></a>Andere voor beelden

De volgende voor beelden worden ook gepubliceerd door het Cognitive Search-team, maar er wordt niet naar verwezen in de documentatie. Gekoppelde Leesmij-bestanden bieden gebruiks instructies.

| Voorbeelden | Description |
|---------|-------------|
| [Azure-zoeken-Power-vaardig heden](https://github.com/Azure-Samples/azure-search-power-skills)  | Bron code voor verbruikte aangepaste vaardig heden die u kunt opnemen in uw gewonnen oplossingen.  |
| [Oplossingsverbetering voor kennisanalyse](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Bevat sjablonen, ondersteunings bestanden en analytische rapporten die u helpen bij het prototypen van een end-to-end kennis analyse oplossing.  |
| [Covid-19-opslag plaats voor zoeken in apps](https://github.com/liamca/covid19search) | Opslag plaats van de bron code voor de op Cognitive Search gebaseerde [Covid-19-Zoek toepassing](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Meer informatie over de [JFK-oplossing](https://www.microsoft.com/ai/ai-lab-jfk-files). |