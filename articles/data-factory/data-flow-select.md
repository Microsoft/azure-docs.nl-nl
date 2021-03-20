---
title: Trans formatie selecteren in gegevens stroom toewijzen
description: Trans formatie Azure Data Factory toewijzing van gegevens stroom selecteren
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 2d8c4d1915e22ccabf193f1b34c5fc4797ead549
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040236"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Trans formatie selecteren in gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de optie trans formatie selecteren om de kolommen te hernoemen, te verwijderen of opnieuw in te delen. Met deze trans formatie worden geen rijgegevens gewijzigd, maar wordt gekozen welke kolommen downstream worden door gegeven. 

In een SELECT-trans formatie kunnen gebruikers vaste toewijzingen opgeven, patronen gebruiken om toewijzingen op basis van een regel te maken of automatische toewijzing in te scha kelen. Vaste en op regels gebaseerde toewijzingen kunnen beide worden gebruikt binnen dezelfde trans formatie selecteren. Als een kolom niet overeenkomt met een van de gedefinieerde toewijzingen, wordt deze verwijderd.

## <a name="fixed-mapping"></a>Vaste toewijzing

Als er minder dan 50 kolommen in de projectie zijn gedefinieerd, krijgen alle gedefinieerde kolommen standaard een vaste toewijzing. Een vaste toewijzing heeft een gedefinieerde, binnenkomende kolom en wijst deze een exacte naam toe.

![Vaste toewijzing](media/data-flow/fixedmapping.png "Vaste toewijzing")

> [!NOTE]
> U kunt een kolom met vaste toewijzing niet toewijzen of de naam ervan wijzigen

### <a name="mapping-hierarchical-columns"></a>Hiërarchische kolommen toewijzen

Vaste toewijzingen kunnen worden gebruikt om een subkolom van een hiërarchische kolom toe te wijzen aan een kolom op het hoogste niveau. Als u een gedefinieerde hiërarchie hebt, gebruikt u de vervolg keuzelijst voor kolommen om een subkolom te selecteren. Met de trans formatie selecteren wordt een nieuwe kolom gemaakt met de waarde en het gegevens type van de subkolom.

![hiërarchische toewijzing](media/data-flow/select-hierarchy.png "hiërarchische toewijzing")

## <a name="rule-based-mapping"></a>Toewijzing op basis van een regel


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

Als u veel kolommen tegelijk wilt toewijzen of geplaatste kolommen downstream wilt gebruiken, gebruikt u toewijzing op basis van regels om uw toewijzingen te definiëren met behulp van kolom patronen. Overeenkomst op basis van `name` de `type` kolommen,, en `stream` `position` . U kunt een combi natie van vaste en op regels gebaseerde toewijzingen hebben. Standaard worden alle projecties met meer dan 50 kolommen standaard ingesteld op een op een regel gebaseerde toewijzing die overeenkomt met elke kolom en de naam die is gegenereerd, wordt uitgevoerd. 

Als u een op een regel gebaseerde toewijzing wilt toevoegen, klikt u op **toewijzing toevoegen** en selecteert u **toewijzing op basis van een regel**.

![Scherm afbeelding toont toewijzing op basis van een regel die is geselecteerd vanuit toewijzing toevoegen.](media/data-flow/rule2.png "Toewijzing op basis van een regel")

Voor elke op een regel gebaseerde toewijzing zijn twee invoer waarden vereist: de voor waarde waaraan moet worden voldaan en de naam van elke toegewezen kolom. Beide waarden worden gegenereerd via de [opbouw functie voor expressies](concepts-data-flow-expression-builder.md). Voer in het vak linker expressie de voor waarde Boole match in. Geef in het vak rechter expressie op aan welke overeenkomende kolom wordt toegewezen.

![Scherm afbeelding toont een toewijzing.](media/data-flow/rule-based-mapping.png "Toewijzing op basis van een regel")

Gebruik `$$` syntaxis om te verwijzen naar de invoer naam van een overeenkomende kolom. Gebruik de bovenstaande afbeelding als voor beeld. Stel dat een gebruiker wilt zoeken op alle teken reeks kolommen waarvan de namen korter zijn dan zes tekens. Als de naam van één binnenkomende kolom is opgegeven `test` , `$$ + '_short'` wordt de naam van de kolom gewijzigd in de expressie `test_short` . Als dat de enige toewijzing is die bestaat, worden alle kolommen verwijderd die niet aan de voor waarde voldoen.

Patronen komen overeen met zowel gedrijfde als gedefinieerde kolommen. Als u wilt zien welke gedefinieerde kolommen worden toegewezen door een regel, klikt u op het pictogram bril naast de regel. Controleer uw uitvoer met behulp van de voorbeeld gegevens.

### <a name="regex-mapping"></a>Regex-toewijzing

Als u op het pictogram met de pijl omlaag klikt, kunt u een voor waarde voor regex-toewijzing opgeven. Een regex-toewijzings voorwaarde komt overeen met alle kolom namen die overeenkomen met de opgegeven regex-voor waarde. Dit kan worden gebruikt in combi natie met standaard toewijzingen op basis van regels.

![Scherm afbeelding toont de regex-toewijzings voorwaarde met hiërarchie niveau en naam overeenkomsten.](media/data-flow/regex-matching.png "Toewijzing op basis van een regel")

Het bovenstaande voor beeld komt overeen met het regex-patroon `(r)` of een kolom naam die een kleine letter r bevat. Net als bij standaard toewijzing op basis van regels worden alle overeenkomende kolommen gewijzigd aan de hand van de voor waarde aan de rechter kant met de `$$` syntaxis.

Als u meerdere regex-overeenkomsten in uw kolom naam hebt, kunt u verwijzen naar specifieke overeenkomsten waarbij `$n` ' n ' verwijst naar de overeenkomst. Bijvoorbeeld: ' $2 ' verwijst naar de tweede overeenkomst in een kolom naam.

### <a name="rule-based-hierarchies"></a>Op regels gebaseerde hiërarchieën

Als uw gedefinieerde projectie een hiërarchie heeft, kunt u toewijzing op basis van een regel gebruiken om de hiërarchieën te koppelen. Geef een overeenkomende voor waarde en de complexe kolom op waarvan u de subkolomen wilt toewijzen. Elke overeenkomende subkolom wordt gegenereerd met de regel ' naam als ' die aan de rechter kant is opgegeven.

![Scherm afbeelding toont een op regels gebaseerde toewijzing die voor een hiërarchie wordt gebruikt.](media/data-flow/rule-based-hierarchy.png "Toewijzing op basis van een regel")

Het bovenstaande voor beeld komt overeen met alle subkolomsen van een complexe kolom `a` . `a` bevat twee subkoloms `b` en `c` . Het uitvoer schema bevat twee kolommen `b` en `c` de voor waarde ' name als ' is `$$` .

### <a name="parameterization"></a>Parameterisering

U kunt kolom namen para meters met toewijzing op basis van een regel. Gebruik het sleutel woord ```name``` om binnenkomende kolom namen te vergelijken met een para meter. Als u bijvoorbeeld een para meter voor de gegevens stroom hebt ```mycolumn``` , kunt u een regel maken die overeenkomt met een kolom naam die gelijk is aan ```mycolumn``` . U kunt de naam van de overeenkomende kolom wijzigen in een in code vastgelegde teken reeks, zoals ' bedrijfs sleutel ', en ernaar verwijzen. In dit voor beeld is de voor waarde matching ```name == $mycolumn``` en de naam voorwaarde is bedrijfs sleutel. 

## <a name="auto-mapping"></a>Automatische toewijzing

Bij het toevoegen van een SELECT-trans formatie kan **automatische toewijzing** worden ingeschakeld door de schuif regelaar voor automatisch toewijzen te wijzigen. Als automatische toewijzing is ingesteld, worden alle binnenkomende kolommen met de optie trans formatie toegewezen, met uitzonde ring van duplicaten, met dezelfde naam als de invoer. Dit geldt ook voor gedrijfde kolommen. Dit betekent dat de uitvoer gegevens kolommen bevatten die niet in uw schema zijn gedefinieerd. Zie [schema drift](concepts-data-flow-schema-drift.md)voor meer informatie over gedrijfde kolommen.

![Automatische toewijzing](media/data-flow/automap.png "Automatische toewijzing")

Als automatische toewijzing is ingeschakeld, wordt de optie dubbele instellingen overs Laan en een nieuwe alias voor de bestaande kolommen weer geven. Aliasing is handig bij het uitvoeren van meerdere samen voegingen of lookups op dezelfde stroom en in Self-samenvoegings scenario's. 

## <a name="duplicate-columns"></a>Dubbele kolommen

De Select-trans formatie drupt standaard dubbele kolommen in zowel de invoer-als uitvoer projectie. Dubbele invoer kolommen komen vaak voort uit deel nemen en lookup-trans formaties waarbij kolom namen worden gedupliceerd aan elke zijde van de koppeling. Er kunnen dubbele uitvoer kolommen optreden als u twee verschillende invoer kolommen aan dezelfde naam toewijst. Kies of u dubbele kolommen wilt neerzetten of door te geven door het selectie vakje in te scha kelen.

![Dubbele items overs Laan](media/data-flow/select-skip-dup.png "Dubbele items overs Laan")

## <a name="ordering-of-columns"></a>Volg orde van kolommen

De volg orde van toewijzingen bepaalt de volg orde van de uitvoer kolommen. Als een invoer kolom meerdere keren wordt toegewezen, wordt alleen de eerste toewijzing gehonoreerd. Voor een dubbele kolom die wordt neergezet, wordt de eerste overeenkomst bewaard.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Voorbeeld

Hieronder ziet u een voor beeld van een SELECT-toewijzing en het gegevens stroom script:

![Script voorbeeld selecteren](media/data-flow/select-script-example.png "Script voorbeeld selecteren")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Volgende stappen
* Gebruik de [sink-trans formatie](data-flow-sink.md) om uw gegevens in een gegevens archief in te voeren nadat u selecteren voor naam wijzigen, volg orde en alias kolommen hebt gebruikt.
