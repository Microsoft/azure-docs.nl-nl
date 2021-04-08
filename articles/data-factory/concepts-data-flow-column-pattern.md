---
title: Kolom patronen in de gegevens stroom van Azure Data Factory toewijzing
description: Gegeneraliseerde gegevens transformatie patronen maken met behulp van kolom patronen in Azure Data Factory gegevens stromen toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 68c211608cfceedaa9d13a595be6d1e5de17f1d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94845000"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Kolom patronen gebruiken bij het toewijzen van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met verschillende toewijzings gegevensstroom transformaties kunt u verwijzen naar sjabloon kolommen op basis van patronen in plaats van in code vastgelegde kolom namen. Deze overeenkomst wordt ook wel *kolom patronen* genoemd. U kunt patronen definiëren die overeenkomen met kolommen op basis van naam, gegevens type, stroom, oorsprong of positie, in plaats van exacte veld namen te vereisen. Er zijn twee scenario's waarin kolom patronen handig zijn:

* Als binnenkomende bron velden vaak veranderen, zoals het wijzigen van kolommen in tekst bestanden of NoSQL-data bases. Dit scenario wordt schema- [drift](concepts-data-flow-schema-drift.md)genoemd.
* Als u een gemeen schappelijke bewerking wilt uitvoeren op een grote groep kolommen. Bijvoorbeeld wilt dat elke kolom met ' Total ' in een dubbele waarde wordt omgezet in de kolom naam.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Kolom patronen in afgeleide kolom en aggregatie

Als u een kolom patroon wilt toevoegen in een afgeleide kolom, aggregatie of venster transformatie, klikt u **op boven de** kolom lijst of het plus-pictogram naast een bestaande afgeleide kolom. Kies **kolom patroon toevoegen**.

![Scherm afbeelding toont het plus pictogram om het kolom patroon toe te voegen.](media/data-flow/add-column-pattern.png "Kolompatronen")

Gebruik de [expressie Builder](concepts-data-flow-expression-builder.md) om de match-voor waarde in te voeren. Maak een Boole-expressie die overeenkomt met kolommen op basis van de `name` ,, `type` `stream` , `origin` en `position` van de kolom. Het patroon is van invloed op een kolom, die is geplaatste of gedefinieerd, waarbij de voor waarde waar retourneert.

De twee expressie vakken onder de voor waarde match geven de nieuwe namen en waarden van de betrokken kolommen op. `$$`Wordt gebruikt om te verwijzen naar de bestaande waarde van het overeenkomende veld. In het vak linker expressie definieert u de naam en de rechter expressie Box definieert de waarde.

![Scherm afbeelding toont het tabblad instellingen van de afgeleide kolom.](media/data-flow/edit-column-pattern.png "Kolompatronen")

Het bovenstaande kolom patroon komt overeen met elke kolom van het type Double en maakt één afgeleide kolom per overeenkomst. `$$`Als het veld kolom naam wordt vermeld, wordt elke overeenkomende kolom met dezelfde naam bijgewerkt. De waarde van elke kolom is de bestaande waarde afgerond op twee decimale punten.

Als u wilt controleren of uw overeenkomende voor waarde juist is, kunt u het uitvoer schema van gedefinieerde kolommen valideren in het tabblad **controleren** of een moment opname van de gegevens op het tabblad **voor het voor beeld van gegevens** ophalen. 

![Scherm afbeelding toont het tabblad uitvoer schema.](media/data-flow/columnpattern3.png "Kolompatronen")

### <a name="hierarchical-pattern-matching"></a>Hiërarchisch patroon vergelijking

U kunt ook patroon vergelijking maken binnen complexe hiërarchische structuren. Vouw de sectie uit `Each MoviesStruct that matches` waarin u wordt gevraagd naar elke hiërarchie in uw gegevens stroom. U kunt vervolgens overeenkomende patronen maken voor eigenschappen binnen de gekozen hiërarchie.

![Scherm opname toont het hiërarchische kolom patroon.](media/data-flow/patterns-hierarchy.png "Kolom patronen in hiërarchieën")

## <a name="rule-based-mapping-in-select-and-sink"></a>Toewijzing op basis van een regel in SELECT en Sink

Bij het toewijzen van kolommen in de bron en het selecteren van trans formaties, kunt u een vaste toewijzing of op regels gebaseerde toewijzingen toevoegen. Overeenkomst op basis van `name` de `type` kolommen,, `stream` , `origin` en `position` . U kunt een combi natie van vaste en op regels gebaseerde toewijzingen hebben. Standaard worden alle projecties met meer dan 50 kolommen standaard ingesteld op een op een regel gebaseerde toewijzing die overeenkomt met elke kolom en de naam die is gegenereerd, wordt uitgevoerd. 

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

### <a name="rule-based-hierarchies"></a>Op regels gebaseerde hiërarchieën

Als uw gedefinieerde projectie een hiërarchie heeft, kunt u toewijzing op basis van een regel gebruiken om de hiërarchieën te koppelen. Geef een overeenkomende voor waarde en de complexe kolom op waarvan u de subkolomen wilt toewijzen. Elke overeenkomende subkolom wordt gegenereerd met de regel ' naam als ' die aan de rechter kant is opgegeven.

![Scherm afbeelding toont een op regels gebaseerde toewijzing die voor een hiërarchie wordt gebruikt.](media/data-flow/rule-based-hierarchy.png "Toewijzing op basis van een regel")

Het bovenstaande voor beeld komt overeen met alle subkolomsen van een complexe kolom `a` . `a` bevat twee subkoloms `b` en `c` . Het uitvoer schema bevat twee kolommen `b` en `c` de voor waarde ' name als ' is `$$` .

## <a name="pattern-matching-expression-values"></a>Patroon waarden die overeenkomen met de expressie.

* `$$` wordt omgezet naar de naam of waarde van elk match tijdens runtime. Denk aan `$$` als gelijk aan `this` .
* `name` vertegenwoordigt de naam van elke binnenkomende kolom
* `type` vertegenwoordigt het gegevens type van elke binnenkomende kolom
* `stream` vertegenwoordigt de naam die is gekoppeld aan elke stroom of trans formatie in uw stroom
* `position` is het Volg nummer van de kolommen in uw gegevens stroom
* `origin` is de trans formatie waarbij een kolom afkomstig is of het laatst is bijgewerkt

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de data flow- [expressie](data-flow-expression-functions.md) van de toewijzings gegevens voor gegevens transformaties
* Kolom patronen in de [sink-trans formatie](data-flow-sink.md) gebruiken en [trans formatie selecteren](data-flow-select.md) met toewijzing op basis van een regel
