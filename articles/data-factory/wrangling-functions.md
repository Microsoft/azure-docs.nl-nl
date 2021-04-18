---
title: Data-wrangling-functies in Azure Data Factory
description: Een overzicht van de beschikbare data-Wrangling-functies in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: f7a4041d87e00fa01ae5ae4dff0cade3b9755d31
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600931"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Transformatiefuncties in Power Query voor data-wrangling

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Met Data Wrangling in Azure Data Factory kunt u flexibele gegevensvoorbereiding en -wrangling zonder code uitvoeren op cloudschaal door de Power Query scripts om te zetten in ```M``` Gegevensstroom script. ADF kan worden geïntegreerd [met Power Query Online](/powerquery-m/power-query-m-reference) en maakt Power Query-functies beschikbaar voor data-wrangling via Spark-uitvoering met behulp van de ```M``` Spark-infrastructuur van de gegevensstroom. 

> [!NOTE]
> Power Query in ADF is momenteel beschikbaar als openbare preview

Momenteel worden niet Power Query M-functies ondersteund voor data-wrangling, ondanks dat deze beschikbaar zijn tijdens het maken. Tijdens het bouwen van uw mash-ups wordt het volgende foutbericht weergegeven als een functie niet wordt ondersteund:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

Hieronder vindt u een lijst met ondersteunde Power Query M-functies.

## <a name="column-management"></a>Kolombeheer

* Selectie: [Table.SelectColumns](/powerquery-m/table-selectcolumns)
* Verwijdering: [Table.RemoveColumns](/powerquery-m/table-removecolumns)
* Naam wijzigen: [Table.RenameColumns,](/powerquery-m/table-renamecolumns) [Table.PrefixColumns,](/powerquery-m/table-prefixcolumns) [Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Volgorde wijzigen: [Table.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Rijfiltering

Gebruik de [M-functie Table.SelectRows om](/powerquery-m/table-selectrows) te filteren op de volgende voorwaarden:

* Gelijkheid en ongelijkheid
* Numerieke, tekst- en datumvergelijkingen (maar niet Datum/tijd)
* Numerieke informatie zoals [Number.IsEven](/powerquery-m/number-iseven) / [Odd](/powerquery-m/number-iseven)
* Tekst insluiting met [behulp van Text.Contains,](/powerquery-m/text-contains) [Text.StartsWith](/powerquery-m/text-startswith)of [Text.EndsWith](/powerquery-m/text-endswith)
* Datumbereiken met inbegrip van alle functies 'IsIn' [Date](/powerquery-m/date-functions)) 
* Combinaties van deze gebruiken en, of, of niet voorwaarden

## <a name="adding-and-transforming-columns"></a>Kolommen toevoegen en transformeren

Met de volgende M-functies worden kolommen toevoegen of transformeren: [Table.AddColumn](/powerquery-m/table-addcolumn), [Table.TransformColumns](/powerquery-m/table-transformcolumns), [Table.ReplaceValue](/powerquery-m/table-replacevalue), [Table.DuplicateColumn.](/powerquery-m/table-duplicatecolumn) Hieronder vindt u de ondersteunde transformatiefuncties.

* Numerieke rekenkundige berekeningen
* Tekst samenvoegen
* Rekenkundige berekeningen voor datum en tijd (rekenkundige operators, [Date.AddDays,](/powerquery-m/date-adddays) [Date.AddMonths](/powerquery-m/date-addmonths), [Date.AddQuarters,](/powerquery-m/date-addquarters) [Date.AddWeeks](/powerquery-m/date-addweeks), [Date.AddYears](/powerquery-m/date-addyears))
* Duur kan worden gebruikt voor rekenkundige berekeningen voor datum en tijd, maar moet worden omgezet in een ander type voordat ze naar een sink worden geschreven (rekenkundige operators, [#duration](/powerquery-m/sharpduration), [Duration.Days](/powerquery-m/duration-days), [Duration.Hours](/powerquery-m/duration-hours), [Duration.Minutes](/powerquery-m/duration-minutes), [Duration.Seconds](/powerquery-m/duration-seconds), [Duration.TotalDays](/powerquery-m/duration-totaldays), [Duration.TotalHours](/powerquery-m/duration-totalhours), [Duration.TotalMinutes](/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](/powerquery-m/duration-totalseconds))    
* De meeste standaard, wetenschappelijke en trigonometrische numerieke functies (Alle functies onder [Bewerkingen,](/powerquery-m/number-functions#operations) [Afronding](/powerquery-m/number-functions#rounding)en [Trigonometrie](/powerquery-m/number-functions#trigonometry) behalve Number.Factorial, Number.Permutations en Number.Combinations) 
* Replacement ([Replacer.ReplaceText](/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue](/powerquery-m/replacer-replacevalue), [Text.Replace](/powerquery-m/text-replace), [Text.Remove](/powerquery-m/text-remove))
* Positionele tekstextractie ([Text.PositionOf](/powerquery-m/text-positionof), [Text.Length](/powerquery-m/text-length), [Text.Start](/powerquery-m/text-start), [Text.End](/powerquery-m/text-end), [Text.Middle](/powerquery-m/text-middle), [Text.ReplaceRange](/powerquery-m/text-replacerange), [Text.RemoveRange](/powerquery-m/text-removerange))
* Eenvoudige tekstopmaak ([Text.Lower,](/powerquery-m/text-lower) [Text.Upper](/powerquery-m/text-upper), [Text.Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [End,](/powerquery-m/text-trimend) [Text.PadStart](/powerquery-m/text-padstart) / [End](/powerquery-m/text-padend), [Text.Reverse](/powerquery-m/text-reverse))
* Datum/tijd-functies ([Date.Day](/powerquery-m/date-day), [Date.Month](/powerquery-m/date-month), [Date.Year](/powerquery-m/date-year) [Time.Hour](/powerquery-m/time-hour), [Time.Minute](/powerquery-m/time-minute), [Time.Second](/powerquery-m/time-second), [Date.DayOfWeek](/powerquery-m/date-dayofweek), [Date.DayOfYear](/powerquery-m/date-dayofyear), [Date.DaysInMonth](/powerquery-m/date-daysinmonth))
* If-expressies (maar vertakkingen moeten overeenkomende typen hebben)
* Rijfilters als logische kolom
* Constanten getal, tekst, logisch, datum en datum/tijd

<a name="mergingjoining-tables"></a>Tabellen samenvoegen/samenvoegen
----------------------
* Power Query genereert een geneste join (Table.NestedJoin; gebruikers kunnen ook handmatig [Table.AddJoinColumn schrijven).](/powerquery-m/table-addjoincolumn)
    Gebruikers moeten vervolgens de geneste join-kolom uitbreiden naar een niet-geneste join (Table.ExpandTableColumn, die in geen enkele andere context wordt ondersteund).
* De M-functie   [Table.Join](/powerquery-m/table-join) kan rechtstreeks worden geschreven om te voorkomen dat er een extra uitbreidingsstap nodig is, maar de gebruiker moet ervoor zorgen dat de samengevoegde tabellen geen dubbele kolomnamen hebben
* Ondersteunde join-soorten:   [Inner](/powerquery-m/joinkind-inner),   [LeftOuter](/powerquery-m/joinkind-leftouter),   [RightOuter,](/powerquery-m/joinkind-rightouter)   [FullOuter](/powerquery-m/joinkind-fullouter)
* Zowel   [Value.Equals](/powerquery-m/value-equals) als   [Value.NullableEquals](/powerquery-m/value-nullableequals) worden ondersteund als vergelijkingspunten voor sleutelgelijkheid

## <a name="group-by"></a>Groeperen op

Gebruik [Table.Group om](/powerquery-m/table-group) waarden te aggregeren.
* Moet worden gebruikt met een aggregatiefunctie
* Ondersteunde aggregatiefuncties:   [List.Sum](/powerquery-m/list-sum),   [List.Count](/powerquery-m/list-count),   [List.Average](/powerquery-m/list-average),   [List.Min,](/powerquery-m/list-min)   [List.Max,](/powerquery-m/list-max)   [List.StandardDeviation](/powerquery-m/list-standarddeviation),   [List.First](/powerquery-m/list-first),   [List.Last](/powerquery-m/list-last)

## <a name="sorting"></a>Sorteren

Gebruik [Table.Sort om](/powerquery-m/table-sort) waarden te sorteren.

## <a name="reducing-rows"></a>Rijen verminderen

Top behouden en verwijderen, Bereik behouden (bijbehorende M-functies, alleen ondersteunende tellingen, niet voorwaarden: [Table.FirstN,](/powerquery-m/table-firstn) [Table.Skip,](/powerquery-m/table-skip) [Table.RemoveFirstN](/powerquery-m/table-removefirstn), [Table.Range,](/powerquery-m/table-range) [Table.MinN](/powerquery-m/table-minn), [Table.MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Bekende niet-ondersteunde functies

| Functie | Status |
| -- | -- |
| Table.PromoteHeaders | Wordt niet ondersteund. Hetzelfde resultaat kan worden bereikt door 'Eerste rij als header' in te stellen in de gegevensset. |
| Table.CombineColumns | Dit is een algemeen scenario dat niet rechtstreeks wordt ondersteund, maar kan worden bereikt door een nieuwe kolom toe te voegen die twee opgegeven kolommen samenvoegt.  Bijvoorbeeld Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | Dit wordt in de meeste gevallen ondersteund. De volgende scenario's worden niet ondersteund: tekenreeks transformeren naar valutatype, tekenreeks transformeren naar tijdtype, tekenreeks transformeren naar Percentagetype. |
| Table.NestedJoin | Als u alleen een join doet, resulteert dit in een validatiefout. De kolommen moeten worden uitgebreid om deze te laten werken. |
| Table.Distinct | Dubbele rijen verwijderen wordt niet ondersteund. |
| Table.RemoveLastN | Onderste rijen verwijderen wordt niet ondersteund. |
| Table.RowCount | Niet ondersteund, maar kan worden bereikt door een aangepaste kolom met de waarde 1 toe te voegen en die kolom vervolgens samen te voegen met List.Sum. Table.Group wordt ondersteund. | 
| Foutafhandeling op rijniveau | Foutafhandeling op rijniveau wordt momenteel niet ondersteund. Als u bijvoorbeeld niet-numerieke waarden uit een kolom wilt filteren, kunt u de tekstkolom transformeren naar een getal. Elke cel die niet kan worden getransformeerd, heeft een foutmelding en moet worden gefilterd. Dit scenario is niet mogelijk in scale-out M. |
| Table.Transpose | Niet ondersteund |
| Table.Pivot | Niet ondersteund |
| Table.SplitColumn | Gedeeltelijk ondersteund |

## <a name="m-script-workarounds"></a>Tijdelijke oplossingen voor M-script

### <a name="for-splitcolumn-there-is-an-alternate-for-split-by-length-and-by-position"></a>Voor ```SplitColumn``` is er een alternatief voor splitsen op lengte en positie

* Table.AddColumn(Source, "First characters", each Text.Start([Email], 7), type text)
* Table.AddColumn(#"Ingevoegde eerste tekens", "Tekstbereik", elke Text.Middle([Email], 4, 9), type text)

Deze optie is toegankelijk via de optie Extraheren op het lint

![Power Query Kolom toevoegen](media/wrangling-data-flow/pq-split.png)

### <a name="for-tablecombinecolumns"></a>Voor ```Table.CombineColumns```

* Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " " & [LastName])


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een data-wrangling-Power Query in ADF](wrangling-tutorial.md).
