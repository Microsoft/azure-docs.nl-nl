---
title: Toewijzingsgegevensstromen parameteriseren
description: Meer informatie over het para meters van een toewijzings gegevens stroom van data factory-pijp lijnen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 564c7cf6e9627db08d543b964ce476e71bfb473d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93040754"
---
# <a name="parameterizing-mapping-data-flows"></a>Toewijzingsgegevensstromen parameteriseren

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Het toewijzen van gegevens stromen in Azure Data Factory ondersteunen het gebruik van para meters. Definieer para meters in de definitie van de gegevens stroom en gebruik deze in uw expressies. De parameter waarden worden ingesteld door de aanroepende pijp lijn via de activiteit gegevens stroom uitvoeren. U hebt drie opties voor het instellen van de waarden in de gegevens stroom activiteit expressies:

* De expressie taal voor de controle stroom van de pijp lijn gebruiken om een dynamische waarde in te stellen
* De data flow-expressie taal gebruiken om een dynamische waarde in te stellen
* Een van de expressie taal gebruiken om een statische letterlijke waarde in te stellen

Gebruik deze mogelijkheid om uw gegevens te stroom, flexibel en herbruikbaar te maken. U kunt para meters en expressies voor gegevens stromen met deze para meters.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Para meters maken in een toewijzings gegevens stroom

Als u para meters wilt toevoegen aan uw gegevens stroom, klikt u op het lege deel van het canvas voor gegevens stromen om de algemene eigenschappen weer te geven. In het deel venster instellingen ziet u een tabblad met de naam **para meter**. Selecteer **Nieuw** om een nieuwe para meter te genereren. Voor elke para meter moet u een naam toewijzen, een type selecteren en optioneel een standaard waarde instellen.

![Data flow-para meters maken](media/data-flow/create-params.png "Data flow-para meters maken")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Para meters in een toewijzings gegevens stroom gebruiken 

In elke gegevens stroom expressie kan naar para meters worden verwezen. Para meters beginnen met $ en zijn onveranderbaar. De lijst met beschik bare para meters kunt u vinden in de opbouw functie voor expressies op het tabblad **para meters** .

![Scherm afbeelding toont de beschik bare para meters op het tabblad para meters.](media/data-flow/parameter-expression.png "Expressie voor de gegevens stroom parameter")

U kunt snel extra para meters toevoegen door **nieuwe para meter** te selecteren en de naam en het type op te geven.

![Scherm afbeelding toont de para meters op het tabblad para meters met nieuwe para meters toegevoegd.](media/data-flow/new-parameter-expression.png "Expressie voor de gegevens stroom parameter")

## <a name="assign-parameter-values-from-a-pipeline"></a>Parameter waarden toewijzen vanuit een pijp lijn

Zodra u een gegevens stroom met para meters hebt gemaakt, kunt u deze uitvoeren vanuit een pijp lijn met de activiteit gegevens stroom uitvoeren. Nadat u de activiteit aan uw pijplijn doek hebt toegevoegd, worden de beschik bare gegevensstroom parameters weer gegeven op het tabblad **para meters** van de activiteit.

Bij het toewijzen van parameter waarden kunt u de taal van de [pijplijn expressie](control-flow-expression-language-functions.md) of de [Data flow-expressie taal](data-flow-expression-functions.md) gebruiken op basis van Spark-typen. Elke toewijzings gegevens stroom kan een combi natie van de para meters van de pijplijn-en data flow-expressie hebben.

![Scherm afbeelding toont het tabblad para meters met de data flow-expressie die is geselecteerd voor de waarde van myParam.](media/data-flow/parameter-assign.png "Een para meter voor een gegevens stroom instellen")

### <a name="pipeline-expression-parameters"></a>Para meters voor pijplijn expressie

Met para meters voor de pijplijn expressie kunt u verwijzen naar systeem variabelen, functies, pijplijn parameters en variabelen die vergelijkbaar zijn met andere pijplijn activiteiten. Wanneer u op **pijplijn expressie** klikt, wordt een kantlijn navigatie geopend, zodat u een expressie kunt invoeren met de opbouw functie voor expressies.

![Scherm afbeelding toont het deel venster opbouw functie voor expressies.](media/data-flow/parameter-pipeline.png "Een para meter voor een gegevens stroom instellen")

Wanneer ernaar wordt verwezen, worden de pijplijn parameters geëvalueerd en wordt de waarde ervan gebruikt in de taal van de gegevens stroom expressie. Het type pijplijn expressie hoeft niet overeen te komen met het parameter type van de gegevens stroom. 

#### <a name="string-literals-vs-expressions"></a>Letterlijke teken reeksen versus expressies

Wanneer u een para meter voor een pijplijn expressie van het type teken reeks toewijst, worden standaard aanhalings tekens toegevoegd en wordt de waarde geëvalueerd als een literal. Als u de parameter waarde als een gegevensstroom expressie wilt lezen, schakelt u het selectie vakje expressie naast de para meter in.

![Scherm afbeelding toont de weer gave-expressie voor het deel venster gegevens stroom die is geselecteerd voor een para meter.](media/data-flow/string-parameter.png "Een para meter voor een gegevens stroom instellen")

Als de para meter data flow `stringParam` verwijst naar een pijplijn parameter met een waarde `upper(column1)` . 

- Als expressie is ingeschakeld, `$stringParam` evalueert de waarde van Kolom1 alle hoofd letters.
- Als expressie niet is ingeschakeld (standaard gedrag),  `$stringParam` evalueert naar `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Door geven van tijds tempels

In de taal van de pipeline-expressie, systeem variabelen zoals `pipeline().TriggerTime` en functions, zoals `utcNow()` retour tempels als teken reeksen in de notatie jjjj-mm-dd \' T \' uu: mm: SS. SSSSSSZ'. Als u deze wilt converteren naar de gegevensstroom parameters van het type Time Stamp, gebruikt u interpolatie van teken reeksen om de gewenste tijds tempel in een functie op te genomen `toTimestamp()` . U kunt bijvoorbeeld gebruiken om de trigger tijd van de pijp lijn om te zetten in een para meter voor de gegevens stroom `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Scherm afbeelding toont het tabblad para meters waar u een trigger tijd kunt invoeren.](media/data-flow/parameter-timestamp.png "Een para meter voor een gegevens stroom instellen")

> [!NOTE]
> Gegevens stromen kunnen Maxi maal drie milliseconde cijfers ondersteunen. De `left()` functie wordt gebruikt om extra cijfers uit te snijden.

#### <a name="pipeline-parameter-example"></a>Voor beeld van pijplijn parameter

Stel dat u een integer-para meter hebt `intParam` die verwijst naar een pijplijn parameter van het type teken reeks, `@pipeline.parameters.pipelineParam` . 

![Scherm afbeelding toont het tabblad para meters met de para meters met de naam stringParam en intParam.](media/data-flow/parameter-pipeline-2.png "Een para meter voor een gegevens stroom instellen")

`@pipeline.parameters.pipelineParam` wordt tijdens runtime een waarde van toegewezen `abs(1)` .

![Scherm afbeelding toont het tabblad para meters met de waarde a b s (1) geselecteerd.](media/data-flow/parameter-pipeline-4.png "Een para meter voor een gegevens stroom instellen")

Wanneer `$intParam` wordt verwezen in een expressie, zoals een afgeleide kolom, wordt `abs(1)` return geëvalueerd `1` . 

![Scherm afbeelding toont de waarde columns.](media/data-flow/parameter-pipeline-3.png "Een para meter voor een gegevens stroom instellen")

### <a name="data-flow-expression-parameters"></a>Para meters voor de gegevens stroom expressie

Met de **expressie gegevens stroom** selecteren wordt de opbouw functie voor de data flow-expressie geopend. U kunt in uw gegevens stroom verwijzen naar functies, andere para meters en elke gedefinieerde schema kolom. Deze expressie wordt geëvalueerd alsof deze wordt genoemd.

> [!NOTE]
> Als u een ongeldige expressie doorgeeft of verwijst naar een schema kolom die niet in die trans formatie voor komt, wordt de para meter geëvalueerd naar null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Het door geven van een kolom naam als para meter

Een gemeen schappelijk patroon is het door geven van een kolom naam als een parameter waarde. Als de kolom is gedefinieerd in het schema van de gegevens stroom, kunt u deze rechtstreeks als een teken reeks expressie verwijzen. Als de kolom niet in het schema is gedefinieerd, gebruikt u de `byName()` functie. Vergeet niet om de kolom naar het juiste type te casten met een cast-functie zoals `toString()` .

Als u bijvoorbeeld een teken reeks kolom wilt toewijzen op basis van een para meter `columnName` , kunt u een afgeleide kolom transformatie toevoegen gelijk aan `toString(byName($columnName))` .

![Het door geven van een kolom naam als para meter](media/data-flow/parameterize-column-name.png "Het door geven van een kolom naam als para meter")

## <a name="next-steps"></a>Volgende stappen
* [Activiteit gegevens stroom uitvoeren](control-flow-execute-data-flow-activity.md)
* [Controle stroom expressies](control-flow-expression-language-functions.md)
