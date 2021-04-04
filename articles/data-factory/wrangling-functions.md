---
title: Data wrangling-functies in Azure Data Factory
description: Een overzicht van de beschik bare functies voor gegevens Wrangling in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 659f6527d43e1b45a11fddf774050ca6d42bfe12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896660"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Transformatie functies in Power Query voor gegevens wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Met data Wrangling in Azure Data Factory kunt u flexibelere gegevens voorbereiding en Wrangling in de Cloud schalen door Power Query-scripts te vertalen ```M``` naar een gegevensstroom script. ADF kan worden geïntegreerd met [Power query online](/powerquery-m/power-query-m-reference) en maakt Power query ```M``` functies beschikbaar voor gegevens wrangling via Spark-infra structuur. 

> [!NOTE]
> Power Query in ADF is momenteel beschik bare in open bare preview

Momenteel niet alle Power Query M-functies worden ondersteund voor gegevens wrangling ondanks dat deze beschikbaar zijn tijdens het ontwerpen. Tijdens het bouwen van uw mix verder wordt u gevraagd het volgende fout bericht op te nemen als een functie niet wordt ondersteund:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

Hieronder vindt u een lijst met ondersteunde functies van Power Query M.

## <a name="column-management"></a>Kolom beheer

* Selectie: [Table. select columns](/powerquery-m/table-selectcolumns)
* Verwijderen: [Table. RemoveColumns](/powerquery-m/table-removecolumns)
* Naam wijzigen: [Table. RenameColumns](/powerquery-m/table-renamecolumns), [Table. PrefixColumns](/powerquery-m/table-prefixcolumns), [Table. TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Volg orde wijzigen: [Table. ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Rijen filteren

Gebruik M Function [Table. SelectRows](/powerquery-m/table-selectrows) om te filteren op de volgende voor waarden:

* Gelijkheid en ongelijkheid
* Cijfers, tekst en datum vergelijkingen (maar geen DateTime)
* Numerieke gegevens, zoals [Number. isEven](/powerquery-m/number-iseven) / [oneven](/powerquery-m/number-iseven)
* Tekst insluiting met behulp van [tekst. contains](/powerquery-m/text-contains), [Text. StartsWith](/powerquery-m/text-startswith)of [Text. EndsWith](/powerquery-m/text-endswith)
* Datumbereiken, inclusief alle ' IsIn' [date-functies](/powerquery-m/date-functions)) 
* Combi Naties van deze met en, of of geen voor waarden

## <a name="adding-and-transforming-columns"></a>Kolommen toevoegen en transformeren

Met de volgende functies worden kolommen toegevoegd of omgezet: [Table. AddColumn](/powerquery-m/table-addcolumn), [Table. TransformColumns](/powerquery-m/table-transformcolumns), [Table. ReplaceValue](/powerquery-m/table-replacevalue), [Table. DuplicateColumn](/powerquery-m/table-duplicatecolumn). Hieronder vindt u de ondersteunde transformatie functies.

* Numeriek reken kundig
* Tekst samenvoeging
* Date andTime aritmetische (reken kundige operators, [datum. addDays](/powerquery-m/date-adddays), [date. AddMonths](/powerquery-m/date-addmonths), [date. AddQuarters](/powerquery-m/date-addquarters), [date. AddWeeks](/powerquery-m/date-addweeks), [date. AddYears](/powerquery-m/date-addyears))
* De duur kan worden gebruikt voor de reken kundige datum en tijd, maar moet worden omgezet in een ander type voordat deze naar een Sink (reken kundige Opera Tors, [#duration](/powerquery-m/sharpduration), [duur. dagen](/powerquery-m/duration-days), [duur. uren](/powerquery-m/duration-hours), [duur. minuten](/powerquery-m/duration-minutes), [duur. seconden](/powerquery-m/duration-seconds), duur. [TotalDays](/powerquery-m/duration-totaldays), [duration. TotalHours](/powerquery-m/duration-totalhours), duration. [TotalMinutes](/powerquery-m/duration-totalminutes), [duration. TotalSeconds](/powerquery-m/duration-totalseconds)) worden getransformeerd.    
* De meeste standaard-, weten schappelijke en trigonometrische numerieke functies (alle functies onder [bewerkingen](/powerquery-m/number-functions#operations), [afronding](/powerquery-m/number-functions#rounding)en [trigonometrie](/powerquery-m/number-functions#trigonometry) , *behalve* aantal. faculteit, aantal permutaties en aantal combi Naties)
* Vervangen (vervanging[. ReplaceText](/powerquery-m/replacer-replacetext), vervanging [. ReplaceValue](/powerquery-m/replacer-replacevalue), [Text. Replace](/powerquery-m/text-replace), [Text. Remove](/powerquery-m/text-remove))
* Positie tekst extractie ([tekst. PositionOf](/powerquery-m/text-positionof), [tekst. lengte](/powerquery-m/text-length), [tekst. begin](/powerquery-m/text-start), [tekst. End](/powerquery-m/text-end), [tekst. Midden](/powerquery-m/text-middle), [tekst. ReplaceRange](/powerquery-m/text-replacerange), [Text. RemoveRange](/powerquery-m/text-removerange))
* Basic-tekst opmaak ([tekst. Lower](/powerquery-m/text-lower), [tekst. Upper](/powerquery-m/text-upper), [tekst. begin datum bijsnijden](/powerquery-m/text-trim) / [](/powerquery-m/text-trimstart) / [](/powerquery-m/text-trimend), [tekst. PadStart](/powerquery-m/text-padstart) / [einde](/powerquery-m/text-padend), [tekst. omgekeerde](/powerquery-m/text-reverse))
* Datum-en tijd functies ([datum. dag](/powerquery-m/date-day), [datum. maand](/powerquery-m/date-month), [datum. jaar](/powerquery-m/date-year) [. uur](/powerquery-m/time-hour), [tijd. minuut](/powerquery-m/time-minute), [tijd. seconde](/powerquery-m/time-second), [datum. DayOfWeek](/powerquery-m/date-dayofweek), [datum. DayOfYear](/powerquery-m/date-dayofyear), [date. DaysInMonth](/powerquery-m/date-daysinmonth))
* Als-expressies (maar vertakkingen moeten overeenkomende typen hebben)
* Rij filters als logische kolom
* De constanten getal, tekst, logische, datum en datum/tijd

<a name="mergingjoining-tables"></a>Tabellen samen voegen/koppelen
----------------------
* Power Query wordt een geneste koppeling gegenereerd (Table. NestedJoin; gebruikers kunnen ook hand matig [Table. AddJoinColumn](/powerquery-m/table-addjoincolumn)) schrijven.
    Gebruikers moeten vervolgens de geneste samenvoegings kolom uitvouwen tot een niet-geneste koppeling (Table. ExpandTableColumn, die niet wordt ondersteund in een andere context).
* De functie   [Table. join](/powerquery-m/table-join) kan rechtstreeks worden geschreven om te voor komen dat er een extra uitbreidings stap nodig is, maar de gebruiker moet er zeker van zijn dat er geen dubbele kolom namen zijn in de gekoppelde tabellen
* Ondersteunde typen joins:   [inner](/powerquery-m/joinkind-inner),   [LeftOuter](/powerquery-m/joinkind-leftouter),   [RightOuter](/powerquery-m/joinkind-rightouter),   [FullOuter](/powerquery-m/joinkind-fullouter)
* Beide   [waarden. equals](/powerquery-m/value-equals) en   [Value. NullableEquals](/powerquery-m/value-nullableequals) worden ondersteund als Key gelijkheids vergelijkingen

## <a name="group-by"></a>Groeperen op

Gebruik [Table. Group](/powerquery-m/table-group) om waarden samen te voegen.
* Moet worden gebruikt met een statistische functie
* Ondersteunde aggregatie functies:   [lijst. som](/powerquery-m/list-sum),   [lijst. aantal](/powerquery-m/list-count),   [lijst. Gem](/powerquery-m/list-average),   [lijst. min](/powerquery-m/list-min),   [lijst. Max](/powerquery-m/list-max),   [List. StandardDeviation](/powerquery-m/list-standarddeviation),   [List. First](/powerquery-m/list-first), List.   [last](/powerquery-m/list-last)

## <a name="sorting"></a>Sorteren

Gebruik [Table. sort](/powerquery-m/table-sort) om waarden te sorteren.

## <a name="reducing-rows"></a>Rijen beperken

Hoog behoud en verwijderen, bereik (overeenkomende M-functies, alleen ondersteunings tellingen, niet-voor waarden): [tabel. voors](/powerquery-m/table-firstn)telling, tabel. [overs Laan](/powerquery-m/table-skip), [tabel.](/powerquery-m/table-removefirstn)reMinNe, [tabel. bereik](/powerquery-m/table-range), [tabel.](/powerquery-m/table-minn), [tabel. MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Bekende niet-ondersteunde functies

| Functie | Status |
| -- | -- |
| Table.PromoteHeaders | Wordt niet ondersteund. U kunt hetzelfde resultaat bereiken door in de gegevensset ' eerste rij als koptekst ' in te stellen. |
| Table.CombineColumns | Dit is een veelvoorkomend scenario dat niet rechtstreeks wordt ondersteund, maar kan worden bereikt door een nieuwe kolom toe te voegen waarmee twee opgegeven kolommen worden samengevoegd.  Bijvoorbeeld Table. AddColumn (RemoveEmailColumn, "name", elk [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | Dit wordt in de meeste gevallen ondersteund. De volgende scenario's worden niet ondersteund: het transformeren van de teken reeks naar het valuta type, het transformeren van de teken reeks naar het type tijd, het transformeren van de teken reeks naar het type percentage. |
| Table.NestedJoin | Als u een koppeling uitvoert, treedt er een validatie fout op. De kolommen moeten worden uitgevouwen om te kunnen werken. |
| Table.Distinct | Verwijder dubbele rijen worden niet ondersteund. |
| Table.RemoveLastN | Onderste rijen verwijderen wordt niet ondersteund. |
| Table.RowCount | Niet ondersteund, maar kan worden bereikt door een aangepaste kolom met de waarde 1 toe te voegen en deze kolom vervolgens samen te voegen met List. sum. Table. Group wordt ondersteund. | 
| Fout afhandeling op rijniveau | Fout afhandeling op rijniveau wordt momenteel niet ondersteund. Als u bijvoorbeeld niet-numerieke waarden uit een kolom wilt filteren, kunt u de tekst kolom omzetten in een getal. Elke cel die niet kan worden getransformeerd, heeft een fout status en moet worden gefilterd. Dit scenario is niet mogelijk in een uitgeschaalde M. |
| Table.Transpose | Niet ondersteund |
| Table.Pivot | Niet ondersteund |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een gegevens wrangling Power query in ADF](wrangling-tutorial.md).
