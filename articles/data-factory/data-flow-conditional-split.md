---
title: Voorwaardelijke Splits Transform in toewijzing van gegevens stroom
description: Gegevens in verschillende stromen splitsen met behulp van de Conditional Split trans formatie in Azure Data Factory toewijzing van gegevens stroom
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/21/2020
ms.openlocfilehash: eece6f97e82f3800d4f59ac1849b34c2a1e4635b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "83800087"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Voorwaardelijke Splits Transform in toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de Conditional Split trans formatie worden gegevens rijen naar verschillende stromen gerouteerd op basis van overeenkomende voor waarden. De Conditional Split trans formatie is vergelijkbaar met een beslissings structuur voor de situatie in een programmeer taal. De trans formatie evalueert expressies en op basis van de resultaten, stuurt de gegevensrij door naar de opgegeven stroom.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>Configuratie

De instelling **splitsen op** bepaalt of de rij van de gegevens naar de eerste overeenkomende stream of elke stream die overeenkomt met moet stromen.

Gebruik de opbouw functie voor de data flow-expressie om een expressie voor de Splits voorwaarde in te voeren. Klik op het plus pictogram in een bestaande rij om een nieuwe voor waarde toe te voegen. Er kan ook een standaard stroom worden toegevoegd voor rijen die niet overeenkomen met een voor waarde.

![Conditional Split](media/data-flow/conditionalsplit1.png "Conditional Split opties")

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Voorbeeld

Het onderstaande voor beeld is een Conditional Split trans formatie met de naam `SplitByYear` die in de inkomende stroom neemt `CleanData` . Deze trans formatie heeft twee gesplitste voor waarden `year < 1960` en `year > 1980` . `disjoint` is onwaar, omdat de gegevens worden verplaatst naar de eerste overeenkomende voor waarde. Elke rij die overeenkomt met de eerste voor waarde, gaat naar uitvoer stroom `moviesBefore1960` . Alle resterende rijen die overeenkomen met de tweede voor waarde, gaan naar uitvoer stroom `moviesAFter1980` . Alle andere rijen stromen via de standaard stroom `AllOtherMovies` .

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Conditional Split](media/data-flow/conditionalsplit1.png "Conditional Split opties")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Volgende stappen

Gemeen schappelijke gegevensstroom transformaties die worden gebruikt met Conditional Split zijn de trans [formatie voor samen voegen](data-flow-join.md), de [Zoek transformatie](data-flow-lookup.md)en de [selectie transformatie](data-flow-select.md)
