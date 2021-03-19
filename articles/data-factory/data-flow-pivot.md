---
title: Draai transformatie in toewijzings gegevens stroom
description: Gegevens uit rijen naar kolommen draaien met Azure Data Factory de draai transformatie van de gegevens stroom koppelen
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: e098182c000cbe05df533434a41c55b797ef876f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87086656"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Draai transformatie in toewijzings gegevens stroom


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de draai transformatie om meerdere kolommen te maken op basis van de unieke rijwaarden van één kolom. Pivot is een aggregatie transformatie waarbij u groeperen op kolommen selecteert en draai tabel kolommen genereert met behulp van [statistische functies](data-flow-expression-functions.md#aggregate-functions).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>Configuratie

Voor de draai transformatie zijn drie verschillende invoer waarden vereist: groeperen op kolommen, de draai sleutel en het genereren van de gedraaide kolommen

### <a name="group-by"></a>Groeperen op

![Groeperen op opties](media/data-flow/pivot2.png "Groeperen op opties")

Selecteer de kolommen waarin de gedraaide kolommen moeten worden geaggregeerd. In de uitvoer gegevens worden alle rijen met dezelfde groep door waarden gegroepeerd in één rij. De aggregatie die in de gedraaide kolom wordt uitgevoerd, vindt plaats boven elke groep.

Deze sectie is optioneel. Als er geen kolommen voor groeperen op zijn geselecteerd, wordt de volledige gegevens stroom geaggregeerd en wordt er slechts één rij gegenereerd.

### <a name="pivot-key"></a>Draai sleutel

![Draai sleutel](media/data-flow/pivot3.png "Draai sleutel")

De draai tabel is de kolom waarvan de rijwaarden in nieuwe kolommen worden weer geven. Standaard maakt de draai transformatie een nieuwe kolom voor elke unieke Rijwaarde.

In de sectie met de label **waarde** kunt u specifieke rijgegevens opgeven die moeten worden gedraaid. Alleen de rijwaarden die in deze sectie worden ingevoerd, worden gedraaid. Als u een **Null-waarde** inschakelt, wordt er een gedraaide kolom gemaakt voor de Null-waarden in de kolom.

### <a name="pivoted-columns"></a>Gedraaide kolommen

![Gedraaide kolommen](media/data-flow/pivot4.png "Gedraaide kolommen")

Genereer voor elke unieke draai tabel waarde die een kolom wordt een geaggregeerde Rijwaarde voor elke groep. U kunt meerdere kolommen per draai sleutel maken. Elke draai kolom moet ten minste één [statistische functie](data-flow-expression-functions.md#aggregate-functions)bevatten.

**Patroon voor kolom naam:** Selecteren hoe de kolom naam van elke draai kolom moet worden opgemaakt. De kolom naam die u hebt gegenereerd, is een combi natie van de waarde van de draai sleutel, het voor voegsel van de kolom en het optionele voor voegsel, voldoende, middelste tekens. 

Volg **orde van kolommen:** Als u per draai tabel meer dan één draai kolom wilt genereren, kiest u hoe u de kolommen wilt belasten. 

**Kolom voorvoegsel:** Als u per draai tabel meer dan één draai kolom wilt genereren, voert u een kolom voorvoegsel in voor elke kolom. Deze instelling is optioneel als u slechts één gedraaide kolom hebt.

## <a name="help-graphic"></a>Help-afbeelding

De onderstaande Help-afbeelding laat zien hoe de verschillende draai grafiek onderdelen met elkaar communiceren

![Help grafische objecten draaien](media/data-flow/pivot5.png "Help-afbeelding van Pivot")

## <a name="pivot-metadata"></a>Meta gegevens van draai tabel

Als er geen waarden zijn opgegeven in de configuratie van de draai sleutel, worden de gedraaide kolommen dynamisch gegenereerd tijdens runtime. Het aantal gedraaide kolommen is gelijk aan het aantal unieke draai sleutel waarden vermenigvuldigd met het aantal draai tabel kolommen. Aangezien dit een wijzigings nummer kan zijn, worden de meta gegevens van de kolom niet weer gegeven in de UX op het tabblad **controleren** en is er geen doorgifte van de kolom. Als u deze kolommen wilt transformeren, gebruikt u de [kolom patroon](concepts-data-flow-column-pattern.md) mogelijkheden van toewijzing van gegevens stroom. 

Als er specifieke waarden van de draai tabel worden ingesteld, worden de gedraaide kolommen weer gegeven in de meta gegevens. De kolom namen worden voor u beschikbaar gesteld in de toewijzingen controleren en wastafel.

### <a name="generate-metadata-from-drifted-columns"></a>Meta gegevens genereren op basis van gedrijfte kolommen

Met Pivot worden nieuwe kolom namen dynamisch gegenereerd op basis van rijwaarden. U kunt deze nieuwe kolommen toevoegen aan de meta gegevens waarnaar later in uw gegevens stroom kan worden verwezen. U doet dit door de snelle actie [toewijzen](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) in de voorbeeld gegevens te gebruiken. 

![Draaikolommen](media/data-flow/newpivot1.png "Geplaatste draai kolommen toewijzen")

### <a name="sinking-pivoted-columns"></a>Zinkt gedraaide kolommen

Hoewel gedraaide kolommen dynamisch zijn, kunnen ze nog steeds worden geschreven in de doel gegevens opslag. Schakel **schema-drift toestaan** in uw Sink-instellingen in. Hiermee kunt u kolommen schrijven die niet zijn opgenomen in de meta gegevens. De nieuwe dynamische namen worden niet weer gegeven in de meta gegevens van de kolom, maar met de optie schema-drift kunt u de gegevens in het land brengen.

### <a name="rejoin-original-fields"></a>Oorspronkelijke velden opnieuw samen voegen

Met de draai transformatie worden alleen de groep gegroepeerd op en de gedraaide kolommen geprojecteerd. Als u wilt dat uw uitvoer gegevens andere invoer kolommen bevatten, gebruikt u een [Self-deel nemende](data-flow-join.md#self-join) patroon.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Voorbeeld

De schermen die in de configuratie sectie worden weer gegeven, hebben het volgende gegevens stroom script:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Volgende stappen

Voer de [trans formatie van het draai diagram](data-flow-unpivot.md) uit om kolom waarden om te zetten in rijwaarden. 
