---
title: Date_Bucket (Transact-SQL)-Azure SQL Edge
description: Meer informatie over het gebruik van Date_Bucket in Azure SQL Edge
keywords: Date_Bucket, SQL-rand
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 9d81419721e94a2e181f094c0e0e64b1b23544a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93073516"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

Deze functie retourneert de waarde voor datum/tijd die overeenkomt met het begin van elke datetime-Bucket, van de tijds tempel die is gedefinieerd door de `origin` para meter of de standaard waarde oorsprong `1900-01-01 00:00:00.000` als de para meter Origin niet is opgegeven. 

Zie [datum-en tijd gegevens typen en-functies &#40;Transact-SQL-&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/) voor een overzicht van alle gegevens typen en functies van de Transact-SQL-datum en-tijd.

[Transact-SQL-syntaxis conventies](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>Syntaxis

```sql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>Argumenten

*Part*

Het deel van de *datum* dat met de para meter ' Number ' wordt gebruikt. Bijvoorbeeld Jaar, maand, minuut, seconde, enzovoort

> [!NOTE]
> `DATE_BUCKET` accepteert geen door de gebruiker gedefinieerde variabele-equivalenten voor de argumenten *datepPart* .
  
|*Part*|Afkortingen|  
|---|---|
|**profieldag**|**dd**, **d**|  
|**gevormd**|**WK**, **WW**| 
|**maand**|**mm**, **m**|
|**cijfers**|**QQ**, **q**|  
|**jaar**|**JJ**, **yyyy**|  
|**uur**|**hh**|  
|**notulen**|**mi**, **n**|  
|**tweede**|**SS**, **s**|  
|**milliseconde**|**ms**|  

*getal*

Het gehele getal dat de breedte van de Bucket in combi natie met het argument *date Part* bepaalt. Dit vertegenwoordigt de breedte van de data Part bevindt buckets van de oorspronkelijke tijd. **`This argument cannot be a negative integer value`**. 

*date*

Een expressie die kan worden omgezet in een van de volgende waarden:

+ **date**
+ **datum/tijd**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

Voor *datum* `DATE_BUCKET` accepteert een kolom expressie, expressie of door de gebruiker gedefinieerde variabele als deze worden omgezet naar een van de hierboven genoemde gegevens typen.

**Oorsprong** 

Een optionele expressie die kan worden omgezet in een van de volgende waarden:

+ **date**
+ **datum/tijd**
+ **datetimeoffset**
+ **datetime2**
+ **smalldatetime**
+ **time**

Het gegevens type voor `Origin` moet overeenkomen met het gegevens type van de `Date` para meter. 

`DATE_BUCKET` maakt gebruik van een standaard datum waarde van `1900-01-01 00:00:00.000` 12:00 uur op maandag, januari 1 1900 als er geen waarde is opgegeven voor de functie.

## <a name="return-type"></a>Retour type

Het gegevens type van de retour waarde voor deze methode is dynamisch. Het retour type is afhankelijk van het argument dat is opgegeven voor `date` . Als er een geldig invoer gegevens type is opgegeven voor `date` , `DATE_BUCKET` wordt hetzelfde gegevens type geretourneerd. `DATE_BUCKET` Hiermee wordt een fout gegenereerd als een letterlijke teken reeks wordt opgegeven voor de `date` para meter.

## <a name="return-values"></a>Retour waarden

### <a name="understanding-the-output-from-date_bucket"></a>Uitleg over de uitvoer van `DATE_BUCKET`

`Date_Bucket` retourneert de laatste datum-of tijd waarde, die overeenkomt met de para meter date Part en Number. In de onderstaande expressies `Date_Bucket` retourneert bijvoorbeeld de uitvoer waarde van `2020-04-13 00:00:00.0000000` , wanneer de uitvoer wordt berekend op basis van de standaard tijd van de oorsprong van `1900-01-01 00:00:00.000` . De waarde `2020-04-13 00:00:00.0000000` is 6276 weken vanaf de oorspronkelijke waarde van `1900-01-01 00:00:00.000` . 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

Voor alle onderstaande expressies moet dezelfde uitvoer waarde van `2020-04-13 00:00:00.0000000` worden geretourneerd. Dit komt omdat `2020-04-13 00:00:00.0000000` 6276 weken vanaf de oorspronkelijke datum en 6276 deelbaar is door 2, 3, 4 en 6.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

De uitvoer voor de onderstaande expressie is een `2020-04-06 00:00:00.0000000` waarde van 6275 weken vanaf de standaardtijds oorsprong `1900-01-01 00:00:00.000` .

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

De uitvoer voor de onderstaande expressie is `2020-06-09 00:00:00.0000000` 75 weken vanaf de opgegeven begin tijd `2019-01-01 00:00:00` .

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>Argument date Part

**DAYOFYEAR**, **Day** en **Weekday** geven dezelfde waarde als resultaat. Elke *date Part* en de afkortingen retour neren dezelfde waarde.
  
## <a name="number-argument"></a>Argument getal

Het argument *Number* kan niet groter zijn dan het bereik van positieve **inte** -waarden. In de volgende instructies is het argument voor *getal* groter dan het bereik van **int** door 1. Met de volgende instructie wordt het volgende fout bericht geretourneerd: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

Als een negatieve waarde voor getal wordt door gegeven aan de `Date_Bucket` functie, wordt de volgende fout geretourneerd. 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>datum argument  

`DATE_BUCKET` de basis waarde Retour neren die overeenkomt met het gegevens type van het `date` argument. In het volgende voor beeld wordt een uitvoer waarde met het gegevens type DATETIME2 geretourneerd. 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>Argument oorsprong  

Het gegevens type van de `origin` `date` argumenten en moet hetzelfde zijn. Als er verschillende gegevens typen worden gebruikt, wordt er een fout gegenereerd.

## <a name="remarks"></a>Opmerkingen

Gebruik `DATE_BUCKET` in de volgende componenten:

+ GROUP BY
+ HAVING
+ ORDER BY
+ UITGESCHAKELD \<list>
+ WHERE

## <a name="examples"></a>Voorbeelden

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. Date_Bucket berekenen met een Bucket breedte van 1 vanaf de oorspronkelijke tijd

Elk van deze instructies verhoogt *date_bucket* met een Bucket breedte van 1 vanaf de oorspronkelijke tijd:

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

Dit is de resultatenset.

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. Expressies gebruiken als argumenten voor de para meters aantal en datum

In deze voor beelden worden verschillende typen expressies gebruikt als argumenten voor de para meters *aantal* en *datum* . Deze voor beelden zijn gebouwd op basis van de AdventureWorksDW2017-data base.
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>Door de gebruiker gedefinieerde variabelen opgeven als numeriek en datum  

In dit voor beeld worden door de gebruiker gedefinieerde variabelen opgegeven als argumenten voor het *getal* en de *datum*:
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

Dit is de resultatenset.

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>Een kolom als datum opgeven

In het onderstaande voor beeld berekenen we de som van OrderQuantity en de som van prijs per week aantal weken die zijn gegroepeerd.
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

Dit is de resultatenset.
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>Scalaire systeem functie opgeven als datum

In dit voor beeld wordt de `SYSDATETIME` *datum* opgegeven. De exacte waarde die wordt geretourneerd, is afhankelijk van de dag en tijd van de uitvoering van de instructie:
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

Dit is de resultatenset.

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>Scalaire subquery's en scalaire functies opgeven als getal en datum

In dit voor beeld wordt gebruikgemaakt van scalaire subquery's, `MAX(OrderDate)` , als argumenten voor *getal* en *datum*. `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` fungeert als een kunst matig argument voor de para meter Number om te laten zien hoe u een *Numeriek* argument kunt selecteren in een lijst met waarden.
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>Numerieke expressies en scalaire systeem functies opgeven als getal en datum

In dit voor beeld wordt een numerieke expressie ((10/2)) en scalaire systeem functies (SYSDATETIME) gebruikt als argumenten voor getal en datum.
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>Het opgeven van een statistische venster functie als getal

In dit voor beeld wordt een statistische venster functie gebruikt als argument voor *getal*.
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 
### <a name="c-using-a-non-default-origin-value"></a>C. Een niet-standaard waarde voor oorsprong gebruiken

In dit voor beeld wordt een niet-standaard waarde voor Orgin gebruikt om de datum buckets te genereren. 

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>Zie ook

[&#40;Transact-SQL-&#41;CASTen en converteren ](/sql/t-sql/functions/cast-and-convert-transact-sql/)
