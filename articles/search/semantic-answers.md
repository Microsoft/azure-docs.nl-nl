---
title: Een semantisch antwoord retour neren
titleSuffix: Azure Cognitive Search
description: Hierin wordt de samen stelling van een semantisch antwoord beschreven en wordt uitgelegd hoe u antwoorden kunt verkrijgen op basis van een resultatenset.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 9bb62544887e0bc0269b98cd98fbf97fc477352f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722426"
---
# <a name="return-a-semantic-answer-in-azure-cognitive-search"></a>Een semantisch antwoord op Azure Cognitive Search retour neren

> [!IMPORTANT]
> Semantische zoek opdracht bevindt zich in de open bare preview, die alleen beschikbaar is via de preview-REST API. Preview-functies worden aangeboden als-is, onder [aanvullende gebruiks voorwaarden](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)en zijn niet gegarandeerd dezelfde implementatie bij algemene Beschik baarheid. Deze functies zijn Factureerbaar. Zie [Beschik baarheid en prijzen](semantic-search-overview.md#availability-and-pricing)voor meer informatie.

Bij het formuleren van een [semantische query](semantic-how-to-query-request.md)kunt u eventueel inhoud extra heren uit de bovenste documenten die de query rechtstreeks beantwoorden. Een of meer antwoorden kunnen worden opgenomen in het antwoord, dat u vervolgens kunt weer geven op een zoek pagina om de gebruikers ervaring van uw app te verbeteren.

In dit artikel leert u hoe u een semantisch antwoord kunt aanvragen, het antwoord kunt uitpakken en weet welke inhouds kenmerken het meest geschikt zijn voor het produceren van antwoorden van hoge kwaliteit.

## <a name="prerequisites"></a>Vereisten

Alle vereisten die van toepassing zijn op [semantische query's](semantic-how-to-query-request.md) , zijn ook van toepassing op antwoorden, met inbegrip van de servicelaag en de regio.

+ De query logica moet de semantische query parameters bevatten, plus de para meter ' Answers '. De vereiste para meters worden in dit artikel besproken.

+ Query teken reeksen die door de gebruiker worden ingevoerd, moeten worden geformuleerd in de taal die de kenmerken van een vraag heeft (wat, waar, wanneer, hoe).

+ Zoeken in documenten moet tekst bevatten die de kenmerken van een antwoord bevat en die tekst moet zijn opgenomen in een van de velden in searchFields. Als er bijvoorbeeld een query ' wat is een hash-tabel ', als geen van de searchFields gangen bevat met ' een hash-tabel is... ', is het niet waarschijnlijk dat een antwoord wordt geretourneerd.

## <a name="what-is-a-semantic-answer"></a>Wat is een semantisch antwoord?

Een semantisch antwoord is een substructuur van een [semantisch query-antwoord](semantic-how-to-query-request.md). Het bestaat uit een of meer Verbatim die worden door gegeven aan een zoek document dat is geformuleerd als antwoord op een query die eruitziet als een vraag. Voor een antwoord dat moet worden geretourneerd, moeten zinsdelen of zinnen bestaan in een zoek document dat de taal kenmerken van een antwoord heeft en de query zelf moet als een vraag worden beschouwd.

Cognitive Search een lees vaardigheids model voor machines gebruikt om het beste antwoord te kiezen. Het model produceert een aantal potentiële antwoorden van de beschik bare inhoud en wanneer het een hoog betrouwbaarheids niveau bereikt, wordt er een antwoord Voorst Ellen.

Antwoorden worden geretourneerd als een onafhankelijk object op het hoogste niveau in de nettolading van de query-reactie die u kunt weer geven op zoek pagina's, naast de resultaten van de zoek opdracht. Structureel is het een matrix element binnen het antwoord dat bestaat uit tekst, een document sleutel en een betrouwbaarheids Score.

<a name="query-params"></a>

## <a name="how-to-request-semantic-answers-in-a-query"></a>Semantische antwoorden in een query opvragen

Als u een semantisch antwoord wilt retour neren, moet de query de semantisch "query type", "queryLanguage", "searchFields" en de para meter "antwoorden" hebben. Het opgeven van de para meter antwoorden garandeert niet dat u een antwoord krijgt, maar de aanvraag moet deze para meter bevatten als de antwoord verwerking helemaal moet worden aangeroepen.

De para meter ' searchFields ' is van cruciaal belang voor het retour neren van een antwoord van hoge kwaliteit, zowel wat betreft inhoud als volg orde (zie hieronder). 

```json
{
    "search": "how do clouds form",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "title,locations,content",
    "answers": "extractive|count-3",
    "count": "true"
}
```

+ Een query reeks mag niet null zijn en moet worden geformuleerd als vraag. In deze preview-versie moet het ' query type ' en ' queryLanguage ' precies worden ingesteld zoals in het voor beeld wordt weer gegeven.

+ De para meter ' searchFields ' bepaalt welke teken reeks velden tokens aan het extractie model bieden. In dezelfde velden die bijschriften produceren, worden ook antwoorden gegenereerd. Zie [set searchFields](semantic-how-to-query-request.md#searchfields)voor nauw keurige richt lijnen voor het instellen van dit veld zodat het geschikt is voor bijschriften en antwoorden. 

+ Voor "antwoorden" is de para meter constructie `"answers": "extractive"` , waarbij het standaard aantal geretourneerde antwoorden één is. U kunt het aantal antwoorden verhogen door een aantal toe te voegen, zoals wordt weer gegeven in het bovenstaande voor beeld, tot een maximum van vijf.  Of u meer dan één antwoord nodig hebt, is afhankelijk van de gebruikers ervaring van uw app en hoe u de resultaten wilt weer geven.

## <a name="deconstruct-an-answer-from-the-response"></a>Een antwoord uit het antwoord ontbouwen

De antwoorden worden in de @search.answers matrix weer gegeven, die als eerste in het antwoord wordt vermeld. Als een antwoord onbepaald is, wordt het antwoord weer gegeven als `"@search.answers": []` . Wanneer u een pagina met zoek resultaten ontwerpt die antwoorden bevat, moet u ervoor zorgen dat er geen antwoorden worden gevonden.

In @search.answers is de ' sleutel ' de document sleutel of id van de overeenkomst. Op basis van een document sleutel kunt u met behulp van de [opzoek document](/rest/api/searchservice/lookup-document) -API alle onderdelen van het zoek document ophalen die u wilt opnemen op de zoek pagina of een detail pagina.

Zowel "tekst" als "hooglichten" bieden identieke inhoud, zowel in tekst zonder opmaak als met hooglichten. Standaard worden hooglichten opgemaakt als `<em>` , die u kunt overschrijven met de bestaande highlightPreTag-en highlightPostTag-para meters. Zoals elders vermeld, is de economische realiteit van een antwoord Verbatim inhoud uit een zoek document. Het uitpakkende model zoekt naar eigenschappen van een antwoord om de juiste inhoud te vinden, maar stelt geen nieuwe taal in het antwoord in.

De ' Score ' is een betrouw bare Score die de sterkte van het antwoord weerspiegelt. Als het antwoord meerdere antwoorden bevat, wordt deze score gebruikt om de volg orde te bepalen. De belangrijkste antwoorden en de bovenste bijschriften kunnen worden afgeleid van verschillende Zoek documenten, waarbij het bovenste antwoord afkomstig is uit het ene document en het bovenste bijschrift van een andere, maar in het algemeen ziet u dezelfde documenten in de bovenste posities binnen elke matrix.

Antwoorden worden gevolgd door de matrix ' waarde ', die altijd scores, bijschriften en velden bevat die standaard kunnen worden opgehaald. Als u de Select-para meter hebt opgegeven, is de matrix ' waarde ' beperkt tot de velden die u hebt opgegeven. Zie [een semantische query maken](semantic-how-to-query-request.md)voor meer informatie over items in het antwoord.

Op basis van de query ' How do Clouds Form ', wordt het volgende antwoord geretourneerd in het antwoord:

```json
{
    "@search.answers": [
        {
            "key": "4123",
            "text": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form where air is ascending (over land in this case),   but not where it is descending (over the river).",
            "highlights": "Sunlight heats the land all day, warming that moist air and causing it to rise high into the   atmosphere until it cools and condenses into water droplets. Clouds generally form<em> where air is ascending</em> (over land in this case),   but not where it is<em> descending</em> (over the river).",
            "score": 0.94639826
        }
    ],
    "value": [
        {
            "@search.score": 0.5479723,
            "@search.rerankerScore": 1.0321671911515296,
            "@search.captions": [
                {
                    "text": "Like all clouds, it forms when the air reaches its dew point—the temperature at which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley fog, which is common in the Pacific Northwest of North America.",
                    "highlights": "Like all<em> clouds</em>, it<em> forms</em> when the air reaches its dew point—the temperature at    which an air mass is cool enough for its water vapor to condense into liquid droplets. This false-color image shows valley<em> fog</em>, which is common in the Pacific Northwest of North America."
                }
            ],
            "title": "Earth Atmosphere",
            "content": "Fog is essentially a cloud lying on the ground. Like all clouds, it forms when the air reaches its dew point—the temperature at  \n\nwhich an air mass is cool enough for its water vapor to condense into liquid droplets.\n\nThis false-color image shows valley fog, which is common in the Pacific Northwest of North America. On clear winter nights, the \n\nground and overlying air cool off rapidly, especially at high elevations. Cold air is denser than warm air, and it sinks down into the \n\nvalleys. The moist air in the valleys gets chilled to its dew point, and fog forms. If undisturbed by winds, such fog may persist for \n\ndays. The Terra satellite captured this image of foggy valleys northeast of Vancouver in February 2010.\n\n\n",
            "locations": [
                "Pacific Northwest",
                "North America",
                "Vancouver"
            ]
        }
```

## <a name="tips-for-producing-high-quality-answers"></a>Tips voor het produceren van antwoorden van hoge kwaliteit

Voor de beste resultaten geeft u semantische antwoorden op een document verzameling met de volgende kenmerken:

+ "searchFields" moet velden bevatten die voldoende tekst bieden waarin een antwoord waarschijnlijk wordt gevonden. Alleen Verbatim-tekst uit een document kan als antwoord worden weer gegeven.

+ query teken reeksen mogen niet Null (Search = `*` ) zijn en de teken reeks moet de kenmerken van een vraag hebben, in plaats van een zoek opdracht op tref woorden (een opeenvolgende lijst met wille keurige termen of woord groepen). Als de query reeks niet het antwoord lijkt, wordt de antwoord verwerking overgeslagen, zelfs als de aanvraag "antwoorden" als query parameter specificeert.

+ Semantische extractie en samen vatting hebben beperkingen ten opzichte van het aantal tokens per document dat tijdig kan worden geanalyseerd. Als u grote documenten hebt die op honderden pagina's worden uitgevoerd, moet u de inhoud eerst in kleinere documenten opdelen.

## <a name="next-steps"></a>Volgende stappen

+ [Overzicht van semantisch zoeken](semantic-search-overview.md)
+ [Algoritme voor semantische classificatie](semantic-ranking.md)
+ [Classificatie algoritme voor gelijkenis](index-ranking-similarity.md)
+ [Een semantische query maken](semantic-how-to-query-request.md)