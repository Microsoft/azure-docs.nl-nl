---
title: Trans formatie koppelen in gegevens stroom toewijzen
description: Gegevens uit twee gegevens bronnen combi neren met behulp van de deelname transformatie in Azure Data Factory gegevens stroom toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ac84ce17f53145ffd85ffa31b6633d8b4b184962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93042643"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Trans formatie koppelen in gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de koppelings transformatie om gegevens uit twee bronnen of streams te combi neren in een toewijzings gegevens stroom. De uitvoer stroom bevat alle kolommen van beide bronnen die overeenkomen op basis van een samenvoegings voorwaarde. 

## <a name="join-types"></a>Koppelings typen

Toewijzing van gegevens stromen ondersteunt momenteel vijf verschillende jointypen.

### <a name="inner-join"></a>Inner join

Inner join uitvoer rijen met overeenkomende waarden in beide tabellen.

### <a name="left-outer"></a>Linker outer join

Links outer join retourneert alle rijen uit de linker stroom en overeenkomende records uit de juiste stroom. Als een rij uit de linker stroom niet overeenkomt, worden de uitvoer kolommen van de juiste stroom ingesteld op NULL. De uitvoer is de rijen die worden geretourneerd door een inner join plus de niet-overeenkomende rijen uit de linker stroom.

> [!NOTE]
> De Spark-engine die wordt gebruikt door gegevens stromen kan af en toe mislukken vanwege mogelijke Cartesische producten in uw samenvoegings voorwaarden. Als dit het geval is, kunt u overschakelen naar een aangepaste cross-koppeling en de voor waarde voor samen voegen hand matig invoeren. Dit kan leiden tot tragere prestaties in uw gegevens stromen, omdat de engine voor het uitvoeren van de uitvoering mogelijk alle rijen van beide kanten van de relatie moet berekenen en vervolgens rijen filtert.

### <a name="right-outer"></a>Rechter outer join

De rechter outer join retourneert alle rijen uit de juiste stroom en overeenkomende records uit de linker stroom. Als een rij uit de juiste stroom niet overeenkomt, worden de uitvoer kolommen van de linker stroom ingesteld op NULL. De uitvoer is de rijen die worden geretourneerd door een inner join plus de niet-overeenkomende rijen uit de juiste stroom.

### <a name="full-outer"></a>Volledige outer join

Volledige outer join alle kolommen en rijen van beide zijden met NULL-waarden voor kolommen die niet overeenkomen, worden uitgevoerd.

### <a name="custom-cross-join"></a>Aangepaste cross-koppeling

Met cross-koppeling wordt het cross-product van de twee stromen op basis van een voor waarde uitgevoerd. Als u een voor waarde gebruikt die niet gelijk is aan elkaar, geeft u een aangepaste expressie op als cross-samenvoegings voorwaarde. De uitvoer stroom is alle rijen die voldoen aan de voor waarde voor samen voegen.

U kunt dit jointype gebruiken voor niet-equi-samen voegingen en ```OR``` voor waarden.

Als u een volledig Cartesisch product expliciet wilt maken, gebruikt u de afgeleide kolom transformatie in elk van de twee onafhankelijke streams voordat de koppeling wordt gemaakt om een synthetische sleutel te maken die overeenkomt met. Maak bijvoorbeeld een nieuwe kolom in de afgeleide kolom in elke stroom met de naam ```SyntheticKey``` en stel deze in op gelijk aan ```1``` . Gebruik vervolgens ```a.SyntheticKey == b.SyntheticKey``` als uw aangepaste joinexpressie voor samen voegen.

> [!NOTE]
> Zorg ervoor dat u ten minste één kolom toevoegt aan elke zijde van uw linker-en rechter relatie in een aangepaste cross-koppeling. Het uitvoeren van cross-samen voegingen met statische waarden in plaats van kolommen van elke kant resulteert in volledige scans van de hele gegevensset, waardoor uw gegevens stroom slecht kan worden uitgevoerd.

## <a name="configuration"></a>Configuratie

1. Kies in de vervolg keuzelijst **juiste stream** de gegevens stroom waarmee u wilt samen voegen.
1. Het **jointype** selecteren
1. Kies welke sleutel kolommen u wilt vergelijken voor de voor waarde voor samen voegen. De gegevens stroom zoekt standaard naar gelijkheid tussen één kolom in elke stroom. Als u wilt vergelijken via een berekende waarde, houdt u de muis aanwijzer boven de vervolg keuzelijst kolom en selecteert u **berekende kolom**.

![Trans formatie koppelen](media/data-flow/join.png "Deelnemen")

### <a name="non-equi-joins"></a>Niet-equi-samen voegingen

Als u een voorwaardelijke operator zoals niet gelijk is aan (! =) of groter dan (>) wilt gebruiken in uw samenvoegings voorwaarden, wijzigt u de vervolg keuzelijst van de operator tussen de twee kolommen. Voor niet-equi-join's moeten ten minste één van de twee stromen worden uitgezonden met behulp van **vaste** broadcast op het tabblad **Optimize** .

![Niet-equi-koppeling](media/data-flow/non-equi-join.png "Niet-equi-koppeling")

## <a name="optimizing-join-performance"></a>Prestaties van deelname optimaliseren

In tegens telling tot merge-koppeling in hulpprogram ma's als SSIS is de koppelings transformatie geen verplichte samenvoeg bewerking samen voegen. De koppelings sleutels hoeven niet te worden gesorteerd. De koppelings bewerking vindt plaats op basis van de optimale koppelings bewerking in Spark, broadcast of toewijzing aan de kaart zijde.

![Trans formatie optimaliseren](media/data-flow/joinoptimize.png "Deelname aan optimalisatie")

In samen voegingen, zoek acties en bestaande trans formatie, als een of beide gegevens stromen in het geheugen van het worker-knoop punt passen, kunt u de prestaties optimaliseren door **broadcast** in te scha kelen. Standaard wordt door de Spark-Engine automatisch besloten of één zijde al dan niet moet worden uitgezonden. Selecteer **vast** om hand matig te kiezen welke zijde u wilt uitzenden.

Het is niet raadzaam om Broadcasting uit te scha kelen via de optie **uit** , tenzij uw samen voegingen worden uitgevoerd in time-outfouten.

## <a name="self-join"></a>Self-Join

Als u een gegevens stroom met zichzelf wilt samen voegen, maakt u een alias voor een bestaande stroom met een SELECT trans formatie. Maak een nieuwe vertakking door te klikken op het plus pictogram naast een trans formatie en **nieuwe vertakking** te selecteren. Voeg een SELECT trans formatie toe om de oorspronkelijke stroom te aliassen. Voeg een koppelings transformatie toe en kies de oorspronkelijke stroom als de **linker stroom** en de Selecteer trans formatie als de **juiste stroom**.

![Self-deelname](media/data-flow/selfjoin.png "Self-deelname")

## <a name="testing-join-conditions"></a>Voor waarden voor samen voegen testen

Wanneer u de trans formaties van de koppeling met de preview-versie van gegevens in de foutopsporingsmodus test, moet u een kleine set bekende gegevens gebruiken. Wanneer u rijen van een grote gegevensset bemonstert, kunt u niet voors pellen welke rijen en sleutels worden gelezen voor testen. Het resultaat is niet-deterministisch, wat betekent dat uw samenvoegings voorwaarden geen overeenkomsten kunnen retour neren.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Voor beeld van inner join

Het onderstaande voor beeld is een koppelings transformatie met de naam `JoinMatchedData` die de stream `TripData` en de juiste stroom gebruikt `TripFare` .  De voor waarde voor samen voegen is de expressie `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` die waar retourneert als de `hack_license` `medallion` kolommen,, `vendor_id` en `pickup_datetime` in elke stroom overeenkomen. De `joinType` is `'inner'` . Het inschakelen van de uitzending in alleen de linker stroom `broadcast` heeft een waarde `'left'` .

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Scherm afbeelding toont de trans formatie met het tabblad Instellingen voor samen voegen geselecteerd en het join-type Inner.](media/data-flow/join-script1.png "Voor beeld van koppeling")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>Voor beeld van aangepaste cross-koppeling

Het onderstaande voor beeld is een koppelings transformatie met de naam `JoiningColumns` die de stream `LeftStream` en de juiste stroom gebruikt `RightStream` . Deze trans formatie heeft twee stromen en voegt samen met alle rijen waarin de kolom `leftstreamcolumn` groter is dan de kolom `rightstreamcolumn` . De `joinType` is `cross` . Broadcasting is niet ingeschakeld `broadcast` heeft waarde `'none'` .

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Scherm afbeelding toont de trans formatie met het tabblad Instellingen voor samen voegen geselecteerd en het jointype aangepast (kruis).](media/data-flow/join-script2.png "Voor beeld van koppeling")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Volgende stappen

Nadat u gegevens hebt toegevoegd, maakt u een [afgeleide kolom](data-flow-derived-column.md) en [sinkt](data-flow-sink.md) u uw gegevens naar een doel gegevens archief.
