---
title: Nieuwe functies in Azure Cognitive Search
description: Aankondigingen van nieuwe en verbeterde functies, waaronder een wijziging van de servicenaam van Azure Search in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 11/12/2020
ms.custom: references_regions
ms.openlocfilehash: 4c1284c13d1516ac4061319287adb64ab7e14ff5
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579467"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nieuwe functies in Azure Cognitive Search

Meer informatie over nieuwe functies in de service. Voeg een bladwijzer toe aan deze pagina om up-to-date te blijven over de service.

## <a name="november-2020"></a>November 2020

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|------------------------------|----------|-------------|---------------|
|[versleuteling via door de klant beheerde sleutels voor indexeerfuncties, gegevensbronnen en vaardighedensets](search-security-manage-encryption-keys.md) | Beveiliging | Deze toevoeging breidt door de klant beheerde versleuteling uit naar het volledige activabereik dat is gemaakt en beheerd met een zoekservice. Zoals u weet voegt CMK-ondersteuning een extra versleutelingslaag toe bovenop de basisversleuteling die wordt uitgevoerd en beheerd met Microsoft. | Algemeen beschikbaar via REST API-versie = 2020-06-30 |  

## <a name="september-2020"></a>September 2020

Maak een identiteit voor een zoekservice in Azure Active Directory en gebruik vervolgens Azure RBAC-machtigingen om de identiteit alleen-lezenmachtigingen voor Azure-gegevensbronnen te verlenen. Kies desgewenst de mogelijkheid [Vertrouwde service-uitzondering](search-indexer-howto-access-trusted-service-exception.md) als IP-regels geen optie zijn.

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|------------------------------|----------|-------------|---------------|
| [Beheerde service-identiteit](search-howto-managed-identities-data-sources.md) | Indexeerfuncties, beveiliging | Maak een identiteit voor een zoekservice in Azure Active Directory en gebruik vervolgens Azure RBAC-machtigingen om toegang tot Azure-gegevensbronnen te verlenen. Deze aanpak elimineert de noodzaak van referenties op de verbindingsreeks. <br><br>Een andere manier om een beheerde service-identiteit te gebruiken als IP-regels geen optie zijn, is via een [Vertrouwde service-uitzondering](search-indexer-howto-access-trusted-service-exception.md). | Algemeen verkrijgbaar. Gebruik deze mogelijkheid wanneer u de portal of [Gegevensbron maken (REST)](/rest/api/searchservice/create-data-source) met api-version=2020-06-30 gebruikt. |
| [Uitgaande aanvragen met behulp van een privélink](search-indexer-howto-access-private.md) | Indexeerfuncties, beveiliging | Maak een gedeelde privélinkresource die gebruikt kan worden door indexeerfuncties om toegang te krijgen tot Azure-resources die zijn beveiligd met Azure Private Link. Voor meer informatie over alle manieren waarop u indexeerfuncties kunt beveiligen, kunt u [Indexeertoegang tot inhoud die is beveiligd met Azure-netwerkbeveiligingsfuncties](search-indexer-securing-resources.md) raadplegen. | Algemeen verkrijgbaar. Gebruik deze mogelijkheid wanneer u de portal of [Gedeelde privélink (REST)](/rest/api/searchmanagement/sharedprivatelinkresources) met api-version=2020-08-01 gebruikt. |
| [Beheer van REST API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | REST | Nieuwe stabiele REST API voegt ondersteuning toe bij het maken van gedeelde privélinkresources. | Algemeen verkrijgbaar. |
| [Beheer van REST API (2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | REST | Hiermee wordt een gedeelde privélinkresource toegevoegd voor Azure Functions en Azure SQL voor MySQL-databases. | Openbare preview. |
| [Beheer van .NET SDK 4.0](/dotnet/api/overview/azure/search/management) | .NET SDK | Azure SDK-update voor het beheer van SDK, gericht op REST API versie 2020-08-01. | Algemeen verkrijgbaar. |

## <a name="august-2020"></a>Augustus 2020

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|---------|------------------|-------------|---------------|
| [Dubbele versleuteling](search-security-overview.md#encryption) | Beveiliging | U kunt dubbele versleuteling inschakelen in de opslaglaag door CMK-versleuteling (door klant beheerde sleutels) te configureren voor nieuwe zoekservices. Maak een nieuwe service, [configureer door klant beheerde sleutels en pas deze toe](search-security-manage-encryption-keys.md) op indexen of synoniemtoewijzingen en u kunt gebruikmaken van dubbele versleuteling voor die inhoud. | Algemeen beschikbaar (GA) voor alle zoekservices die zijn gemaakt na 1 augustus 2020 in deze regio's: VS - west 2, VS - oost, VS - zuid-centraal, US Gov - Virginia, US Gov - Arizona. Gebruik de portal, REST-API's voor beheer of SDK's om de service te maken. |

## <a name="july-2020"></a>Juli 2020

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|---------|------------------|-------------|---------------|
| [Azure.Search.Documents-clientbibliotheek](/dotnet/api/overview/azure/search.documents-readme) | Azure-SDK voor .NET | E.NET-clientbibliotheek uitgebracht door het team van Azure SDK. Deze is compatibel met andere .NET-clientbibliotheken. <br/><br/>Versie 11 is gericht op de Search REST API versie 2020-06-30, maar biedt nog geen ondersteuning voor het kennisarchief of georuimtelijke typen. <br/><br/>Zie voor meer informatie [Quickstart: Een index maken](search-get-started-dotnet.md) en [Upgraden naar Azure.Search.Documents (v11)](search-dotnet-sdk-migration-version-11.md). | Algemeen verkrijgbaar. </br> Installeer het [Azure.Search.Documents-pakket](https://www.nuget.org/packages/Azure.Search.Documents/) van NuGet. |
| [azure.search.documents-clientbibliotheek](/python/api/overview/azure/search-documents-readme)  | Azure-SDK voor Python| Python-clientbibliotheek uitgebracht door het team van Azure SDK. Deze is compatibel met andere Python-clientbibliotheken. <br/><br/>Versie 11 is gericht op de Search REST api-version=2020-06-30. | Algemeen verkrijgbaar. </br> Installeer het [azure-search-documents-pakket](https://pypi.org/project/azure-search-documents/) van PyPI. |
| [@azure/search-documents clientbibliotheek](/javascript/api/overview/azure/search-documents-readme)  | Azure-SDK voor JavaScript | JavaScript-clientbibliotheek uitgebracht door het team van Azure SDK. Deze is compatibel met andere JavaScript-clientbibliotheken. <br/><br/>Versie 11 is gericht op de Search REST api-version=2020-06-30. | Algemeen verkrijgbaar. </br> Installeer het [@azure/search-documents-pakket](https://www.npmjs.com/package/@azure/search-documents) van npm. |

## <a name="june-2020"></a>Juni 2020

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|---------|------------------|-------------|---------------|
[**Kennisarchief**](knowledge-store-concept-intro.md) | AI-verrijking | Uitvoer van een met AI verrijkte indexeerfunctie, die inhoud opslaat in Azure Storage voor gebruik in andere apps en processen. | Algemeen verkrijgbaar. </br> Gebruik [Search REST API 2020-06-30](/rest/api/searchservice/) of hoger, of het portal. |
| [**Search REST API 2020-06-30**](/rest/api/searchservice/) | REST | Een nieuwe stabiele versie van de REST API's. Naast het kennisarchief bevat deze versie verbeteringen wat betreft de relevantie en scoren van de zoekresultaten. | Algemeen verkrijgbaar. |
| [**Okapi BM25 relevantie-algoritme**](https://en.wikipedia.org/wiki/Okapi_BM25) | Query’s uitvoeren | Nieuw algoritme voor relevantieclassificatie wordt automatisch gebruikt voor alle nieuwe zoekservices die zijn gemaakt na 15 juli. Voor services die vroeger zijn aangemaakt kunt u het gebruiken door de eigenschap `similarity` in te stellen op indexvelden. | Algemeen verkrijgbaar. </br> Gebruik [Search REST API 2020-06-30](/rest/api/searchservice/) of hoger, of REST API 2019-05-06. |
| **executionEnvironment** | Beveiliging (Indexeerfuncties) | Stel de configuratie-eigenschap van deze indexeerfunctie expliciet in op `private` om alle verbindingen met externe gegevensbronnen via een privé-eindpunt te laten verlopen. Enkel van toepassing op zoekservices die gebruikmaken van Azure Private Link. | Algemeen verkrijgbaar. </br> Gebruik [Search REST API 2020-06-30](/rest/api/searchservice/) om deze parameter voor algemene configuratie in te stellen. |

## <a name="may-2020-microsoft-build"></a>Mei 2020 (Microsoft Build)

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|---------|------------------|-------------|---------------|
| [**Foutopsporingssessies**](cognitive-search-debug-session.md) | AI-verrijking | Foutopsporingssessies bieden een portalinterface waarin u problemen kunt onderzoeken en oplossen met een bestaande vaardighedenset. Fixes die zijn gemaakt in de foutopsporingssessie, kunnen worden opgeslagen in de productievaardighedenset. Ga aan de slag met [deze zelfstudie](cognitive-search-tutorial-debug-sessions.md). | Openbare preview in het portal. |
| [**IP-regels voor inkomende firewall-ondersteuning**](service-configure-firewall.md) | Beveiliging | Beperk de toegang tot een zoekservice-eindpunt voor specifieke IP-adressen. | Algemeen verkrijgbaar. </br> Gebruik [Management REST API 2020-03-13](/rest/api/searchmanagement/) of hoger, of het portal. |
| [**Azure Private Link voor een persoonlijk zoekeindpunt**](service-create-private-endpoint.md) | Beveiliging| Scherm een zoekservice af van het openbare internet door deze uit te voeren als een Private Link-resource, die enkel toegankelijk is voor client-apps en andere Azure-services in hetzelfde virtuele netwerk. | Algemeen verkrijgbaar. </br> Gebruik [Management REST API 2020-03-13](/rest/api/searchmanagement/) of hoger, of het portal. |
| [**door het systeem beheerde identiteit (preview)**](search-howto-managed-identities-data-sources.md) | Beveiliging (Indexeerfuncties) | Registreer een zoekservice als vertrouwde service met Azure Active Directory om verbindingen met de ondersteunde Azure-gegevensbron in te stellen voor indexering. Geldt voor [indexeerfuncties](search-indexer-overview.md) die inhoud opnemen uit Azure-gegevensbronnen, zoals Azure SQL Database, Azure Cosmos DB en Azure Storage. | Openbare preview. </br> Gebruik het portal om de zoekservice te registreren. |
| [**sessionId query parameter**](index-similarity-and-scoring.md), [scoringStatistics=global parameter](index-similarity-and-scoring.md#scoring-statistics) | Query (relevantie) | Voeg sessionID toe aan een query om een sessie voor het berekenen van zoekscores te maken, met scoringStatistics=Global om scores te verzamelen van alle shards en zoekscores consistenter te berekenen. | Algemeen verkrijgbaar. </br> Gebruik [Search REST API 2020-06-30](/rest/api/searchservice/) of hoger, of REST API 2019-05-06. |
| [**featuresMode (preview)**](index-similarity-and-scoring.md#featuresMode-param) | Query’s uitvoeren | Voeg deze queryparameter toe om een relevantiescore uit te breiden om meer details weer te geven: vergelijkbaarheidsscore per veld, termfrequentie per veld, en gematcht aantal unieke tokens per veld. U kunt deze gegevenspunten gebruiken in aangepaste scorealgoritmen. Zie [Add machine learning (LearnToRank) to search relevance](https://github.com/Azure-Samples/search-ranking-tutorial) voor een voorbeeld waarin deze mogelijkheid wordt gedemonstreerd. | Openbare preview. </br> Gebruik [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) of REST API 2019-05-06-Preview. |

## <a name="march-2020"></a>Maart 2020

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|---------|------------------|-------------|---------------|
| [**Voorlopig verwijderen van systeemeigen blob (preview)**](search-howto-index-changed-deleted-blobs.md) | Indexeerfuncties | Een Azure Blob Storage-indexeerfunctie in Azure Cognitive Search herkent de blobs die de status Voorlopig verwijderd hebben, en verwijdert het bijbehorende zoekdocument tijdens het indexeren. | Openbare preview. </br> Gebruik de [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) en REST API 2019-05-06-Preview, met Uitvoering van indexeerfunctie voor een gegevensbron van een Azure-blob waarvoor een systeemeigen 'voorlopig verwijderen' is ingeschakeld. |
| [**Management REST API (2020-03-13)**](/rest/api/searchmanagement/management-api-versions) | REST | Nieuwe stabiele REST API voor het maken en beheren van een zoekservice. Voegt ondersteuning voor IP-firewall en Private Link toe | Algemeen verkrijgbaar. |

## <a name="february-2020"></a>Februari 2020

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|---------|------------------|-------------|---------------|
| [**PII-detectie (preview)**](cognitive-search-skill-pii-detection.md) | AI-verrijking | Een nieuwe cognitieve vaardigheid die wordt gebruikt tijdens de indexering. Hiermee worden persoonsgegevens opgehaald uit een invoertekst en krijgt u de mogelijkheid om deze op verschillende manieren in deze tekst te maskeren. | Openbare preview. </br> Gebruik het portal of [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) of REST API 2019-05-06-Preview. |
| [**Aangepaste entiteit zoeken (preview)**](cognitive-search-skill-custom-entity-lookup.md )| AI-verrijking | Een nieuwe cognitieve vaardigheid die zoekt naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinsdelen. Aan de hand van deze lijst worden alle documenten met overeenkomende entiteiten gelabeld. De vaardigheid ondersteunt ook het zoeken naar fuzzy overeenkomsten, waarmee overeenkomsten worden gevonden die vergelijkbaar zijn, maar niet exact. | Openbare preview. </br> Gebruik het portal of [Search REST API 2020-06-30-Preview](/rest/api/searchservice/index-preview) of REST API 2019-05-06-Preview. |

## <a name="january-2020"></a>Januari 2020

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|---------|------------------|-------------|---------------|
| [**Door de klant beheerde versleutelingssleutels**](search-security-manage-encryption-keys.md) |Beveiliging | Voegt een extra laag versleuteling toe naast de ingebouwde versleuteling van het platform. U kunt met behulp van een versleutelingssleutel die u maakt en beheert, de inhoud van een index en synoniemtoewijzingen versleutelen voordat de payload een zoekservice bereikt. | Algemeen verkrijgbaar. </br> Gebruik Search REST API 2019-05-06 of hoger. Voor beheerde code is het juiste pakket nog steeds [.NET SDK versie 8.0-preview](search-dotnet-sdk-migration-version-9.md), ook al is de preview-fase van de functie voorbij. |
| [**IP-regels voor inkomende firewall-ondersteuning (preview)**](service-configure-firewall.md) | Beveiliging | Beperk de toegang tot een zoekservice-eindpunt voor specifieke IP-adressen. De preview-API bevat de nieuwe eigenschappen **IpRule** en **NetworkRuleSet** in [CreateOrUpdate-API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service). Deze preview-functie is beschikbaar in bepaalde regio's. |  Openbare Preview met api-version=2019-10-01-Preview.  |
| [**Azure Private Link voor een persoonlijk zoekeindpunt (preview)**](service-create-private-endpoint.md) | Beveiliging| Scherm een zoekservice af van het openbare internet door deze uit te voeren als een Private Link-resource, die enkel toegankelijk is voor client-apps en andere Azure-services in hetzelfde virtuele netwerk. | Openbare Preview met api-version=2019-10-01-Preview.  |

## <a name="features-in-2019"></a>Functies in 2019

### <a name="december-2019"></a>December 2019

+ [Demo-app maken (preview)](search-create-app-portal.md) is een nieuwe wizard in de portal waarmee een downloadbaar HTML-bestand wordt gegenereerd met query-toegang (alleen-lezen) tot een index. Het bestand wordt geleverd met een ingesloten script waarmee een operationele 'localhost' -achtige web-app wordt weergegeven die is gebonden aan een index in uw zoekservice. Pagina's kunnen worden geconfigureerd in de wizard en kunnen een zoekbalk, een resultaatgebied, navigatie in de zijbalk en ondersteuning voor typeahead-query's bevatten. U kunt de HTML offline wijzigen om de werkstroom of weergave uit te breiden of aan te passen. Een demo-app kan niet gemakkelijk worden uitgebreid met de beveiligings- en hosting-lagen die gewoonlijk nodig zijn in productiescenario's. U dient dit te beschouwen als een validatie- en testhulpmiddel, en niet als een kortere weg naar een volledige client-app.

+ In [Een privé-eindpunt maken voor veilige verbindingen (preview)](service-create-private-endpoint.md) wordt uitgelegd hoe u Private Link instelt voor veilige verbindingen met uw zoekservice. Deze preview-functie is op aanvraag beschikbaar en gebruikt [Azure Private Link](../private-link/private-link-overview.md) en [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) als onderdeel van de oplossing.

### <a name="november-2019---ignite-conference"></a>November 2019: Ignite Conference

+ Met [Incrementele verrijking (preview)](cognitive-search-incremental-indexing-conceptual.md) voegt u opslaan in cache en statefullness toe aan een verrijkingspijplijn, zodat u aan specifieke stappen of fasen kunt werken zonder inhoud te verliezen die al is verwerkt. Voorheen moest een verrijkingspijplijn volledig opnieuw worden gebouwd na een wijziging. Met stapsgewijze verrijking blijft de uitvoer van kostbare analyses, met name afbeeldingsanalyses, behouden.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [Documentextractie (preview)](cognitive-search-skill-document-extraction.md) is een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren. U kunt hiermee de inhoud van een bestand extraheren uit een vaardighedenset. Voorheen werd een document alleen gekraakt voordat een vaardighedenset werd uitgevoerd. Door de toevoeging van deze vaardigheid kun je deze bewerking ook uitvoeren binnen de uitvoering van de vaardighedenset.

+ [Tekstomzetting](cognitive-search-skill-text-translation.md) is een cognitieve vaardigheid die wordt gebruikt tijdens indexering. Hiermee wordt tekst geëvalueerd en wordt voor elke record de tekst geretourneerd die is vertaald in de opgegeven doeltaal.

+ Met [Power BI-sjablonen](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) kunt u aan de slag met uw visualisaties en analyses van verrijkte inhoud in een kennisarchief in Power BI Desktop. Deze sjabloon is ontworpen voor Azure-tabelprojecties die zijn gemaakt met de [wizard Gegevens importeren](knowledge-store-create-portal.md).

+ [Azure Data Lake Storage Gen2 (preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin API (preview)](search-howto-index-cosmosdb.md) en [Cosmos DB Cassandra API (preview)](search-howto-index-cosmosdb.md) worden nu ondersteund in indexeerfuncties. U kunt zich registreren via [dit formulier](https://aka.ms/azure-cognitive-search/indexer-preview). Je ontvangt een bevestigingsmail zodra je bent geaccepteerd in het previewprogramma.

### <a name="july-2019"></a>Juli 2019

+ Algemeen beschikbaar in [Azure Government Cloud](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search).

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nieuwe servicenaam

Azure Search heet nu **Azure Cognitive Search** vanwege het uitgebreide (maar optionele) gebruik van cognitieve vaardigheden en AI-verwerking in kernbewerkingen. API-versies, NuGet-pakketten, naamruimten en eindpunten zijn ongewijzigd. De wijziging van de servicenaam is niet van invloed op nieuwe en bestaande zoekoplossingen.

## <a name="service-updates"></a>Service-updates

[Aankondigingen van service-updates](https://azure.microsoft.com/updates/?product=search&status=all) voor Azure Cognitive Search kunt u vinden op de Azure-website.