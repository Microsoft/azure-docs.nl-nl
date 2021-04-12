---
title: Prestatiebenchmarks
titleSuffix: Azure Cognitive Search
description: Meer informatie over de prestaties van Azure Cognitive Search via verschillende benchmarks voor prestaties
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 9f4473d6c8a584bf60e5c8fe2d69d6a56a55e71d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108215"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Benchmarks voor Azure Cognitive Search-prestaties

De prestaties van Azure Cognitive Search zijn afhankelijk van een [aantal factoren](search-performance-tips.md) , zoals de grootte van de zoek service en de typen query's die u verzendt. Om te helpen bij het schatten van de grootte van de zoek service die nodig is voor uw workload, hebben we verschillende benchmarks uitgevoerd om de prestaties voor verschillende Zoek Services en configuraties te documenteren. Deze benchmarks bieden geen enkele manier om een bepaald prestatie niveau van uw service te garanderen, maar u kunt wel een idee krijgen van de prestaties die u kunt verwachten.

We hebben een reeks verschillende use cases uitgevoerd met benchmarks voor twee belang rijke scenario's:

* **Zoek opdracht voor e-commerce** : deze bench Mark emuleert een echt E-commerce scenario en is gebaseerd op de E-commerce- [CDON](https://cdon.com)van het Scandinavische bedrijf.
* **Zoeken in documenten** : dit scenario bestaat uit het zoeken naar tref woorden via volledige-tekst documenten vanuit [semantische Scholar](http://s2-public-api-prod.us-west-2.elasticbeanstalk.com/corpus/download/). Hiermee wordt een typische Zoek oplossing voor documenten geëmuleerd.

Hoewel deze scenario's verschillende use cases weer spie gelen, is elk scenario anders, zodat u altijd de prestaties van uw afzonderlijke workload kunt testen. We hebben een [oplossing voor prestatie tests gepubliceerd met behulp van jmeter](https://github.com/Azure-Samples/azure-search-performance-testing) , zodat u vergelijk bare tests kunt uitvoeren op uw eigen service.

## <a name="testing-methodology"></a>Test methodologie

Om de prestaties van de Azure-Cognitive Search te benchmarksen, hebben we tests uitgevoerd voor twee verschillende scenario's op verschillende lagen en combi Naties van replica/partities.

Voor het maken van deze benchmarks is de volgende methodologie gebruikt:

1. De test begint bij `X` query's per seconde (qps) gedurende 180 seconden. Dit was meestal 5 of 10 QPS.
2. QPS wordt vervolgens verhoogd met `X` een andere 180 seconden.
3. Elke 180 seconden wordt de test verhoogd met `X` qps totdat de gemiddelde latentie groter is dan 1000 MS of minder dan 99% van de query's geslaagd.

In de volgende grafiek ziet u een visueel voor beeld van de belasting van de test query:

![Voor beeld testen](./media/performance-benchmarks/example-test.png)

Elk scenario gebruikt ten minste 10.000 unieke query's om te voor komen dat tests worden schuingetrokken door caching.

> [!IMPORTANT]
> Deze tests omvatten alleen query werkbelastingen. Als u verwacht een groot aantal volumne te indexeren, moet u rekening houden met de factoren die u in uw schatting en prestaties wilt testen. In deze [zelf studie](tutorial-optimize-indexing-push-api.md)vindt u voorbeeld code voor het simuleren van indexeren.

### <a name="definitions"></a>Definities

- **Maximum aantal qps** : de maximum qps-cijfers hieronder zijn gebaseerd op de hoogste qps die is bereikt in een test waarbij 99% van de query's is voltooid zonder beperking en gemiddelde latentie van 1000 MS.

- **Percentage van maximale qps** : een percentage van de maximale qps die is behaald voor een bepaalde test. Als een bepaalde test bijvoorbeeld een maximum van 100 QPS bereikt, zou 20% van de maximale QPS 20 QPS zijn.

- **Latentie** : de latentie van de server voor een query; Deze getallen bevatten geen [Round-trip vertraging (RTT)](https://en.wikipedia.org/wiki/Round-trip_delay). De onderstaande waarden zijn in milliseconden (MS).

### <a name="disclaimer"></a>Disclaimer

De code die we hebben gebruikt voor het uitvoeren van deze benchmarks is [hier](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools)beschikbaar. Het is een goed idee dat er iets lagere QPS-niveaus werden waargenomen met de oplossing voor het testen van de [jmeter-prestaties](https://github.com/Azure-Samples/azure-search-performance-testing) dan in de onderstaande benchmarks. De verschillen kunnen worden toegeschreven aan verschillen in de stijl van de tests. Dit heeft tot het belang om uw prestatie tests zo vergelijkbaar mogelijk te maken met uw productiewerk belasting.

Deze benchmarks bieden geen enkele manier om een bepaald prestatie niveau van uw service te garanderen, maar u kunt wel een idee krijgen van de prestaties die u op basis van uw scenario kunt verwachten.

Als u vragen of problemen hebt, kunt u contact opnemen met ons op azuresearch_contact@microsoft.com .

## <a name="benchmark-1-e-commerce-search"></a>Bench Mark 1: E-commerce-zoek opdracht

:::row:::
   :::column span="1":::
      ![CDON-logo](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      Deze bench Mark is gemaakt in samen werking met het e-commerce-bedrijf [CDON](https://cdon.com), de grootste online Marketplace van de Scandinavische regio met bewerkingen in Zweden, Finland, Noor wegen en Denemarken. Via de 1.500-handelaars biedt CDON een breed scala assortiment met meer dan 8.000.000 producten. In 2020 had CDON meer dan 120.000.000 bezoekers en 2.000.000 actieve klanten. In [dit artikel](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/)vindt u meer informatie over het gebruik van Azure COGNITIVE Search door CDON.
   :::column-end:::
:::row-end:::

Om deze tests uit te voeren, hebben we een moment opname van de productie-zoek index van CDON en duizenden unieke query's op hun [website](https://cdon.com)gebruikt.

### <a name="scenario-details"></a>Details van scenario

- **Aantal documenten**: 6.000.000 
- **Index grootte**: 20 GB
- **Index schema**: een brede index met 250 velden totaal, 25 Doorzoek bare velden en befacet bare/filter bare velden van 200
- **Query typen**: Zoek query's in volledige tekst, waaronder facetten, filters, volg orde en Score profielen

### <a name="s1-performance"></a>Prestaties S1

#### <a name="queries-per-second"></a>Query's per seconde

In de onderstaande grafiek ziet u de hoogste belasting van een service die gedurende een lange periode kan worden verwerkt in termen van query's per seconde (QPS).

![Hoogst onderhouden QPS van Commerce](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>Query latentie

De latentie van query's varieert op basis van de belasting van de service en de services onder hogere belasting heeft een hogere gemiddelde latentie van query's. De volgende tabel toont de 25e, 50e, 75e, negen tigste, 95e en 99e percentielen van de query latentie voor drie verschillende gebruiks niveaus.

| Percentage van maximale QPS  | Gemiddelde latentie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 104 MS  | 35 MS  | 115 MS   | 177 MS | 257 MS | 738 MS |
| 50%  | 140 MS  | 47 MS  | 144 MS   | 241 MS | 400 MS | 1175 MS |
| 80%  | 239 MS  | 77 MS  | 248 MS   | 466 MS | 763 MS | 1752 MS | 


### <a name="s2-performance"></a>Prestaties S2

#### <a name="queries-per-second"></a>Query's per seconde

In de onderstaande grafiek ziet u de hoogste belasting van een service die gedurende een lange periode kan worden verwerkt in termen van query's per seconde (QPS).

![Hoogst te onderhouden QPS-commerce S2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>Query latentie

De latentie van query's varieert op basis van de belasting van de service en de services onder hogere belasting heeft een hogere gemiddelde latentie van query's. De volgende tabel toont de 25e, 50e, 75e, negen tigste, 95e en 99e percentielen van de query latentie voor drie verschillende gebruiks niveaus.

| Percentage van maximale QPS  | Gemiddelde latentie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- | 
| 20%  | 56 MS | 21 MS | 68 MS  | 106 MS  | 132 MS | 210 MS | 
| 50%  | 71 MS  | 26 MS  | 83 MS   | 132 MS | 177 MS | 329 MS |
| 80%  | 140 MS  | 47 MS  | 153 MS   | 293 MS | 452 MS | 924 MS | 

### <a name="s3-performance"></a>S3-prestaties

#### <a name="queries-per-second"></a>Query's per seconde

In de onderstaande grafiek ziet u de hoogste belasting van een service die gedurende een lange periode kan worden verwerkt in termen van query's per seconde (QPS).

![Hoogst onderhouden QPS commerce S3](./media/performance-benchmarks/s3-ecom-qps.png)

In dit geval zien we dat wanneer u een tweede partitie toevoegt, de maximale QPS aanzienlijk toeneemt, maar een derde partitie toe te voegen om de marginale opbrengsten te verminderen. De kleinere verbetering is waarschijnlijk omdat alle gegevens al worden opgehaald in het S3's actieve geheugen met slechts twee partities.

#### <a name="query-latency"></a>Query latentie

De latentie van query's varieert op basis van de belasting van de service en de services onder hogere belasting heeft een hogere gemiddelde latentie van query's. De volgende tabel toont de 25e, 50e, 75e, negen tigste, 95e en 99e percentielen van de query latentie voor drie verschillende gebruiks niveaus.

| Percentage van maximale QPS  | Gemiddelde latentie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 50 MS  | 20 MS  | 64 MS   | 83 MS | 98 MS | 160 MS |
| 50%  | 62 MS  | 24 MS  | 80 MS   | 107 MS | 130 MS | 253 MS |
| 80%  | 115 MS  | 38 MS  | 121 MS   | 218 MS | 352 MS | 828 MS |

## <a name="benchmark-2-document-search"></a>Bench Mark 2: zoeken in documenten

### <a name="scenario-details"></a>Details van scenario

- **Aantal documenten**: 7.500.000
- **Index grootte**: 22 GB
- **Index schema**: 23 velden; 8 Doorzoek bare, 10 filterbaar/facetbaar
- **Query typen**: tref woorden zoeken met facetten en treffers markeren

### <a name="s1-performance"></a>Prestaties S1

#### <a name="queries-per-second"></a>Query's per seconde

In de onderstaande grafiek ziet u de hoogste belasting van een service die gedurende een lange periode kan worden verwerkt in termen van query's per seconde (QPS).

![Hoogst onderhouden QPS-document zoeken S1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>Query latentie

De latentie van query's varieert op basis van de belasting van de service en de services onder hogere belasting heeft een hogere gemiddelde latentie van query's. De volgende tabel toont de 25e, 50e, 75e, negen tigste, 95e en 99e percentielen van de query latentie voor drie verschillende gebruiks niveaus.

| Percentage van maximale QPS  | Gemiddelde latentie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 67 MS  | 44 MS  | 77 MS   | 103 MS | 126 MS | 216 MS |
| 50%  | 93 MS  | 59 MS  | 110 MS   | 150 MS | 184 MS | 304 MS |
| 80%  | 150 MS  | 96 MS  | 184 MS   | 248 MS | 297 MS | 424 MS |

### <a name="s2-performance"></a>Prestaties S2

#### <a name="queries-per-second"></a>Query's per seconde

In de onderstaande grafiek ziet u de hoogste belasting van een service die gedurende een lange periode kan worden verwerkt in termen van query's per seconde (QPS).

![Hoogst onderhouden QPS-document zoeken S2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>Query latentie

De latentie van query's varieert op basis van de belasting van de service en de services onder hogere belasting heeft een hogere gemiddelde latentie van query's. De volgende tabel toont de 25e, 50e, 75e, negen tigste, 95e en 99e percentielen van de query latentie voor drie verschillende gebruiks niveaus.

| Percentage van maximale QPS  | Gemiddelde latentie | 25% | 75% | 90% | 95% | 99%|
|---|---|---|---| --- | --- | --- |
| 20%  | 45 MS  | 31 MS  | 55 MS   | 73 MS | 84 MS | 109 MS |
| 50%  | 63 MS  | 39 MS  | 81 MS   | 106 MS | 123 MS | 163 MS |
| 80%  | 115 MS  | 73 MS  | 145 MS   | 191 MS | 224 MS | 291 MS |

## <a name="takeaways"></a>Opgedane kennis

Via deze benchmarks kunt u een idee krijgen van de prestaties van Azure Cognitive Search-aanbiedingen. U kunt ook verschillen zien tussen services op verschillende lagen.

Enkele belang rijke manieren van deze benchmarks zijn:

* Een S2 kan meestal ten minste vier maal het query volume als S1 verwerken
* Een S2 heeft normaal gesp roken een lagere latentie dan een S1 bij vergelijk bare query volumes
* Bij het toevoegen van replica's, kan de QPS een service normaal gesp roken lineair schalen (bijvoorbeeld als één replica 10 QPS kan verwerken en vijf replica's meestal 50 QPS kunnen verwerken)
* Hoe hoger de belasting van de service, hoe hoger de gemiddelde latentie wordt

U kunt ook zien dat prestaties aanzienlijk kunnen variëren tussen scenario's. Als u niet de verwachte prestaties krijgt, raadpleeg dan de [Tips voor betere prestaties](search-performance-tips.md).

## <a name="next-steps"></a>Volgende stappen

Nu u de benchmarks voor prestaties hebt gezien, kunt u meer te weten komen over het analyseren van de prestaties van Cognitive Search en de belangrijkste factoren die invloed hebben op de prestaties.

> [!div class="nextstepaction"]
> [Prestaties analyseren](search-performance-analysis.md) 
>  [Tips voor betere prestaties](search-performance-tips.md)