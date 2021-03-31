---
title: Er bestaat een trans formatie in de toewijzing van gegevens stroom
description: Controleren op bestaande rijen met de trans formatie exists in Azure Data Factory gegevens stroom toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82982629"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Er bestaat een trans formatie in de toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De exists-trans formatie is een filter transformatie voor rijen waarmee wordt gecontroleerd of uw gegevens zich in een andere bron of stroom bevindt. De uitvoer stroom bevat alle rijen in de linker stroom die ofwel bestaan of niet voor komen in de juiste stroom. De exists-trans formatie is vergelijkbaar met ```SQL WHERE EXISTS``` en ```SQL WHERE NOT EXISTS``` .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Configuratie

1. Kies welke gegevens stroom u in de vervolg keuzelijst naar de **juiste stroom** wilt controleren.
1. Geef op of u wilt dat de gegevens bestaan of niet aanwezig zijn in de instelling voor het **bestaande type** .
1. Selecteer of u een **aangepaste expressie** wilt of niet.
1. Kies welke sleutel kolommen u als voor waarden wilt vergelijken. De gegevens stroom zoekt standaard naar gelijkheid tussen één kolom in elke stroom. Als u wilt vergelijken via een berekende waarde, houdt u de muis aanwijzer boven de vervolg keuzelijst kolom en selecteert u **berekende kolom**.

![Instellingen voor exists](media/data-flow/exists.png "bestaat 1")

### <a name="multiple-exists-conditions"></a>Meerdere voor waarden voor bestaan

Als u meerdere kolommen van elke stroom wilt vergelijken, voegt u een nieuwe exists-voor waarde toe door op het plus pictogram naast een bestaande rij te klikken. Elke aanvullende voor waarde wordt gekoppeld door een and-instructie. Vergelijken van twee kolommen is hetzelfde als de volgende expressie:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Aangepaste expressie

Als u een vrije-vorm expressie wilt maken die andere opera tors dan ' en ' en ' is gelijk aan ' bevat, selecteert u het veld **aangepaste expressie** . Voer een aangepaste expressie in via de opbouw functie voor de data flow-expressie door te klikken op het blauwe vak.

![Aangepaste instellingen bestaan](media/data-flow/exists1.png "bestaat aangepast")

## <a name="broadcast-optimization"></a>Optimalisatie van broadcast berichten

![Broadcast-koppeling](media/data-flow/broadcast.png "Broadcast-koppeling")

In samen voegingen, zoek acties en bestaande trans formatie, als een of beide gegevens stromen in het geheugen van het worker-knoop punt passen, kunt u de prestaties optimaliseren door **broadcast** in te scha kelen. Standaard wordt door de Spark-Engine automatisch besloten of één zijde al dan niet moet worden uitgezonden. Selecteer **vast** om hand matig te kiezen welke zijde u wilt uitzenden.

Het is niet raadzaam om Broadcasting uit te scha kelen via de optie **uit** , tenzij uw samen voegingen worden uitgevoerd in time-outfouten.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Voorbeeld

Het onderstaande voor beeld is een bestaande trans formatie met de naam die de stream `checkForChanges` `NameNorm2` en de juiste stroom gebruikt `TypeConversions` .  De exists-voor waarde is de expressie `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` die waar retourneert als de `EMPID` `Region` kolommen en in elke stroom overeenkomen. Als we controleren op bestaan, `negate` is onwaar. Het inschakelen van een broadcast op het tabblad Optimize heeft daarom geen `broadcast` waarde `'none'` .

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Voor beeld van exist](media/data-flow/exists-script.png "Voor beeld van exist")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Volgende stappen

Vergelijk bare trans formaties zijn [Opzoeken](data-flow-lookup.md) en [samen voegen](data-flow-join.md).
