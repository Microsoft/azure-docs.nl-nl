---
title: Verdeling van geclusterde metrische gegevens
description: Het effect van plaatsings beperkingen voor de verdeling en het afhandelen hiervan
author: nipavlo
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: nipavlo
ms.openlocfilehash: 7f571a851e4da147240c524b742bcd652bc54181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82183110"
---
# <a name="balancing-of-subclustered-metrics"></a>Verdeling van geclusterde metrische gegevens

## <a name="what-is-subclustering"></a>Wat is subclustering?

Subclustering treedt op wanneer Services met verschillende plaatsings beperkingen een gemeen schappelijke metrische waarde hebben en beide rapporten laden. Als de door de services gemelde belasting significant afwijkt, heeft de totale belasting op de knoop punten een grote standaard afwijking en lijkt het alsof het cluster niet meer in balans is, zelfs wanneer het het best mogelijke saldo heeft.

## <a name="how-subclustering-affects-load-balancing"></a>De invloed van subclustering op taak verdeling

Als de belasting die door de services op verschillende knoop punten wordt gerapporteerd aanzienlijk afwijkt, kan het lijken alsof er sprake is van een grote onevenwichtigheid. Als het onrecht matige onevenwichtigheid dat is veroorzaakt door subclustering groter is dan het werkelijke onevenwichtigheid, heeft het de mogelijkheid om het taakverdelings algoritme van Resource Manager te verwarren en om een suboptimaal evenwicht in het cluster te maken.

Stel bijvoorbeeld dat er vier services zijn en dat alle een belasting voor metrische Metric1 rapporteert:

* Service A: heeft een plaatsings beperking "NodeType = = frontend", rapporteert een belasting van 10
* Service B: heeft een plaatsings beperking "NodeType = = frontend", rapporteert een belasting van 10
* Service C: heeft een plaatsings beperking "NodeType = = back-end", rapporteert een belasting van 100
* Service D: heeft een plaatsings beperking "NodeType = = back-end", en rapporteert een belasting van 100
* En we hebben vier knoop punten. Twee hiervan hebben NodeType als ' front-end ' en de andere twee ' back-end ' zijn.

En we hebben de volgende plaatsing:

<center>

![Voor beeld van een geclusterde plaatsing][Image1]
</center>

Het cluster kan niet in evenwicht zijn, we hebben een grote belasting voor de knoop punten 3 en 4, maar deze plaats maakt in deze situatie het beste saldo.

Resource Manager kan subclustering situaties herkennen en in nagenoeg alle gevallen kan het optimale saldo voor de gegeven situatie worden geproduceerd.

Voor sommige uitzonderlijke situaties waarbij Resource Manager niet in staat is een geclusterde metriek te verdelen, wordt er nog steeds subclustering gedetecteerd en wordt er een status rapport gegenereerd om u te adviseren om het probleem op te lossen.

## <a name="types-of-subclustering-and-how-they-are-handled"></a>Typen subclustering en hoe deze worden verwerkt

Scenario's voor subclusteren kunnen worden geclassificeerd in drie verschillende categorieën. De categorie van een specifieke subclustering-situatie bepaalt hoe deze wordt verwerkt door Resource Manager.

### <a name="first-category--flat-subclustering-with-disjoint-node-groups"></a>Eerste categorie – platte subclustering met niet-aaneengesloten knooppunt groepen

Deze categorie heeft de eenvoudigste vorm van subclustering waarbij knoop punten kunnen worden onderverdeeld in verschillende groepen en elke service kan alleen worden geplaatst op knoop punten in een van deze groepen. Elk knoop punt behoort tot één groep en slechts één groep. De hierboven beschreven situatie behoort tot deze categorie als de meeste subclusterings situaties. 

In het geval van de situatie in deze categorie kan de Resource Manager het optimale saldo produceren en is er geen verdere tussen komst vereist.

### <a name="second-category--subclustering-with-hierarchical-node-groups"></a>Tweede categorie-subclustering met hiërarchische knooppunt groepen

Deze situatie treedt op wanneer een groep knoop punten die is toegestaan voor één service een subset is van de groep knoop punten die voor een andere service zijn toegestaan. Het meest voorkomende voor beeld van deze situatie is wanneer voor een bepaalde service een plaatsings beperking is gedefinieerd en een andere service geen plaatsings beperking heeft en op een wille keurig knoop punt kan worden geplaatst.

Voorbeeld:

* Service A: geen plaatsings beperking
* Service B: plaatsings beperking "NodeType = = frontend"
* Service C: plaatsings beperking "NodeType = = back-end"

Deze configuratie maakt een subset-relatie tussen knooppunt groepen voor verschillende services.

<center>

![Subset van subclusteren van hoofd verzameling][Image2]
</center>

In dit geval is er een kans dat er een suboptimaal saldo wordt gemaakt.

Resource Manager herkent deze situatie en produceert een status rapport waarin u wordt geadviseerd om service A te splitsen in twee services: service a1 die kunnen worden geplaatst op frontend knooppunten en service a2 die op back-end-knoop punten kunnen worden geplaatst. Dit brengt ons terug naar de eerste categorie situatie die optimaal kan worden gesaldeerd.

### <a name="third-category--subclustering-with-partial-overlap-between-node-sets"></a>Derde categorie: subclustering met gedeeltelijk overlappende knooppunt sets

Deze situatie doet zich voor wanneer er sprake is van een deel van de knoop punten waarop sommige services kunnen worden geplaatst.

Als we bijvoorbeeld een knooppunt eigenschap met de naam NodeColor hebben en er drie knoop punten zijn:

* Knoop punt 1: NodeColor = rood
* Knoop punt 2: NodeColor = Blue
* Knoop punt 2: NodeColor = groen

En we hebben twee services:

* Service A: with placement CONSTRAINT "Color = = Red | | Color = = Blue "
* Service B: met plaatsings beperking "Color = = Blue | | Kleur = = groen

Als gevolg hiervan kan service A op knoop punten 1 en 2 worden geplaatst en kan service B op knoop punten 2 en 3 worden geplaatst.

In dit geval is er een kans dat er een suboptimaal saldo wordt gemaakt.

Resource Manager herkent deze situatie en produceert een status rapport waarin u wordt geadviseerd om een aantal van de services te splitsen.

In dit geval kan de Resource Manager geen voor stel geven om de services te splitsen omdat meerdere splitsingen kunnen worden uitgevoerd en er geen manier is om te schatten welke manier het beste is om de services te splitsen.

## <a name="configuring-subclustering"></a>Subclustering configureren

Het gedrag van Resource Manager over subclustering kan worden gewijzigd door de volgende configuratie parameters te wijzigen:
* SubclusteringEnabled-para meter bepaalt of resource manager in aanmerking komt voor subclusteren bij het uitvoeren van de taak verdeling. Als deze para meter is uitgeschakeld, wordt subclustering in Resource Manager genegeerd en wordt het optimale saldo op een globaal niveau bereikt. De standaard waarde van deze para meter is onwaar.
* SubclusteringReportingPolicy: bepaalt hoe Resource Manager status rapporten voor hiërarchische en gedeeltelijke overlappende subclustering zal genereren. Een waarde van nul betekent dat status rapporten over subclusteren worden uitgeschakeld. "1" betekent dat er waarschuwings status rapporten worden gemaakt voor suboptimale subclusterings situaties en de waarde ' 2 ' resulteert in status rapporten ' OK '. De standaard waarde voor deze para meter is 1.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="SubclusteringEnabled" Value="true" />
            <Parameter Name="SubclusteringReportingPolicy" Value="1" />
        </Section>
```

via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "SubclusteringEnabled",
          "value": "true"
      },
      {
          "name": "SubclusteringReportingPolicy",
          "value": "1"
      },
    ]
  }
]
```

## <a name="next-steps"></a>Volgende stappen
* Raadpleeg het artikel over het [verdelen](service-fabric-cluster-resource-manager-balancing.md) van de taken in het cluster voor meer informatie over hoe de cluster resource manager de belasting beheert en balanceert.
* Zie [Eigenschappen van knoop punten en plaatsings beperkingen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints) voor meer informatie over hoe uw services kunnen worden beperkt zodat deze alleen op bepaalde knoop punten worden geplaatst.

[Image1]:./media/cluster-resource-manager-subclustering/subclustered-placement.png
[Image2]:./media/cluster-resource-manager-subclustering/subset-superset-nodes.png
