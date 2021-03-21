---
title: Semantische classificatie
titleSuffix: Azure Cognitive Search
description: Meer informatie over hoe het algoritme voor semantische classificatie werkt in azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: bb65a53f1ba6e97a39bd0c0170c5c41da38aee8b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720505"
---
# <a name="semantic-ranking-in-azure-cognitive-search"></a>Semantische classificatie in azure Cognitive Search

> [!IMPORTANT]
> Semantische zoek functies bevinden zich in de open bare preview-versie, die alleen beschikbaar is via de preview-REST API. Preview-functies worden aangeboden als-is, onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)en zijn niet gegarandeerd dezelfde implementatie bij algemene Beschik baarheid. Deze functies zijn Factureerbaar. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

Semantische classificatie is een uitbrei ding van de pijp lijn voor het uitvoeren van query's die de nauw keurigheid verbetert en terughalen door de beste overeenkomsten van een eerste resultatenset te herordenen. Semantische classificatie wordt ondersteund door de geavanceerde machine Lees vaardigheids modellen, getraind voor query's in natuurlijke taal, in tegens telling tot taal kundige overeenkomsten met tref woorden. In tegens telling tot het [standaard classificatie algoritme voor gelijkenis](index-ranking-similarity.md), gebruikt de semantische rang orde de context en betekenis van woorden om de relevantie te bepalen.

Semantische classificatie is zowel resource als tijd intensief. Om de verwerking binnen de verwachte latentie van een query bewerking te volt ooien, worden de invoer geconsolideerd en vereenvoudigd, zodat samen vatting en analyse zo snel mogelijk kunnen worden voltooid.

## <a name="preparation-for-semantic-ranking"></a>Voor bereiding voor semantische classificatie

Voordat de score voor relevantie wordt genoteerd, moet de inhoud worden gereduceerd tot een aantal para meters die efficiënt kunnen worden verwerkt door de semantische rangorde. Inhouds reductie omvat de volgende reeks stappen.

1. Inhouds reductie begint met het gebruik van de oorspronkelijke resultaten die worden geretourneerd door het standaard [classificatie algoritme](index-ranking-similarity.md) voor het vergelijken van de zoek opdracht. Zoek resultaten kunnen Maxi maal 1.000 overeenkomsten omvatten, maar semantische classificatie verwerkt alleen de Top 50. 

   Op basis van de query kunnen initiële resultaten veel kleiner zijn dan 50, afhankelijk van het aantal treffers dat is gevonden. Ongeacht het aantal documenten, is de initiële resultatenset het document verzameling voor semantische classificatie.

1. In de verzameling van het document wordt de inhoud van elk veld in ' searchFields ' geëxtraheerd en gecombineerd tot een lange teken reeks.

1. Alle teken reeksen die buitensporig lang zijn, worden bijgesneden om ervoor te zorgen dat de totale lengte voldoet aan de invoer vereisten van het samenvattings model. Daarom is het belang rijk om beknopte velden eerst in ' searchFields ' te plaatsen, zodat ze in de teken reeks zijn opgenomen. Als u zeer grote documenten met tekst zware velden hebt, hoeft u niets te doen nadat de maximum limiet is genegeerd.

Elk document wordt nu vertegenwoordigd door één lange teken reeks.

> [!NOTE]
> Parameter invoer voor de modellen zijn tokens die geen tekens of woorden zijn. Tokenisatie wordt bepaald door de analyse toewijzing voor Doorzoek bare velden. Voor inzichten in hoe teken reeksen worden getokend, kunt u de token uitvoer van een Analyzer controleren met behulp van de [test analyzer rest API](/rest/api/searchservice/test-analyzer).
>
> In deze preview-versie kunnen lange teken reeksen een maximale grootte hebben van 8.000-tokens. Als de zoek opdracht niet in staat is om een verwacht antwoord van diep binnen een document te leveren, kunt u weten waarom inhoud moet worden inge sneden. 

## <a name="summarization"></a>Samenvatting

Na het verminderen van de teken reeks is het nu mogelijk om de para meters door te geven via Lees-en taal weergave van de machine om te bepalen welke zinnen en zinsdelen het model het beste samenvatten ten opzichte van de query.

Invoer voor samen vatting is de lange teken reeks van de voorbereidings fase. Vanaf die invoer evalueert het samenvattings model de inhoud om te zoeken naar gangen die de meeste representatief zijn.

Output is een [semantisch bijschrift](semantic-how-to-query-request.md), in tekst zonder opmaak en met hooglichten. Het bijschrift is kleiner dan de lange teken reeks, meestal minder dan 200 woorden per document, en wordt beschouwd als de meeste vertegenwoordiger van het document. 

Er wordt ook een [semantisch antwoord](semantic-answers.md) gegeven als u de para meter ' antwoorden ' hebt opgegeven, als de query is opgenomen als een vraag, en als er een door tocht kan worden gevonden in de lange teken reeks die eruitziet als een plausible-antwoord op de vraag.

## <a name="scoring-and-ranking"></a>Score en rang schikking

Op dit moment hebt u nu bijschriften voor elk document. De bijschriften worden geëvalueerd voor relevantie voor de query.

1. Scores worden bepaald door elk bijschrift te evalueren voor conceptuele en semantische relevantie, ten opzichte van de opgegeven query.

   Het volgende diagram geeft een illustratie van wat ' semantische relevantie ' betekent. Houd rekening met het begrip "kapitaal", dat kan worden gebruikt in de context van Finance, Law, geografie of grammatica. Als een query termen uit dezelfde vector ruimte bevat (bijvoorbeeld ' kapitaal ' en ' investering '), krijgt een document dat ook tokens in hetzelfde cluster bevat, een Score van meer dan een die niet.

   :::image type="content" source="media/semantic-search-overview/semantic-vector-representation.png" alt-text="Vector weergave voor context" border="true":::

1. De uitvoer van deze fase is een @search.rerankerScore toegewezen aan elk document. Zodra alle documenten zijn gescoord, worden ze weer gegeven in aflopende volg orde en opgenomen in de query reactie payload. De payload bevat antwoorden, tekst zonder opmaak en gemarkeerde bijschriften, en alle velden die u hebt gemarkeerd als ophaalbaar of opgegeven in een component SELECT.

## <a name="next-steps"></a>Volgende stappen

Semantische classificatie wordt aangeboden voor de standaard lagen in bepaalde regio's. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie over beschik bare en registratie. Met een nieuw query type worden de rang schikking van de relevantie en de reactie structuren van semantisch zoeken ingeschakeld. [Maak een semantische query](semantic-how-to-query-request.md)om aan de slag te gaan.

U kunt ook de volgende artikelen over de standaard volgorde controleren. De semantische rang orde is afhankelijk van de rang schikking van de gelijkenis om de oorspronkelijke resultaten te retour neren. Meer informatie over het uitvoeren van query's en classificatie krijgt u een breed inzicht in hoe het hele proces werkt.

+ [Zoeken in volledige tekst in azure Cognitive Search](search-lucene-query-architecture.md)
+ [Gelijkenis en score in azure Cognitive Search](index-similarity-and-scoring.md)
+ [Analyse functies voor tekst verwerking in azure Cognitive Search](search-analyzers.md)