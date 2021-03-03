---
title: Nieuwe functies in Azure Cognitive Search
description: Aankondigingen van nieuwe en verbeterde functies, waaronder een wijziging van de servicenaam van Azure Search in Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 36f10bebfc42ae5e9e75206392e8a5f8ccef563a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694594"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Nieuwe functies in Azure Cognitive Search

Meer informatie over nieuwe functies in de service. Voeg een bladwijzer toe aan deze pagina om up-to-date te blijven over de service. Bekijk de [Preview-functie lijst](search-api-preview.md) voor een uitgebreide lijst met functies die nog niet algemeen beschikbaar zijn.

## <a name="march-2021"></a>2021 maart

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Beschrijving | Beschikbaarheid  |
|------------------------------|---------------|---------------|
| [Semantische zoekopdrachten](semantic-search-overview.md) | Een verzameling query-gerelateerde functies waarmee de relevantie van zoek resultaten met weinig moeite wordt verbeterd. Met kleine wijzigingen in een zoek opdracht kunt u deze functies uitproberen op bestaande indexen.</br></br>[Semantische query](semantic-how-to-query-request.md) is een nieuw query type dat versnelt in de verwerking van natuurlijke taal verkrijgt om de rang orde te verbeteren, en om inzicht te krijgen in de query intentie om antwoorden, bijschriften en semantische hooglichten te bieden.</br></br>[Semantische classificatie en Reacties (antwoorden, bijschriften en hooglichten)](semantic-how-to-query-response.md) verwijzen naar het model dat de resultaten evalueert en de mogelijkheid van het model om een structuur toe te voegen aan het antwoord. | Open bare Preview ([op aanvraag](https://aka.ms/SemanticSearchPreviewSignup)). </br></br>[Zoek documenten (rest)](/rest/api/searchservice/preview-api/search-documents) API-Version = 2020-06 -30-preview en [Search explorer](search-explorer.md) gebruiken in azure Portal. </br></br>De beperkingen voor de regio en de laag zijn van toepassing. |
| [Spelling controle van query termen](speller-how-to-add.md) | Voordat de query termen de zoek machine bereiken, kunt u ze controleren op spel fouten. De `speller` optie werkt met elk query type (eenvoudig, volledig of semantisch). |  Open bare preview, alleen REST, API-Version = 2020-06 -30-preview|
| [Indexer van share point online](search-howto-index-sharepoint-online.md) | Met deze indexer maakt u verbinding met een share point online-site, zodat u inhoud kunt indexeren vanuit een document bibliotheek. | Open bare preview, alleen REST, API-Version = 2020-06 -30-preview |

## <a name="february-2021"></a>Februari 2021

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Beschrijving | Beschikbaarheid  |
|------------------------------|---------------|---------------|
| [Documenten opnieuw instellen (preview-versie)](search-howto-run-reset-indexers.md) |  Afzonderlijke geselecteerde Zoek documenten in de werk belasting van de Indexeer functie opnieuw verwerken. | [Search REST API 2020-06-30-preview](/rest/api/searchservice/index-preview) |
| [Beschikbaarheidszones](search-performance-optimization.md#availability-zones)| Zoek Services met twee of meer replica's in bepaalde regio's, zoals wordt weer gegeven in [schaal voor prestaties](search-performance-optimization.md#availability-zones), profiteer van tolerantie door replica's te hebben op twee of meer verschillende fysieke locaties.  | De regio en de datum waarop de zoek service is gemaakt, bepalen de beschik baarheid. Zie de schaal voor het prestatie artikel voor meer informatie. |
| [Azure-CLI](/cli/azure/search) </br>[Azure PowerShell](/powershell/module/az.search/) | Nieuwe revisies bieden nu het volledige aantal bewerkingen in het management REST API 2020-08-01, inclusief ondersteuning voor IP-firewall regels en privé-eind punten. | Algemeen verkrijgbaar. |

## <a name="january-2021"></a>Januari 2021

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Beschrijving | Beschikbaarheid  |
|------------------------------|-------------|---------------|
| [Oplossings versneller voor Azure Cognitive Search en QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Hiermee worden vragen en antwoorden uit het document opgehaald en worden de meest relevante antwoorden voorgesteld. Een live demo-app kan worden gevonden op [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) .  | Open-source project (geen SLA) |

## <a name="2020-archive"></a>2020-archief

| Maand | Functie | Beschrijving |
|-------|---------|-------------|
| November | [Versleuteling door de klant beheerde sleutel (uitgebreid)](search-security-manage-encryption-keys.md) | Breidt door de klant beheerde versleuteling uit over het volledige aantal assets dat door een zoek service wordt gemaakt en beheerd. Algemeen verkrijgbaar.|
| september | [Visual Studio code Extension voor Azure Cognitive Search](search-get-started-vs-code.md) | Hiermee voegt u een werk ruimte, navigatie, IntelliSense en sjablonen toe voor het maken van indexen, Indexeer functies, gegevens bronnen en vaardig heden. Deze functie is momenteel beschikbaar als openbare preview-versie.| 
| september | [Beheerde service-identiteit (Indexeer functies)](search-howto-managed-identities-data-sources.md) | Algemeen verkrijgbaar.  |
| september | [Uitgaande aanvragen met behulp van een privélink](search-indexer-howto-access-private.md) | Algemeen verkrijgbaar.  |
| september | [Beheer van REST API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Algemeen verkrijgbaar. |
| september | [Beheer van REST API (2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | Hiermee wordt een gedeelde privélinkresource toegevoegd voor Azure Functions en Azure SQL voor MySQL-databases. |
| september | [Beheer van .NET SDK 4.0](/dotnet/api/overview/azure/search/management) |  Azure SDK-update voor het beheer van SDK, gericht op REST API versie 2020-08-01. Algemeen verkrijgbaar.|
| Augustus | [Dubbele versleuteling](search-security-overview.md#encryption) | Algemeen beschikbaar op alle zoek services die zijn gemaakt na 1 augustus 2020 in deze regio's: VS-West 2, VS-Oost, Zuid-Centraal VS, US Gov-Virginia, US Gov-Arizona. |
| Juli | [Azure.Search.Documents-clientbibliotheek](/dotnet/api/overview/azure/search.documents-readme) | Azure SDK voor .NET, algemeen beschikbaar. |
| Juli | [azure.search.documents-clientbibliotheek](/python/api/overview/azure/search-documents-readme)  | Azure SDK voor python, algemeen beschikbaar. |
| Juli | [@azure/search-documents clientbibliotheek](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK voor Java script, algemeen beschikbaar. |
| Juni | [Kennisarchief](knowledge-store-concept-intro.md) | Algemeen verkrijgbaar. |
| Juni | [Search REST API 2020-06-30](/rest/api/searchservice/) | Algemeen verkrijgbaar. |
| Juni | [Search REST API 2020-06-30-preview](/rest/api/searchservice/) | Hiermee wordt het opnieuw instellen van vakkennis ingesteld op selectieve vaardig heden en incrementele verrijking. |
| Juni | [Okapi BM25 relevantie-algoritme](index-ranking-similarity.md) | Algemeen verkrijgbaar. |
| Juni |  **executionEnvironment** (van toepassing op zoek services met behulp van een persoonlijke Azure-koppeling.) | Algemeen verkrijgbaar. |
| Juni | [AML-vaardigheid (preview-versie)](cognitive-search-aml-skill.md) | Een cognitieve vaardigheid waarmee AI-verrijking wordt uitgebreid met een aangepast Azure Machine Learning (AML)-model. |
| mei | [Sessies voor fout opsporing (preview-versie)](cognitive-search-debug-session.md) | Vaardig heden-fout opsporing in de portal.  |
| mei | [IP-regels voor inkomende firewall-ondersteuning](service-configure-firewall.md) | Algemeen verkrijgbaar.  |
| mei | [Azure Private Link voor een persoonlijk zoekeindpunt](service-create-private-endpoint.md) | Algemeen verkrijgbaar.  |
| mei | [Beheerde service-identiteit (Indexeer functies)-(preview)](search-howto-managed-identities-data-sources.md) | Verbinding maken met Azure-gegevens bronnen met behulp van een beheerde identiteit.  |
| mei | [sessionId query parameter](index-similarity-and-scoring.md), [scoringStatistics=global parameter](index-similarity-and-scoring.md#scoring-statistics)  | Algemene Zoek statistieken, handig voor [machine learning modellen (LearnToRank) voor de relevantie van de zoek opdracht](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| mei | [featuresMode-relevantiescore-uitbreiding (preview)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|Maart  | [Voorlopig verwijderen van systeemeigen blob (preview)](search-howto-index-changed-deleted-blobs.md) | Hiermee worden Zoek documenten verwijderd als de bron-BLOB zacht is verwijderd in Blob Storage. |
|Maart  | [Management REST API (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Algemeen verkrijgbaar. |
|Februari | [PII-detectie vaardigheid (preview-versie)](cognitive-search-skill-pii-detection.md)  | Een cognitieve vaardigheid waarmee persoonlijke gegevens worden geëxtraheerd en gemaskeerd. |
|Februari | [Zoek vaardigheid aangepaste entiteit (preview-versie)](cognitive-search-skill-custom-entity-lookup.md) | Een cognitieve vaardigheid waarmee woorden en zinsdelen uit een lijst worden gevonden en alle documenten met overeenkomende entiteiten worden gelabeld.  |
|Januari | [Door de klant beheerde sleutel versleuteling](search-security-manage-encryption-keys.md) | Algemeen beschikbaar  |
|Januari | [IP-regels voor inkomende firewall-ondersteuning (preview)](service-configure-firewall.md) | Nieuwe **IpRule** -en **NetworkRuleSet** -eigenschappen in de [CreateOrUpdate-API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service).  |
|Januari | [Een persoonlijk eind punt maken (preview)](service-create-private-endpoint.md) | Stel een persoonlijke koppeling in voor beveiligde verbindingen met uw zoek service. Deze preview-functie heeft een afhankelijk [Azure-persoonlijke koppeling](../private-link/private-link-overview.md) en [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) als onderdeel van de oplossing. |

## <a name="2019-archive"></a>2019-archief

| Maand | Functie | Beschrijving |
|-------|---------|-------------|
|december | [Demo-app maken (preview)](search-create-app-portal.md) | Een wizard waarmee een HTML-bestand dat kan worden gedownload met de query (alleen-lezen) wordt gegenereerd voor een index die is bedoeld als een validatie-en test programma in plaats van een korte cut naar een volledige client-app.|
|November | [Incrementele verrijking (preview-versie)](cognitive-search-incremental-indexing-conceptual.md) | Caches voor het verwerken van vaardig heden voor toekomstig gebruik.  |
|November | [Vaardigheid van document extractie (preview-versie)](cognitive-search-skill-document-extraction.md) | Een cognitieve vaardigheid voor het extra heren van de inhoud van een bestand in een vakkennisset.|
|November | [Vaardigheid van tekst vertalingen](cognitive-search-skill-text-translation.md) | Een cognitieve vaardigheid die wordt gebruikt tijdens het indexeren waarmee tekst wordt geëvalueerd en vertaald. Algemeen verkrijgbaar.|
|November | [Power BI sjablonen](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Sjabloon voor het visualiseren van inhoud in het kennis archief |
|November | [Azure data Lake Storage Gen2 (preview)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB Gremlin-API (preview)](search-howto-index-cosmosdb.md)en [Cosmos DB Cassandra-API (preview-versie)](search-howto-index-cosmosdb.md) | Nieuwe Indexeer functie gegevens bronnen in open bare preview. |
|Juli | [Cloud ondersteuning Azure Government](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia&products=search) | Algemeen verkrijgbaar.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Nieuwe servicenaam

Azure Search is in oktober 2019 gewijzigd in **Azure Cognitive Search** om het uitgebreide (nog optioneel) gebruik van cognitieve vaardig heden en AI-verwerking in kern bewerkingen weer te geven. API-versies, NuGet-pakketten, naamruimten en eindpunten zijn ongewijzigd. De wijziging van de servicenaam is niet van invloed op nieuwe en bestaande zoekoplossingen.

## <a name="service-updates"></a>Service-updates

[Aankondigingen van service-updates](https://azure.microsoft.com/updates/?product=search&status=all) voor Azure Cognitive Search kunt u vinden op de Azure-website.