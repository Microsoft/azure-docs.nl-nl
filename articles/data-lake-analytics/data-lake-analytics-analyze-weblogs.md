---
title: Websitelogboeken analyseren met Azure Data Lake Analytics
description: Meer informatie over het analyseren van website logboeken met Azure Data Lake Analytics om U-SQL-functies en-query's uit te voeren.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: d2764e5e3994eb7b80a8e7cf62fb7f437323c97e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015295"
---
# <a name="analyze-website-logs-using-azure-data-lake-analytics"></a>Websitelogboeken analyseren met Azure Data Lake Analytics
Meer informatie over het analyseren van website logboeken met behulp van Data Lake Analytics, met name voor het vinden van de verwijzende functies bij het bezoeken van de website.

## <a name="prerequisites"></a>Vereisten
* **Visual studio 2015 of Visual studio 2013**.
* **[Data Lake Tools voor Visual Studio](https://aka.ms/adltoolsvs)**.

    Als Data Lake-Hulpprogram Ma's voor Visual Studio is geïnstalleerd, ziet u een **Data Lake** item in het menu **extra** in Visual Studio:

    ![Visual Studio-menu van U-SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)
* **Basis kennis van data Lake Analytics en de data Lake-Hulpprogram ma's voor Visual Studio**. Als u aan de slag wilt gaan, raadpleegt u:

  * [U-SQL-script ontwikkelen met data Lake-hulpprogram ma's voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* **Een Data Lake Analytics-account.**  Zie [een Azure data Lake Analytics-account maken](data-lake-analytics-get-started-portal.md).
* **Installeer de voorbeeld gegevens.** Open in azure Portal de optie Data Lake Analytics account en klik op **voorbeeld scripts** in het linkermenu en klik vervolgens op **voorbeeld gegevens kopiëren**. 

## <a name="connect-to-azure"></a>Verbinding maken met Azure
Voordat u u-SQL-scripts kunt bouwen en testen, moet u eerst verbinding maken met Azure.

### <a name="to-connect-to-data-lake-analytics"></a>Verbinding maken met Data Lake Analytics

1. Open Visual Studio.
2. Klik op **Data Lake > opties en instellingen**.
3. Klik op **Aanmelden** of **Wijzig de gebruiker** als iemand zich heeft aangemeld en volg de instructies.
4. Klik op **OK** om het dialoog venster Opties en instellingen te sluiten.

### <a name="to-browse-your-data-lake-analytics-accounts"></a>Door uw Data Lake Analytics-accounts bladeren

1. Open in Visual Studio **Server Explorer** door op **CTRL + ALT + S** te drukken.
2. Vouw in **Server Explorer** achtereenvolgens **Azure** en **Data Lake Analytics** uit. U ziet een lijst met uw Data Lake Analytics-accounts, als u die hebt. U kunt geen Data Lake Analytics accounts maken vanuit Studio. Als u een account wilt maken, raadpleegt u aan de [slag met Azure data Lake Analytics met behulp van Azure Portal](data-lake-analytics-get-started-portal.md) of [aan de slag met Azure data Lake Analytics met behulp van Azure PowerShell](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>U-SQL-toepassing ontwikkelen
Een U-SQL-toepassing is doorgaans een U-SQL-script. Zie aan de [slag met u-SQL](data-lake-analytics-u-sql-get-started.md)voor meer informatie over u-SQL.

U kunt aanvullende door de gebruiker gedefinieerde Opera tors toevoegen aan de toepassing.  Zie door de [gebruiker gedefinieerde U-SQL-Opera tors ontwikkelen voor data Lake Analytics-taken](data-lake-analytics-u-sql-develop-user-defined-operators.md)voor meer informatie.

### <a name="to-create-and-submit-a-data-lake-analytics-job"></a>Een Data Lake Analytics-taak maken en verzenden

1. Klik op het **bestand > nieuw > project**.

2. Selecteer het type U-SQL Project.

   ![nieuw U-SQL Visual Studio-project](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Klik op **OK**. Visual maakt een oplossing met een Script.usql-bestand.

4. Voer het volgende script in het script. usql-bestand in:

   ```usql
   // Create a database for easy reuse, so you don't need to read from a file very time.
   CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

   // Create a Table valued function. TVF ensures that your jobs fetch data from he weblog file with the correct schema.
   DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
   CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
   RETURNS @result TABLE
   (
       s_date DateTime,
       s_time string,
       s_sitename string,
       cs_method string,
       cs_uristem string,
       cs_uriquery string,
       s_port int,
       cs_username string,
       c_ip string,
       cs_useragent string,
       cs_cookie string,
       cs_referer string,
       cs_host string,
       sc_status int,
       sc_substatus int,
       sc_win32status int,
       sc_bytes int,
       cs_bytes int,
       s_timetaken int
   )
   AS
   BEGIN

       @result = EXTRACT
           s_date DateTime,
           s_time string,
           s_sitename string,
           cs_method string,
           cs_uristem string,
           cs_uriquery string,
           s_port int,
           cs_username string,
           c_ip string,
           cs_useragent string,
           cs_cookie string,
           cs_referer string,
           cs_host string,
           sc_status int,
           sc_substatus int,
           sc_win32status int,
           sc_bytes int,
           cs_bytes int,
           s_timetaken int
       FROM @"/Samples/Data/WebLog.log"
       USING Extractors.Text(delimiter:' ');
       RETURN;
   END;

   // Create a table for storing referrers and status
   DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
   @weblog = SampleDBTutorials.dbo.WeblogsView();
   CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
   (
       INDEX idx1
       CLUSTERED(Year ASC)
       DISTRIBUTED BY HASH(Year)
   ) AS

   SELECT s_date.Year AS Year,
       s_date.Month AS Month,
       s_date.Day AS Day,
       cs_referer,
       sc_status,
       COUNT(DISTINCT c_ip) AS cnt
   FROM @weblog
   GROUP BY s_date,
           cs_referer,
           sc_status;
   ```

    Zie aan de [slag met data Lake Analytics u-SQL-taal](data-lake-analytics-u-sql-get-started.md)voor meer informatie over de u-SQL.

5. Voeg een nieuw U-SQL-script toe aan uw project en voer het volgende in:

   ```usql
   // Query the referrers that ran into errors
   @content =
       SELECT *
       FROM SampleDBTutorials.dbo.ReferrersPerDay
       WHERE sc_status >=400 AND sc_status < 500;

   OUTPUT @content
   TO @"/Samples/Outputs/UnsuccessfulResponses.log"
   USING Outputters.Tsv();
   ```

6. Ga terug naar het eerste U-SQL-script en geef uw Analytics-account op naast de knop **verzenden** .

7. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Build Script**. Controleer de resultaten in het deel venster uitvoer.

8. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Submit Script**.

9. Controleer het **Analytics-account** waarin u de taak wilt uitvoeren en klik vervolgens op **verzenden**. Het resultaat van het verzenden en de koppeling naar de taak zijn beschikbaar in het resultaatvenster van Data Lake Tools voor Visual Studio wanneer het verzenden is voltooid.

10. Wacht tot de taak is voltooid.  Als de taak is mislukt, ontbreekt er waarschijnlijk het bron bestand.  Raadpleeg de sectie vereisten van deze zelf studie. Zie [Azure data Lake Analytics Jobs controleren en problemen oplossen](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)voor meer informatie over het oplossen van problemen.

    Wanneer de taak is voltooid, wordt het volgende scherm weer gegeven:

    ![Data Lake Analytics Weblogboeken website logboeken analyseren](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Herhaal nu stap 7-10 voor **script1. usql**.

### <a name="to-see-the-job-output"></a>De taakuitvoer weergeven

1. Ga naar **Server Explorer**, vouw **Azure** uit, vouw **Data Lake Analytics** uit, vouw uw Data Lake Analytics-account uit, vouw **Storage Accounts** uit, klik met de rechtermuisknop op het Data Lake Storage-standaardaccount en klik vervolgens op **Explorer**.
2. Dubbel klik op voor **beelden** om de map te openen en dubbel klik vervolgens op **uitvoer**.
3. Dubbel klik op **UnsuccessfulResponses. log**.
4. U kunt ook dubbel klikken op het uitvoer bestand in de grafiek weergave van de taak om rechtstreeks naar de uitvoer te navigeren.

## <a name="next-steps"></a>Volgende stappen
Om aan de slag te gaan met Data Lake Analytics met verschillende hulpprogramma's, zie:

* [Aan de slag met Data Lake Analytics met behulp van Azure Portal](data-lake-analytics-get-started-portal.md)
* [Aan de slag met Data Lake Analytics met Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Aan de slag met Data Lake Analytics met .NET SDK](./data-lake-analytics-get-started-cli.md)