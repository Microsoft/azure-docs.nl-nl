---
title: Aan de slag met U-SQL-taal in Azure Data Lake Analytics
description: Meer informatie over de basis principes van de U-SQL-taal in Azure Data Lake Analytics. Schrijf uw eerste query met behulp van variabelen naar extra gegevens uit bestanden, Transformeer de rijenset en Verzamel gegevens.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/23/2017
ms.openlocfilehash: 5920f2b5823e568d56d13107200eb0025a654db0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219918"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Aan de slag met U-SQL in Azure Data Lake Analytics

U-SQL is een taal die declaratieve SQL combineert met dwingende C#, zodat u gegevens op elke schaal kunt verwerken. U kunt met behulp van de schaal bare, gedistribueerde query mogelijkheden van U-SQL efficiënt gegevens in relationele winkels, zoals Azure SQL Database, analyseren. Met U-SQL kunt u ongestructureerde gegevens verwerken door schema toe te passen op lezen en aangepaste logica en Udf's in te voegen. Daarnaast bevat U-SQL-uitbrei ding waarmee u nauw keurige controle krijgt over het uitvoeren op schaal.

## <a name="learning-resources"></a>Trainingsmateriaal

* De [u-SQL-zelf studie](https://aka.ms/usqltutorial) biedt een begeleide walkthrough van de meeste u-SQL-taal. Dit document wordt aanbevolen om te lezen voor alle ontwikkel aars die U-SQL willen leren kennen.
* Zie de naslag informatie voor de [u-SQL](/u-sql/)-taal voor gedetailleerde gegevens over de **u-SQL-taal syntaxis**.
* Zie voor meer informatie over de **u-SQL-ontwerp filosofie** de Visual Studio blog post [Inleiding U-SQL – een taal die het verwerken van Big data eenvoudig maakt](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Vereisten

Voordat u de U-SQL-voor beelden in dit document gaat door lopen, lees en volledige [zelf studie: U-SQL-scripts ontwikkelen met data Lake-Hulpprogram ma's voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md). In deze zelf studie worden de mechanismen uitgelegd van het gebruik van U-SQL met Azure Data Lake-Hulpprogram Ma's voor Visual Studio.

## <a name="your-first-u-sql-script"></a>Uw eerste U-SQL-script

Het volgende U-SQL-script is eenvoudig en Hiermee kunnen we veel aspecten van de U-SQL-taal verkennen.

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Dit script heeft geen transformatie stappen. Het Lees programma van het bron bestand met de naam `SearchLog.tsv` schematizes en schrijft de rijenset terug naar een bestand met de naam SearchLog-first-u-sql.csv.

Let op het vraag teken naast het gegevens type in het `Duration` veld. Dit betekent dat het `Duration` veld Null kan zijn.

### <a name="key-concepts"></a>Belangrijkste concepten

* **Variabelen van de rijenset**: elke query-expressie die een rijenset produceert, kan worden toegewezen aan een variabele. U-SQL volgt het naamgevings patroon T-SQL-variabele ( `@searchlog` bijvoorbeeld) in het script.
* Het sleutel woord **extract** leest gegevens uit een bestand en definieert het schema bij lezen. `Extractors.Tsv` is een ingebouwde U-SQL-extractor voor bestanden met door tabs gescheiden waarden. U kunt aangepaste Extracts ontwikkelen.
* De **uitvoer** schrijft gegevens van een rijenset naar een bestand. `Outputters.Csv()` is een ingebouwde U-SQL-outputter voor het maken van een bestand met door komma's gescheiden waarden. U kunt aangepaste outputters ontwikkelen.

### <a name="file-paths"></a>Bestands paden

De instructies EXTRACT en OUTPUT gebruiken bestands paden. Bestands paden kunnen absoluut of relatief zijn:

Het volgende absolute bestandspad verwijst naar een bestand in een Data Lake Store met de naam `mystore` :

```usql
adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv
```

Het volgende bestandspad begint met `"/"` . Dit verwijst naar een bestand in de standaard Data Lake Store account:

```usql
/output/SearchLog-first-u-sql.csv
```

## <a name="use-scalar-variables"></a>Scalaire variabelen gebruiken

U kunt scalaire variabelen ook gebruiken om uw script onderhoud eenvoudiger te maken. Het vorige U-SQL-script kan ook worden geschreven als:

```usql
DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
DECLARE @out string = "/output/SearchLog-scalar-variables.csv";
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv();
OUTPUT @searchlog
    TO @out
    USING Outputters.Csv();
```

## <a name="transform-rowsets"></a>Rijen sets transformeren

Gebruik **selecteren** om rijen sets te transformeren:

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";
OUTPUT @rs1
    TO "/output/SearchLog-transform-rowsets.csv"
    USING Outputters.Csv();
```

De component WHERE maakt gebruik van een [C# Boole-expressie](/dotnet/csharp/language-reference/operators/index). U kunt de C#-expressie taal gebruiken om uw eigen expressies en functies uit te voeren. U kunt zelfs complexere filters uitvoeren door ze te combi neren met logische samen voegingen (and) en ontkoppelingen (or).

Het volgende script maakt gebruik van de methode DateTime. parse () en een combi natie van.

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";
@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");
OUTPUT @rs1
    TO "/output/SearchLog-transform-datetime.csv"
    USING Outputters.Csv();
```

 >[!NOTE]
 >De tweede query wordt uitgevoerd op het resultaat van de eerste rijenset, waardoor een samen stelling van de twee filters wordt gemaakt. U kunt ook een naam van een variabele hergebruiken en de namen worden op een lexicale scope toegepast.

## <a name="aggregate-rowsets"></a>Cumulatieve rijen sets

U-SQL geeft u de bekende volg orde van, groeperen op en aggregaties.

Met de volgende query wordt de totale duur per regio gezocht, waarna de vijf meest voorkomende duur in de volg orde wordt weer gegeven.

U-SQL-rijen sets bewaren de volg orde van de volgende query niet. Als u een uitvoer wilt best Ellen, moet u dus een bestelling toevoegen aan de instructie OUTPUT:

```usql
DECLARE @outpref string = "/output/Searchlog-aggregation";
DECLARE @out1    string = @outpref+"_agg.csv";
DECLARE @out2    string = @outpref+"_top5agg.csv";
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM @searchlog
GROUP BY Region;
@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;
OUTPUT @rs1
    TO @out1
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
OUTPUT @res
    TO @out2
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

Voor de U-SQL ORDER BY-component moet de component FETCH worden gebruikt in een SELECT-expressie.

De component U-SQL HAVING kan worden gebruikt om de uitvoer te beperken tot groepen die voldoen aan de voor waarde HAVING:

```usql
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;
OUTPUT @res
    TO "/output/Searchlog-having.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

Zie de naslag documentatie over U-SQL voor [aggregatie, analyse en referentie functies](/u-sql/built-in-functions) voor geavanceerde aggregatie scenario's

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [U-SQL-scripts ontwikkelen met Data Lake-tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)