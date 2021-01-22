---
title: Inleiding tot Azure Cognitive Search
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search is een volledig beheerde cloudzoekservice van Microsoft. Ontdek de cases, de ontwikkelingswerkstroom, een vergelijking met andere Microsoft-zoekproducten en hoe u aan de slag kunt gaan.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/22/2021
ms.custom: contperf-fy21q1
ms.openlocfilehash: 893bf37a5a4c8a314e5182bf2ac4bc28502b98d9
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98699431"
---
# <a name="what-is-azure-cognitive-search"></a>Wat is Azure Cognitive Search?

Azure Cognitive Search ([voorheen 'Azure Search'](whats-new.md#new-service-name)) is een cloudzoekservice die ontwikkelaars API’s en hulpprogramma’s biedt. Hiermee kunnen ze een uitgebreide zoekervaring bouwen binnen privé- en heterogene inhoud in web-, mobiele en ondernemingstoepassingen. 

Een zoek service heeft de volgende onderdelen:

+ Zoek machine voor zoeken in volledige tekst
+ Permanente opslag van geïndexeerde inhoud die eigendom is van de gebruiker
+ Api's voor indexering en query's
+ Optionele [op AI gebaseerde verrijkingen](cognitive-search-concept-intro.md), waardoor Doorzoek bare inhoud kan worden gemaakt van installatie kopieën, onbewerkte tekst, toepassings bestanden
+ Optionele integratie met andere Azure-Services voor gegevens, machine learning/AI en beveiliging

In architectuur is een zoek service tussen de externe gegevens archieven die uw niet-geïndexeerde gegevens bevatten, en uw client-app die query aanvragen naar een zoek index verzendt en het antwoord verwerkt.

![Architectuur van Azure Cognitive Search](media/search-what-is-azure-search/azure-search-diagram.svg "Architectuur van Azure Cognitive Search")

Daarnaast kan de zoek functie worden geïntegreerd met andere Azure-Services in de vorm van *Indexeer* functies waarmee gegevens opname wordt geautomatiseerd en opgehaald uit Azure-gegevens bronnen, en *vaardig heden* die verbruiks AI van Cognitive Services integreert, zoals afbeeldings-en tekst analyse, of aangepaste AI-elementen die u in Azure Machine Learning maakt of in azure functions inpakt.

## <a name="inside-a-search-service"></a>Binnen een zoek service

Binnen de zoekservice zelf zijn de twee primaire werkbelastingen *indexeren* en *query's uitvoeren*. 

+ [Indexeren](search-what-is-an-index.md) is een inhaal proces dat inhoud in uw zoek service laadt en doorzoekbaar maakt. Intern wordt inkomende tekst verwerkt in tokens en opgeslagen in omgekeerde indexen voor snelle scans. U kunt alle tekst uploaden die in de vorm van JSON-documenten is.

  Als uw inhoud gemengde bestanden bevat, kunt u ook *AI-verrijking* toevoegen via [cognitieve vaardig heden](cognitive-search-working-with-skillsets.md). AI-verrijking kan Inge sloten tekst in toepassings bestanden extra heren en tekst en structuur van niet-tekst bestanden afleiden door de inhoud te analyseren. 

  De vaardig heden die de analyse bieden, zijn vooraf gedefinieerde van micro soft, of aangepaste vaardig heden die u maakt. De daaropvolgende analyse en transformaties kunnen leiden tot nieuwe informatie en structuren die voorheen nog niet bestonden, waardoor veel hulp voor een groot aantal zoek- en kennisanalysescenario's wordt geboden.

+ Het [uitvoeren van query's](search-query-overview.md) kan gebeuren wanneer een index is gevuld met Doorzoek bare tekst, wanneer uw client-app query aanvragen verzendt naar een zoek service en reacties verwerkt. Alle uitvoeringen van query's verlopen via een zoekindex die u in uw service maakt, bezit en opslaat. In uw client-app wordt de zoekervaring gedefinieerd met behulp van API's van Azure Cognitive Search. De zoekervaring kan het volgende omvatten: afstemming van relevantie, automatische aanvulling, overeenkomende synoniemen, zoeken bij benadering, patroonvergelijking, filteren en sorteren.

Functionaliteit wordt beschikbaar gemaakt via een eenvoudige [REST API](/rest/api/searchservice/) of [.NET SDK](search-howto-dotnet-sdk.md) waarmee de inherente complexiteit van het ophalen van gegevens wordt gemaskeerd. Azure Portal biedt daarnaast ondersteuning voor service-administratie en inhoudsbeheer, door middel van hulpprogramma’s voor het ontwikkelen van prototypen, het doorzoeken van indexen en het doorzoeken van vaardighedensets. Omdat de service wordt uitgevoerd in de cloud, worden de infrastructuur en beschikbaarheid beheerd met Microsoft.

## <a name="why-use-cognitive-search"></a>Redenen om Cognitive Search te gebruiken

Azure Cognitive Search is geschikt voor de volgende toepassingsscenario's:

+ heterogene inhoud consolideren in een index die privé, enkelvoudig en doorzoekbaar is.

+ Implementeer eenvoudig zoek functies: relevantie afstemmen, facet navigatie, filters (inclusief georuimtelijke Zoek opdrachten), synoniemen toewijzing en automatisch aanvullen.

+ Transformeer grote niet-gedifferentieerde tekst-of afbeeldings bestanden of toepassings bestanden die zijn opgeslagen in Azure Blob Storage of Cosmos DB, naar Doorzoek bare JSON-documenten. Dit wordt bereikt tijdens de index via [cognitieve vaardig heden](cognitive-search-concept-intro.md) die externe verwerking toevoegen.

+ Voeg taal kundige of aangepaste tekst analyse toe. Als u niet-Engelstalige inhoud hebt, ondersteunt Azure Cognitive Search zowel Lucene-analyses als Microsofts natuurlijketaalprocessoren. U kunt ook analysefuncties configureren om gespecialiseerde verwerking van onbewerkte inhoud uit te voeren, zoals het uitfilteren van diakritische tekens of het herkennen en behouden van patronen in tekenreeksen.

Zie [Functies van Azure Cognitive Search](search-features-list.md) voor meer informatie over specifieke functionaliteiten

## <a name="how-to-get-started"></a>Hoe gaat u aan de slag

Een end-to-end-onderzoek van de belangrijkste zoekfuncties kan in vier stappen worden bereikt:

1. [**Maak een zoek service**](search-create-service-portal.md) in de gedeelde gratis laag of een [factureer bare laag](https://azure.microsoft.com/pricing/details/search/) voor speciale resources die alleen door uw service worden gebruikt. Alle Snelstartgids en zelf studies kunnen worden uitgevoerd op een gedeelde service.

1. [**Maak een zoek index**](search-what-is-an-index.md) met behulp van de portal, [rest API](/rest/api/searchservice/create-index), [.NET SDK](search-howto-dotnet-sdk.md)of een andere SDK. Het indexschema bepaalt de structuur van doorzoekbare inhoud.

1. [**Upload inhoud**](search-what-is-data-import.md) met het [' push '-model](tutorial-optimize-indexing-push-api.md) om JSON-documenten vanuit een wille keurige bron te pushen, of gebruik het [pull-model (Indexeer functies)](search-indexer-overview.md) als uw bron gegevens zich in azure bevindt.

1. [**Vraag een index op**](search-query-overview.md) met behulp van [Search Explorer](search-explorer.md) in de portal, [REST API](search-get-started-rest.md), [.NET SDK](/dotnet/api/azure.search.documents.searchclient.search) of een andere SDK.

> [!TIP]
> Beperk het aantal stappen door te beginnen met de [**wizard Gegevens importeren**](search-get-started-portal.md) en een Azure-gegevensbron om in een paar minuten een index te maken, te laden en op te vragen.

## <a name="compare-search-options"></a>Zoekopties vergelijken

Klanten vragen vaak wat de verschillen zijn tussen Azure Cognitive Search en andere zoekgerelateerde oplossingen. In de volgende tabel worden de verschillen samengevat.

| Vergeleken met | Belangrijke verschillen |
|-------------|-----------------|
| Microsoft Search | [Microsoft Search](/microsoftsearch/overview-microsoft-search) is voor geverifieerde Microsoft 365-gebruikers die query's willen uitvoeren op inhoud in SharePoint. Het wordt aangeboden als een kant-en-klare zoekervaring die is ingeschakeld en geconfigureerd door beheerders, met de mogelijkheid om externe inhoud te accepteren via connectors van Microsoft en andere bronnen. Als dit uw situatie omschrijft, is Microsoft Search met Microsoft 365 een aantrekkelijke optie om te verkennen.<br/><br/>Met Azure Cognitive Search daarentegen worden query's uitgevoerd op een door u gedefinieerde index, die is gevuld met gegevens en documenten waarvan u de eigenaar bent, vaak afkomstig uit verschillende bronnen. Azure Cognitive Search heeft verkenningsmogelijkheden voor bepaalde Azure-gegevensbronnen via [indexeerfuncties](search-indexer-overview.md), maar u kunt elk JSON-document dat overeenkomt met uw indexschema naar een enkele geconsolideerde, doorzoekbare resource pushen. U kunt ook de indexeerpijplijn aanpassen zodat deze machine learning en lexicale analysefuncties bevat. Omdat Cognitive Search is gebouwd als invoegonderdeel voor grotere oplossingen, kunt u de zoekfunctie integreren in vrijwel elke app en op elk platform.|
|Bing | Met [Bing Webzoekopdrachten-API](../cognitive-services/bing-web-search/index.yml) wordt in de indexen op Bing.com gezocht naar overeenkomsten voor termen die u hebt ingediend. Indexen zijn gebouwd uit HTML, XML en andere webinhoud op openbare sites. [Bing Aangepaste zoekopdrachten](/azure/cognitive-services/bing-custom-search/), dat op dezelfde basis is gebouwd, biedt dezelfde verkenningstechnologie voor typen webinhoud, met een bereik dat is ingesteld op afzonderlijke websites.<br/><br/>In Cognitive Search kunt u de index definiëren en invullen. U kunt [indexeerfuncties](search-indexer-overview.md) gebruiken om gegevens te verkennen in Azure-gegevensbronnen of een indexconform JSON-document naar uw zoekservice te pushen. |
|Zoeken in database | Veel databaseplatforms beschikken over een ingebouwde zoekfunctie. SQL Server heeft een functie voor [zoeken in volledige tekst](/sql/relational-databases/search/full-text-search). Cosmos DB en soortgelijke technologieën hebben indexen waarop kan worden gezocht. Bij de evaluatie van producten die zoeken en opslag combineren, is het soms lastig om te bepalen wat u moet kiezen. Veel oplossingen gebruiken beide: DBMS voor opslag en Azure Cognitive Search voor gespecialiseerde zoekfuncties.<br/><br/>Vergeleken met DBMS slaat Azure Cognitive Search de inhoud van heterogene bronnen op en biedt het gespecialiseerde tekstverwerkingsfuncties, zoals linguïstische tekstverwerking (woordstammen, lemmata, woordvormen) in [56 talen](/rest/api/searchservice/language-support). Het ondersteunt ook automatische correctie van verkeerd gespelde woorden, [synoniemen](/rest/api/searchservice/synonym-map-operations), [suggesties](/rest/api/searchservice/suggestions), [scoringsbesturingselementen](/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetten](./search-filters-facets.md) en [aangepaste tokenisering](/rest/api/searchservice/custom-analyzers-in-azure-search). De [engine voor zoekopdrachten in volledige tekst](search-lucene-query-architecture.md) in Azure Cognitive Search is gebouwd op Apache Lucene, een industrienorm op het gebied van het ophalen van gegevens. Hoewel Azure Cognitive Search gegevens bewaart in de vorm van een omgekeerde index, is het geen vervanging voor echte gegevensopslag en raden we niet aan om Azure Cognitive Search op die manier te gebruiken. Zie dit [forumbericht](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data) voor meer informatie. <br/><br/>Een ander belangrijk punt in deze categorie is het gebruik van resources. Indexering en bepaalde querybewerkingen zijn vaak rekenintensief. Door de zoekopdracht van de DBMS naar een toegewezen oplossing in de cloud te verplaatsen, blijven systeemresources beschikbaar voor transactieverwerking. En door de zoekfunctie te externaliseren, kunt u eenvoudig schalen naar het juiste queryvolume.|
|Toegewezen zoekoplossing | Ervan uitgaande dat u hebt gekozen voor een toegewezen zoekoplossing met het volledige spectrum aan functies, dan is er nog een essentieel onderscheid tussen on-premises oplossingen en een cloudservice. Veel zoektechnologieën bieden controle over het indexeren en doorzoeken van pijplijnen, toegang tot rijkere syntaxis voor filteren en het uitvoeren van query’s, beheer van rangen en relevantie, en functies voor zelfgestuurde en intelligente zoekopdrachten. <br/><br/>Als u op zoek bent naar een pasklare oplossing met minimale overhead, minimaal onderhoud en aanpasbare schaling is een cloudservice de juiste keuze voor u. <br/><br/>Binnen het cloudmodel bieden verschillende providers vergelijkbare basisfuncties, met Zoekopdracht in volledige tekst, op geografische locaties zoeken en de mogelijkheid om te gaan met een zekere dubbelzinnigheid van zoekinvoergegevens. Meestal bepaalt een [gespecialiseerde functie](search-features-list.md), of het gemak en de algehele eenvoud van de API’s, de hulpprogramma’s en het beheer, welke oplossing het meest geschikt voor u is. |

Azure Cognitive Search is van alle cloudproviders het sterkst op het gebied van zoeken in volledige tekst in inhoudsopslag en databases in Azure, voor apps die hoofdzakelijk afhankelijk zijn van het ophalen van gegevens en van inhoudsnavigatie. 

Belangrijke pluspunten zijn onder andere:

+ Azure-gegevensintegratie (verkenners) in de indexeringslaag
+ Azure Private Link-integratie om offline ondersteuning te bieden voor beveiligingsvereisten
+ Integratie met AI-verwerking om tekst in niet-doorzoekbare inhoudstypen doorzoekbaar te maken.
+ Taalkundige en aangepaste analyse met analysefuncties voor grondig zoeken in volledige tekst in 56 talen
+ [Kritieke functies](search-features-list.md): rijke querytaal, relevantie afstemmen, facetteren, automatisch aanvullen, synoniemen, geo-zoekopdracht, en resultaatcompositie.
+ Schaling, betrouwbaarheid en beschikbaarheid van wereldklasse in Azure

Onder onze klanten bevinden zich degenen die het breedste scala aan functies in Azure Cognitive Search gebruiken, onder andere onlinecatalogussen, Line-Of-Business-programma’s en toepassingen voor het ontdekken van documenten.

## <a name="watch-this-video"></a>Deze video bekijken

In deze vijftien minuten durende video introduceert programmamanager Luis Cabrera Azure Cognitive Search.

>[!VIDEO https://www.youtube.com/embed/kOJU0YZodVk?version=3]