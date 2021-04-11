---
title: Tips voor prestaties
titleSuffix: Azure Cognitive Search
description: Meer informatie over tips en aanbevolen procedures voor het optimaliseren van de prestaties van een zoek service.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 28325a1bbda1b2d4a4bb060ae3e79057275ee42a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582264"
---
# <a name="tips-for-better-performance-in-azure-cognitive-search"></a>Tips voor betere prestaties in azure Cognitive Search

Dit artikel is een verzameling tips en aanbevolen procedures die vaak worden aanbevolen voor betere prestaties. Als u weet welke factoren waarschijnlijk invloed hebben op de zoek prestaties, kunt u inefficiënties voor komen en optimaal profiteren van uw zoek service. Enkele belang rijke factoren zijn:

+ Index samenstelling (schema en grootte)
+ Typen query's
+ Service capaciteit (laag en aantal replica's en partities)

## <a name="index-size-and-schema"></a>Index grootte en-schema

Query's worden sneller uitgevoerd op kleinere indexen. Dit is gedeeltelijk een functie waarbij minder velden moeten worden gescand, maar dit is ook het gevolg van de manier waarop het systeem inhoud in de cache opslaat voor toekomstige query's. Na de eerste query blijft sommige inhoud in het geheugen staan, waar deze efficiënter wordt doorzocht. Omdat de index grootte in de loop van de tijd toeneemt, is een best practice regel matig de index samenstelling, zowel schema's als documenten, om te zoeken naar inhouds reductie kansen. Als de index echter een juiste grootte heeft, kunt u de capaciteit verg Roten door [replica's toe te voegen](search-capacity-planning.md#adjust-capacity) of door een upgrade van de servicelaag uit te voeren. De sectie [' Tip: upgrade to a standard S2 tier ']] (#tip-upgrade-to-a-standard-S2-tier) laat zien hoe u de schaal van omhoog en uitschalen kunt evalueren.

Schema complexiteit kan ook een negatieve invloed hebben op de prestaties van indexeren en query's. Buitensporige veld toewijzing is gebaseerd op beperkingen en verwerkings vereisten. Het duurt langer om [complexe typen](search-howto-complex-data-types.md) te indexeren en query's uit te voeren. De volgende secties verkennen elke voor waarde.

### <a name="tip-be-selective-in-field-attribution"></a>Tip: selectief selecteren in veld toewijzing

Een veelvoorkomende fout die beheerders en ontwikkel aars maken bij het maken van een zoek index is het selecteren van alle beschik bare eigenschappen voor de velden, in plaats van alleen de eigenschappen te selecteren die nodig zijn. Als een veld bijvoorbeeld niet in volledige tekst moet worden doorzocht, slaat u dat veld over bij het instellen van het Doorzoek bare kenmerk.

:::image type="content" source="media/search-performance/perf-selective-field-attributes.png" alt-text="Selectief toerekenen" border="true":::

Ondersteuning voor filters, facetten en sorteren kan vier opslag vereisten hebben. Als u suggesties toevoegt, gaan opslag vereisten nog verder. Zie [kenmerken en index grootte](search-what-is-an-index.md#index-size)voor een illustratie over de impact van kenmerken op opslag.

De implicaties van het overschrijden van de toewijzing zijn onder andere:

+ Het afnemen van index prestaties vanwege het extra werk dat nodig is om de inhoud in het veld te verwerken en vervolgens op te slaan in de index van de doorlopende zoek opdracht (stel het kenmerk ' Doorzoek bare ' in op velden die Doorzoek bare inhoud bevatten).

+ Hiermee maakt u een groter Opper vlak dat elke query moet omvatten. Alle velden die als doorzoekbaar zijn gemarkeerd, worden gescand in een zoek opdracht in volledige tekst.

+ Verhoogt de operationele kosten vanwege extra opslag. Filteren en sorteren vereist extra ruimte voor het opslaan van originele (niet-geanalyseerde) teken reeksen. Vermijd het instellen van filterbaar of sorteerbaar op velden die niet nodig zijn.

+ In veel gevallen beperkt de capaciteit van het veld via de toewijzing. Als een veld bijvoorbeeld facetbaar, filterbaar en doorzoekbaar is, kunt u slechts 16 KB tekst in een veld opslaan, terwijl een doorzoekbaar veld Maxi maal 16 MB tekst kan bevatten.

> [!NOTE]
> Alleen onnodige toewijzing moet worden vermeden. Filters en facetten zijn vaak essentieel voor de zoek ervaring en in gevallen waarin filters worden gebruikt, moet u regel matig sorteren, zodat u de resultaten kunt ordenen (filters op zichzelf retour neren in een niet-geordende set).

### <a name="tip-consider-alternatives-to-complex-types"></a>Tip: denk aan alternatieven voor complexe typen

Complexe gegevens typen zijn handig wanneer gegevens een gecompliceerd geneste structuur hebben, zoals de bovenliggende/onderliggende elementen die in JSON-documenten worden gevonden. Het nadeel van complexe typen is de extra opslag vereisten en aanvullende bronnen die nodig zijn om de inhoud te indexeren, in vergelijking met niet-complexe gegevens typen. 

In sommige gevallen kunt u deze afwegingen vermijden door een complexe gegevens structuur toe te wijzen aan een eenvoudiger veld type, zoals een verzameling. U kunt er ook voor kiezen om een veld hiërarchie samen te voegen in afzonderlijke velden op hoofd niveau.

:::image type="content" source="media/search-performance/perf-flattened-field-hierarchy.png" alt-text="platte veld structuur" border="true":::

## <a name="types-of-queries"></a>Typen query's

De typen query's die u verzendt, zijn een van de belangrijkste factoren voor de prestaties en de optimalisatie van query's kan drastische prestaties verbeteren. Bij het ontwerpen van query's moet u rekening houden met de volgende punten:

+ **Aantal Doorzoek bare velden.** Voor elk extra Doorzoek bare veld is aanvullende werkzaamheden vereist voor de zoek service. Met de para meter ' searchFields ' kunt u de velden die op query tijdstip worden doorzocht, beperken. Het is raadzaam om alleen de velden op te geven waarmee u de prestaties wilt verbeteren.

+ **De hoeveelheid gegevens die wordt geretourneerd.** Het ophalen van een grote hoeveelheid inhoud kan query's langzamer maken. Wanneer u een query structureert, retourneert u alleen de velden die u nodig hebt om de resultaten pagina weer te geven en haalt u vervolgens de resterende velden op met de [lookup-API](/rest/api/searchservice/lookup-document) zodra een gebruiker een overeenkomst selecteert.

+ **Gebruik van zoek opdrachten op de gedeeltelijke term.** Zoek opdrachten op de [korte termijn](search-query-partial-matching.md), zoals de zoek functie voor voor voegsels, fuzzy Zoek opdrachten en reguliere expressies zoeken, zijn meer reken kracht dan gebruikelijke Zoek opdrachten voor het tref woord, omdat de volledige index scans vereist zijn voor het produceren van resultaten.

+ **Aantal facetten.** Het toevoegen van facetten aan query's vereist aggregaties voor elke query. In het algemeen voegt u alleen de facetten toe die u wilt weer geven in uw app.

+ **Velden met hoge kardinaliteit beperken.**  Een *hoog veld voor kardinaliteit* verwijst naar een facetbaar of filterbaar veld met een groot aantal unieke waarden, en als gevolg hiervan worden aanzienlijke bronnen verbruikt bij het berekenen van resultaten. Zo kunt u bijvoorbeeld een product-ID of beschrijvings veld instellen als facetbaar en kan het filter worden beschouwd als hoge kardinaliteit, omdat de meeste waarden van document naar document uniek zijn.

### <a name="tip-use-search-functions-instead-overloading-filter-criteria"></a>Tip: zoek functies gebruiken in plaats van filter criteria te verwijderen

Wanneer een query steeds [complexe filter criteria](search-query-odata-filter.md#filter-size-limitations)gebruikt, zal de prestaties van de zoek query afnemen. Bekijk het volgende voor beeld waarin het gebruik van filters wordt gedemonstreerd voor het afkappen van resultaten op basis van een gebruikers-id:

```json
$filter= userid eq 123 or userid eq 234 or userid eq 345 or userid eq 456 or userid eq 567
```

In dit geval worden de filter expressies gebruikt om te controleren of één veld in elk document gelijk is aan een van de vele mogelijke waarden van een gebruikers-id. U zult dit patroon waarschijnlijk vinden in toepassingen die [beveiliging](search-security-trimming-for-azure-search.md) kunnen beperken (een veld met een of meer Principal-id's controleren op basis van een lijst met Principal-id's die de gebruiker die de query heeft uitgegeven).

Een efficiëntere manier om filters uit te voeren die een groot aantal waarden bevatten, is het gebruik van een [ `search.in` functie](search-query-odata-search-in-function.md), zoals wordt weer gegeven in dit voor beeld:

```json
search.in(userid, '123,234,345,456,567', ',')
```

### <a name="tip-add-partitions-for-slow-individual-queries"></a>Tip: partities voor langzame afzonderlijke query's toevoegen

Wanneer de prestaties van query's langzaam worden vertraagd, wordt het probleem vaak opgelost door meer replica's toe te voegen. Maar wat gebeurt er als het probleem wordt veroorzaakt door één query die te lang duurt om te volt ooien? In dit scenario is het toevoegen van replica's geen uitkomst, maar kunnen er ook extra partities worden toegevoegd. Een partitie splitst gegevens over extra computer bronnen. Met twee partities worden gegevens in de helft gesplitst, een derde partitie gesplitst in derde, enzovoort. 

Een positieve neven effect van het toevoegen van partities is dat tragere query's soms sneller worden uitgevoerd als gevolg van parallelle computing. We hebben op parallel Lise ring genoteerd dat er weinig selectiviteit-query's zijn, zoals query's die overeenkomen met veel documenten, of facetten die aantallen bieden over een groot aantal documenten. Aangezien er een belang rijke berekening is vereist om de relevancy van de documenten te beoordelen, of om het aantal documenten te tellen, kunnen er met extra partities sneller query's worden uitgevoerd.  

Gebruik [Azure Portal](search-create-service-portal.md), [Power shell](search-manage-powershell.md), [Azure cli](search-manage-azure-cli.md)of een management SDK om partities toe te voegen.

## <a name="service-capacity"></a>Service capaciteit

Een service wordt overbelast wanneer query's te lang duren of wanneer de service aanvragen verwijdert. Als dit het geval is, kunt u het probleem oplossen door de service bij te werken of door capaciteit toe te voegen.

De laag van uw zoek service en het aantal replica's/partities hebben ook een grote invloed op de prestaties. Elke hogere laag biedt snellere Cpu's en meer geheugen, beide met een positieve invloed op de prestaties.

### <a name="tip-upgrade-to-a-standard-s2-tier"></a>Tip: upgraden naar een Standard S2-laag

De standaard zoek categorie S1 is vaak waar klanten beginnen. Een algemeen patroon voor S1-Services is dat indexen na verloop van tijd groter worden, waardoor er meer partities nodig zijn. Meer partities leiden tot tragere reactie tijden, zodat er meer replica's worden toegevoegd om de query belasting af te handelen. Zoals u kunt Voorst Ellen, worden de kosten voor het uitvoeren van een S1-service nu geduurd op het niveau van de oorspronkelijke configuratie.

Op dit moment is een belang rijke vraag om te vragen of het nuttig is om over te stappen op een hogere laag, in tegens telling tot geleidelijk het aantal partities of replica's van de huidige service. 

Houd rekening met de volgende topologie als voor beeld van een service die is uitgevoerd op toenemende capaciteits niveaus:

+ Standard S1-laag
+ Index grootte: 190 GB
+ Aantal partities: 8 (op S1 is de partitie grootte 25 GB per partitie)
+ Aantal replica's: 2
+ Totaal aantal Zoek eenheden: 16 (8 partities x 2 replica's)
+ Hypothetische retail prijs: ~ $4.000 USD/maand (Stel dat $250 USD x 16 Zoek eenheden)

Stel dat de service beheerder nog steeds hogere latentie tarieven ziet en overweegt om een andere replica toe te voegen. Hierdoor wordt het aantal replica's gewijzigd van 2 in 3 en als gevolg hiervan wordt het aantal Zoek eenheden gewijzigd in 24 en een prijs van $6.000 USD/maand.

Als de beheerder echter heeft gekozen om naar een Standard S2-laag te gaan, ziet de topologie er als volgt uit:

+ Standard S2-laag
+ Index grootte: 190 GB
+ Aantal partities: 2 (op S2, partitie grootte is 100 GB per partitie)
+ Aantal replica's: 2
+ Totaal aantal Zoek eenheden: 4 (2 partities x 2 replica's)
+ Hypothetische prijs van de detail handel: $4.000 USD per maand ($1000 USD x 4 Zoek eenheden)

Zoals dit hypothetische scenario illustreert, kunt u configuraties hebben op lagere lagen die resulteren in vergelijk bare kosten als wanneer u de eerste plaats hebt gekozen voor een hogere laag. Hogere lagen worden echter wel geleverd met Premium-opslag, waardoor het indexeren sneller wordt. Hogere lagen hebben ook veel meer reken kracht, evenals extra geheugen. Voor dezelfde kosten kunt u een krachtigere infra structuur maken met dezelfde index.

Een belang rijk voor deel van het toegevoegde geheugen is dat meer van de index in de cache kan worden opgeslagen, wat resulteert in een lagere Zoek latentie en een groter aantal query's per seconde. Met deze extra kracht is het mogelijk dat de beheerder het aantal replica's niet hoeft te verhogen en dat mogelijk minder kan betalen dan door op de S1-service te blijven.

## <a name="next-steps"></a>Volgende stappen

Lees deze aanvullende artikelen over de prestaties van services.

+ [Prestaties analyseren](search-performance-analysis.md)
+ [Een servicelaag kiezen](search-sku-tier.md)
+ [Capaciteit toevoegen (replica's en partities)](search-capacity-planning.md#adjust-capacity)
