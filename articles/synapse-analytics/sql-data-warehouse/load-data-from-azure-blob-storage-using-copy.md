---
title: "Zelf studie: over taxi's-gegevens voor New York laden"
description: In de zelf studie worden Azure Portal en SQL Server Management Studio gebruikt voor het laden van nieuwe over taxi's-gegevens uit een Azure-Blob voor Synapse SQL.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: bd5c56ef74fbe0c60a9d395a7b8a0fbc496e773c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95534837"
---
# <a name="tutorial-load-the-new-york-taxicab-dataset"></a>Zelf studie: de over taxi's-gegevensset van New York laden

In deze zelf studie wordt de [instructie Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) gebruikt voor het laden van een New York over taxi's-gegevensset vanuit een Azure Blob Storage-account. De zelfstudie gebruikt [Azure Portal](https://portal.azure.com) en [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SSMS) voor het volgende:

> [!div class="checklist"]
>
> * Een gebruiker maken die wordt aangewezen om gegevens te laden
> * De tabellen voor de voor beeld-gegevensset maken 
> * De instructie COPY T-SQL gebruiken om gegevens in uw data warehouse te laden
> * De voortgang van de gegevens weergeven terwijl deze worden geladen

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Download en installeer voordat u met deze zelfstudie begint de nieuwste versie van [SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (SQL Server Management Studio).  

In deze zelf studie wordt ervan uitgegaan dat u al een toegewezen SQL-groep hebt gemaakt in de volgende [zelf studie](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/create-data-warehouse-portal#connect-to-the-server-as-server-admin).

## <a name="create-a-user-for-loading-data"></a>Een gebruiker maken voor het laden van gegevens

De serverbeheerdersaccount is bedoeld voor het uitvoeren van beheerbewerkingen en is niet geschikt voor het uitvoeren van query's op gebruikersgegevens. Het laden van gegevens is een geheugenintensieve bewerking. Geheugen limieten worden gedefinieerd op basis van de geconfigureerde [Data Warehouse-eenheden](what-is-a-data-warehouse-unit-dwu-cdwu.md) en de [resource klasse](resource-classes-for-workload-management.md) .

Het is raadzaam een aanmelding en gebruiker te maken die speciaal wordt toegewezen voor het laden van gegevens. Voeg vervolgens de ladende gebruiker toe aan een [bronklasse](resource-classes-for-workload-management.md). Hiermee wordt een maximale hoeveelheid geheugen ingesteld.

Maak verbinding als server beheerder zodat u aanmeldingen en gebruikers kunt maken. Gebruik deze stappen om een aanmelding en gebruiker te maken met de naam **LoaderRC20**. Wijs de gebruiker vervolgens toe aan de bronklasse **staticrc20**.

1. Klik in SSMS met de rechter muisknop op **model** om een vervolg keuzelijst weer te geven en kies **nieuwe query**. Een nieuwe queryvenster wordt geopend.

    ![Nieuwe query in Hoofd](./media/load-data-from-azure-blob-storage-using-polybase/create-loader-login.png)

2. Voer in het queryvenster deze T-SQL-opdrachten in om een aanmelding en een gebruiker te maken met de naam LoaderRC20, waarbij u uw eigen wachtwoord vervangt door 'een123STERKwachtwoord!'.

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    ```

3. Selecteer **Uitvoeren**.

4. Klik met de rechtermuisknop op **mySampleDataWarehouse** en kies **Nieuwe query**. Er wordt een nieuw queryvenster geopend.  

    ![Nieuwe query op voorbeeld van datawarehouse](./media/load-data-from-azure-blob-storage-using-polybase/create-loading-user.png)

5. Voer de volgende T-SQL-opdrachten in om een databasegebruiker met de naam LoaderRC20 te maken voor de aanmelding LoaderRC20. De tweede regel verleent de nieuwe gebruiker beheermachtigingen voor het nieuwe datawarehouse.  Deze machtigingen zijn vergelijkbaar met de machtigingen als u de gebruiker de eigenaar van de database maakt. De derde regel voegt de nieuwe gebruiker toe als lid van de [bronklasse](resource-classes-for-workload-management.md) staticrc20.

    ```sql
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

6. Selecteer **Uitvoeren**.

## <a name="connect-to-the-server-as-the-loading-user"></a>Verbinding maken met de server als de ladende gebruiker

De eerste stap voor het laden van gegevens bestaat uit aanmelding als LoaderRC20.  

1. Selecteer in Objectverkenner de vervolg keuzelijst **verbinding maken** en selecteer **Data base-engine**. Het dialoogvenster **Verbinding maken met server** wordt geopend.

    ![Verbinding maken met nieuwe aanmelding](./media/load-data-from-azure-blob-storage-using-polybase/connect-as-loading-user.png)

2. Voer de volledig gekwalificeerde servernaam in en voer **LoaderRC20** als de aanmelding in.  Voer uw wachtwoord in voor LoaderRC20.

3. Selecteer **Verbinden**.

4. Wanneer de verbinding gereed is, ziet u twee serververbindingen in Objectverkenner. Eén verbinding als de serverbeheerder en één verbinding als MedRCLogin.

    ![Verbinding geslaagd](./media/load-data-from-azure-blob-storage-using-polybase/connected-as-new-login.png)

## <a name="create-tables-for-the-sample-data"></a>Tabellen maken voor de voorbeeld gegevens

U bent klaar om te beginnen met het laden van gegevens in uw nieuwe datawarehouse. In dit deel van de zelf studie ziet u hoe u de instructie COPY kunt gebruiken om de gegevens van het nieuwe taxi-CAB-bestand in de Utrecht te laden vanuit een Azure Storage-blob. Zie het [overzicht van laden](design-elt-data-loading.md)voor meer informatie over het ophalen van uw gegevens naar Azure Blob-opslag of het rechtstreeks laden vanuit uw bron.

Voer de volgende SQL-scripts uit en geef informatie op over de gegevens die u wilt laden. Deze informatie omvat de locatie waar de gegevens zich bevinden, de indeling van de inhoud van de gegevens en de tabeldefinitie voor de gegevens.

1. In de vorige sectie hebt u zich bij uw datawarehouse aangemeld als LoaderRC20. Klik in SMMS met de rechtermuisknop op uw LoaderRC20-verbinding en selecteer **Nieuwe query**.  Er wordt een nieuw queryvenster geopend.

    ![Nieuw venster voor het laden van een query](./media/load-data-from-azure-blob-storage-using-polybase/new-loading-query.png)

2. Vergelijk uw queryvenster met de vorige afbeelding.  Controleer of uw nieuwe queryvenster wordt uitgevoerd als LoaderRC20 en query's uitvoert op uw MySampleDataWarehouse-database. Gebruik dit queryvenster om alle laadstappen uit te voeren.

7. Voer de volgende T-SQL-instructies uit om de tabellen te maken:

    ```sql
    CREATE TABLE [dbo].[Date]
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    
    CREATE TABLE [dbo].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );
    ```
    

## <a name="load-the-data-into-your-data-warehouse"></a>De gegevens in uw datawarehouse laden

In deze sectie wordt de [instructie Copy gebruikt om](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) de voorbeeld gegevens van Azure Storage BLOB te laden.  

> [!NOTE]
> In deze zelfstudie worden de gegevens rechtstreeks in de definitieve tabel geladen. Normaal gesp roken laadt u in een faserings tabel voor de werk belasting van uw productie. U kunt alle benodigde transformaties uitvoeren wanneer de gegevens zich in de faseringstabel bevinden. 

1. Voer de volgende instructies uit om de gegevens te laden:

    ```sql
    COPY INTO [dbo].[Date]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Date'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Date] - Taxi dataset');
    
    
    COPY INTO [dbo].[Geography]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Geography'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Geography] - Taxi dataset');
    
    COPY INTO [dbo].[HackneyLicense]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/HackneyLicense'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset');
    
    COPY INTO [dbo].[Medallion]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Medallion'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Medallion] - Taxi dataset');
    
    COPY INTO [dbo].[Time]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Time'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = ''
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Time] - Taxi dataset');
    
    COPY INTO [dbo].[Weather]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Weather'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = ',',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Weather] - Taxi dataset');
    
    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```

2. Bekijk uw gegevens tijdens het laden. U laadt verschillende GB van gegevens en comprimeert deze in zeer krachtige, geclusterde column Store-indexen. Voer de volgende query uit, die gebruikmaakt van dynamische beheerweergaven (DMV's) om de status van de belasting weer te geven.

    ```sql
    SELECT  r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command
    ,       sum(bytes_processed) AS bytes_processed
    ,       sum(rows_processed) AS rows_processed
    FROM    sys.dm_pdw_exec_requests r
                  JOIN sys.dm_pdw_dms_workers w
                         ON r.[request_id] = w.request_id
    WHERE [label] = 'COPY : Load [dbo].[Date] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Geography] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[HackneyLicense] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Medallion] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Time] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Weather] - Taxi dataset' OR
        [label] = 'COPY : Load [dbo].[Trip] - Taxi dataset' 
    and session_id <> session_id() and type = 'WRITER'
    GROUP BY r.[request_id]                           
    ,       r.[status]                               
    ,       r.resource_class                         
    ,       r.command;
    ```
    
3. Bekijk alle systeemquery's.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Al uw gegevens zijn netjes geladen in uw datawarehouse.

    ![Geladen tabellen weergeven](./media/load-data-from-azure-blob-storage-using-polybase/view-loaded-tables.png)

## <a name="clean-up-resources"></a>Resources opschonen

Er kunnen kosten in rekening worden gebracht voor rekenresources en gegevens die in uw datawarehouse worden geladen. Deze worden afzonderlijk gefactureerd.

* Als u de gegevens in de opslag wilt houden, kunt u het berekenen onderbreken wanneer u het datawarehouse niet gebruikt. Door de berekening te onderbreken, worden alleen kosten voor gegevensopslag in rekening gebracht en kunt u de berekening hervatten wanneer u weer met de gegevens wilt gaan werken.
* Als u in de toekomst geen kosten meer wilt hebben, kunt u de datawarehouse verwijderen.

Volg deze stappen om de resources op te schonen zoals gewenst.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en selecteer uw data warehouse.

    ![Resources opschonen](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Als u het berekenen wilt onderbreken, selecteert u de knop **Onderbreken**. Als het datawarehouse is onderbroken, ziet u de knop **Start**.  Als u de berekening wilt hervatten, selecteert u **Starten**.

3. Selecteer **verwijderen** om het Data Warehouse te verwijderen, zodat er geen kosten in rekening worden gebracht voor berekenen of opslag.

4. Als u de server die u hebt gemaakt, wilt verwijderen, selecteert u **mynewserver-20180430.database.Windows.net** in de vorige installatie kopie en selecteert u vervolgens **verwijderen**.  Wees hiermee voorzichtig. Als u de server verwijdert, worden ook alle databases verwijderd die zijn toegewezen aan de server.

5. Als u de resourcegroep wilt verwijderen, selecteert u **myResourceGroup**. Selecteer vervolgens **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een datawarehouse en een gebruiker voor het laden van gegevens maakt. U hebt de [instructie Simple Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#examples) gebruikt voor het laden van gegevens in uw data warehouse.

U hebt het volgende gedaan:
> [!div class="checklist"]
>
> * Een datawarehouse gemaakt in Azure Portal
> * Een serverfirewallregel ingesteld in Azure Portal
> * Verbinding gemaakt met het datawarehouse met SMMS
> * Een gebruiker gemaakt die wordt aangewezen om gegevens te laden
> * De tabellen voor de voorbeeld gegevens zijn gemaakt
> * De COPY T-SQL-instructie is gebruikt om gegevens in uw data warehouse te laden
> * De voortgang van de gegevens weergegeven terwijl deze werden geladen

Ga naar het overzicht voor ontwikkel aars voor meer informatie over het migreren van een bestaande Data Base naar Azure Synapse Analytics:

> [!div class="nextstepaction"]
> [Ontwerp beslissingen voor het migreren van een bestaande Data Base naar Azure Synapse Analytics](sql-data-warehouse-overview-develop.md)

Raadpleeg de volgende documentatie voor meer informatie over het laden van voor beelden en verwijzingen:

- [Naslag documentatie over het kopiëren van instructies](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest#syntax)
- [Voor beelden kopiëren voor elke authenticatie methode](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples)
- [Quick start voor één tabel kopiëren](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-bulk-load-copy-tsql)
