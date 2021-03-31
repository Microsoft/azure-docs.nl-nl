---
title: Statistische trans formatie in toewijzing van gegevens stroom
description: Meer informatie over het samen stellen van gegevens op schaal in Azure Data Factory met de statistische trans formatie toewijzings gegevens stroom.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 71f5488b1f689e8892155b013730bcbb3c8e0e35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531917"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Statistische trans formatie in toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De cumulatieve trans formatie definieert aggregaties van kolommen in uw gegevens stromen. Met de opbouw functie voor expressies kunt u verschillende typen aggregaties definiëren, zoals som, MIN, maximum en aantal, gegroepeerd op bestaande of berekende kolommen.

## <a name="group-by"></a>Groeperen op

Selecteer een bestaande kolom of maak een nieuwe berekende kolom die u wilt gebruiken als een component Group by voor uw aggregatie. Als u een bestaande kolom wilt gebruiken, selecteert u deze in de vervolg keuzelijst. Als u een nieuwe berekende kolom wilt maken, houdt u de muis aanwijzer boven de component en klikt u op **berekende kolom**. Hiermee opent u de [opbouw functie voor de data flow-expressie](concepts-data-flow-expression-builder.md). Wanneer u de berekende kolom maakt, voert u de naam van de uitvoer kolom onder het veld **naam als in** . Als u een component Group by wilt toevoegen, houdt u de muis aanwijzer over een bestaande component en klikt u op het pictogram met het plus teken.

![Statistische transformatie groep op instellingen](media/data-flow/agg.png "Statistische transformatie groep op instellingen")

Een component Group by is optioneel in een geaggregeerde trans formatie.

## <a name="aggregate-columns"></a>Statistische kolommen

Ga naar het tabblad **aggregaties** om aggregatie-expressies samen te stellen. U kunt een bestaande kolom overschrijven met een aggregatie of een nieuw veld maken met een nieuwe naam. De expressie aggregatie wordt ingevoerd in het rechter vak naast de kolom naam kiezer. Als u de expressie wilt bewerken, klikt u op het tekstvak en opent u de opbouw functie voor expressies. Als u meer geaggregeerde kolommen **wilt toevoegen, klikt u boven de** kolom lijst of het plus pictogram naast een bestaande statistische kolom. Kies **kolom toevoegen** of een **kolom patroon toevoegen**. Elke aggregatie-expressie moet ten minste één statistische functie bevatten.

![Statistische instellingen](media/data-flow/aggregate-columns.png "Statistische instellingen")

> [!NOTE]
> In de foutopsporingsmodus kan de opbouw functie voor expressies geen gegevens previews maken met statistische functies. Voor het weer geven van voor beelden van gegevens voor samengevoegde trans formaties, sluit u de opbouw functie voor expressies en bekijkt u de gegevens via het tabblad gegevens voorbeeld.

### <a name="column-patterns"></a>Kolompatronen

Gebruik [kolom patronen](concepts-data-flow-column-pattern.md) om dezelfde aggregatie toe te passen op een set kolommen. Dit is handig als u veel kolommen van het invoer schema wilt behouden, omdat deze standaard worden verwijderd. Gebruik een heuristische, bijvoorbeeld `first()` om invoer kolommen door de aggregatie te behouden.

## <a name="reconnect-rows-and-columns"></a>Rijen en kolommen opnieuw verbinden

Samengestelde trans formaties zijn vergelijkbaar met SQL-query's voor aggregatie selectie. Kolommen die niet zijn opgenomen in de component Group by of statistische functies, stromen niet naar de uitvoer van uw geaggregeerde trans formatie. Als u andere kolommen in de cumulatieve uitvoer wilt opnemen, voert u een van de volgende methoden uit:

* Gebruik een statistische functie, zoals `last()` of `first()` om deze aanvullende kolom op te nemen.
* De kolommen opnieuw samen voegen met de uitvoer stroom met het [Self-deelname patroon](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Dubbele rijen verwijderen

Een veelgebruikte toepassing van de cumulatieve trans formatie is het verwijderen of identificeren van dubbele vermeldingen in de bron gegevens. Dit proces staat bekend als ontdubbeling. Gebruik een heuristiek van uw keuze om te bepalen welke dubbele rij moet worden bewaard op basis van een set met Group by-sleutels. Algemene heuristiek zijn `first()` , `last()` , en `max()` `min()` . Gebruik [kolom patronen](concepts-data-flow-column-pattern.md) om de regel toe te passen op elke kolom, met uitzonde ring van de Group by-kolommen.

![Ontdubbeling](media/data-flow/agg-dedupe.png "Ontdubbeling")

In het bovenstaande voor beeld `ProductID` worden kolommen en `Name` worden gebruikt voor het groeperen. Als twee rijen dezelfde waarden voor deze twee kolommen hebben, worden ze beschouwd als duplicaten. In deze cumulatieve trans formatie worden de waarden van de eerste rij die overeenkomt, bewaard en alle andere worden verwijderd. Met de syntaxis van het kolom patroon worden alle kolommen waarvan de namen niet `ProductID` `Name` overeenkomen en worden toegewezen aan de bestaande kolom naam en gegeven de waarde van de eerste overeenkomende rijen. Het uitvoer schema is hetzelfde als het invoer schema.

Voor scenario's voor gegevens validatie `count()` kan de functie worden gebruikt om te tellen hoeveel duplicaten er er zijn.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Voorbeeld

In het onderstaande voor beeld wordt een inkomende stroom gebruikt `MoviesYear` en worden rijen per kolom gegroepeerd `year` . De trans formatie maakt een statistische kolom `avgrating` die het gemiddelde van de kolom evalueert `Rating` . Deze cumulatieve trans formatie heet `AvgComedyRatingsByYear` .

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Groeperen op voor beeld](media/data-flow/agg-script1.png "Groeperen op voor beeld")

![Voor beeld van geaggregeerde](media/data-flow/agg-script2.png "Voor beeld van geaggregeerde")

Het gegevens stroom script voor deze trans formatie bevindt zich in het onderstaande fragment.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Script voor gecombineerde gegevens stroom](media/data-flow/aggdfs1.png "Script voor gecombineerde gegevens stroom")

```MoviesYear```: Afgeleide kolom waarin jaar-en titel kolommen worden gedefinieerd ```AvgComedyRatingByYear``` : statistische trans formatie voor gemiddelde waardering van comedies gegroepeerd per jaar ```avgrating``` : de naam van de nieuwe kolom die wordt gemaakt om de geaggregeerde waarde te bevatten

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Volgende stappen

* Aggregatie op basis van een venster definiëren met behulp van de [venster transformatie](data-flow-window.md)
