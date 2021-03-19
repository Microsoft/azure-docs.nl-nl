---
title: Afgeleide kolom transformatie bij toewijzing van gegevens stroom
description: Meer informatie over het transformeren van gegevens op schaal in Azure Data Factory met de toewijzing gegevens stroom die is afgeleide kolom transformatie.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531979"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Afgeleide kolom transformatie bij toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de afgeleide kolom transformatie om nieuwe kolommen in uw gegevens stroom te genereren of om bestaande velden te wijzigen.

## <a name="create-and-update-columns"></a>Kolommen maken en bijwerken

Wanneer u een afgeleide kolom maakt, kunt u een nieuwe kolom genereren of een bestaande bijwerken. Geef in het tekstvak **kolom** de kolom op die u maakt. Als u een bestaande kolom in uw schema wilt overschrijven, kunt u de vervolg keuzelijst kolom gebruiken. Als u de expressie van de afgeleide kolom wilt maken, klikt u op het tekstvak **expressie invoeren** . U kunt beginnen met het typen van uw expressie of de opbouw functie voor expressies openen om uw logica te maken.

![Afgeleide kolom instellingen](media/data-flow/create-derive-column.png "Afgeleide kolom instellingen")

Als u meer afgeleide kolommen wilt **toevoegen, klikt u boven de** kolom lijst of het plus pictogram naast een bestaande afgeleide kolom. Kies **kolom toevoegen** of een **kolom patroon toevoegen**.

![Nieuwe afgeleide kolom selectie](media/data-flow/add-derived-column.png "Nieuwe afgeleide kolom selectie")

### <a name="column-patterns"></a>Kolompatronen

In gevallen waarin uw schema niet expliciet is gedefinieerd of als u een set kolommen in bulk wilt bijwerken, moet u kolom Patters maken. Met kolom patronen kunt u kolommen koppelen met behulp van regels op basis van de meta gegevens van de kolom en afgeleide kolommen maken voor elke overeenkomende kolom. Meer informatie vindt u in het [bouwen van kolom patronen](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) in de afgeleide kolom transformatie.

![Kolompatronen](media/data-flow/column-pattern-derive.png "Kolompatronen")

## <a name="building-schemas-using-the-expression-builder"></a>Schema's maken met behulp van de opbouw functie voor expressies

Wanneer u de [opbouw functie](concepts-data-flow-expression-builder.md)voor het toewijzen van gegevensstroom expressies gebruikt, kunt u afgeleide kolommen maken, bewerken en beheren in de sectie **afgeleide kolommen** . Alle kolommen die in de trans formatie worden gemaakt of gewijzigd, worden weer gegeven. Kies interactief welke kolom of welk patroon u wilt bewerken door te klikken op de kolom naam. Als u een extra kolom wilt toevoegen, selecteert u **nieuwe maken** en kiest u of u één kolom of een patroon wilt toevoegen.

![Nieuwe kolom maken](media/data-flow/derive-add-column.png "Nieuwe kolom maken")

Wanneer u met complexe kolommen werkt, kunt u subkolomen maken. Hiertoe klikt u op het plus pictogram naast een kolom en selecteert u **subkolom toevoegen**. Zie [JSON handling in mapping data flow](format-json.md#mapping-data-flow-properties)(Engelstalig) voor meer informatie over het verwerken van complexe typen in de gegevens stroom.

![Subkolom toevoegen](media/data-flow/derive-add-subcolumn.png "Subkolom toevoegen")

Zie [JSON handling in mapping data flow](format-json.md#mapping-data-flow-properties)(Engelstalig) voor meer informatie over het verwerken van complexe typen in de gegevens stroom.

![Complexe kolom toevoegen](media/data-flow/derive-complex-column.png "Kolommen toevoegen")

### <a name="locals"></a>Locals

Als u logica deelt over meerdere kolommen of als u uw logica wilt compartimenteren, kunt u een lokale maken binnen een afgeleide kolom transformatie. Een lokale is een set logica die niet aan de volgende trans formatie wordt door gegeven: downstream. Lokalen kunnen worden gemaakt in de opbouw functie voor expressies door naar **expressie-elementen** te gaan en **lokale** items te selecteren. Maak een nieuwe door **nieuwe maken** te selecteren.

![Lokaal maken](media/data-flow/create-local.png "Lokaal maken")

Lokalen kunnen verwijzen naar elk expressie-element een afgeleide kolom, inclusief functies, invoer schema, para meters en andere lokalen. Als er wordt verwezen naar andere lokalen, moet de volg orde van de lokale verwijzingen ' boven ' zijn.

![Lokaal maken 2](media/data-flow/create-local-2.png "Lokaal maken 2")

Als u wilt verwijzen naar een lokaal in een afgeleide kolom, klikt u op de lokale weer gave van de **expressie-elementen** of verwijst u hiernaar met een dubbele punt vóór de naam. Er wordt bijvoorbeeld verwezen naar een lokale, met de naam local1 `:local1` . Als u een lokale definitie wilt bewerken, plaatst u de muis aanwijzer in de weer gave expressie elementen en klikt u op het potlood pictogram.

![Lokalen gebruiken](media/data-flow/using-locals.png "Lokalen gebruiken")

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Voorbeeld

Het onderstaande voor beeld is een afgeleide kolom met de naam `CleanData` die een inkomende stroom gebruikt `MoviesYear` en twee afgeleide kolommen maakt. De eerste afgeleide kolom vervangt de kolom `Rating` met de classificatie waarde als een geheel getal. De tweede afgeleide kolom is een patroon dat overeenkomt met elke kolom waarvan de naam begint met ' films '. Voor elke overeenkomende kolom wordt een kolom gemaakt `movie` die gelijk is aan de waarde van de overeenkomende kolom, voorafgegaan door ' movie_ '. 

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Voor beeld afleiden](media/data-flow/derive-script.png "Voor beeld afleiden")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [taal van de toewijzings gegevens stroom](data-flow-expression-functions.md).
