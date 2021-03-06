---
title: Semantische zoekopdrachten
titleSuffix: Azure Cognitive Search
description: Meer informatie over de manier waarop Cognitive Search semantische Zoek modellen van een grondige leer bewerking van Bing kunt gebruiken om Zoek resultaten intuïtief te maken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/01/2021
ms.custom: references_regions
ms.openlocfilehash: 04300b8d148bb22bf585aa81481c475b347ad462
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222041"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Semantisch zoeken in azure Cognitive Search

> [!IMPORTANT]
> Semantische zoek opdracht bevindt zich in de open bare preview, die beschikbaar is via de preview-REST API en-Portal Preview-functies worden aangeboden als-is, onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)en zijn niet gegarandeerd dezelfde implementatie bij algemene Beschik baarheid. Deze functies zijn Factureerbaar. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

Semantisch zoeken is een verzameling query-gerelateerde mogelijkheden die semantische relevantie en taal memorandum toevoegen aan Zoek resultaten. Met *semantische classificatie* zoekt u naar de context en de onderlinge afhankelijkheid van de query. Met language Standing vindt u *ondertiteling* en *antwoorden* binnen uw inhoud die het overeenkomende document samenvatten of een vraag beantwoorden, die vervolgens kan worden weer gegeven op een pagina met zoek resultaten voor een productievere Zoek ervaring.

Geavanceerde vooraf getrainde modellen worden gebruikt voor samen vattingen en classificatie. Om de snelle prestaties te behouden die gebruikers van de zoek opdracht verwachten, worden semantische samen vatting en rang schikking alleen toegepast op de belangrijkste 50 resultaten, zoals wordt gescoord door het [standaard Score algoritme](index-similarity-and-scoring.md#similarity-ranking-algorithms)voor het vergelijken van de overeenkomst. Door deze resultaten te gebruiken als document verzameling, worden deze resultaten opnieuw gescoord op basis van de semantische sterkte van de overeenkomst.

De onderliggende technologie is van Bing en micro soft Research, en is geïntegreerd in de Cognitive Search-infra structuur als een invoeg toepassing. Zie How of the [Powering Azure Cognitive Search (micro soft Research Blog)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)voor meer informatie over de onderzoek-en AI-investeringen die een back-up van semantische zoek acties maken.

De volgende video biedt een overzicht van de mogelijkheden.

> [!VIDEO https://www.youtube.com/embed/yOf0WfVd_V0]

## <a name="components-and-workflow"></a>Onderdelen en werk stroom

Semantische zoek actie verbetert de nauw keurigheid en intrekken met de volgende mogelijkheden:

| Functie | Beschrijving |
|---------|-------------|
| [Spellingcontrole](speller-how-to-add.md) | Corrigeert type fouten voordat de query termen de zoek machine bereiken. |
| [Semantische classificatie](semantic-ranking.md) | Gebruikt de context of semantische betekenis om een nieuwe relevantie score te berekenen. |
| [Semantische bijschriften en hooglichten](semantic-how-to-query-request.md) | Zinnen en zinsdelen uit een document dat de inhoud het beste samenvatten, met meer informatie over de belangrijkste door gangen voor het eenvoudig scannen. Bijschriften waarin een resultaat wordt samenvatten, zijn handig wanneer afzonderlijke inhouds velden te dicht op de resultaten pagina zijn. Gemarkeerde tekst breidt de meest relevante termen en zinsdelen uit, zodat gebruikers snel kunnen bepalen waarom een overeenkomst relevant is. |
| [Semantische antwoorden](semantic-answers.md) | Een optionele en aanvullende substructuur die door een semantische query wordt geretourneerd. Het biedt een direct antwoord op een query die eruitziet als een vraag. |

### <a name="order-of-operations"></a>Volg orde van bewerkingen

Onderdelen van semantische zoek actie breiden de bestaande pijp lijn voor query uitvoering uit in beide richtingen. Als u de spelling correctie inschakelt, corrigeert de spelling controle op het begin [van de query](speller-how-to-add.md) , voordat de voor waarden de zoek machine bereiken.

:::image type="content" source="media/semantic-search-overview/semantic-workflow.png" alt-text="Semantische onderdelen in uitvoering van query's" border="true":::

Query's worden uitgevoerd zoals gebruikelijk, met het parseren van termen, analyses en scans over de omgedraaide indexen. De engine haalt documenten op met behulp van token matching en vergelijkt de resultaten met behulp van het [standaard scoreer algoritme](index-similarity-and-scoring.md#similarity-ranking-algorithms)voor het vergelijken van de Scores worden berekend op basis van de mate van taal kundige gelijkenis tussen query termen en overeenkomende termen in de index. Als u deze hebt gedefinieerd, worden Score profielen ook in deze fase toegepast. De resultaten worden vervolgens door gegeven aan het semantische Zoek subsysteem.

In de voorbereidings stap wordt de document verzameling geretourneerd door de eerste resultatenset op de zin en alinea niveau geanalyseerd om door gangen te zoeken die elk document samenvatten. In tegens telling tot zoeken met tref woorden, wordt met deze stap machine lezen en lees bewerkingen gebruikt om de inhoud te evalueren. Via deze fase van inhouds verwerking retourneert een semantische query [bijschriften](semantic-how-to-query-request.md) en [antwoorden](semantic-answers.md). Om ze te formuleren, wordt door semantische zoek opdracht gebruik gemaakt van de taal weergave om de sleutel door gang te extra heren en te markeren die het beste een resultaat oplevert. Als de zoek query een vraag-en-antwoord is aangevraagd, bevat de reactie ook een tekst die het beste beantwoordt aan de vraag, zoals wordt aangegeven door de zoek opdracht. 

Voor bijschriften en antwoorden wordt bestaande tekst in de formulering gebruikt. De semantische modellen vormen geen nieuwe zinnen of zinnen van de beschik bare inhoud en passen geen logica toe om nieuwe conclusies te ontvangen. Kortom, het systeem zal nooit inhoud retour neren die nog niet bestaat.

De resultaten worden vervolgens opnieuw beoordeeld op basis van de [conceptuele gelijkenis](semantic-ranking.md) van de query termen.

Als u semantische mogelijkheden in query's wilt gebruiken, moet u kleine wijzigingen aanbrengen in de [Zoek](semantic-how-to-query-request.md)opdracht, maar er is geen extra configuratie of opnieuw indexeren vereist.

## <a name="availability-and-pricing"></a>Beschik baarheid en prijzen

Semantische mogelijkheden zijn beschikbaar via registratie van de aanmelding, op zoek services die zijn gemaakt in een Standard [-](https://aka.ms/SemanticSearchPreviewSignup)laag (S1, S2, S3), die zich in een van deze regio's bevinden: Noord-Centraal VS, VS-West, VS-West 2, VS-Oost 2, Europa-noord, Europa-West. 

De spelling correctie is beschikbaar in dezelfde regio's, maar heeft geen laag beperkingen. Als u een bestaande service hebt die voldoet aan de criteria voor lagen en regio's, is aanmelden alleen vereist.

Tussen de preview-versie van 2 maart tot en met eind april, worden spelling correctie en semantische classificatie gratis aangeboden. Later in april worden de reken kosten voor het uitvoeren van deze functionaliteit een factureer bare gebeurtenis. De verwachte kosten zijn ongeveer USD $500/maand voor 250.000 query's. U vindt gedetailleerde informatie over de kosten die wordt beschreven op de [pagina Cognitive Search prijzen](https://azure.microsoft.com/pricing/details/search/) en in [schatting en beheer kosten](search-sku-manage-costs.md).

## <a name="next-steps"></a>Volgende stappen

Met een nieuw query type worden de rang schikking van de relevantie en de reactie structuren van semantisch zoeken ingeschakeld.

[Maak een semantische query](semantic-how-to-query-request.md) om aan de slag te gaan. Of Raadpleeg de volgende artikelen voor gerelateerde informatie.

+ [Spelling controle toevoegen aan query termen](speller-how-to-add.md)
+ [Een semantisch antwoord retour neren](semantic-answers.md)
+ [Semantische classificatie](semantic-ranking.md)
+ [Kennis Making met semantisch zoeken (blog bericht)](https://techcommunity.microsoft.com/t5/azure-ai/introducing-semantic-search-bringing-more-meaningful-results-to/ba-p/2175636)
+ [Herken bare inzichten vinden met behulp van semantische mogelijkheden (AI show video)](https://channel9.msdn.com/Shows/AI-Show/Find-meaningful-insights-using-semantic-capabilities-in-Azure-Cognitive-Search)