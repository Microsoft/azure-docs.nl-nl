---
title: De U-SQL-catalogus gebruiken in Azure Data Lake Analytics
description: Meer informatie over het gebruik van de U-SQL-catalogus voor het delen van code en gegevens. Maak functies voor tabel waarden, maak weer gaven, maak tabellen en zoek ze op.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/09/2017
ms.openlocfilehash: f92aadc8ccf18dd91b5dd4b35285f60b174e4cf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92220071"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Aan de slag met de U-SQL-catalogus in Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Een TVF maken

In het vorige U-SQL-script hebt u het gebruik van EXTRACT voor het lezen van hetzelfde bron bestand herhaald. Met de U-SQL-functie voor tabel waarden (TVF) kunt u de gegevens inkapselen voor toekomstig gebruik.  

Met het volgende script maakt u een TVF die wordt aangeroepen `Searchlog()` in de standaard database en het schema:

```usql
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
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
RETURN;
END;
```

In het volgende script ziet u hoe u de TVF gebruikt die in het vorige script is gedefinieerd:

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Weergaven maken

Als u één query-expressie hebt, kunt u in plaats van een TVF een U-SQL-weer gave gebruiken om die expressie te integreren.

Met het volgende script maakt u een weer gave die wordt aangeroepen `SearchlogView` in de standaard database en het schema:

```usql
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

Het volgende script toont het gebruik van de gedefinieerde weer gave:

```usql
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Tabellen maken
Net als bij relationele database tabellen kunt u met U-SQL een tabel maken met een vooraf gedefinieerd schema of een tabel maken die het schema afleidt van de query waarmee de tabel wordt gevuld (ook wel CREATE TABLE als SELECT of CTAS).

Maak een Data Base en twee tabellen met behulp van het volgende script:

```usql
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Querytabellen
U kunt een query uitvoeren op tabellen, zoals die zijn gemaakt in het vorige script, op dezelfde manier als u een query uitvoert op de gegevens bestanden. In plaats van een rijenset te maken met behulp van EXTRACT, kunt u nu verwijzen naar de tabel naam.

Als u de tabellen wilt lezen, wijzigt u het transformatie script dat u eerder hebt gebruikt:

```usql
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Op dit moment kunt u geen selectie uitvoeren in een tabel in hetzelfde script als de locatie waar u de tabel hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics-taken bewaken en problemen oplossen met Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
