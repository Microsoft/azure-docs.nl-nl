---
title: Semantische classificatie
titleSuffix: Azure Cognitive Search
description: Meer informatie over hoe het algoritme voor semantische classificatie werkt in azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: bf311eb2b2d0ff7a9c17380d2e384bc05c6f05f3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562032"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Semantische classificatie in azure Cognitive Search

> [!IMPORTANT]
> Semantische zoek functies bevinden zich in de open bare preview-versie, die beschikbaar is via de preview-REST API en-Portal. Preview-functies worden aangeboden als-is, onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)en zijn niet gegarandeerd dezelfde implementatie bij algemene Beschik baarheid. Deze functies zijn Factureerbaar. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

Semantische classificatie is een uitbrei ding van de pijp lijn voor het uitvoeren van query's waarmee de nauw keurigheid wordt verbeterd door de meest voorkomende treffers van een eerste resultatenset te herordenen. Semantische classificatie wordt ondersteund door grote transformator netwerken, getraind voor het vastleggen van de semantische betekenis van query termen, in tegens telling tot taal kundige overeenkomsten met tref woorden. In tegens telling tot het [standaard classificatie algoritme voor gelijkenis](index-ranking-similarity.md), gebruikt de semantische rang orde de context en betekenis van woorden om de relevantie te bepalen.

Semantische classificatie is zowel resource als tijd intensief. Voor het volt ooien van de verwerking binnen de verwachte latentie van een query bewerking, worden de invoer van de semantische rang orde geconsolideerd en beperkt zodat de onderliggende samenvattings-en herordenings stappen zo snel mogelijk kunnen worden voltooid.

## <a name="pre-processing"></a>Vóór verwerking

Voordat de score voor relevantie wordt genoteerd, moet de inhoud worden gereduceerd tot een beheersbaar aantal ingangen dat efficiënt kan worden verwerkt door de semantische rangorde.

1. De eerste keer dat de inhoud wordt verkleind, begint de oorspronkelijke resultaten die worden geretourneerd door het standaard [classificatie algoritme](index-ranking-similarity.md) voor het vergelijken van de overeenkomst. Voor elke gewenste query kunnen de resultaten uit een aantal documenten bestaan, tot de maximum limiet van 1.000. Omdat de verwerking van een groot aantal overeenkomsten te lang duurt, wordt alleen de bovenste 50-voortgang naar de semantische classificatie genoteerd.

   Ongeacht het aantal documenten, of een van de twee of 50, wordt met de eerste resultatenset de eerste iteratie van het document verzameling voor semantische classificatie ingesteld.

1. Vervolgens wordt de inhoud van elk veld in ' searchFields ' geëxtraheerd en gecombineerd tot een lange teken reeks in de verzameling.

1. Na het samen voegen van de teken reeks worden alle teken reeksen die overmatig lang zijn, afgekapt om ervoor te zorgen dat de totale lengte voldoet aan de invoer vereisten van de stap samen vatting.

   Daarom is het belang rijk om beknopte velden eerst in ' searchFields ' te plaatsen, zodat ze in de teken reeks zijn opgenomen. Als u zeer grote documenten met tekst zware velden hebt, hoeft u niets te doen nadat de maximum limiet is genegeerd.

Elk document wordt nu vertegenwoordigd door één lange teken reeks.

> [!NOTE]
> De teken reeks bestaat uit tokens, geen tekens of woorden. Tokenisatie wordt bepaald door de analyse toewijzing voor Doorzoek bare velden. Als u gebruikmaakt van gespecialiseerde analyse, zoals nGram of EdgeNGram, kunt u dat veld het beste uitsluiten van searchFields. Voor inzichten in hoe teken reeksen worden getokend, kunt u de token uitvoer van een Analyzer controleren met behulp van de [test analyzer rest API](/rest/api/searchservice/test-analyzer).

## <a name="extraction"></a>Extractie

Na het verminderen van de teken reeks is het nu mogelijk om de gereduceerde invoer door te geven via machine Lees vaardigheid en taal weergave modellen om te bepalen welke zinnen en zinsdelen het document het beste samenvatten ten opzichte van de query. In deze fase wordt inhoud geëxtraheerd uit de teken reeks die naar de semantische rang orde gaat.

Invoer voor samen vatting is de lange teken reeksen die voor elk document worden opgehaald in de voorbereidings fase. Vanuit elke teken reeks vindt u in het overzichts model een door gang die de meest representatief is. Deze passage vormt ook een [semantisch bijschrift](semantic-how-to-query-request.md) voor het document. Elk bijschrift is beschikbaar in een onbewerkte tekst versie en een markeerstift versie en is vaak minder dan 200 woorden per document.

Er wordt ook een [semantisch antwoord](semantic-answers.md) gegeven als u de para meter ' antwoorden ' hebt opgegeven, als de query is opgenomen als een vraag en als er een passeren kan worden gevonden in de lange teken reeks die waarschijnlijk een antwoord op de vraag vormt.

## <a name="semantic-ranking"></a>Semantische classificatie

1. Bijschriften worden geëvalueerd op basis van conceptuele en semantische relevantie, ten opzichte van de opgegeven query.

   Het volgende diagram geeft een illustratie van wat ' semantische relevantie ' betekent. Houd rekening met het begrip "kapitaal", dat kan worden gebruikt in de context van Finance, Law, geografie of grammatica. Als een query termen uit dezelfde vector ruimte bevat (bijvoorbeeld ' kapitaal ' en ' investering '), krijgt een document dat ook tokens in hetzelfde cluster bevat, een Score van meer dan een die niet.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vector weergave voor context" border="true":::

1. De @search.rerankerScore wordt toegewezen aan elk document op basis van de semantische relevantie van het bijschrift.

1. Nadat alle documenten zijn gescoord, worden ze weer gegeven in aflopende volg orde gesorteerd op Score en opgenomen in de nettolading van het query-antwoord. De payload bevat antwoorden, tekst zonder opmaak en gemarkeerde bijschriften, en alle velden die u hebt gemarkeerd als ophaalbaar of opgegeven in een component SELECT.

## <a name="next-steps"></a>Volgende stappen

Semantische classificatie wordt aangeboden voor de standaard lagen in bepaalde regio's. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie over beschik baarheid en registreren. Met een nieuw query type worden de classificatie-en reactie structuren van semantisch zoeken ingeschakeld. [Maak een semantische query](semantic-how-to-query-request.md)om aan de slag te gaan.

U kunt ook de volgende artikelen over de standaard volgorde controleren. De semantische rang orde is afhankelijk van de rang schikking van de gelijkenis om de oorspronkelijke resultaten te retour neren. Meer informatie over het uitvoeren van query's en classificatie krijgt u een breed inzicht in hoe het hele proces werkt.

+ [Zoeken in volledige tekst in azure Cognitive Search](search-lucene-query-architecture.md)
+ [Gelijkenis en score in azure Cognitive Search](index-similarity-and-scoring.md)
+ [Analyse functies voor tekst verwerking in azure Cognitive Search](search-analyzers.md)