---
title: Trans formatie opzoeken in toewijzings gegevens stroom
description: Referentie gegevens uit een andere bron met behulp van de opzoek transformatie in de toewijzing van gegevens stroom.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/19/2021
ms.openlocfilehash: b8754742c572a8dbc1f55c64e47bec640d757d65
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739365"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Trans formatie opzoeken in toewijzings gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de lookup-trans formatie om te verwijzen naar gegevens uit een andere bron in een gegevens stroom stroom. De opzoek transformatie voegt kolommen toe van overeenkomende gegevens aan de bron gegevens.

Een zoek transformatie is vergelijkbaar met een left outer join. Alle rijen van de primaire stroom bevinden zich in de uitvoer stroom met aanvullende kolommen uit de opzoek stroom.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xsVT]

## <a name="configuration"></a>Configuratie

![Scherm afbeelding toont het tabblad Zoek instellingen met de labels die in de volgende tekst worden beschreven.](media/data-flow/lookup1.png "Opzoeken")

**Primaire stroom:** De binnenkomende gegevens stroom. Deze stroom is gelijk aan de linkerkant van een samen voeging.

**Opzoek stroom:** De gegevens die worden toegevoegd aan de primaire stroom. Welke gegevens worden toegevoegd, wordt bepaald door de zoek voorwaarden. Deze stroom is gelijk aan de rechter kant van een koppeling.

**Overeenkomen met meerdere rijen:** Als deze functie is ingeschakeld, retourneert een rij met meerdere overeenkomsten in de primaire stroom meerdere rijen. Anders wordt slechts één rij geretourneerd op basis van de voor waarde match on.

**Zoeken op:** Alleen zichtbaar als ' overeenkomen met meerdere rijen ' niet is geselecteerd. Kies of u wilt zoeken naar een wille keurige rij, de eerste of de laatste overeenkomst. Elke rij wordt aanbevolen omdat deze het snelst uitvoert. Als de eerste rij of laatste rij is geselecteerd, moet u de sorteer voorwaarden opgeven.

**Zoek voorwaarden:** Selecteer de kolommen die u wilt laten overeenkomen. Als aan de gelijkheids voorwaarde wordt voldaan, worden de rijen als een overeenkomst beschouwd. Houd de muis aanwijzer en selecteer berekende kolom om een waarde op te halen met behulp van de taal van de [Data flow-expressie](data-flow-expression-functions.md).

Alle kolommen van beide stromen worden opgenomen in de uitvoer gegevens. Als u dubbele of ongewenste kolommen wilt verwijderen, voegt u een [selectie transformatie](data-flow-select.md) toe na uw lookup-trans formatie. Kolommen kunnen ook worden verwijderd of de naam ervan worden gewijzigd in een Sink-trans formatie.

### <a name="non-equi-joins"></a>Niet-equi-samen voegingen

Als u een voorwaardelijke operator zoals niet gelijk is aan (! =) of groter dan (>) wilt gebruiken in uw zoek voorwaarden, wijzigt u de vervolg keuzelijst van de operator tussen de twee kolommen. Voor niet-equi-join's moeten ten minste één van de twee stromen worden uitgezonden met behulp van **vaste** broadcast op het tabblad **Optimize** .

![Niet-equi lookup](media/data-flow/non-equi-lookup.png "Niet-equi lookup")

## <a name="analyzing-matched-rows"></a>Overeenkomende rijen analyseren

Na uw lookup-trans formatie kan de functie `isMatch()` worden gebruikt om te zien of de zoek opdracht overeenkomt met afzonderlijke rijen.

![Zoek patroon](media/data-flow/lookup111.png "Zoek patroon")

Een voor beeld van dit patroon is het gebruik van de Conditional Split trans formatie voor het splitsen van de `isMatch()` functie. In het bovenstaande voor beeld passeren overeenkomende rijen de bovenste stroom en niet-overeenkomende rijen stromen via de ```NoMatch``` stroom.

## <a name="testing-lookup-conditions"></a>Zoek voorwaarden testen

Gebruik een kleine set bekende gegevens bij het testen van de zoek transformatie met de preview-versie van gegevens in de foutopsporingsmodus. Wanneer u rijen van een grote gegevensset bemonstert, kunt u niet voors pellen welke rijen en sleutels worden gelezen voor testen. Het resultaat is niet-deterministisch, wat betekent dat uw samenvoegings voorwaarden geen overeenkomsten kunnen retour neren.

## <a name="broadcast-optimization"></a>Optimalisatie van broadcast berichten

![Broadcast-koppeling](media/data-flow/broadcast.png "Broadcast-koppeling")

In samen voegingen, zoek acties en bestaande trans formatie, als een of beide gegevens stromen in het geheugen van het worker-knoop punt passen, kunt u de prestaties optimaliseren door **broadcast** in te scha kelen. Standaard wordt door de Spark-Engine automatisch besloten of één zijde al dan niet moet worden uitgezonden. Selecteer **vast** om hand matig te kiezen welke zijde u wilt uitzenden.

Het is niet raadzaam om Broadcasting uit te scha kelen via de optie **uit** , tenzij uw samen voegingen worden uitgevoerd in time-outfouten.

## <a name="cached-lookup"></a>In de cache opgeslagen zoekopdrachten

Als u meerdere kleinere zoek acties uitvoert op dezelfde bron, is een gefilterde Sink en zoek actie mogelijk een betere use-case dan de lookup-trans formatie. Veelvoorkomende voor beelden waarbij een cache-Sink beter is, opzoeken een maximum waarde in een gegevens archief en overeenkomende fout codes aan een Data Base met fout berichten. Meer informatie vindt u in de [cache-sinks](data-flow-sink.md#cache-sink) en [Zoek acties in de cache](concepts-data-flow-expression-builder.md#cached-lookup).

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Voorbeeld

![Scherm afbeelding toont het tabblad Zoek instellingen voor de volgende code.](media/data-flow/lookup-dsl-example.png "Opzoeken")

Het gegevens stroom script voor de bovenstaande opzoek configuratie bevindt zich in het onderstaande code fragment.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Volgende stappen

* De trans formaties [koppelen](data-flow-join.md) en [bestaan](data-flow-exists.md) beide nemen in meerdere stream-invoer
* Een [Conditional Split trans formatie](data-flow-conditional-split.md) gebruiken ```isMatch()``` om rijen te splitsen op overeenkomende en niet-overeenkomende waarden
