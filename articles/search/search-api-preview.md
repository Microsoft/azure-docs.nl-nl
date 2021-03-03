---
title: Lijst met preview-functies
titleSuffix: Azure Cognitive Search
description: Preview-functies worden uitgebracht zodat klanten feedback kunnen geven over hun ontwerp en hulp programma. Dit artikel bevat een uitgebreide lijst met alle functies die momenteel als preview-versie beschikbaar zijn.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: a03707133b384f7717a624e810d0f7a41267969d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670881"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Preview-functies in azure Cognitive Search

Dit artikel bevat een uitgebreide lijst met alle functies die beschikbaar zijn in de open bare preview. Deze previewfunctie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Preview-functies die worden overgezet naar algemene Beschik baarheid, worden verwijderd uit deze lijst. Als een functie hieronder niet wordt vermeld, kunt u ervan uitgaan dat deze algemeen beschikbaar is. Zie [service-updates](https://azure.microsoft.com/updates/?product=search) of [Wat is er nieuw](whats-new.md)voor aankondigingen over algemene Beschik baarheid.

|Functie&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Categorie | Beschrijving | Beschikbaarheid  |
|---------|------------------|-------------|---------------|
| [**Semantische zoekopdrachten**](semantic-search-overview.md) | Relevantie (Score) | Een algoritme voor semantische classificatie, query type en query-antwoord. | [Search REST API 2020-06-30-preview](/rest/api/searchservice/) |
| [**speller**](cognitive-search-aml-skill.md) | Query’s uitvoeren | Optionele spelling correctie voor invoer van de query term voor eenvoudige, volledige en semantische query's. | [Search REST API 2020-06-30-preview](/rest/api/searchservice/) |
| Indexer van share point online | Gegevens bron van Indexeer functie | Nieuwe gegevens bron voor indexering op basis van Indexeer functie van share point-inhoud. | [Search REST API 2020-06-30-preview](/rest/api/searchservice/) |
| [**Vaardigheid van Azure Machine Learning (AML)**](cognitive-search-aml-skill.md) | AI-verrijking| Een nieuw vaardigheids type voor het integreren van een Afleidings eindpunt van Azure Machine Learning. Ga aan de slag met [deze zelfstudie](cognitive-search-tutorial-aml-custom-skill.md). | [Zoek REST API 2020-06-30-preview](/rest/api/searchservice/) of 2019-05-06-preview gebruiken. Ook beschikbaar in de portal, in het ontwerp van vaardig heden, aangenomen dat Cognitive Search en Azure ML-Services in hetzelfde abonnement zijn geïmplementeerd. |
| [**featuresMode-para meter**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | Relevantie (Score) | Uitbrei ding van relevantie score voor het toevoegen van Details: per veld vergelijk bare Score, per veld term frequentie en per veld aantal unieke tokens die overeenkomen. U kunt deze gegevens punten gebruiken in [aangepaste Score oplossingen](https://github.com/Azure-Samples/search-ranking-tutorial). | Voeg deze query parameter toe met behulp van [Zoek documenten (rest)](/rest/api/searchservice/preview-api/search-documents) met API-Version = 2020-06 -30-preview of 2019-05-06-preview. |
| [**Sessies voor fout opsporing**](cognitive-search-debug-session.md) | Portal, AI-verrijking (vaardig heden) | Een in-Session kwalificatieset-editor die wordt gebruikt om problemen met een vaardig heden te onderzoeken en op te lossen. Oplossingen die worden toegepast tijdens een foutopsporingssessie, kunnen worden opgeslagen in een vakkennisset in de service. | Alleen Portal, met behulp van koppelingen op de pagina overzicht voor het openen van een foutopsporingssessie. |
| [**Voorlopig verwijderen van systeem eigen BLOB**](search-howto-index-changed-deleted-blobs.md) | Indexeer functies, Azure-blobs| De Azure Blob Storage Indexeer functie in azure Cognitive Search herkent blobs met een voorlopig verwijderde status en verwijdert het bijbehorende Zoek document tijdens het indexeren. | Voeg deze configuratie-instelling toe met [Create Indexeer functie (rest)](/rest/api/searchservice/create-indexer) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**Zoek vaardigheid aangepaste entiteit**](cognitive-search-skill-custom-entity-lookup.md ) | AI-verrijking (vaardig heden) | Een cognitieve vaardigheid die zoekt naar tekst van een aangepaste, door de gebruiker gedefinieerde lijst met woorden en zinsdelen. Aan de hand van deze lijst worden alle documenten met overeenkomende entiteiten gelabeld. De vaardigheid ondersteunt ook het zoeken naar fuzzy overeenkomsten, waarmee overeenkomsten worden gevonden die vergelijkbaar zijn, maar niet helemaal exact. | Raadpleeg deze preview-vaardigheid met de editor van de vaardig heden in de portal of [Maak vaardig heden (rest)](/rest/api/searchservice/create-skillset) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**PERSOONLIJKE detectie vaardigheid**](cognitive-search-skill-pii-detection.md) | AI-verrijking (vaardig heden) | Een cognitieve vaardigheid die wordt gebruikt tijdens de indexering die persoonlijke gegevens ophaalt uit een invoer tekst en u de mogelijkheid biedt om deze op verschillende manieren te maskeren uit die tekst. | Raadpleeg deze preview-vaardigheid met de editor van de vaardig heden in de portal of [Maak vaardig heden (rest)](/rest/api/searchservice/create-skillset) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**Incrementele verrijking**](cognitive-search-incremental-indexing-conceptual.md) | Configuratie van de Indexeer functie| Voegt caching toe aan een verrijkings pijplijn, zodat u de bestaande uitvoer opnieuw kunt gebruiken als een gerichte wijziging, zoals een update van een vakkennisset of een ander object, de inhoud niet wijzigt. Caching is alleen van toepassing op verrijkte documenten die worden geproduceerd door een vaardig heden.| Voeg deze configuratie-instelling toe met [Create Indexeer functie (rest)](/rest/api/searchservice/create-indexer) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**Cosmos DB Indexer: MongoDB-API, Gremlin-API, Cassandra-API**](search-howto-index-cosmosdb.md) | Gegevens bron van Indexeer functie | Voor Cosmos DB is SQL API algemeen beschikbaar, maar MongoDB-, Gremlin-en Cassandra-Api's zijn in preview. | [Meld u eerst](https://aka.ms/azure-cognitive-search/indexer-preview) alleen voor Gremlin en Cassandra aan zodat ondersteuning voor uw abonnement op de back-end kan worden ingeschakeld. MongoDB gegevens bronnen kunnen worden geconfigureerd in de portal. Anders wordt de configuratie van de gegevens bron voor alle drie de Api's ondersteund met behulp van [Create Data Source (rest)](/rest/api/searchservice/create-data-source) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
|  [**Indexeer functie Azure Data Lake Storage Gen2**](search-howto-index-azure-data-lake-storage.md) | Gegevens bron van Indexeer functie | Inhoud en meta gegevens indexeren van Data Lake Storage Gen2.| [Registreren](https://aka.ms/azure-cognitive-search/indexer-preview) is vereist zodat ondersteuning kan worden ingeschakeld voor uw abonnement op de back-end. Toegang tot deze gegevens bron met behulp van [Create Data Source (rest)](/rest/api/searchservice/create-data-source) met API-Version = 2020-06 -30-preview of API-Version = 2019-05 -06-preview. |
| [**moreLikeThis**](search-more-like-this.md) | Query’s uitvoeren | Zoekt documenten die relevant zijn voor een specifiek document. Deze functie heeft eerdere voor beelden. | Voeg deze query parameter toe aan [Zoek documenten (rest)](/rest/api/searchservice/search-documents) -aanroepen met API-Version = 2020-06 -30-preview, 2019-05-06-preview, 2016-09-01-preview of 2017-11-11-preview. |

## <a name="how-to-call-a-preview-rest-api"></a>Een preview-REST API aanroepen

Met Azure Cognitive Search worden eerst experimentele functies vooraf vrijgegeven via de REST API en vervolgens via voorlopige versies van de .NET SDK.

Preview-functies zijn beschikbaar voor testen en experimenten, met als doel het verzamelen van feedback over het ontwerp en de implementatie van functies. Daarom kunnen preview-functies na verloop van tijd veranderen, mogelijk op manieren die achterwaartse compatibiliteit verstoren. Dit is in tegens telling tot functies in een GA-versie, die stabiel zijn en waarschijnlijk niet worden gewijzigd, met uitzonde ring van kleine neerwaarts compatibele oplossingen en uitbrei dingen. Daarnaast maken preview-functies niet altijd deel uit van een GA-versie.

Hoewel sommige preview-functies beschikbaar zijn in de portal en .NET SDK, bevat de REST API altijd preview-functies.

+ Voor zoek bewerkingen [**`2020-06-30-Preview`**](/rest/api/searchservice/index-preview) is de huidige preview-versie.

+ Voor beheer bewerkingen [**`2019-10-01-Preview`**](/rest/api/searchmanagement/index-2019-10-01-preview) is de huidige preview-versie.

Oudere previews zijn nog operationeel, maar worden na verloop van tijd verouderd. Als uw code aanroept `api-version=2019-05-06-Preview` of `api-version=2016-09-01-Preview` of `api-version=2017-11-11-Preview` , zijn deze aanroepen nog geldig. De nieuwste preview-versie wordt echter vernieuwd met verbeteringen.

De volgende voorbeeld syntaxis illustreert een aanroep van de preview API-versie.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

De Azure Cognitive Search-service is beschikbaar in meerdere versies. Zie [API-versies](search-api-versions.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Bekijk de referentie documentatie voor de zoek REST-voor beeld-API. Als u problemen ondervindt, vraagt u ons om hulp voor [stack overflow](https://stackoverflow.com/) of [neemt u contact op met de ondersteuning](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Naslag informatie over de zoek service REST API (preview-versie)](/rest/api/searchservice/index-preview)