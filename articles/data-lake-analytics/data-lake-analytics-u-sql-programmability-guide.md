---
title: Programmeer handleiding voor U-SQL voor Azure Data Lake
description: Meer informatie over de U-SQL Overview en UDF-programmeer baarheid in Azure Data Lake Analytics om u in staat te stellen een goed USQL-script te maken.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: d263f2616607a96a8aa14f1ad1c06330b1b44644
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96510859"
---
# <a name="u-sql-programmability-guide-overview"></a>Overzicht van de programmeer handleiding voor U-SQL

U-SQL is een query taal die is ontworpen voor big data type werk belastingen. Een van de unieke functies van U-SQL is de combi natie van de SQL-achtige declaratieve taal met de uitbreid baarheid en programmeer baarheid die wordt verschaft door C#. In deze hand leiding wordt gestreefd naar de uitbreid baarheid en programmeer baarheid van de U-SQL-taal die is ingeschakeld door C#.

## <a name="requirements"></a>Vereisten

Down load en Installeer [Azure data Lake-Hulpprogram ma's voor Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

## <a name="get-started-with-u-sql"></a>Aan de slag met U-SQL  

Bekijk het volgende U-SQL-script:

```usql
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

Dit script definieert twee rijen sets: `@a` en `@results` . De rijenset `@results` is gedefinieerd vanuit `@a` .

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-typen en-expressies in U-SQL-script

Een U-SQL-expressie is een C#-expressie in combi natie met logische U-SQL-bewerkingen, zoals, `AND` `OR` en `NOT` . U-SQL-expressies kunnen worden gebruikt in combi natie met selecteren, uitpakken, waar, groeperen op en DECLAReren. Met het volgende script wordt bijvoorbeeld een teken reeks geparseerd als een DateTime-waarde.

```usql
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

In het volgende fragment wordt een teken reeks geparseerd als DateTime-waarde in een DECLARe-instructie.

```usql
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C#-expressies gebruiken voor gegevens type conversies

In het volgende voor beeld ziet u hoe u een datum/tijd-gegevens conversie kunt uitvoeren met behulp van C#-expressies. In dit specifieke scenario worden datetime-gegevens met een teken reeks geconverteerd naar een standaard-datetime met een datum notatie van middernacht 00:00:00.

```usql
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>C#-expressies gebruiken voor de datum van vandaag

We kunnen de volgende C#-expressie gebruiken om de datum van vandaag te halen: `DateTime.Now.ToString("M/d/yyyy")`

Hier volgt een voor beeld van het gebruik van deze expressie in een script:

```usql
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>.NET-assembly's gebruiken

Het uitbreidings model van U-SQL is sterk afhankelijk van de mogelijkheid om aangepaste code uit .NET-assembly's toe te voegen. 

### <a name="register-a-net-assembly"></a>Een .NET-assembly registreren

Gebruik de `CREATE ASSEMBLY` instructie voor het plaatsen van een .NET-assembly in een U-SQL database. Daarna kunnen U-SQL-scripts deze assembly's gebruiken met behulp van de- `REFERENCE ASSEMBLY` instructie. 

De volgende code laat zien hoe u een assembly registreert:

```usql
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

De volgende code laat zien hoe u naar een assembly verwijst:

```usql
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Raadpleeg de [instructies van de assembly-registratie](/archive/blogs/azuredatalake/how-to-register-u-sql-assemblies-in-your-u-sql-catalog) voor meer informatie over dit onderwerp.


### <a name="use-assembly-versioning"></a>Assembly versie beheer gebruiken
Op dit moment gebruikt U-SQL de .NET Framework versie 4.7.2. Zorg ervoor dat uw eigen assembly's compatibel zijn met deze versie van de runtime.

Zoals eerder vermeld, voert U-SQL-code uit in een 64-bits (x64)-indeling. Zorg er dus voor dat uw code wordt gecompileerd om te worden uitgevoerd op x64. Anders krijgt u de onjuiste indelings fout die eerder is weer gegeven.

Elke geüploade assembly-DLL en resource bestand, zoals een andere runtime, een systeem eigen assembly of een configuratie bestand, kunnen Maxi maal 400 MB groot zijn. De totale grootte van geïmplementeerde resources, hetzij via DEPLOY RESOURCE, hetzij via verwijzingen naar assembly's en hun extra bestanden, kan niet groter zijn dan 3 GB.

Houd er rekening mee dat elke U-SQL database slechts één versie van een bepaalde assembly mag bevatten. Als u bijvoorbeeld versie 7 en versie 8 van de Json.NET-bibliotheek van Newton Soft nodig hebt, moet u deze registreren in twee verschillende data bases. Bovendien kan elk script slechts verwijzen naar één versie van een bepaalde assembly-DLL. In dit opzicht volgt U-SQL de C#-assemblage beheer en versie beheer semantiek.

## <a name="use-user-defined-functions-udf"></a>Door de gebruiker gedefinieerde functies gebruiken: UDF
Door de gebruiker gedefinieerde U-SQL-functies of UDF zijn programmeer routines die para meters accepteren, een actie uitvoeren (zoals een complexe berekening) en het resultaat van die actie als een waarde Retour neren. De geretourneerde waarde van UDF kan alleen één scalair zijn. U-SQL UDF kan worden aangeroepen in U-SQL-basis script, zoals elke andere scalaire functie van C#.

U wordt aangeraden de door de gebruiker gedefinieerde U-SQL-functies als **Public** en **static** te initialiseren.

```usql
public static string MyFunction(string param1)
{
    return "my result";
}
```

Eerst kijken we naar het eenvoudige voor beeld van het maken van een UDF.

In dit geval moet u de fiscale periode bepalen, inclusief het fiscale kwar taal en de boek maand van de eerste aanmelding voor de specifieke gebruiker. De eerste boek maand van het jaar in ons scenario is juni.

Als u de boek periode wilt berekenen, wordt de volgende C#-functie geïntroduceerd:

```usql
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
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

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

De fiscale maand en het kwar taal worden alleen berekend en er wordt een teken reeks waarde geretourneerd. Voor juni, de eerste maand van het eerste boek kwartaal, gebruiken we "Q1: P1". Voor juli gebruiken we "Q1: P2", enzovoort.

Dit is een reguliere C#-functie die we gaan gebruiken in het U-SQL-project.

Hier ziet u hoe de code-behind sectie in dit scenario wordt weer gegeven:

```usql
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
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

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Nu gaan we deze functie aanroepen vanuit het basis-U-SQL-script. Hiervoor moeten we een volledig gekwalificeerde naam voor de functie opgeven, met inbegrip van de naam ruimte, in dit geval naam ruimte. class. function (para meter).
```usql
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Hieronder vindt U het feitelijke U-SQL-basis script:
```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Hieronder volgt het uitvoer bestand van de uitvoering van het script:

```output
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

In dit voor beeld wordt een eenvoudig gebruik van inline UDF in U-SQL gedemonstreerd.

### <a name="keep-state-between-udf-invocations"></a>Status tussen UDF-aanroepen blijven
U-SQL C#-programmeer bare objecten kunnen geavanceerder worden en interactiviteit gebruiken via de globale variabelen van de code-behind. Laten we eens kijken naar het volgende scenario voor zakelijk gebruik.

In grote organisaties kunnen gebruikers scha kelen tussen varieteiten van interne toepassingen. Dit kan micro soft Dynamics CRM, Power BI, enzovoort zijn. Klanten willen mogelijk een telemetrie-analyse Toep assen op de manier waarop gebruikers scha kelen tussen verschillende toepassingen, de gebruiks trends, enzovoort. Het doel van het bedrijf is het optimaliseren van het toepassings gebruik. Het kan ook zijn dat er verschillende toepassingen of specifieke aanmeldings routines moeten worden gecombineerd.

Om dit doel te verzorgen, moeten we de sessie-Id's en vertragings tijd tussen de laatste sessie bepalen die zijn opgetreden.

U moet een eerdere aanmelding vinden en deze aanmelding vervolgens toewijzen aan alle sessies die worden gegenereerd voor dezelfde toepassing. De eerste uitdaging is dat U-SQL-basis script geen berekeningen kan Toep assen op reeds berekende kolommen met de functie LAG. De tweede uitdaging is dat de specifieke sessie binnen dezelfde periode voor alle sessies moet worden bewaard.

Om dit probleem op te lossen, gebruiken we een globale variabele in een code-behind-sectie: `static public string globalSession;` .

Deze globale variabele wordt tijdens de uitvoering van het script toegepast op de hele rijenset.

Dit is de code-behind sectie van het U-SQL-programma:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

In dit voor beeld ziet u de globale variabele `static public string globalSession;` die wordt gebruikt in de functie en die telkens opnieuw `getStampUserSession` wordt geïnitialiseerd wanneer de sessie parameter wordt gewijzigd.

Het U-SQL base-script ziet er als volgt uit:

```usql
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

`USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)`De functie wordt hier genoemd tijdens de tweede berekening van de geheugen rijenset. De `UserSessionTimestamp` kolom wordt door gegeven en retourneert de waarde totdat deze `UserSessionTimestamp` is gewijzigd.

Het uitvoer bestand is als volgt:

```output
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

In dit voor beeld ziet u een complexere gebruiks scenario waarbij we een globale variabele in een code-behind-sectie gebruiken die wordt toegepast op de hele geheugen rijenset.

## <a name="next-steps"></a>Volgende stappen
* [Programmeer handleiding voor U-SQL-UDT en UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)
* [Programmeer handleiding voor U-SQL-UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)