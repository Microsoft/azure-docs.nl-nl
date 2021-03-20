---
title: Zoeken in inhoud van Azure Blob-opslag
titleSuffix: Azure Cognitive Search
description: Meer informatie over het extra heren van tekst uit Azure-blobs en het maken van een zoek opdracht in volledige tekst in een Azure Cognitive Search-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f61bf635cc61a2153a7bb016ef4b4711d7ba7391
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91355292"
---
# <a name="search-over-azure-blob-storage-content"></a>Zoeken in inhoud van Azure Blob-opslag

Het kan lastig zijn om te zoeken in de verschillende typen inhoud die zijn opgeslagen in Azure Blob Storage. Bekijk in dit artikel de basis werk stroom voor het uitpakken van inhoud en meta gegevens uit blobs en het verzenden ervan naar een zoek index in azure Cognitive Search. De resulterende index kan worden opgevraagd met behulp van zoeken in volledige tekst.

> [!NOTE]
> Hebt u al bekend met de werk stroom en samen stelling? Het [configureren van een BLOB-Indexeer functie](search-howto-indexing-azure-blob-storage.md) is uw volgende stap.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Wat betekent het toevoegen van volledige tekst zoeken naar BLOB-gegevens

Azure Cognitive Search is een zoek service die ondersteuning biedt voor het indexeren en opvragen van query's op basis van door de gebruiker gedefinieerde indexen die uw externe Doorzoek bare inhoud bevatten die wordt gehost in de Cloud. Als u uw Doorzoek bare inhoud met de query-engine wilt zoeken, hebt u behoefte aan prestaties, waardoor de resultaten worden geretourneerd op een snelheid waarmee gebruikers zich kunnen verwachten van zoek query's.

Cognitive Search integreert met Azure Blob Storage in de indexerings-laag, waarna de blob-inhoud wordt geïmporteerd als Zoek documenten die worden geïndexeerd in *omgekeerde indexen* en andere query structuren die ondersteuning bieden voor vrije-tekst query's en filter expressies. Omdat uw blob-inhoud is geïndexeerd in een zoek index, kunt u het volledige bereik van query functies in azure Cognitive Search gebruiken om informatie te vinden in de blob-inhoud.

Invoer zijn uw blobs in een enkele container in Azure Blob Storage. Blobs kunnen bijna elk soort tekst gegevens zijn. Als uw blobs installatie kopieën bevatten, kunt u [AI-verrijking toevoegen aan BLOB-indexering](search-blob-ai-integration.md) om tekst uit afbeeldingen te maken en uit te pakken.

Output is altijd een Azure Cognitive Search-index, die wordt gebruikt voor het snel zoeken van tekst, ophalen en verkennen in client toepassingen. Tussenliggende bevindt zich in de architectuur voor het indexeren van pijp lijnen. De pijp lijn is gebaseerd op de *Indexeer* functie, zoals beschreven in dit artikel.

Zodra de index is gemaakt en gevuld, bestaat deze onafhankelijk van de BLOB-container, maar u kunt de indexerings bewerkingen opnieuw uitvoeren om uw index te vernieuwen op basis van gewijzigde documenten. Informatie over de tijds tempel van afzonderlijke blobs wordt gebruikt voor het detecteren van wijzigingen. U kunt ervoor kiezen om geplande uitvoeringen of on-demand indexering te indexeren als het mechanisme voor het vernieuwen.

## <a name="required-resources"></a>Vereiste bronnen

U hebt zowel Azure Cognitive Search als Azure Blob-opslag nodig. In Blob Storage hebt u een container nodig die bron inhoud levert.

U kunt rechtstreeks beginnen op de portal-pagina van uw opslag account. Klik in de linker navigatie pagina onder **BLOB service** op **Azure Cognitive Search toevoegen** om een nieuwe service te maken of een bestaande te selecteren. 

Zodra u Azure Cognitive Search aan uw opslag account hebt toegevoegd, kunt u het standaard proces voor het indexeren van BLOB-gegevens volgen. U wordt aangeraden de wizard **gegevens importeren** in azure Cognitive Search te gebruiken voor een eenvoudige inleiding of de rest-api's aan te roepen met een hulp programma zoals postman. In deze zelf studie wordt u begeleid bij het aanroepen van de REST API in postman: [index-en zoek semi-gestructureerde gegevens (JSON-blobs) in Azure Cognitive Search](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Een BLOB-indexer gebruiken

Een *Indexeer functie* is een subservice met gegevens bron bewust in cognitive Search, voorzien van interne logica voor het bemonsteren van gegevens, het lezen van meta gegevens, het ophalen van gegevens en het serialiseren van gegevens van systeem eigen indelingen in JSON-documenten voor de volgende import. 

Blobs in Azure Storage worden geïndexeerd met behulp van de [Azure Cognitive Search Blob Storage indexer](search-howto-indexing-azure-blob-storage.md). U kunt deze Indexeer functie aanroepen met behulp van de wizard **gegevens importeren** , een rest API of de .NET SDK. In code gebruikt u deze Indexeer functie door het type in te stellen en door verbindings informatie op te geven die een Azure Storage account bevat samen met een BLOB-container. U kunt uw blobs samendeelnen door een virtuele map te maken, die u vervolgens kunt door geven als een para meter of door te filteren op een bestands extensie.

Een Indexeer functie voert het ' document kraken ' uit en opent een blob om inhoud te controleren. Nadat u verbinding hebt gemaakt met de gegevens bron, is de eerste stap in de pijp lijn. Voor BLOB-gegevens zijn dit de locatie waar PDF, Office docs en andere inhouds typen worden gedetecteerd. Het kraken van documenten met tekst extractie wordt niet in rekening gebracht. Als uw blobs afbeeldings inhoud bevatten, worden afbeeldingen genegeerd, tenzij u [AI-verrijking toevoegt](search-blob-ai-integration.md). Standaard indexering is alleen van toepassing op tekst inhoud.

De BLOB-indexer wordt geleverd met configuratie parameters en biedt ondersteuning voor het bijhouden van wijzigingen als de onderliggende gegevens voldoende informatie bevatten. Meer informatie over de kern functionaliteit vindt u in [Azure Cognitive Search Blob Storage indexer](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Ondersteunde inhouds typen

Door een BLOB Indexeer functie uit te voeren via een container, kunt u tekst en meta gegevens uit de volgende inhouds typen extra heren met één query:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>BLOB-meta gegevens indexeren

Een veelvoorkomend scenario waarmee u gemakkelijk kunt sorteren op blobs van elk inhouds type is het indexeren van zowel aangepaste meta gegevens als systeem eigenschappen voor elke blob. Op deze manier wordt informatie voor alle blobs geïndexeerd, ongeacht het document type dat is opgeslagen in een index in uw zoek service. Met de nieuwe index kunt u vervolgens door gaan met het sorteren, filteren en facet voor alle inhoud van de Blob-opslag.

> [!NOTE]
> BLOB-index Tags worden standaard geïndexeerd door de Blob Storage-service en beschikbaar gesteld voor het uitvoeren van query's. Als de sleutel/waarde-kenmerken van uw blobs indexerings-en filter functies vereisen, moeten BLOB-index Tags worden gebruikt in plaats van meta gegevens.
>
> Zie [gegevens beheren en zoeken op Azure Blob Storage met Blob-index](../storage/blobs/storage-manage-find-blobs.md)voor meer informatie over blob-indexen.

### <a name="indexing-json-blobs"></a>JSON-blobs indexeren

Indexeer functies kunnen worden geconfigureerd voor het extra heren van gestructureerde inhoud gevonden in blobs die JSON bevatten. Een Indexeer functie kan JSON-blobs lezen en de gestructureerde inhoud parseren in de juiste velden van een zoek document. Indexeer functies kunnen ook blobs maken die een matrix van JSON-objecten bevatten en elk element toewijzen aan een afzonderlijk Zoek document. U kunt een parseringsfout instellen om het type van het JSON-object te beïnvloeden dat door de Indexeer functie is gemaakt.

## <a name="search-blob-content-in-a-search-index"></a>Blob-inhoud zoeken in een zoek index 

De uitvoer van een Indexeer functie is een zoek index, die wordt gebruikt voor interactief verkennen met behulp van vrije tekst en gefilterde query's in een client-app. Voor de eerste keer onderzoek en verificatie van inhoud kunt u het beste beginnen met [Search Explorer](search-explorer.md) in de portal om de document structuur te controleren. U kunt een [eenvoudige query syntaxis](query-simple-syntax.md), de [volledige query syntaxis](query-lucene-syntax.md)en de syntaxis van de [filter expressie](query-odata-filter-orderby-syntax.md) gebruiken in Search Explorer.

Een meer permanente oplossing is het verzamelen van query-invoer en het weer geven van het antwoord als Zoek resultaten in een client toepassing. In de volgende C#-zelf studie wordt uitgelegd hoe u een zoek toepassing bouwt: [uw eerste toepassing maken in Azure Cognitive Search](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Volgende stappen

+ [Het uploaden, downloaden en vermelden van blobs met de Azure Portal (Azure Blob-opslag)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Een BLOB-indexer instellen (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md)