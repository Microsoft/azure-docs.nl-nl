---
title: Programmeer gids U-SQL UDT en UDAGG voor Azure Data Lake
description: Meer informatie over de programmeer baarheid U-SQL UDT en UDAGG in Azure Data Lake Analytics om u in staat te stellen een goed USQL-script te maken.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 10fcce9a667d9a08318f5adab804f482387052ff
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606654"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>Programmeer handleiding voor U-SQL-UDT en UDAGG



## <a name="use-user-defined-types-udt"></a>Door de gebruiker gedefinieerde typen gebruiken: UDT
Door de gebruiker gedefinieerde typen of UDT is een andere programmeer baarheids functie van U-SQL. U-SQL UDT fungeert als een standaard door de gebruiker gedefinieerd C#-type. C# is een sterk getypeerde taal waarmee ingebouwde en aangepaste door de gebruiker gedefinieerde typen kunnen worden gebruikt.

U-SQL kan wille keurige UDTs niet impliciet serialiseren of deserialiseren wanneer de UDT tussen hoek punten in rijen sets wordt door gegeven. Dit betekent dat de gebruiker een expliciete formatter moet opgeven met behulp van de IFormatter-interface. Dit biedt U-SQL met de methoden serialiseren en deserialiseren voor de UDT.

> [!NOTE]
> De ingebouwde extracten en outputters van U-SQL kunnen momenteel geen UDT-gegevens serialiseren of deserialiseren naar of van bestanden, zelfs als de IFormatter is ingesteld. Als u UDT-gegevens naar een bestand met de uitvoer instructie schrijft of als u een extractie uitvoert met een Extractor, moet u deze door geven als een teken reeks of byte matrix. Vervolgens roept u de code voor serialisatie en deserialisatie aan (dat wil zeggen, de methode ToString () van UDT). Door de gebruiker gedefinieerde uittreksels en outputters kunnen ook UDTs lezen en schrijven.

Als we UDT proberen te gebruiken in EXTRACTOR of outputter (van eerdere selectie), zoals hier wordt weer gegeven:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

De volgende fout wordt weer gegeven:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Als u wilt werken met UDT in outputter, moet u deze serialiseren naar een teken reeks met de methode ToString () of een aangepaste outputter maken.

UDTs kan momenteel niet worden gebruikt in GROUP BY. Als UDT in GROUP BY wordt gebruikt, wordt de volgende fout gegenereerd:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

We moeten het volgende doen om een UDT te definiëren:

1. Voeg de volgende naam ruimten toe:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. Add `Microsoft.Analytics.Interfaces` , vereist voor de UDT-interfaces. Daarnaast `System.IO` kan het nodig zijn om de IFormatter-interface te definiëren.

3. Definieer een gebruikt gedefinieerd type met het kenmerk SqlUserDefinedType.

**SqlUserDefinedType** wordt gebruikt om een type definitie in een assembly te markeren als een door de gebruiker gedefinieerd type (UDT) in U-SQL. De eigenschappen van het kenmerk reflecteren de fysieke kenmerken van de UDT. Deze klasse kan niet worden overgenomen.

SqlUserDefinedType is een vereist kenmerk voor de UDT-definitie.

De constructor van de klasse:  

* SqlUserDefinedTypeAttribute (type formatter)

* Type formatter: vereiste para meter voor het definiëren van een UDT-formatter--specifiek moet het type van de `IFormatter` Interface hier worden door gegeven.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Voor de standaard-UDT is ook definitie van de IFormatter-interface vereist, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

De `IFormatter` interface is een serialisatie en de serialisatie van een object grafiek met het hoofd type van \<typeparamref name="T"> .

\<typeparam name="T">Het hoofd type voor de object grafiek die moet worden geserialiseerd en gedeserialiseerd.

* **Deserialiseren**: de gegevens op de verschafte stroom deserialiseren en het grafiek object opnieuw indelen.

* **Serialiseren**: een object of grafiek met objecten met de opgegeven basis naar de opgegeven stroom serialiseren.

`MyType` exemplaar: exemplaar van het type.  
`IColumnWriter` schrijver/ `IColumnReader` lezer: de onderliggende kolom stroom.  
`ISerializationContext` context: Enum die een set vlaggen definieert waarmee de bron-of doel context voor de stream wordt opgegeven tijdens de serialisatie.

* **Tussenliggend**: Hiermee geeft u op dat de bron-of doel context geen persistente archief is.

* **Persistentie**: Hiermee geeft u op dat de bron-of doel context een persistente opslag is.

Als een normaal C#-type kan een U-SQL UDT-definitie onderdrukkingen bevatten voor Opera tors zoals +/= =/! =. Het kan ook statische methoden bevatten. Als we deze UDT bijvoorbeeld gaan gebruiken als een para meter voor een statistische functie met U-SQL MIN, moeten we < operator override definiëren.

Eerder in deze hand leiding is een voor beeld van de identificatie van de fiscale periode van de specifieke datum in de notatie gedemonstreerd `Qn:Pn (Q1:P10)` . In het volgende voor beeld ziet u hoe u een aangepast type definieert voor fiscale periode waarden.

Hieronder volgt een voor beeld van een code-behind sectie met de aangepaste UDT-en IFormatter-Interface:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Het gedefinieerde type bevat twee getallen: kwar taal en maand. De Opera tors `==/!=/>/<` en de statische methode `ToString()` worden hier gedefinieerd.

Zoals eerder vermeld, kan UDT worden gebruikt in SELECT-expressies, maar kan niet worden gebruikt in outputter/EXTRACTOR zonder aangepaste serialisatie. De waarde moet worden geserialiseerd als een teken reeks met `ToString()` of worden gebruikt in combi natie met een aangepaste OUTputter/extractor.

Nu gaan we het gebruik van UDT bespreken. In de sectie code-behind is de functie GetFiscalPeriod gewijzigd in het volgende:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Zoals u kunt zien, wordt de waarde van het type FiscalPeriod geretourneerd.

Hier bieden we een voor beeld van hoe u dit verder in het U-SQL-basis script kunt gebruiken. In dit voor beeld worden verschillende soorten UDT-aanroep van het U-SQL-script gedemonstreerd.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
        start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        fiscalquarter,
        fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Hier volgt een voor beeld van een volledige code-behind:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Door de gebruiker gedefinieerde statistische functies gebruiken: UDAGG
Door de gebruiker gedefinieerde aggregaties zijn alle functies voor aggregatie die niet aan U zijn gekoppeld met U-SQL. Het voor beeld kan een statistische functie zijn voor het uitvoeren van aangepaste wiskundige berekeningen, het samen voegen van teken reeksen, bewerkingen met teken reeksen, enzovoort.

De door de gebruiker gedefinieerde definitie van de basis klasse is als volgt:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde statistische functie. Deze klasse kan niet worden overgenomen.

Het kenmerk SqlUserDefinedType is **optioneel** voor de definitie van de UDAGG.


Met de basis klasse kunt u drie abstracte para meters door geven: twee als invoer parameters en een als resultaat. De gegevens typen zijn variabele en moeten tijdens de overname van de klasse worden gedefinieerd.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** roept één keer op voor elke groep tijdens de berekening. Het bevat een initialisatie routine voor elke samenvoegings groep.  
* De **accumulatie** wordt één keer uitgevoerd voor elke waarde. Het biedt de belangrijkste functionaliteit voor het aggregatie algoritme. Het kan worden gebruikt om waarden samen te voegen met verschillende gegevens typen die tijdens de overname van de klasse worden gedefinieerd. Er kunnen twee para meters van variabele gegevens typen worden geaccepteerd.
* Er wordt één keer per samenvoegings groep een **afsluiting** uitgevoerd om het resultaat voor elke groep te kunnen uitvoeren.

Als u de juiste invoer-en uitvoer gegevens typen wilt declareren, gebruikt u de klassedefinitie als volgt:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: eerste para meter die moet worden verzameld
* T2: tweede para meter die moet worden verzameld
* TResult: retour type van Terminate

Bijvoorbeeld:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

of

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>UDAGG gebruiken in U-SQL
Als u UDAGG wilt gebruiken, moet u deze eerst definiëren in de code-behind of ernaar verwijzen vanuit de bestaande programmeer baarheids-DLL, zoals eerder besproken.

Gebruik vervolgens de volgende syntaxis:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Hier volgt een voor beeld van UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

En base U-SQL-script:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

In dit scenario voor gebruik worden klasse-GUID'S voor de specifieke gebruikers samengevoegd.

## <a name="next-steps"></a>Volgende stappen
* [Programmeer handleiding U-SQL-overzicht](data-lake-analytics-u-sql-programmability-guide.md)
* [Programmeer handleiding voor U-SQL-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
