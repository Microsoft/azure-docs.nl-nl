---
title: Toewijzingsgegevensstromen parameteriseren
description: Meer informatie over het parameteriseren van een toewijzingsgegevensstroom data factory pijplijnen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 22c4fc0680d8666d8c2dfafb8829436e27cf1ebd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725706"
---
# <a name="parameterizing-mapping-data-flows"></a>Toewijzingsgegevensstromen parameteriseren

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Toewijzingsgegevensstromen in Azure Data Factory en Azure Synapse Analytics ondersteuning voor het gebruik van parameters. Definieer parameters in uw gegevensstroomdefinitie en gebruik deze in uw expressies. De parameterwaarden worden ingesteld door de aanroepende pijplijn via de Gegevensstroom uitvoeren. U hebt drie opties voor het instellen van de waarden in de expressies voor gegevensstroomactiviteit:

* De taal van de stroomexpressie van het pijplijnbeheer gebruiken om een dynamische waarde in te stellen
* De expressietaal van de gegevensstroom gebruiken om een dynamische waarde in te stellen
* Een van beide expressietalen gebruiken om een statische letterlijke waarde in te stellen

Gebruik deze mogelijkheid om uw gegevensstromen algemeen, flexibel en herbruikbaar te maken. U kunt de instellingen en expressies van de gegevensstroom parameteriseren met deze parameters.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Parameters maken in een toewijzingsgegevensstroom

Als u parameters wilt toevoegen aan uw gegevensstroom, klikt u op het lege gedeelte van het gegevensstroom-canvas om de algemene eigenschappen te bekijken. In het deelvenster Instellingen ziet u een tabblad met de naam **Parameter**. Selecteer **Nieuw** om een nieuwe parameter te genereren. Voor elke parameter moet u een naam toewijzen, een type selecteren en eventueel een standaardwaarde instellen.

![Parameters Gegevensstroom maken](media/data-flow/create-params.png "Parameters Gegevensstroom maken")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Parameters gebruiken in een toewijzingsgegevensstroom 

Er kan naar parameters worden verwezen in elke gegevensstroomexpressie. Parameters beginnen met $ en zijn onveranderbaar. U vindt de lijst met beschikbare parameters in de Expression Builder op het **tabblad Parameters.**

![Schermopname van de beschikbare parameters op het tabblad Parameters.](media/data-flow/parameter-expression.png "Parameterexpressie voor gegevensstroom")

U kunt snel extra parameters toevoegen door Nieuwe **parameter te selecteren** en de naam en het type op te geven.

![Schermopname van de parameters op het tabblad Parameters met nieuwe parameters toegevoegd.](media/data-flow/new-parameter-expression.png "Parameterexpressie voor gegevensstroom")

## <a name="assign-parameter-values-from-a-pipeline"></a>Parameterwaarden toewijzen vanuit een pijplijn

Zodra u een gegevensstroom met parameters hebt gemaakt, kunt u deze uitvoeren vanuit een pijplijn met de Gegevensstroom uitvoeren. Nadat u de activiteit aan uw pijplijn-canvas hebt toevoegen, ziet u de beschikbare gegevensstroomparameters op het **tabblad Parameters van de** activiteit.

Wanneer u parameterwaarden toewijst, [](control-flow-expression-language-functions.md) kunt u de taal van de pijplijnexpressie of de expressietaal van de [gegevensstroom gebruiken](data-flow-expression-functions.md) op basis van spark-typen. Elke toewijzingsgegevensstroom kan elke combinatie van pijplijn- en gegevensstroomexpressieparameters hebben.

![Schermopname van het tabblad Parameters met Gegevensstroom expressie geselecteerd voor de waarde van myparam.](media/data-flow/parameter-assign.png "Een parameter Gegevensstroom instellen")

### <a name="pipeline-expression-parameters"></a>Parameters voor pijplijnexpressie

Met parameters voor pijplijnexpressie kunt u verwijzen naar systeemvariabelen, functies, pijplijnparameters en variabelen die vergelijkbaar zijn met andere pijplijnactiviteiten. Wanneer u op **Pijplijnexpressie klikt,** wordt er een zijnavigatievenster geopend waarin u een expressie kunt invoeren met behulp van de opbouwer voor expressies.

![Schermopname van het deelvenster opbouw van expressies.](media/data-flow/parameter-pipeline.png "Een parameter Gegevensstroom instellen")

Wanneer hier naar wordt verwezen, worden pijplijnparameters geëvalueerd en wordt de waarde ervan gebruikt in de taal van de gegevensstroomexpressie. Het type pijplijnexpressie hoeft niet overeen te komen met het parametertype van de gegevensstroom. 

#### <a name="string-literals-vs-expressions"></a>Letterlijke tekenreeksen versus expressies

Wanneer u een parameter voor de pijplijnexpressie van het type tekenreeks toewijst, worden standaard aanhalingstekens toegevoegd en wordt de waarde geëvalueerd als een letterlijke waarde. Als u de parameterwaarde wilt lezen als een gegevensstroomexpressie, controleert u het expressievak naast de parameter .

![Schermopname van het deelvenster Gegevensstroomparameters Expressie geselecteerd voor een parameter.](media/data-flow/string-parameter.png "Een parameter Gegevensstroom instellen")

Als de gegevensstroomparameter `stringParam` verwijst naar een pijplijnparameter met de waarde `upper(column1)` . 

- Als de expressie is ingeschakeld, `$stringParam` wordt geëvalueerd naar de waarde van column1 in hoofdletters.
- Als de expressie niet is ingeschakeld (standaardgedrag),  `$stringParam` wordt geëvalueerd als `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Tijdstempels doorgeven

In de taal van de pijplijnexpressie worden systeemvariabelen zoals en functies zoals retourtijdstempels als tekenreeksen in notatie `pipeline().TriggerTime` `utcNow()` 'yyyy-MM-dd \' T \' HH:mm:ss. SSSSSZ'. Als u deze wilt converteren naar gegevensstroomparameters van het type timestamp, gebruikt u tekenreeksinterpolatie om de gewenste tijdstempel op te nemen in een `toTimestamp()` functie. Als u bijvoorbeeld de pijplijntriggertijd wilt converteren naar een gegevensstroomparameter, kunt u `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` gebruiken. 

![Schermopname van het tabblad Parameters waar u een triggertijd kunt invoeren.](media/data-flow/parameter-timestamp.png "Een parameter Gegevensstroom instellen")

> [!NOTE]
> Gegevensstromen kunnen slechts maximaal 3 milliseconden ondersteunen. De `left()` functie wordt gebruikt om extra cijfers af te korten.

#### <a name="pipeline-parameter-example"></a>Voorbeeld van pijplijnparameter

Stel dat u een geheel getal-parameter hebt die verwijst naar een `intParam` pijplijnparameter van het type Tekenreeks, `@pipeline.parameters.pipelineParam` . 

![Schermopname van het tabblad Parameters met parameters stringParam en intParam.](media/data-flow/parameter-pipeline-2.png "Een parameter Gegevensstroom instellen")

`@pipeline.parameters.pipelineParam` wordt een waarde van toegewezen `abs(1)` tijdens runtime.

![Schermopname van het tabblad Parameters met de waarde van een b s (1) geselecteerd.](media/data-flow/parameter-pipeline-4.png "Een parameter Gegevensstroom instellen")

Wanneer `$intParam` wordt verwezen in een expressie zoals een afgeleide kolom, wordt het retourneren `abs(1)` `1` geëvalueerd. 

![Schermopname van de waarde voor kolommen.](media/data-flow/parameter-pipeline-3.png "Een parameter Gegevensstroom instellen")

### <a name="data-flow-expression-parameters"></a>Parameters voor gegevensstroomexpressie

Als u **Gegevensstroomexpressie selecteert,** wordt de opbouwer van de gegevensstroomexpressie geopend. U kunt in uw gegevensstroom verwijzen naar functies, andere parameters en een gedefinieerde schemakolom. Deze expressie wordt geëvalueerd zoals wanneer hier naar wordt verwezen.

> [!NOTE]
> Als u een ongeldige expressie doorgeeft of verwijst naar een schemakolom die niet bestaat in die transformatie, wordt de parameter geëvalueerd als null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Een kolomnaam doorgeven als parameter

Een veelvoorkomende patroon is het doorgeven van een kolomnaam als parameterwaarde. Als de kolom is gedefinieerd in het gegevensstroomschema, kunt u er rechtstreeks naar verwijzen als een tekenreeksexpressie. Als de kolom niet is gedefinieerd in het schema, gebruikt u de `byName()` functie . Vergeet niet om de kolom naar het juiste type te casten met een cast-functie zoals `toString()` .

Als u bijvoorbeeld een tekenreekskolom wilt toevoegen op basis van een parameter , kunt u een afgeleide `columnName` kolomtransformatie toevoegen die gelijk is aan `toString(byName($columnName))` .

![Een kolomnaam doorgeven als parameter](media/data-flow/parameterize-column-name.png "Een kolomnaam doorgeven als parameter")

## <a name="next-steps"></a>Volgende stappen
* [Gegevensstroomactiviteit uitvoeren](control-flow-execute-data-flow-activity.md)
* [Expressies voor controlestromen](control-flow-expression-language-functions.md)
