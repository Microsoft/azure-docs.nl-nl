---
title: Inleiding tot Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search is een volledig beheerde cloudzoekservice van Microsoft. Ontdek de cases, de ontwikkelingswerkstroom, een vergelijking met andere Microsoft-zoekproducten en hoe u aan de slag kunt gaan.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 10/22/2020
ms.custom: contperfq1
ms.openlocfilehash: 0062caff7d8d25b263a9b1202f61691c056469af
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701079"
---
# <a name="what-is-azure-cognitive-search"></a>Wat is Azure Cognitive Search?

Azure Cognitive Search ([voorheen 'Azure Search'](whats-new.md#new-service-name)) is een cloudzoekservice die ontwikkelaars API’s en hulpprogramma’s biedt. Hiermee kunnen ze een uitgebreide zoekervaring bouwen binnen privé- en heterogene inhoud in web-, mobiele en ondernemingstoepassingen.

Wanneer u een Cognitive Search-service maakt, krijgt u een zoekmachine die het indexeren en uitvoeren van query's regelt, persistente opslag van indexen die u maakt en beheert uitvoert, en een querytaal voor het samenstellen van eenvoudige tot complexe query's uitvoert. Een zoekservice kan eventueel worden geïntegreerd met andere Azure-services. Dit kan in de vorm van specifieke *indexeerfuncties* en *vaardighedensets*. De indexeerfuncties nemen of halen gegevens op van Azure-gegevensbronnen. De vaardighedensets integreren verbruikbare AI van Cognitive Services (zoals afbeeldings- en tekstanalyse) of aangepaste AI-elementen die u in Azure Machine Learning maakt of binnen Azure Functions inpakt.

![Architectuur van Azure Cognitive Search](media/search-what-is-azure-search/azure-search-diagram.svg "Architectuur van Azure Cognitive Search")

In de architectuur wordt een zoekservice geplaatst tussen de externe gegevensarchieven die uw niet-geïndexeerde gegevens bevatten en een client-app die query-aanvragen naar een zoekindex verzendt en het antwoord verwerkt.  Een indexschema bepaalt de structuur van doorzoekbare inhoud. 

De twee primaire werkbelastingen van een zoekservice zijn *indexeren* en *query's uitvoeren*.

+ Bij het indexeren wordt tekst naar uw zoekservice gebracht en doorzoekbaar gemaakt. Intern wordt inkomende tekst verwerkt in tokens en opgeslagen in omgekeerde indexen voor snelle scans. Tijdens het indexeren hebt u de mogelijkheid om *cognitieve vaardigheden* toe te voegen: vooraf gedefinieerde vaardigheden van Microsoft of aangepaste vaardigheden die u hebt gemaakt. De daaropvolgende analyse en transformaties kunnen leiden tot nieuwe informatie en structuren die voorheen nog niet bestonden, waardoor veel hulp voor een groot aantal zoek- en kennisanalysescenario's wordt geboden.

+ Zodra een index is gevuld met doorzoekbare gegevens, stuurt uw client-app query aanvragen naar een zoekservice en verwerkt deze antwoorden. Alle uitvoeringen van query's verlopen via een zoekindex die u in uw service maakt, bezit en opslaat. In uw client-app wordt de zoekervaring gedefinieerd met behulp van API's van Azure Cognitive Search. De zoekervaring kan het volgende omvatten: afstemming van relevantie, automatische aanvulling, overeenkomende synoniemen, zoeken bij benadering, patroonvergelijking, filteren en sorteren.

Functionaliteit wordt beschikbaar gemaakt via een eenvoudige [REST API](/rest/api/searchservice/) of [.NET SDK](search-howto-dotnet-sdk.md) waarmee de inherente complexiteit van het ophalen van gegevens wordt gemaskeerd. Azure Portal biedt daarnaast ondersteuning voor service-administratie en inhoudsbeheer, door middel van hulpprogramma’s voor het ontwikkelen van prototypen, het doorzoeken van indexen en het doorzoeken van vaardighedensets. Omdat de service wordt uitgevoerd in de cloud, worden de infrastructuur en beschikbaarheid beheerd met Microsoft.

## <a name="when-to-use-cognitive-search"></a>Wanneer u Cognitive Search gebruikt

Azure Cognitive Search is geschikt voor de volgende toepassingsscenario's:

+ Consolidatie van heterogene inhoudstypen in een index die privé, enkelvoudig en doorzoekbaar is. U kunt een index vullen met streams van JSON-documenten van elke bron. Gebruik voor ondersteunde bronnen in Azure een *indexeerfunctie* om het indexeren te automatiseren. Controle over het indexschema en het vernieuwingsschema is een belangrijke reden voor het gebruik van Cognitive Search.

+ Eenvoudige implementatie van zoekfuncties. Azure Cognitive Search API's vereenvoudigen het bouwen van query's, facetnavigatie, filters (inclusief georuimtelijke zoekacties), toewijzing van synoniemen, automatische aanvulling en afstemming van relevantie. Met ingebouwde functies kunt u aan de verwachtingen van eindgebruikers voldoen en een zoekervaring bieden die vergelijkbaar is met commerciële webzoekprogramma's.

+ Onbewerkte inhoud is een grote hoeveelheid niet-gedifferentieerde tekst, afbeeldingsbestanden of toepassingsbestanden in Azure Blob-opslag of Cosmos DB. U kunt [cognitieve vaardigheden](cognitive-search-concept-intro.md) toepassen tijdens het indexeren om tekst te identificeren en op te halen, structuren te maken of nieuwe informatie te maken, zoals vertaalde tekst of entiteiten.

+ Inhoud vereist linguïstische analyse of aangepaste tekstanalyse. Als u niet-Engelstalige inhoud hebt, ondersteunt Azure Cognitive Search zowel Lucene-analyses als Microsofts natuurlijketaalprocessoren. U kunt ook analysefuncties configureren om gespecialiseerde verwerking van onbewerkte inhoud uit te voeren, zoals het uitfilteren van diakritische tekens of het herkennen en behouden van patronen in tekenreeksen.

Zie [Functies van Azure Cognitive Search](search-features-list.md) voor meer informatie over specifieke functionaliteiten

## <a name="how-to-use-cognitive-search"></a>Cognitive Search gebruiken

### <a name="step-1-provision-service"></a>Stap 1: Service inrichten

U kunt [Een gratis service maken](search-create-service-portal.md) die gedeeld wordt met andere abonnees, of een [betaalde laag](https://azure.microsoft.com/pricing/details/search/) met toegewezen resources die alleen door uw service worden gebruikt. Alle snelstartgidsen en zelfstudies kunnen worden gevolgd in de gratis service.

Voor betaalde lagen kunt u een service in twee dimensies schalen om de resourcetoewijzing te kalibreren op basis van productievereisten:

+ Replica’s toevoegen om de capaciteit voor het verwerken van zware querybelastingen te vergroten
+ Partities toevoegen om meer documenten op te kunnen slaan

### <a name="step-2-create-an-index"></a>Stap 2: een index maken

U definieert het indexschema dat moet worden toegewezen, om de structuur van de documenten te weerspiegelen waarin u wilt zoeken, vergelijkbaar met de velden in een database. Een zoekindex is een gespecialiseerde gegevensstructuur die is geoptimaliseerd voor het snel uitvoeren van query's.

Het is gebruikelijk om een [indexschema te maken in de Azure Portal](search-what-is-an-index.md) of programmatisch met behulp van de [.NET SDK](search-howto-dotnet-sdk.md) of [REST API](/rest/api/searchservice/).

> [!TIP]
> Begin met de [quickstart: Wizardgegevens importeren](search-get-started-portal.md) om in een paar minuten een index te maken, te laden en op te vragen.

### <a name="step-3-load-data"></a>Stap 3: Gegevens laden

Nadat u een index hebt gedefinieerd, bent u klaar om inhoud te uploaden. U kunt hiervoor een push- of een pull-model gebruiken.

Het pushmodel 'pusht' JSON-documenten naar een index met behulp van API's van een [SDK](search-howto-dotnet-sdk.md) of [REST](/rest/api/searchservice/addupdate-or-delete-documents). De externe gegevensset kan vrijwel elke gegevensbron zijn, zolang de documenten JSON zijn.

Met het pullmodel worden gegevens opgehaald uit bronnen op Azure en verzonden naar een zoekindex. Het pullmodel wordt ondersteund met [*indexeerfuncties*](/rest/api/searchservice/Indexer-operations) die aspecten van de gegevensopname stroomlijnen en automatiseren, zoals het verbinden met, het lezen van en het serialiseren van gegevens. Ondersteunde gegevensbronnen zijn onder andere Azure Cosmos DB, Azure SQL en Azure Storage.

### <a name="step-4-send-queries-and-handle-responses"></a>Stap 4: Query's verzenden en antwoorden verwerken

Nadat u een index hebt gevuld, kunt u [zoekquery’s verzenden](search-query-overview.md) naar het service-eindpunt met behulp van eenvoudige HTTP-aanvragen met [REST API](/rest/api/searchservice/Search-Documents) of de [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search).

Volg [Uw eerste zoek-app maken](tutorial-csharp-create-first-app.md) om een webpagina te maken en uit te breiden die gebruikersinvoer verzamelt en de resultaten verwerkt. U kun ook [Postman of Visual Studio Code](search-get-started-rest.md) voor interactieve REST-aanroepen gebruiken of de in Azure Portal ingebouwde [Search Explorer](search-explorer.md) om een query uit te voeren op een bestaande index.

## <a name="how-it-compares"></a>Vergelijking

Klanten vragen vaak wat de verschillen zijn tussen Azure Cognitive Search en andere zoekgerelateerde oplossingen. In de volgende tabel worden de verschillen samengevat.

| Vergeleken met | Belangrijke verschillen |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](https://docs.microsoft.com/microsoftsearch/overview-microsoft-search) is voor geverifieerde Microsoft 365-gebruikers die query's willen uitvoeren op inhoud in SharePoint. Het wordt aangeboden als een kant-en-klare zoekervaring die is ingeschakeld en geconfigureerd door beheerders, met de mogelijkheid om externe inhoud te accepteren via connectors van Microsoft en andere bronnen. Als dit uw situatie omschrijft, is Microsoft Search met Microsoft 365 een aantrekkelijke optie om te verkennen.<br/><br/>Met Azure Cognitive Search daarentegen worden query's uitgevoerd op een door u gedefinieerde index, die is gevuld met gegevens en documenten waarvan u de eigenaar bent, vaak afkomstig uit verschillende bronnen. Azure Cognitive Search heeft verkenningsmogelijkheden voor bepaalde Azure-gegevensbronnen via [indexeerfuncties](search-indexer-overview.md), maar u kunt elk JSON-document dat overeenkomt met uw indexschema naar een enkele geconsolideerde, doorzoekbare resource pushen. U kunt ook de indexeerpijplijn aanpassen zodat deze machine learning en lexicale analysefuncties bevat. Omdat Cognitive Search is gebouwd als invoegonderdeel voor grotere oplossingen, kunt u de zoekfunctie integreren in vrijwel elke app en op elk platform.|
|Bing | Met [Bing Webzoekopdrachten-API](../cognitive-services/bing-web-search/index.yml) wordt in de indexen op Bing.com gezocht naar overeenkomsten voor termen die u hebt ingediend. Indexen zijn gebouwd uit HTML, XML en andere webinhoud op openbare sites. [Bing Aangepaste zoekopdrachten](/azure/cognitive-services/bing-custom-search/), dat op dezelfde basis is gebouwd, biedt dezelfde verkenningstechnologie voor typen webinhoud, met een bereik dat is ingesteld op afzonderlijke websites.<br/><br/>In Cognitive Search kunt u de index definiëren en invullen. U kunt [indexeerfuncties](search-indexer-overview.md) gebruiken om gegevens te verkennen in Azure-gegevensbronnen of een indexconform JSON-document naar uw zoekservice te pushen. |
|Zoeken in database | Veel databaseplatforms beschikken over een ingebouwde zoekfunctie. SQL Server heeft een functie voor [zoeken in volledige tekst](/sql/relational-databases/search/full-text-search). Cosmos DB en soortgelijke technologieën hebben indexen waarop kan worden gezocht. Bij de evaluatie van producten die zoeken en opslag combineren, is het soms lastig om te bepalen wat u moet kiezen. Veel oplossingen gebruiken beide: DBMS voor opslag en Azure Cognitive Search voor gespecialiseerde zoekfuncties.<br/><br/>Vergeleken met DBMS slaat Azure Cognitive Search de inhoud van heterogene bronnen op en biedt het gespecialiseerde tekstverwerkingsfuncties, zoals linguïstische tekstverwerking (woordstammen, lemmata, woordvormen) in [56 talen](/rest/api/searchservice/language-support). Het ondersteunt ook automatische correctie van verkeerd gespelde woorden, [synoniemen](/rest/api/searchservice/synonym-map-operations), [suggesties](/rest/api/searchservice/suggestions), [scoringsbesturingselementen](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetten](./search-filters-facets.md) en [aangepaste tokenisering](/rest/api/searchservice/custom-analyzers-in-azure-search). De [engine voor zoekopdrachten in volledige tekst](search-lucene-query-architecture.md) in Azure Cognitive Search is gebouwd op Apache Lucene, een industrienorm op het gebied van het ophalen van gegevens. Hoewel Azure Cognitive Search gegevens bewaart in de vorm van een omgekeerde index, is het geen vervanging voor echte gegevensopslag en raden we niet aan om Azure Cognitive Search op die manier te gebruiken. Zie dit [forumbericht](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data) voor meer informatie. <br/><br/>Een ander belangrijk punt in deze categorie is het gebruik van resources. Indexering en bepaalde querybewerkingen zijn vaak rekenintensief. Door de zoekopdracht van de DBMS naar een toegewezen oplossing in de cloud te verplaatsen, blijven systeemresources beschikbaar voor transactieverwerking. En door de zoekfunctie te externaliseren, kunt u eenvoudig schalen naar het juiste queryvolume.|
|Toegewezen zoekoplossing | Ervan uitgaande dat u hebt gekozen voor een toegewezen zoekoplossing met het volledige spectrum aan functies, dan is er nog een essentieel onderscheid tussen on-premises oplossingen en een cloudservice. Veel zoektechnologieën bieden controle over het indexeren en doorzoeken van pijplijnen, toegang tot rijkere syntaxis voor filteren en het uitvoeren van query’s, beheer van rangen en relevantie, en functies voor zelfgestuurde en intelligente zoekopdrachten. <br/><br/>Als u op zoek bent naar een pasklare oplossing met minimale overhead, minimaal onderhoud en aanpasbare schaling is een cloudservice de juiste keuze voor u. <br/><br/>Binnen het cloudmodel bieden verschillende providers vergelijkbare basisfuncties, met Zoekopdracht in volledige tekst, op geografische locaties zoeken en de mogelijkheid om te gaan met een zekere dubbelzinnigheid van zoekinvoergegevens. Meestal bepaalt een [gespecialiseerde functie](search-features-list.md), of het gemak en de algehele eenvoud van de API’s, de hulpprogramma’s en het beheer, welke oplossing het meest geschikt voor u is. |

Azure Cognitive Search is van alle cloudproviders het sterkst op het gebied van zoeken in volledige tekst in inhoudsopslag en databases in Azure, voor apps die hoofdzakelijk afhankelijk zijn van het ophalen van gegevens en van inhoudsnavigatie. 

Belangrijke pluspunten zijn onder andere:

+ Azure-gegevensintegratie (verkenners) in de indexeringslaag
+ Azure Portal voor centraal beheer
+ Schaling, betrouwbaarheid en beschikbaarheid van wereldklasse in Azure
+ AI-verwerking van onbewerkte gegevens om ze beter doorzoekbaar te maken, met inbegrip van afbeeldingen of het vinden van patronen in ongestructureerde inhoud.
+ Taalkundige en aangepaste analyse met analysefuncties voor grondig zoeken in volledige tekst in 56 talen
+ [Kernfuncties die eigen zijn aan zoekgerichte apps](search-features-list.md): scoren, facetten, suggesties, synoniemen, op geografische locaties zoeken, en meer.

Onder onze klanten bevinden zich degenen die het breedste scala aan functies in Azure Cognitive Search gebruiken, onder andere onlinecatalogussen, Line-Of-Business-programma’s en toepassingen voor het ontdekken van documenten.

## <a name="watch-this-video"></a>Deze video bekijken

In deze vijftien minuten durende video introduceert programmamanager Luis Cabrera Azure Cognitive Search.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]