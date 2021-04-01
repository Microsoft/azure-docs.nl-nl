---
title: API-versies
titleSuffix: Azure Cognitive Search
description: Versie beleid voor Azure Cognitive Search REST-Api's en de client bibliotheek in de .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: d2308d065755df834a224ef3b1df202d7b7dc5ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100518943"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-versies in azure Cognitive Search

De functie-updates worden regel matig door Azure Cognitive Search getotaleerd. Soms is voor deze updates niet altijd een nieuwe versie van de API nodig om achterwaartse compatibiliteit te behouden. Als u een nieuwe versie publiceert, kunt u bepalen wanneer en hoe u de updates van zoek services integreert in uw code.

Als regel publiceert het Azure Cognitive Search-team nieuwe versies alleen wanneer dat nodig is, omdat dit een zekere moeite kan zijn om uw code bij te werken om een nieuwe API-versie te gebruiken. Een nieuwe versie is alleen nodig als een aspect van de API is gewijzigd op een manier die achterwaartse compatibiliteit verbreekt. Dergelijke wijzigingen kunnen zich voordoen als gevolg van oplossingen voor bestaande functies of vanwege nieuwe functies die de bestaande API-surface area wijzigen.

Dezelfde regel is van toepassing op SDK-updates. De Azure Cognitive Search SDK volgt de regels voor [semantische versie beheer](https://semver.org/) , wat betekent dat de versie drie delen heeft: primair, secundair en buildnummer (bijvoorbeeld 1.1.0). Er wordt alleen een nieuwe primaire versie van de SDK uitgebracht voor wijzigingen die achterwaartse compatibiliteit verstoren. Bij het bijwerken van de niet-brekende onderdelen wordt de secundaire versie verhoogd, en met fout oplossingen wordt de build-versie alleen verhoogd.

> [!Important]
> De Azure-Sdk's voor .NET, Java, python en Java script implementeren nieuwe client bibliotheken voor Azure Cognitive Search. Op dit moment bieden geen van de Azure SDK-bibliotheken volledige ondersteuning voor de meest recente Zoek REST Api's (2020-06-30) of beheer REST-Api's (2020-03-13), maar dit wordt in de loop van de tijd gewijzigd. U kunt deze pagina regel matig controleren of [Wat is er nieuw](whats-new.md) voor aankondigingen over functionele uitbrei dingen.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Niet-ondersteunde versies

Upgrade bestaande Zoek oplossingen naar de nieuwste versie van de REST API op 15 oktober 2020. Op dat moment worden de volgende versies van de Azure Cognitive Search REST API buiten gebruik gesteld en worden ze niet meer ondersteund:

+ **2015-02-28**
+ **2015-02-28-preview**
+ **2014-07-31-preview**
+ **2014-10-20-preview**

Daarnaast worden versies van de Azure Cognitive Search .NET SDK die ouder zijn dan [**3.0.0-RC**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc) ook buiten gebruik gesteld, omdat ze een van deze rest API versies richten. 

Na deze datum zullen toepassingen die gebruikmaken van een van de afgeschafte REST API-of SDK-versies niet meer werken en moeten ze worden bijgewerkt. Net als bij elke wijziging van dit type, geven we de kennisgeving van 12 maanden, zodat u voldoende tijd hebt om aan te passen.

Als u Azure Cognitive Search wilt blijven gebruiken, migreert u de bestaande code die de [rest API](search-api-migration.md) streeft naar [rest API versie 2020-06-30](/rest/api/searchservice/) of een nieuwere SDK op 15 oktober 2020.  Als u vragen hebt over het bijwerken van de nieuwste versie, stuurt u een e-mail naar azuresearch_contact@microsoft.com 15 mei 2020 om ervoor te zorgen dat u voldoende tijd hebt om uw code bij te werken.

## <a name="rest-apis"></a>REST-API’s

Een exemplaar van de Azure Cognitive Search-service ondersteunt verschillende REST API versies, met inbegrip van de meest recente versie. U kunt een versie blijven gebruiken wanneer deze niet langer het meest recent is, maar we raden u aan [uw code te migreren](search-api-migration.md) om de nieuwste versie te gebruiken. Wanneer u de REST API gebruikt, moet u in elke aanvraag de API-versie opgeven via de para meter API-Version. Wanneer u de .NET SDK gebruikt, bepaalt de versie van de SDK die u gebruikt, de corresponderende versie van de REST API. Als u een oudere SDK gebruikt, kunt u deze code zonder wijzigingen blijven uitvoeren, zelfs niet als de service wordt bijgewerkt voor ondersteuning van een nieuwere API-versie.

De volgende tabel bevat de versie geschiedenis van huidige en eerder uitgebrachte versies van de Search Service REST API. Documentatie wordt alleen gepubliceerd voor de meest recente stabiele en Preview-versies.

### <a name="search-service-apis"></a>Search Service-Api's

Inhoud maken en beheren in een zoek service.

| Versie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Beschrijving |
|-------------------------|--------|------------------------------|
| [Search 2020-06-30](/rest/api/searchservice/index)| Stabiel | De nieuwste stabiele versie van de zoek REST-Api's, met behulp van relevantie scores en algemeen Beschik baarheid voor kennis opslag.|
| [Search 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Preview | Preview-versie gekoppeld aan stabiele versie. Bevat meerdere [Preview-functies](search-api-preview.md). |
| Search 2019-05-06 | Stabiel  | Voegt [complexe typen](search-howto-complex-data-types.md)toe. |
| Search 2019-05-06-preview | Preview | Preview-versie gekoppeld aan stabiele versie. |
| 2017-11-11 zoeken | Stabiel | Vaardig heden en [AI-verrijking](cognitive-search-concept-intro.md)toevoegen. |
| Search 2017-11-11-preview | Preview | Preview-versie gekoppeld aan stabiele versie. |
| 2016-09-01 zoeken |Stabiel | [Indexeer functies](search-indexer-overview.md)worden toegevoegd. |
| Search 2016-09-01-preview | Preview | Preview-versie gekoppeld aan stabiele versie.|
| 2015-02-28 zoeken | Niet ondersteund na 10-10-2020   | Eerste beschik bare release.  |
| Search 2015-02-28-preview | Niet ondersteund na 10-10-2020  | Preview-versie gekoppeld aan stabiele versie. |
| Search 2014-10-20-preview | Niet ondersteund na 10-10-2020 | Tweede open bare preview. |
| Search 2014-07-31-preview | Niet ondersteund na 10-10-2020  | Eerste open bare preview. |

### <a name="management-rest-apis"></a>REST API's voor beheer

Een zoek service maken en configureren en API-sleutels beheren.

| Versie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Beschrijving |
|-------------------------|--------|------------------------------|
| [Beheer 2020-08-01](/rest/api/searchmanagement/) | Stabiel | Nieuwste stabiele versie van de beheer REST-Api's. Hiermee wordt algemeen beschik bare ondersteuning voor gedeelde persoonlijke koppelings bronnen toegevoegd voor alle bronnen waarvoor toegang wordt verkregen via uitgaande resources, behalve die in de preview-versie |
| [Beheer 2020-08-01-preview](/rest/api/searchmanagement/index-preview) | Preview  | Momenteel als Preview: de resource ondersteuning voor gedeelde persoonlijke koppelingen voor Azure Functions en Azure Database for MySQL. |
| Management 2020-03-13  | Stabiel | Voegt een [persoonlijk eind punt](service-create-private-endpoint.md) toe via een persoonlijke koppeling en [netwerk-IP-regels](service-configure-firewall.md) voor nieuwe services. Zie voor meer informatie deze [Swagger-specificatie](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01). |
| Management 2019-10-01-Preview | Preview  | Er zijn geen preview-functies geïntroduceerd in deze lijst. Dit voor beeld is functioneel equivalent aan 2020-03-13. Zie voor meer informatie deze [Swagger-specificatie](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview). |
| Beheer 2015-08-19  | Stabiel | De eerste beschik bare versie van de beheer REST-Api's. Biedt service-inrichting, opschalen en API-sleutel beheer. Zie voor meer informatie deze [Swagger-specificatie](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |
| Beheer 2015-08-19-preview  | Preview | De eerste preview-versie van de beheer REST-Api's. Zie voor meer informatie deze [Swagger-specificatie](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |

## <a name="azure-sdk-for-net"></a>Azure-SDK voor .NET

De volgende tabel bevat koppelingen naar recentere SDK-versies. 

| SDK-versie | Status | Beschrijving |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stabiel | Nieuwe client bibliotheek van de Azure .NET SDK, uitgebracht op 2020 juli. Streeft naar de Search REST API-Version = 2020-06-30 REST API, maar biedt nog geen systeem eigen ondersteuning voor geo-filters. [Micro soft. ruimtelijk pakket wordt](https://www.nuget.org/packages/Microsoft.Spatial/) aanbevolen voor geografische bewerkingen. Voor beelden zijn beschikbaar voor [System.Text.Jsop](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial/README.md) en [Newtonsoft.Jsop](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Microsoft.Azure.Core.Spatial.NewtonsoftJson/README.md). |
| [Micro soft. Azure. Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stabiel | Uitgebracht op 2019 mei. Streeft naar de Search REST API-Version = 2019-05-06.|
| [Micro soft. Azure. Management. Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stabiel | Streeft naar de beheer REST API-Version = 2020-08-01.  |
| Micro soft. Azure. Management. Search 3.0.0 | Stabiel | Streeft naar de beheer REST API-Version = 2015-08-19.  |

## <a name="azure-sdk-for-java"></a>Azure SDK voor Java

| SDK-versie | Status | Beschrijving  |
|-------------|--------|------------------------------|
| [Java Azure-Search-documenten 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stabiel | Nieuwe client bibliotheek van Azure Java SDK, uitgebracht op 2020 juli. Streeft naar de Search REST API-Version = 2019-05-06. |
| [1.35.0 java-beheer client](/java/api/overview/azure/search/management) | Stabiel | Streeft naar de beheer REST API-Version = 2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>Azure-SDK voor JavaScript

| SDK-versie | Status | Beschrijving  |
|-------------|--------|------------------------------|
| [Java script @azure/search-documents 11,0](https://www.npmjs.com/package/@azure/search-documents) | Stabiel | Nieuwe client bibliotheek van Azure java script & TypesScript SDK, uitgebracht op 2020 juli. Streeft naar de Search REST API-Version = 2016-09-01. |
| [Ondersteunen @azure/arm-search](https://www.npmjs.com/package/@azure/arm-search) | Stabiel | Streeft naar de beheer REST API-Version = 2015-08-19. |

## <a name="azure-sdk-for-python"></a>Azure-SDK voor Python

| SDK-versie | Status | Beschrijving  |
|-------------|--------|------------------------------|
| [Python Azure-Search-documenten 11,0](https://pypi.org/project/azure-search-documents/) | Stabiel | Nieuwe client bibliotheek van Azure python SDK, uitgebracht op 2020 juli. Streeft naar de Search REST API-Version = 2019-05-06. |
| [Python Azure-beheer-Search 8,0](https://pypi.org/project/azure-mgmt-search/) | Stabiel | Streeft naar de beheer REST API-Version = 2015-08-19. |
