---
title: .NET-voorbeelden
titleSuffix: Azure Cognitive Search
description: Zoek Azure Cognitive Search demo C#-code voorbeelden die gebruikmaken van de .NET-client bibliotheken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: d068365cc8197a579c0b043d3fff2da3d54eb803
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686840"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Code voorbeelden van .NET (C#) voor Azure Cognitive Search

Meer informatie over de C#-code voorbeelden die de functies en functionaliteit van Azure Cognitive Search demonstreren. De primaire opslag plaatsen zijn als volgt:

| Opslagplaats | Beschrijving |
|------------|-------------|
| [Azure-SDK-for-net/SDK/Search/Azure.Search.Documents/samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Voor beelden die worden geproduceerd door het team van de Azure SDK die worden geleverd bij de uments-client bibliotheek van Azure.Search.Docin de SDK. U kunt ook de [eenheids tests](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) voor de client bibliotheek bekijken om te zien hoe verschillende api's worden aangeroepen. |
| [Azure-samples/Azure-Search-DotNet-voor beelden](https://github.com/Azure-Samples/azure-search-dotnet-samples) | Voor beelden van artikelen die in de documentatie zijn opgenomen, inclusief [het gebruik van de .net-client bibliotheek](search-howto-dotnet-sdk.md).|
| [Azure-samples/Search-DotNet-Getting-Started](https://github.com/Azure-Samples/search-dotnet-getting-started) | Voor beelden van Snelstartgids en zelf studies in de documentatie.|

> [!Tip]
> Gebruik de voor beelden van de [browser](/samples/browse/?languages=csharp&products=azure-cognitive-search) om te zoeken naar micro soft-code voorbeelden in github, gefilterd op product, service en taal.

## <a name="net-sdk-samples"></a>.NET SDK-voorbeelden

De Azure SDK voor .NET omvat talloze voor beelden en een [Leesmij-bestand voor beelden](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) waarin elke naam wordt beschreven. Deze lijst wordt hieronder vermeld voor uw gemak.

| Voorbeelden | Beschrijving |
|---------|-------------|
| ["Hallo wereld", synchroon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Demonstreert hoe u een client maakt, de verificatie uitvoert en fouten afhandelt met behulp van synchrone methoden.|
| ["Hallo wereld" asynchroon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Demonstreert hoe u een client maakt, een verificatie uitvoert en fouten afhandelt met behulp van asynchrone methoden.  |
| [Bewerkingen op service niveau](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Demonstreert hoe u indexen, Indexeer functies, gegevens bronnen, vaardig heden en synoniemen maakt. Dit voor beeld laat ook zien hoe u service statistieken kunt ophalen en query's kunt uitvoeren op een index.  |
| [Index bewerkingen](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Demonstreert hoe u een actie uitvoert op een bestaande index. in dit geval wordt het aantal documenten opgehaald dat is opgeslagen in de index.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Toont een techniek voor het werken met niet-ondersteunde gegevens typen.  |
| [Documenten indexeren (push model)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Model voor het indexeren van ' push ', waarbij u een JSON-nettolading naar een index van een service verzendt.   |
| [Voor beeld van versleutelings sleutel](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Laat zien hoe u een door de klant beheerde versleutelings sleutel gebruikt om een extra beveiligingslaag via gevoelige inhoud toe te voegen.  |

## <a name="documentation-samples"></a>Documentatievoorbeelden

De volgende voor beelden hebben een bijbehorend artikel in [Azure Cognitive Search-documentatie](https://docs.microsoft.com/azure/search/).

| Voorbeelden | Beschrijving |
|---------|-------------|
| [Snelstartgids](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Bron code voor [Quick Start: Maak een zoek index ](search-get-started-dotnet.md).  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Bron code voor [het gebruik van de .net-client bibliotheek](search-howto-dotnet-sdk.md) |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Synoniemen lijsten worden gebruikt voor het uitbreiden van query's en bieden een afwijkende term die extern is ten opzichte van een index. Dit voor beeld is opgenomen in [voor beelden: synoniemen toevoegen in C#](search-synonyms-tutorial-sdk.md). |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Bron code achter fragmenten van Indexeer functie in diverse artikelen. In dit voor beeld ziet u hoe u een Indexeer functie configureert met een planning, veld Toewijzingen en para meters.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Bron code voor [het configureren van door de klant beheerde sleutels voor gegevens versleuteling](search-security-manage-encryption-keys.md) |
| [Uw eerste app maken in C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Bron code voor [zelf studie: uw eerste Zoek-app maken](tutorial-csharp-create-first-app.md). Hoewel de meeste voor beelden console toepassingen zijn, gebruikt dit MVC-voor beeld een webpagina voor de index van de voor beeld-hotels, met een demonstratie van basis zoeken, paginering, automatisch aanvullen en voorgestelde query's, facetten en filters. |
| [meerdere gegevens bronnen](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Bron code voor [zelf studie: index uit meerdere gegevens bronnen](tutorial-multiple-data-sources.md). |
|  [optimaliseren-gegevens indexeren](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Bron code voor [zelf studie: de indexering optimaliseren met de Push-API](tutorial-optimize-indexing-push-api.md).  |
| [zelf studie-AI-verrijking](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Bron code voor [zelf studie: door AI gegenereerde Doorzoek bare inhoud van Azure-blobs met behulp van de .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

## <a name="standalone-samples-and-solutions"></a>Zelfstandige voor beelden en oplossingen

| Voorbeelden | Beschrijving |
|---------|-------------|
| [Azure-zoeken-Power-vaardig heden](https://github.com/Azure-Samples/azure-search-power-skills)  | Bron code voor verbruikte aangepaste vaardig heden die u kunt opnemen in uw gewonnen oplossingen.  |
| [Oplossingsverbetering voor kennisanalyse](https://docs.microsoft.com/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Bevat sjablonen, ondersteunings bestanden en analytische rapporten die u helpen bij het prototypen van een end-to-end kennis analyse oplossing.  |
| [Covid-19-opslag plaats voor zoeken in apps](https://github.com/liamca/covid19search) | Opslag plaats van de bron code voor de op Cognitive Search gebaseerde [Covid-19-Zoek toepassing](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Meer informatie over de [JFK-oplossing](https://www.microsoft.com/ai/ai-lab-jfk-files). |