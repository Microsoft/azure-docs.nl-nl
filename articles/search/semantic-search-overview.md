---
title: Semantische zoekopdrachten
titleSuffix: Azure Cognitive Search
description: Meer informatie over de manier waarop Cognitive Search semantische Zoek modellen van een grondige leer bewerking van Bing kunt gebruiken om Zoek resultaten intuïtief te maken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/05/2021
ms.custom: references_regions
ms.openlocfilehash: 19b7f9bc19bec989e524dce7172037025e2fe4fd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102432976"
---
# <a name="semantic-search-in-azure-cognitive-search"></a>Semantisch zoeken in azure Cognitive Search

> [!IMPORTANT]
> Semantische zoek functies bevinden zich in de open bare preview-versie, die alleen beschikbaar is via de preview-REST API. Preview-functies worden onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)aangeboden.

Semantisch zoeken is een verzameling query-gerelateerde functies die een hogere, meer natuurlijke query-ervaring ondersteunen. Functies omvatten semantische wijzigingen in de zoek resultaten, evenals bijschriften en antwoorden die met semantische markering worden gegenereerd. De bovenste 50 resultaten die zijn geretourneerd door de [Zoek machine voor zoeken in volledige tekst](search-lucene-query-architecture.md) , worden opnieuw gerangschikt om de meest relevante overeenkomsten te vinden.

De onderliggende technologie is van Bing en micro soft Research, en is geïntegreerd in de Cognitive Search-infra structuur. Zie How of the [Powering Azure Cognitive Search (micro soft Research Blog)](https://www.microsoft.com/research/blog/the-science-behind-semantic-search-how-ai-from-bing-is-powering-azure-cognitive-search/)voor meer informatie over de onderzoek-en AI-investeringen die een back-up van semantische zoek acties maken.

Als u semantisch zoeken in query's wilt gebruiken, moet u kleine wijzigingen aanbrengen in de zoek opdracht, maar er is geen extra configuratie of opnieuw indexeren vereist.

Open bare preview-functies zijn:

+ Semantisch classificatie model dat gebruikmaakt van de context of semantische betekenis voor het berekenen van een relevantie score
+ Semantische bijschriften waarmee de sleutel wordt gesamenvat van een resultaat voor eenvoudig scannen
+ Semantische antwoorden op de query, als de query een vraag is
+ Semantische hooglichten die de nadruk leggen op belang rijke frasen en termen
+ Spelling controle waarmee type fouten worden gecorrigeerd voordat de query termen de zoek machine bereiken

## <a name="availability-and-pricing"></a>Beschik baarheid en prijzen

Semantische classificatie is beschikbaar via registratie van de aanmelding, op zoek services die zijn gemaakt in een Standard [-](https://aka.ms/SemanticSearchPreviewSignup)laag (S1, S2, S3), die zich in een van deze regio's bevindt: Noord-Centraal VS, VS-West, VS-West 2, VS-Oost 2, Europa-noord, Europa-West. De spelling correctie is beschikbaar in dezelfde regio's, maar heeft geen laag beperkingen. Als u een bestaande service hebt die voldoet aan de criteria voor lagen en regio's, is aanmelden alleen vereist.

Tussen de preview-versie van 2 maart tot en met 1 april worden de spelling correctie en de semantische classificatie gratis aangeboden. Na 1 april worden de reken kosten voor het uitvoeren van deze functionaliteit een factureer bare gebeurtenis. De verwachte kosten zijn ongeveer USD $500/maand voor 250.000 query's. U vindt gedetailleerde informatie over de kosten die wordt beschreven op de [pagina Cognitive Search prijzen](https://azure.microsoft.com/pricing/details/search/) en in [schatting en beheer kosten](search-sku-manage-costs.md).

## <a name="semantic-search-architecture"></a>Semantische Zoek architectuur

Componenten van semantische Zoek opdrachten worden gelaagd boven op de bestaande pijp lijn voor het uitvoeren van query's. Spelling correctie (niet weer gegeven in het diagram) verbetert de intrekking door type fouten in afzonderlijke query termen te corrigeren. Nadat het parseren en analyseren is voltooid, haalt de zoek machine de documenten op die overeenkomen met de query en worden ze gescoord met het [standaard Score ALGORITME](index-similarity-and-scoring.md#similarity-ranking-algorithms)BM25 of Classic, afhankelijk van het moment waarop de service is gemaakt. Score profielen worden ook in deze fase toegepast.

Nadat de beste 50 overeenkomsten zijn ontvangen, evalueert het [semantische classificatie model](semantic-how-to-query-response.md) de document verzameling opnieuw. Resultaten kunnen meer dan 50 overeenkomsten bevatten, maar alleen de eerste 50 wordt opnieuw gerangt. Voor de rang schikking gebruikt het model zowel machine learning als overboeking Learning om de documenten opnieuw te scoren op basis van de bedoeling van de query.

Als u bijschriften en antwoorden wilt maken, wordt in semantische zoek functie gebruik gemaakt van de taal weergave voor het uitpakken en markeren van de sleutel door Voer die het beste een resultaat oplevert. Als de zoek query een vraag is en er antwoorden worden gevraagd, bevat het antwoord een tekst die het beste beantwoordt aan de vraag, zoals wordt aangegeven door de zoek opdracht.

:::image type="content" source="media/semantic-search-overview/semantic-query-architecture.png" alt-text="Semantische onderdelen in een query pijplijn" border="true":::

## <a name="next-steps"></a>Volgende stappen

Met een nieuw query type worden de rang schikking van de relevantie en de reactie structuren van semantisch zoeken ingeschakeld.

[Maak een semantische query](semantic-how-to-query-request.md) om aan de slag te gaan. Of raadpleeg een van de volgende artikelen voor gerelateerde informatie.

+ [Spelling controle toevoegen aan query termen](speller-how-to-add.md)
+ [Semantische classificatie en Reacties (antwoorden en bijschriften)](semantic-how-to-query-response.md)