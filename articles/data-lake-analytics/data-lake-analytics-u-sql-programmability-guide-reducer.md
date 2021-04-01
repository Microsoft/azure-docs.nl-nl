---
title: Door de gebruiker gedefinieerde U-SQL-hand leiding voor reducer-programmeer baarheid voor Azure Data Lake
description: 'Meer informatie over de U-SQL UDO Programmeer bare hand leiding: door de gebruiker gedefinieerde verminderr.'
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 52d44685678c3e89dc820042a7925d284500cef8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512564"
---
# <a name="use-user-defined-reducer"></a>Door de gebruiker gedefinieerde reduceerder gebruiken

## <a name="u-sql-udo-user-defined-reducer"></a>U-SQL-UDO: door de gebruiker gedefinieerde verminderr

U kunt met behulp van de door de gebruiker gedefinieerde operator Extensibility Framework in C# aangepaste Rijset-verminderingen schrijven en een IReducer-interface implementeren.

Een door de gebruiker gedefinieerde versmaller of UDR kan worden gebruikt om onnodige rijen te elimineren tijdens het ophalen van gegevens (importeren). Het kan ook worden gebruikt voor het bewerken en evalueren van rijen en kolommen. Op basis van de programmeer baarheids logica kan deze ook bepalen welke rijen moeten worden geëxtraheerd.

## <a name="how-to-define-and-use-user-defined-reducer"></a>Door de gebruiker gedefinieerde reductier definiëren en gebruiken
Als u een UDR-klasse wilt definiëren, moet u een `IReducer` interface met een optioneel `SqlUserDefinedReducer` kenmerk maken.

Deze klasse-interface moet een definitie bevatten voor de onderdrukking van de `IEnumerable` Interface-rijenset.

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Het kenmerk **SqlUserDefinedReducer** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde verminderr. Deze klasse kan niet worden overgenomen.
**SqlUserDefinedReducer** is een optioneel kenmerk voor een door de gebruiker gedefinieerde beperkings definitie. Deze wordt gebruikt voor het definiëren van de eigenschap IsRecursive.

* BOOL IsRecursive    
* **waar**  = geeft aan of deze versmaller een associatief en Commutative is

De belangrijkste Programmeer bare objecten zijn **invoer** en **uitvoer**. Het invoer object wordt gebruikt voor het opsommen van invoer rijen. Uitvoer wordt gebruikt om uitvoer rijen in te stellen als gevolg van het verminderen van de activiteit.

Voor de opsomming van invoer rijen gebruiken we de- `Row.Get` methode.

```csharp
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

De para meter voor de `Row.Get` methode is een kolom die wordt door gegeven als onderdeel van de `PRODUCE` klasse van de `REDUCE` instructie van het U-SQL-basis script. Het juiste gegevens type moet hier ook worden gebruikt.

Gebruik de-methode voor uitvoer `output.Set` .

Het is belang rijk om te begrijpen dat aangepaste reduceerere waarden alleen worden uitgevoerd die zijn gedefinieerd met de aanroep van de `output.Set` methode.

```csharp
output.Set<string>("mycolumn", guid);
```

De werkelijke verminderings uitvoer wordt geactiveerd door aan te roepen `yield return output.AsReadOnly();` .

Hier volgt een voor beeld van een reducer:

```csharp
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

In dit scenario voor gebruik worden rijen met een lege gebruikers naam overs Laan. Voor elke rij in de rijenset wordt elke vereiste kolom gelezen en wordt vervolgens de lengte van de gebruikers naam geëvalueerd. De werkelijke rij wordt alleen uitgevoerd als de waarde van de gebruikers naam meer dan 0 is.

Hieronder volgt een basis-U-SQL-script dat gebruikmaakt van een aangepaste verminderr:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

## <a name="next-steps"></a>Volgende stappen
* [Programmeer handleiding U-SQL-overzicht](data-lake-analytics-u-sql-programmability-guide.md)
* [Programmeer handleiding voor U-SQL-UDT en UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)