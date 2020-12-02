---
title: Door de gebruiker gedefinieerde U-SQL Applier-programmeer gids voor Azure Data Lake
description: Meer informatie over de U-SQL UDO Programmeer bare hand leiding-door de gebruiker gedefinieerde Applier.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 0842a2cfa021ef8ea45c19ec885c7dec371730de
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512570"
---
# <a name="use-user-defined-applier"></a>Door de gebruiker gedefinieerde Applier gebruiken 

## <a name="u-sql-udo-user-defined-applier"></a>U-SQL UDO: door de gebruiker gedefinieerde Applier
Met een door de gebruiker gedefinieerde U-SQL-Applier kunt u een aangepaste C#-functie aanroepen voor elke rij die wordt geretourneerd door de buitenste tabel expressie van een query. De juiste invoer wordt geëvalueerd voor elke rij van de linkerkant invoer en de rijen die worden geproduceerd, worden gecombineerd voor de uiteindelijke uitvoer. De lijst met kolommen die worden geproduceerd door de operator APPLY, is de combi natie van de set kolommen aan de linkerkant en de juiste invoer.


## <a name="how-to-define-and-use-user-defined-applier"></a>Door de gebruiker gedefinieerde Applier definiëren en gebruiken
Door de gebruiker gedefinieerde Applier wordt aangeroepen als onderdeel van de USQL SELECT-expressie.

De standaard aanroep van de door de gebruiker gedefinieerde Applier ziet er als volgt uit:

```usql
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Zie voor meer informatie over het gebruik van appliers in een SELECT [-expressie u-SQL selecteert u selecteren uit cross Apply en outer apply](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

De definitie van de door de gebruiker gedefinieerde Applier-basis klasse is als volgt:

```csharp
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Om een door de gebruiker gedefinieerde Applier te definiëren, moeten we de `IApplier` interface maken met het `SqlUserDefinedApplier` kenmerk [], dat optioneel is voor een door de gebruiker gedefinieerde Applier-definitie.

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Apply wordt aangeroepen voor elke rij van de buitenste tabel. Hiermee wordt de `IUpdatableRow` uitvoer rijenset geretourneerd.
* De constructor-klasse wordt gebruikt om para meters door te geven aan de door de gebruiker gedefinieerde Applier.

**SqlUserDefinedApplier** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerd Applier. Deze klasse kan niet worden overgenomen.

**SqlUserDefinedApplier** is **optioneel** voor een door de gebruiker gedefinieerde Applier-definitie.


De belangrijkste Programmeer bare objecten zijn als volgt:

```csharp
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Invoer rijen worden als invoer door gegeven `IRow` . De uitvoer rijen worden gegenereerd als `IUpdatableRow` uitvoer interface.

U kunt afzonderlijke kolom namen bepalen door de schema methode aan te roepen `IRow` .

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Voor het ophalen van de werkelijke gegevens waarden van de binnenkomende `IRow` , gebruiken we de methode Get () van de `IRow` interface.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

Of we gebruiken de schema kolom naam:

```csharp
row.Get<int>(row.Schema[0].Name)
```

De uitvoer waarden moeten worden ingesteld met de `IUpdatableRow` volgende uitvoer:

```csharp
output.Set<int>("mycolumn", mycolumn)
```

Het is belang rijk om te begrijpen dat aangepaste appliers alleen uitvoer kolommen en waarden die zijn gedefinieerd met `output.Set` methode aanroep.

De daad werkelijke uitvoer wordt geactiveerd door aan te roepen `yield return output.AsReadOnly();` .

De door de gebruiker gedefinieerde Applier-para meters kunnen worden door gegeven aan de constructor. Applier kan een variabel aantal kolommen retour neren dat moet worden gedefinieerd tijdens de aanroep van de Applier in base U-SQL-script.

```csharp
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Hier volgt het door de gebruiker gedefinieerde Applier-voor beeld:

```csharp
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Hieronder vindt U het basis U-SQL-script voor deze door de gebruiker gedefinieerde Applier:

```usql
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

In dit scenario voor gebruik fungeert door de gebruiker gedefinieerde Applier als een door komma's gescheiden waarde parser voor de eigenschappen van de auto vloot. De rijen van het invoer bestand zien er als volgt uit:

```text
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Het is een standaard, tabgescheiden TSV-bestand met een eigenschappen kolom die auto-eigenschappen bevat, zoals merk en model. Deze eigenschappen moeten worden geparseerd naar de tabel kolommen. Met de Applier die wordt geleverd, kunt u ook een dynamisch aantal eigenschappen in de rijenset met resultaten genereren, op basis van de para meter die wordt door gegeven. U kunt alle eigenschappen of een specifieke set eigenschappen genereren.

```text
...USQL_Programmability.ParserApplier ("all")
...USQL_Programmability.ParserApplier ("make")
...USQL_Programmability.ParserApplier ("make&model")
```

De door de gebruiker gedefinieerde Applier kan worden aangeroepen als een nieuw exemplaar van het Applier-object:

```usql
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Of met het aanroepen van een wrapper-fabrieks methode:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```


## <a name="next-steps"></a>Volgende stappen
* [Programmeer handleiding U-SQL-overzicht](data-lake-analytics-u-sql-programmability-guide.md)
* [Programmeer handleiding voor U-SQL-UDT en UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)