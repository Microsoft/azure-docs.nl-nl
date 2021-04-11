---
title: Clustering Point-gegevens op een kaart | Microsoft Azure kaarten
description: Meer informatie over cluster Point-gegevens op kaarten. Zie hoe u de Azure Maps Web-SDK gebruikt om gegevens te clusteren, te reageren op cluster muis gebeurtenissen en cluster aggregaties weer te geven.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: codepen, devx-track-js
ms.openlocfilehash: f798b4a7964eba112ed9948b2db4f6d5c3edd1e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608354"
---
# <a name="clustering-point-data"></a>Clustering Point-gegevens

Bij het visualiseren van veel gegevens punten op de kaart kunnen gegevens punten van elkaar overlappen. De overlap ping kan ertoe leiden dat de kaart onleesbaar wordt en moeilijk te gebruiken is. Het clusteren van puntgegevens is het proces van het combineren van puntgegevens die zich dicht bij elkaar bevinden en deze als één geclusterd gegevenspunt weer te geven op de kaart. Als de gebruiker inzoomt op de kaart, vallen de clusters uiteen in de afzonderlijke gegevenspunten. Wanneer u met een groot aantal gegevens punten werkt, gebruikt u de cluster processen om uw gebruikers ervaring te verbeteren.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Clustering-point-data-in-Azure-Maps/player?format=ny]

## <a name="enabling-clustering-on-a-data-source"></a>Clustering inschakelen op een gegevens bron

Schakel clustering in de- `DataSource` klasse in door de `cluster` optie in te stellen op `true` . Instellen `clusterRadius` om nabijgelegen punten te selecteren en deze te combi neren in een cluster. De waarde van `clusterRadius` is in pixels. Gebruik `clusterMaxZoom` om een zoom niveau op te geven waarmee de cluster logica wordt uitgeschakeld. Hier volgt een voor beeld van het inschakelen van clustering in een gegevens bron.

```javascript
//Create a data source and enable clustering.
var datasource = new atlas.source.DataSource(null, {
    //Tell the data source to cluster point data.
    cluster: true,
    
    //The radius in pixels to cluster points together.
    clusterRadius: 45,
    
    //The maximum zoom level in which clustering occurs.
    //If you zoom in more than this, all points are rendered as symbols.
    clusterMaxZoom: 15
});
```

> [!TIP]
> Als twee gegevens punten dicht bij elkaar staan, is het mogelijk dat het cluster nooit gesplitst is, ongeacht hoe dicht de gebruiker in-of uitzoomt. Als u dit wilt oplossen, kunt u de `clusterMaxZoom` optie voor het uitschakelen van de cluster logica instellen en alleen alles weer geven.

De `DataSource` klasse biedt ook de volgende methoden met betrekking tot clustering.

| Methode | Retourtype | Beschrijving |
|--------|-------------|-------------|
| getClusterChildren (clusterId: getal) | Promise&lt;Matrix&lt;Functie&lt;Geometrie, willekeurig&gt; \| Vorm&gt;&gt; | Hiermee worden de onderliggende elementen opgehaald van het opgegeven cluster op het volgende zoomniveau. Deze elementen kunnen een combinatie zijn van vormen en subclusters. De subclusters bestaan uit functies met eigenschappen die overeenkomen met ClusteredProperties. |
| getClusterExpansionZoom (clusterId: getal) | Promise&lt;getal&gt; | Hiermee wordt een zoomniveau berekend waarbij het cluster gaat uitbreiden of uiteenvalt. |
| getClusterLeaves (clusterId: aantal, limiet: aantal, verschuiving: getal) | Promise&lt;Matrix&lt;Functie&lt;Geometrie, willekeurig&gt; \| Vorm&gt;&gt; | Hiermee worden alle punten in een cluster opgehaald. Stel de `limit` in voor het retourneren van een subset van de punten en gebruik de `offset` om door de punten te bladeren. |

## <a name="display-clusters-using-a-bubble-layer"></a>Clusters weer geven met behulp van een Bubble Layer

Een Bubble slaag is een uitstekende manier om geclusterde punten weer te geven. Gebruik expressies om de RADIUS te schalen en de kleur te wijzigen op basis van het aantal punten in het cluster. Als u clusters met behulp van een Bubble laag weergeeft, moet u een afzonderlijke laag gebruiken om niet-geclusterde gegevens punten weer te geven.

Als u de grootte van het cluster boven op de bel wilt weer geven, gebruikt u een symbool laag met tekst en gebruikt u geen pictogram.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Basis clusters voor bellen lagen" src="//codepen.io/azuremaps/embed/qvzRZY/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de pen <a href='https://codepen.io/azuremaps/pen/qvzRZY/'>Basic Bubble Layer clustering</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-clusters-using-a-symbol-layer"></a>Clusters weer geven met behulp van een symbool-laag

Bij het visualiseren van gegevens punten worden symbolen die elkaar overlappen, automatisch verborgen om ervoor te zorgen dat een schone gebruikers interface wordt weer gegeven. Dit standaard gedrag kan ongewenst zijn als u de densiteit van gegevens punten op de kaart wilt weer geven. Deze instellingen kunnen echter worden gewijzigd. Als u alle symbolen wilt weer geven, stelt `allowOverlap` u de optie van de eigenschap Symbol Layers `iconOptions` in op `true` . 

Gebruik clustering om de densiteit van gegevens punten weer te geven en een schone gebruikers interface te houden. In het onderstaande voor beeld ziet u hoe u aangepaste symbolen kunt toevoegen en hoe u clusters en afzonderlijke gegevens punten kunt weer geven met behulp van de Symbol-laag.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Laag geclusterde symbolen" src="//codepen.io/azuremaps/embed/Wmqpzz/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/Wmqpzz/'>laag geclusterde symbool Layer</a> by Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="clustering-and-the-heat-maps-layer"></a>Clustering en de laag met hitte kaarten

Hitte kaarten zijn een uitstekende manier om de densiteit van gegevens op de kaart weer te geven. Deze visualisatie methode kan een groot aantal gegevens punten zelf verwerken. Als de gegevens punten geclusterd zijn en de cluster grootte wordt gebruikt als het gewicht van de heatmap, kan de heatmap nog meer gegevens verwerken. Als u deze optie wilt instellen, stelt `weight` u de optie van de heatmap van de kaart in op `['get', 'point_count']` . Wanneer de RADIUS van het cluster klein is, zal de heatmap er bijna hetzelfde uitzien als een heatmap met behulp van de niet-geclusterde gegevens punten, maar wordt het veel beter. Hoe kleiner de cluster RADIUS, des te nauw keuriger de heatmap is, maar met minder prestatie voordelen.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Heatmap voor cluster met gewicht" src="//codepen.io/azuremaps/embed/VRJrgO/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het overzicht van de <a href='https://codepen.io/azuremaps/pen/VRJrgO/'>gewogen heatmap</a> van het pen-Cluster door Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="mouse-events-on-clustered-data-points"></a>Muis gebeurtenissen op geclusterde gegevens punten

Wanneer er muis gebeurtenissen optreden op een laag die geclusterde gegevens punten bevat, keert het geclusterde gegevens punt terug naar de gebeurtenis als een functie object van een geojson-punt. Deze punt functie heeft de volgende eigenschappen:

| Naam van eigenschap             | Type    | Beschrijving   |
|---------------------------|---------|---------------|
| `cluster`                 | booleaans | Geeft aan of de functie een cluster vertegenwoordigt. |
| `cluster_id`              | tekenreeks  | Een unieke id voor het cluster die kan worden gebruikt met de methoden `getClusterExpansionZoom`, `getClusterChildren` en `getClusterLeaves` van DataSource. |
| `point_count`             | getal  | Het aantal punten dat het cluster bevat.  |
| `point_count_abbreviated` | tekenreeks  | Een teken reeks die de waarde verkort `point_count` als deze lang is. (4000 wordt bijvoorbeeld 4K).  |

In dit voor beeld wordt een Bubble laag gebruikt die cluster punten rendert en een Click-gebeurtenis toevoegt. Wanneer de knop gebeurtenis triggers wordt geactiveerd, wordt de kaart met de code berekend en ingezoomd op het volgende zoom niveau, waarbij het cluster wordt gesplitst. Deze functionaliteit wordt geïmplementeerd met behulp `getClusterExpansionZoom` van de methode van de `DataSource` klasse en de `cluster_id` eigenschap van het geklikte geclusterde gegevens punt.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Cluster-getClusterExpansionZoom" src="//codepen.io/azuremaps/embed/moZWeV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de Azure Maps <a href='https://codepen.io/azuremaps/pen/moZWeV/'>getClusterExpansionZoom</a> () van het pen-cluster <a href='https://codepen.io/azuremaps'>@azuremaps</a> op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="display-cluster-area"></a>Cluster gebied weer geven 

De punt gegevens die een cluster vertegenwoordigt, worden verdeeld over een gebied. In dit voor beeld wanneer de muis aanwijzer over een cluster wordt gehouden, worden er twee belang rijke gedragingen uitgevoerd. Eerst worden de afzonderlijke gegevens punten in het cluster gebruikt voor het berekenen van een convexe romp. Vervolgens wordt de convexe romp op de kaart weer gegeven om een gebied weer te geven.  Een convexe-romp is een veelhoek die een set punten omregelt zoals een elastische band en kan worden berekend met behulp van de `atlas.math.getConvexHull` methode. Alle punten die deel uitmaken van een cluster kunnen worden opgehaald uit de gegevens bron met behulp van de `getClusterLeaves` methode.

<br/>

 <iframe height="500" style="width: 100%;" scrolling="no" title="Cluster gebied convexe romp" src="//codepen.io/azuremaps/embed/QoXqWJ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie het cluster gebied van de pen <a href='https://codepen.io/azuremaps/pen/QoXqWJ/'>convexe romp</a> per Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="aggregating-data-in-clusters"></a>Gegevens in clusters samen voegen

Vaak worden clusters weer gegeven met behulp van een symbool met het aantal punten dat zich binnen het cluster bevindt. Maar soms is het wenselijk om de stijl van clusters met extra metrische gegevens aan te passen. Met cluster aggregaties kunnen aangepaste eigenschappen worden gemaakt en gevuld met behulp van een [statistische expressie](data-driven-style-expressions-web-sdk.md#aggregate-expression) berekening.  U kunt cluster aggregaties definiëren in `clusterProperties` de optie `DataSource` .

In het volgende voor beeld wordt een statistische expressie gebruikt. De code berekent een telling op basis van de eigenschap entiteit type van elk gegevens punt in een cluster. Wanneer een gebruiker op een cluster klikt, wordt er een pop-upvenster weer gegeven met aanvullende informatie over het cluster.

<iframe height="500" style="width: 100%;" scrolling="no" title="Aggregaties van het cluster" src="//codepen.io/azuremaps/embed/jgYyRL/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Zie de <a href='https://codepen.io/azuremaps/pen/jgYyRL/'>samen voegingen</a> van het pen-Cluster met Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) op <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de klassen en methoden die in dit artikel worden gebruikt:

> [!div class="nextstepaction"]
> [Gegevens bron klasse](/javascript/api/azure-maps-control/atlas.source.datasource)

> [!div class="nextstepaction"]
> [DataSourceOptions-object](/javascript/api/azure-maps-control/atlas.datasourceoptions)

> [!div class="nextstepaction"]
> [naam ruimte van Atlas. math](/javascript/api/azure-maps-control/atlas.math)

Zie code voorbeelden om functionaliteit toe te voegen aan uw app:

> [!div class="nextstepaction"]
> [Een Bubble laag toevoegen](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Een symboollaag toevoegen](map-add-pin.md)

> [!div class="nextstepaction"]
> [Een heatmap-laag toevoegen](map-add-heat-map-layer.md)