---
title: Semantische zoekopdrachten
titleSuffix: Azure Cognitive Search
description: Meer informatie over de manier waarop Cognitive Search semantische Zoek modellen van een grondige leer bewerking van Bing kunt gebruiken om Zoek resultaten intuïtief te maken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: e9cbb7daf61397064bd79f30d851d96fdf63f5a0
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203229"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Semantisch zoeken in azure Cognitive Search

> [!IMPORTANT]
> Semantische zoek functies bevinden zich in de open bare preview-versie, die alleen beschikbaar is via de preview-REST API. Preview-functies worden onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)aangeboden.

Semantisch zoeken is een verzameling query-gerelateerde functies die een hogere, meer natuurlijke query-ervaring ondersteunen. Functies omvatten semantische wijzigingen in de zoek resultaten, evenals bijschriften en antwoorden die met semantische markering worden gegenereerd. De bovenste 50 resultaten die zijn geretourneerd door de [Zoek machine voor zoeken in volledige tekst](search-lucene-query-architecture.md) , worden opnieuw gerangschikt om de meest relevante overeenkomsten te vinden.

De onderliggende technologie maakt gebruik van de investeringen van Bing en micro soft Research, en is geïntegreerd in de Cognitive Search-infra structuur. Als u deze wilt gebruiken, moet u kleine wijzigingen in de zoek aanvraag, maar er is geen extra configuratie of reindexering vereist.

Open bare preview-functies zijn:

+ Semantisch classificatie model dat de resultaten uitscoort op basis van de context of semantische betekenis van zoek query termen
+ Semantische bijschriften die relevante door gangen markeren
+ Semantische antwoorden op de query, ook geformuleerd op basis van resultaten
+ Spelling controle waarmee type fouten worden gecorrigeerd voordat de query termen de zoek machine bereiken

## <a name="semantic-search-architecture"></a>Semantische Zoek architectuur

Componenten van semantische Zoek opdrachten worden gelaagd boven op de bestaande pijp lijn voor het uitvoeren van query's. Spelling correctie (niet weer gegeven in het diagram) verbetert de intrekking door type fouten in afzonderlijke query termen te corrigeren. Nadat alle parsering en analyse zijn voltooid, haalt de zoek machine de documenten op die overeenkomen met de query en worden ze gescoord met het [standaard Score-algoritme](index-similarity-and-scoring.md#similarity-ranking-algorithms), ofwel BM25 of klassiek, afhankelijk van het moment waarop de service is gemaakt. Score profielen worden ook in deze fase toegepast. 

Nadat de beste 50-overeenkomsten zijn ontvangen, evalueert het [semantische classificatie algoritme](semantic-how-to-query-response.md) de document verzameling opnieuw. Resultaten kunnen meer dan 50 overeenkomsten bevatten, maar alleen de eerste 50 wordt opnieuw gerangt. Voor de rang schikking gebruikt het algoritme zowel machine learning als overdrachts leren om de documenten opnieuw te scoren op basis van de bedoeling van de query.

Om bijschriften en antwoorden te maken, worden de taal weergave modellen gebruikt. Binnen een bijschrift gebruikt het algoritme een taal model dat is ontwikkeld door Bing om ondertiteling uit te pakken met hooglichten die de query resultaten het beste samenvatten. Als de zoek query een vraag is en er antwoorden worden gevraagd, wordt in een vergelijkbaar taal model tekst weer gegeven die het beste beantwoordt aan de vraag, zoals wordt aangegeven door de zoek opdracht.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Semantische onderdelen in een query pijplijn" border="true":::

## <a name="availability-and-pricing"></a>Beschik baarheid en prijzen

Semantische classificatie is beschikbaar via registratie van de aanmelding, op zoek services die zijn gemaakt in een Standard [-](https://aka.ms/SemanticSearchPreviewSignup)laag (S1, S2, S3), die zich in een van deze regio's bevindt: Noord-Centraal VS, VS-West, VS-West 2, VS-Oost 2, Europa-noord, Europa-West. Een bestaande zoek service op S1 of hoger in de vermelde regio's komt in aanmerking voor de preview (u hoeft geen nieuwe service te maken).

De spelling correctie is beschikbaar in dezelfde regio's, maar heeft geen laag beperkingen en er is geen registratie vereiste. 

Tussen de preview-versie van 2 maart tot en met 1 april worden de spelling correctie en de semantische classificatie gratis aangeboden. Na 1 april worden de reken kosten voor het uitvoeren van deze functionaliteit een factureer bare gebeurtenis. De verwachte kosten zijn ongeveer USD $500/maand voor 250.000 query's. U vindt gedetailleerde informatie over de kosten die wordt beschreven op de [pagina Cognitive Search prijzen](https://azure.microsoft.com/pricing/details/search/) en in [schatting en beheer kosten](search-sku-manage-costs.md).

## <a name="next-steps"></a>Volgende stappen

Met een nieuw query type worden de rang schikking van de relevantie en de reactie structuren van semantisch zoeken ingeschakeld. [Maak een semantische query](semantic-how-to-query-request.md) om aan de slag te gaan. Of raadpleeg een van de volgende artikelen voor gerelateerde informatie.

+ [Spelling controle toevoegen aan query termen](speller-how-to-add.md)
+ [Semantische classificatie en reacties](semantic-how-to-query-response.md)