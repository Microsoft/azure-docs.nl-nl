---
title: Naslag informatie over SQL-taal voor query versnelling
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van de SQL-syntaxis voor query versnelling.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: ca4fe1410c18357a1fab10cc9c971cf3a81542fd
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105963254"
---
# <a name="query-acceleration-sql-language-reference"></a>Naslag informatie over SQL-taal voor query versnelling

Query versnelling ondersteunt een ANSI SQL-achtige taal voor het uitdrukken van query's via blob-inhoud.  De query versnelling SQL dialect is een subset van ANSI SQL, met een beperkte set ondersteunde gegevens typen, Opera Tors, enzovoort, maar wordt ook uitgebreid naar ANSI SQL om query's te ondersteunen in hiërarchische, semi-gestructureerde gegevens indelingen, zoals JSON. 

## <a name="select-syntax"></a>Syntaxis selecteren

De enige SQL-instructie die wordt ondersteund door de query versnelling is de SELECT-instructie. In dit voor beeld wordt elke rij geretourneerd waarvoor de expressie waar retourneert.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Voor gegevens in CSV-indeling  moet de tabel `BlobStorage` .  Dit betekent dat de query wordt uitgevoerd op basis van welke Blob in de REST-aanroep is opgegeven.
Voor gegevens in JSON-indeling is *tabel* de tabel descriptor.   Zie de sectie [Table descriptors](#table-descriptors) van dit artikel.

In het volgende voor beeld retourneert deze instructie voor elke rij waarvoor de WHERE- *expressie* waar retourneert, een nieuwe rij die wordt gemaakt van de evaluatie van elk van de projectie-expressies.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

In het volgende voor beeld wordt een statistische berekening geretourneerd (bijvoorbeeld: de gemiddelde waarde van een bepaalde kolom) in elk van de rijen waarvoor de *expressie* True retourneert. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

In het volgende voor beeld worden geschikte offsets geretourneerd voor het splitsen van een blob met CSV-indeling.  Zie de sectie [sys. split](#sys-split) van dit artikel.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>Gegevenstypen

|Gegevenstype|Beschrijving|
|---------|-------------------------------------------|
|INT      |64-bits geheel getal met teken.                     |
|FLOAT    |64-bits (Double-Precision) drijvende komma.|
|STRING   |Unicode-teken reeks met variabele lengte.            |
|TIMESTAMP|Een punt in de tijd.                           |
|BOOLEAN  |Waar of onwaar.                             |

Bij het lezen van waarden uit gegevens in CSV-indeling, worden alle waarden als teken reeksen gelezen.  Teken reeks waarden kunnen worden geconverteerd naar andere typen met behulp van CAST-expressies.  Waarden kunnen impliciet worden geconverteerd naar andere typen, afhankelijk van de context. Zie [gegevens type prioriteit (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql)voor meer informatie.

## <a name="expressions"></a>Expressies

### <a name="referencing-fields"></a>Verwijzende velden

Voor gegevens in JSON-indeling, of gegevens in CSV-indeling met een veldnamenrij, kan naar velden worden verwezen met de naam.  Veld namen kunnen worden geciteerd of worden niet in een offerte worden geplaatst. Veld namen in de aanhalings tekens staan tussen dubbele aanhalings teken ("), bevatten mogelijk spaties en zijn hoofdletter gevoelig.  Niet-geciteerde veld namen zijn hoofdletter gevoelig en mogen geen speciale tekens bevatten.

In gegevens in CSV-indeling kunnen velden ook worden verwezen met een rang telwoord, voorafgegaan door een onderstrepings teken (_).  Het eerste veld kan bijvoorbeeld verwijzen naar _1, of naar het elfde veld waarnaar wordt verwezen als _11.  Het verwijzen naar velden op rang telwoord is handig voor gegevens in CSV-indeling die geen koprij bevatten. in dat geval is de enige manier om naar een bepaald veld te verwijzen, gesorteerd op rang telwoord.

### <a name="operators"></a>Operators

De volgende standaard SQL-Opera tors worden ondersteund:

|Operator|Beschrijving|
|--|--|
|[=](/sql/t-sql/language-elements/equals-transact-sql)    |Vergelijkt de gelijkheid van twee expressies (een vergelijkings operator).|
|[!=](/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |Test of een expressie niet gelijk is aan een andere expressie (een vergelijkings operator).|
|[<>](/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |Vergelijkt twee expressies voor niet gelijk aan (een vergelijkings operator).|
|[<](/sql/t-sql/language-elements/less-than-transact-sql)    |Vergelijkt twee expressies voor minder dan (een vergelijkings operator).|
|[<=](/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |Vergelijkt twee expressies voor kleiner dan of gelijk aan (een vergelijkings operator).|
|[>](/sql/t-sql/language-elements/greater-than-transact-sql)    |Vergelijkt twee expressies voor groter dan (een vergelijkings operator). |
|[>=](/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |Vergelijkt twee expressies voor groter dan of gelijk aan (een vergelijkings operator).|
|[+](/sql/t-sql/language-elements/add-transact-sql)    |Telt twee getallen op. Met deze toevoeging reken kundige operator kan ook een getal, in dagen, worden toegevoegd aan een datum.|
|[-](/sql/t-sql/language-elements/subtract-transact-sql)    |Trekt twee getallen af (een reken kundige operator voor aftrekken). |
|[/](/sql/t-sql/language-elements/divide-transact-sql)    |Deelt een getal met een andere waarde (een reken kundige delen-operator).|
|[*](/sql/t-sql/language-elements/multiply-transact-sql)    |Vermenigvuldigt twee expressies (een reken kundige operator voor vermenigvuldigen).|
|[%](/sql/t-sql/language-elements/modulo-transact-sql)    |Retourneert de rest van een getal gedeeld door een ander nummer.|
|[AND](/sql/t-sql/language-elements/bitwise-and-transact-sql)    |Hiermee wordt een bitsgewijze logische en-bewerking uitgevoerd tussen twee waarden met gehele getallen.|
|[OF](/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Hiermee wordt een bitsgewijze logische of-bewerking uitgevoerd tussen twee opgegeven waarden met gehele getallen die worden omgezet naar binaire expressies binnen Transact-SQL-instructies.|
|[NOT](/sql/t-sql/language-elements/not-transact-sql)    |Een Boole-invoer wordt genegeerd.|
|[CAST](/sql/t-sql/functions/cast-and-convert-transact-sql)    |Zet een expressie van één gegevenstype om in een ander type.|
|[VERDEELD](/sql/t-sql/language-elements/between-transact-sql)    |Hiermee geeft u een bereik op om te testen.|
|[Naast](/sql/t-sql/language-elements/in-transact-sql)    |Hiermee wordt bepaald of een opgegeven waarde overeenkomt met een wille keurige waarde in een subquery of een lijst.|
|[NULLIF](/sql/t-sql/language-elements/nullif-transact-sql)    |Retourneert een null-waarde als de twee opgegeven expressies gelijk zijn.|
|[COALESCE](/sql/t-sql/language-elements/coalesce-transact-sql)    |Evalueert de argumenten in volg orde en retourneert de huidige waarde van de eerste expressie die in eerste instantie niet wordt geëvalueerd als NULL.|

Als gegevens typen aan de linkerkant en rechts van een operator verschillend zijn, wordt automatische conversie uitgevoerd volgens de hier opgegeven regels: [gegevens type prioriteit (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql).

De SQL-taal van de query versnelling ondersteunt slechts een zeer kleine subset van de gegevens typen die in dat artikel worden besproken.  Zie de sectie [gegevens typen](#data-types) van dit artikel.

### <a name="casts"></a>Cast conversies

De SQL-taal van de query versnelling ondersteunt de CAST-operator, volgens de regels: [gegevens type conversie (data base-engine)](/sql/t-sql/data-types/data-type-conversion-database-engine).  

De SQL-taal van de query versnelling ondersteunt slechts een kleine subset van de gegevens typen die in dat artikel worden besproken.  Zie de sectie [gegevens typen](#data-types) van dit artikel.

### <a name="string-functions"></a>Tekenreeksfuncties

De SQL-taal voor de query versnelling ondersteunt de volgende standaard SQL-teken reeks functies:

|Functie|Beschrijving|
|--|--|
|CHAR_LENGTH    | Retourneert de lengte in tekens van de teken reeks expressie als de teken reeks expressie van het gegevens type character is; anders retourneert de lengte in bytes van de teken reeks expressie (het kleinste gehele getal niet kleiner dan het aantal bits gedeeld door 8). (Deze functie is hetzelfde als de functie CHARACTER_LENGTH.)|
|CHARACTER_LENGTH    |Retourneert de lengte in tekens van de teken reeks expressie als de teken reeks expressie van het gegevens type character is; anders retourneert de lengte in bytes van de teken reeks expressie (het kleinste gehele getal niet kleiner dan het aantal bits gedeeld door 8). (Deze functie is hetzelfde als de CHAR_LENGTH functie|
|[LOWER](/sql/t-sql/functions/lower-transact-sql)    |Retourneert een teken expressie na het omzetten van hoofd letters in kleine letters.|
|[UPPER](/sql/t-sql/functions/upper-transact-sql)    |Retourneert een teken expressie met kleine teken gegevens die worden omgezet in hoofd letters.|
|[SUBTEKENREEKS](/sql/t-sql/functions/substring-transact-sql)    |Retourneert een deel van een teken-, binaire-, tekst-of afbeeldings expressie in SQL Server.|
|[TRIM](/sql/t-sql/functions/trim-transact-sql)    |Hiermee verwijdert u de spatie tekens (32) of andere opgegeven tekens vanaf het begin en het einde van een teken reeks.|
|WITRUIMTE    |Hiermee verwijdert u de spatie tekens (32) of andere opgegeven tekens vanaf het begin van een teken reeks.|
|Volg    |Hiermee verwijdert u de spatie tekens (32) of andere opgegeven tekens vanaf het einde van een teken reeks.|

Hier volgen enkele voor beelden:

|Functie|Voorbeeld|Resultaat|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>Datumfuncties

De volgende standaard functies voor SQL-datum worden ondersteund:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Momenteel worden alle [datum notaties van standaard-IS08601](https://www.w3.org/TR/NOTE-datetime)geconverteerd. 

#### <a name="date_add-function"></a>Functie DATE_ADD

De SQL-taal voor de query versnelling ondersteunt jaar, maand, dag, uur, minuut en seconde voor de ``DATE_ADD`` functie.

Voorbeelden:

' ' SQL DATE_ADD (date part, Quantity, Time Stamp) DATE_ADD (' minute ', 1, CAST (' 2017-01-02T03:04:05.006 Z ' AS Time Stamp)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>Functie extra heren

Voor een andere uitpakken dan het datum gedeelte ``DATE_ADD`` dat voor de functie wordt ondersteund, ondersteunt de SQL-taal van de query versnelling timezone_hour en timezone_minute als datum gedeelte.

Voorbeelden:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>Functie TO_STRING

Voorbeelden:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

In deze tabel worden teken reeksen beschreven die u kunt gebruiken om de uitvoer indeling van de functie op te geven ``TO_STRING`` .

|Notatietekenreeks    |Uitvoer                               |
|-----------------|-------------------------------------|
|yy               |Jaar met een notatie van 2 cijfers – 1999 als ' 99 '|
|y                |Jaar in 4-cijfer notatie               |
|yyyy             |Jaar in 4-cijfer notatie               |
|M                |Maand van jaar – 1                    |
|MM               |Nul opgevulde maand – 01               |
|MMM              |AFK. maand van jaar-JAN            |
|MMMM             |Volledige maand – mei                      |
|d                |Dag van de maand (1-31)                  |
|dd               |Nul opgevulde dag van de maand (01-31)     |
|een                |AM of PM                             |
|h                |Uur van de dag (1-12)                   |
|hh               |Dag van nul opgevulde uren (01-12)     |
|H                |Uur van de dag (0-23)                   |
|HH               |Nul van de dag (00-23)      |
|m                |Minuut van uur (0-59)                |
|mm               |Nul opgevulde minuut (00-59)           |
|s                |Seconde van minuten (0-59)             |
|ss               |Nul opgevulde seconden (00-59)          |
|S                |Fractie van seconden (0.1-0,9)        |
|SS               |Fractie van seconden (0,01-0.99)      |
|SSS              |Fractie van seconden (0,001-0.999)    |
|X                |Offset in uren                      |
|XX of XXXX       |Offset in uren en minuten (+ 0430)  |
|XXX of XXXXX     |Offset in uren en minuten (-07:00) |
|x                |Verschuiving in uren (7)                  |
|xx of XXXX       |Offset in uur en minuut (+ 0530)    |
|Xxx of xxxxx     |Offset in uur en minuut (+ 05:30)   |

#### <a name="to_timestamp-function"></a>Functie TO_TIMESTAMP

Alleen IS08601-indelingen worden ondersteund.

Voorbeelden:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> U kunt ook de ``UTCNOW`` functie gebruiken om de systeem tijd op te halen.


## <a name="aggregate-expressions"></a>Samengestelde expressies

Een SELECT-instructie kan een of meer projectie-expressies of één statistische expressie bevatten.  De volgende aggregatie expressies worden ondersteund:

|Expressie|Beschrijving|
|--|--|
|[AANTAL ( \* )](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Retourneert het aantal records dat overeenkomt met de predicaat expressie.|
|[AANTAL (expressie)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Retourneert het aantal records waarvoor een expressie niet null is.|
|[GEMIDDELD (expressie)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |Retourneert het gemiddelde van de waarden van de expressie die niet gelijk zijn aan nul.|
|[MIN (expressie)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |Retourneert de minimale niet-null-waarde van een expressie.|
|[MAX (expressie](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |Retourneert de maximum waarde van een expressie die niet null is.|
|[SUM (expressie)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |Retourneert de som van alle waarden in de expressie die niet gelijk zijn aan nul.|

### <a name="missing"></a>ONGELDIGE

De ``IS MISSING`` operator is de enige niet-standaard die de SQL-taal voor de query versnelling ondersteunt.  Voor JSON-gegevens geldt dat als een veld ontbreekt in een bepaalde invoer record, het expressie veld ``IS MISSING`` resulteert in de Booleaanse waarde True.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Tabel descriptoren

Voor CSV-gegevens is de naam van de tabel altijd `BlobStorage` .  Bijvoorbeeld:

```sql
SELECT * FROM BlobStorage
```

Voor JSON-gegevens zijn aanvullende opties beschikbaar:

```sql
SELECT * FROM BlobStorage[*].path
```

Hiermee kunt u query's over subsets van de JSON-gegevens toestaan.

Voor JSON-query's kunt u het pad in een deel van de component FROM vermelden. Deze paden helpen bij het parseren van de subset van JSON-gegevens. Deze paden kunnen verwijzen naar JSON-matrix-en object waarden.

Hier volgt een voor beeld waarin u meer informatie kunt krijgen.

Dit zijn onze voorbeeld gegevens:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

U bent mogelijk alleen geïnteresseerd in het `warehouses` JSON-object van de bovenstaande gegevens. Het `warehouses` object is een JSON-matrix type, zodat u dit kunt vermelden in de component from. Uw voorbeeld query kan er ongeveer als volgt uitzien.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

De query haalt alle velden op, maar selecteert alleen de breedte graad.

Als u alleen toegang wilt krijgen tot de `dimensions` waarde van het JSON-object, kunt u in uw query verwijzen naar dat object. Bijvoorbeeld:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Hiermee beperkt u ook de toegang tot leden van het `dimensions` object. Als u toegang wilt krijgen tot andere leden van JSON-velden en binnenste waarden van JSON-objecten, kunt u een query gebruiken zoals wordt weer gegeven in het volgende voor beeld:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage en BlobStorage [ \* ] verwijzen beide naar het hele object. Als u echter een pad in de component FROM hebt, moet u BlobStorage [ \* ]. Path gebruiken

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys. split

Dit is een speciale vorm van de SELECT-instructie, die alleen beschikbaar is voor gegevens in CSV-indeling.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Gebruik deze instructie in gevallen waarin u CSV-gegevens records wilt downloaden en vervolgens wilt verwerken in batches. Op die manier kunt u records parallel verwerken in plaats van alle records in één keer te downloaden. Deze instructie retourneert geen records uit het CSV-bestand. In plaats daarvan wordt een verzameling batch grootten geretourneerd. U kunt vervolgens elke batch grootte gebruiken om een batch gegevens records op te halen. 

Gebruik de para meter *split_size* om het aantal bytes op te geven dat elke batch moet bevatten. Als u bijvoorbeeld slechts 10 MB aan gegevens per keer wilt verwerken, ziet u de volgende instructie: `SELECT sys.split(10485760)FROM BlobStorage` omdat 10 MB gelijk is aan 10.485.760 bytes. Elke batch bevat zoveel records als kan worden aangepast aan deze 10 MB. 

In de meeste gevallen is de grootte van elke batch iets hoger dan het getal dat u opgeeft. Dat komt omdat een batch geen gedeeltelijke record kan bevatten. Als de laatste record in een batch vóór het einde van de drempel waarde begint, wordt de batch groter zodat de volledige record kan worden opgenomen. De grootte van de laatste batch is waarschijnlijk kleiner dan de grootte die u opgeeft.

>[!NOTE]
> De split_size moet mini maal 10 MB (10485760) zijn.

## <a name="see-also"></a>Zie ook

- [Azure Data Lake Storage-query versnelling](data-lake-storage-query-acceleration.md)
- [Gegevens filteren met behulp van Azure Data Lake Storage-query versnelling](data-lake-storage-query-acceleration-how-to.md)