---
title: Met U-SQL door de gebruiker gedefinieerde combi natie van Programmeer bare hand leiding voor Azure Data Lake
description: 'Meer informatie over de U-SQL UDO Programmeer bare hand leiding: door de gebruiker gedefinieerde combi natie.'
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: a6c560cf4ec11197183711656d69024591e7008c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96512603"
---
# <a name="use-user-defined-combiner"></a>Door de gebruiker gedefinieerde combineerder gebruiken

## <a name="u-sql-udo-user-defined-combiner"></a>U-SQL UDO: door de gebruiker gedefinieerde combi natie
Met de door de gebruiker gedefinieerde combi natie of UDC kunt u rijen uit de rijen sets links en rechts combi neren op basis van aangepaste logica. Door de gebruiker gedefinieerde combi natie wordt gebruikt met de combi natie-expressie.

## <a name="how-to-define-and-use-user-defined-combiner"></a>Een door de gebruiker gedefinieerde combi natie definiëren en gebruiken

Er wordt een combi natie aangeroepen met de combi natie-expressie die de benodigde informatie bevat over de invoer rijen sets, de groeperings kolommen, het verwachte resultaten schema en aanvullende informatie.

Als u een combi natie wilt aanroepen in een base U-SQL-script, gebruikt u de volgende syntaxis:

```usql
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Zie [expressie combi neren (U-SQL)](/u-sql/statements-and-expressions/combine-expression)voor meer informatie.

Voor het definiëren van een door de gebruiker gedefinieerde combi natie moet de interface worden gemaakt `ICombiner` met het `SqlUserDefinedCombiner` kenmerk [], dat optioneel is voor een door de gebruiker gedefinieerde combi Naties definitie.

Basis `ICombiner` klassen definitie:

```csharp
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

De aangepaste implementatie van een `ICombiner` interface moet de definitie voor een `IEnumerable<IRow>` combi natie van onderdrukking bevatten.

```csharp
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

Het kenmerk **SqlUserDefinedCombiner** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde combi natie. Deze klasse kan niet worden overgenomen.

**SqlUserDefinedCombiner** wordt gebruikt om de eigenschap van de combine modus te definiëren. Het is een optioneel kenmerk voor een door de gebruiker gedefinieerde combi Naties definitie.

CombinerMode-modus

CombinerMode Enum kan de volgende waarden hebben:

* Volledig (0) elke uitvoer rij is mogelijk afhankelijk van alle invoer rijen van links en rechts met dezelfde sleutel waarde.

* Links (1) elke uitvoermap is afhankelijk van één rij met invoer van links (en mogelijk alle rijen van rechts met dezelfde sleutel waarde).

* Rechts (2) elke uitvoermap is afhankelijk van één invoer rij van rechts (en mogelijk alle rijen vanaf de linkerkant met dezelfde sleutel waarde).

* Binnenste (3) elke uitvoermap is afhankelijk van één invoer rij van links naar rechts en met dezelfde waarde.

Voor beeld: [ `SqlUserDefinedCombiner(Mode=CombinerMode.Left)` ]


De belangrijkste Programmeer bare objecten zijn:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Invoer rijen worden door gegeven als het **linker** -en **rechter** `IRowset` type van de interface. Beide rijen sets moeten worden geïnventariseerd voor verwerking. U kunt elke interface één keer opsommen. Daarom moet u deze indien nodig opsommen en opslaan in de cache.

Voor cache doeleinden kunnen we een lijst \<T\> Type geheugen structuur maken als resultaat van de uitvoering van een LINQ-query, met name<`IRow`>. Het anonieme gegevens type kan ook worden gebruikt tijdens de inventarisatie.

Zie [Inleiding tot LINQ-query's (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) voor meer informatie over LINQ-query's en de [IEnumerable- \<T\> Interface](/dotnet/api/system.collections.generic.ienumerable-1) voor meer informatie over de \<T\> Interface IEnumerable.

Voor het ophalen van de werkelijke gegevens waarden van de binnenkomende `IRowset` , gebruiken we de methode Get () van de `IRow` interface.

```csharp
mycolumn = row.Get<int>("mycolumn")
```

U kunt afzonderlijke kolom namen bepalen door de schema methode aan te roepen `IRow` .

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Of gebruik de naam van de schema kolom:

```csharp
c# row.Get<int>(row.Schema[0].Name)
```

De algemene opsomming met LINQ ziet er als volgt uit:

```csharp
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Na het opsommen van beide rijen sets gaan we alle rijen door lopen. Voor elke rij in de rijenset links gaan we alle rijen vinden die voldoen aan de voor waarde van onze combi natie.

De uitvoer waarden moeten worden ingesteld met de `IUpdatableRow` uitvoer.

```csharp
output.Set<int>("mycolumn", mycolumn)
```

De daad werkelijke uitvoer wordt geactiveerd door aan te roepen `yield return output.AsReadOnly();` .

Hieronder volgt een combi natie van voor beeld:

```csharp
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

In dit scenario voor gebruik maken we een analyse rapport voor de detail handelaar. Het doel is om alle producten te vinden die meer dan $20.000 kosten en die de website sneller verkopen dan via de normale leverancier binnen een bepaald tijds bestek.

Dit is het basis U-SQL-script. U kunt de logica vergelijken tussen een gewone samen voeging en een combi natie:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Een door de gebruiker gedefinieerde combi natie kan worden aangeroepen als een nieuw exemplaar van het Applier-object:

```csharp
USING new MyNameSpace.MyCombiner();
```


Of met het aanroepen van een wrapper-fabrieks methode:

```csharp
USING MyNameSpace.MyCombiner();
```

## <a name="next-steps"></a>Volgende stappen
* [Programmeer handleiding U-SQL-overzicht](data-lake-analytics-u-sql-programmability-guide.md)
* [Programmeer handleiding voor U-SQL-UDT en UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)