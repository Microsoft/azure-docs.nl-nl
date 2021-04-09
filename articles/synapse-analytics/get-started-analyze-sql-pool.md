---
title: 'Zelfstudie: aan de slag met het analyseren van gegevens met toegewezen SQL-pools'
description: In deze zelfstudie gebruikt u de voorbeeldgegevens van NYC Taxi om de analysemogelijkheden van een SQL-pool te verkennen.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 03/24/2021
ms.openlocfilehash: a1f15330a912c8a8a93fe1f74e88ef8d117441c2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047895"
---
# <a name="analyze-data-with-dedicated-sql-pools"></a>Gegevens analyseren met toegewezen SQL-pools

In deze zelfstudie gebruikt u de voorbeeldgegevens van NYC Taxi om de mogelijkheden van een toegewezen SQL-pool te verkennen.

## <a name="create-a-dedicated-sql-pool"></a>Een toegewezen SQL-pool maken

1. Selecteer in Synapse Studio in het linkerdeelvenster de optie **Beheren** > **SQL-pools**.
1. Selecteer **Nieuw**
1. Selecteer **SQLPOOL1** voor **Naam van SQL-pool**
1. Kies **DW100C** voor **Prestatieniveau**
1. Selecteer **Beoordelen en maken** > **Maken**. Uw toegewezen SQL-pool is binnen een paar minuten klaar. 

Uw toegewezen SQL-groep is gekoppeld aan een SQL database dat ook wel **SQLPOOL1** wordt genoemd.
1. Navigeer naar **Data**  >  **Workspace**.
1. U ziet een Data Base met de naam **SQLPOOL1**. Als u dit niet ziet, klikt u op **vernieuwen**.

Een toegewezen SQL-pool verbruikt factureerbare resources zolang deze worden uitgevoerd. U kunt de pool later onderbreken om de kosten te verlagen.

> [!NOTE] 
> Bij het maken van een nieuwe toegewezen SQL-pool (voorheen SQL DW) in uw werkruimte, wordt de pagina voor het inrichten van de toegewezen SQL-pool geopend. Het inrichten vindt plaats op de logische SQL-server.
## <a name="load-the-nyc-taxi-data-into-sqlpool1"></a>Laad de NYC Taxi-gegevens in SQLPOOL1

1. Ga in Synapse Studio naar de **ontwikkelende** hub, klik op de **+** knop om een nieuwe resource toe te voegen en maak vervolgens een nieuw SQL-script.
1. Selecteer de pool ' SQLPOOL1 ' (groep die in [stap 1](./get-started-create-workspace.md) van deze zelf studie is gemaakt) in de vervolg keuzelijst verbinding maken met boven het script.
1. Voer de volgende code in:
    ```
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

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
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
1. Klik op de knop uitvoeren om het script uit te voeren.
1. Dit script wordt in minder dan 60 seconden voltooid. Het laadt 2.000.000 rijen van NYC taxi-gegevens in een tabel met de naam **dbo. Reis**.

## <a name="explore-the-nyc-taxi-data-in-the-dedicated-sql-pool"></a>De NYC-taxigegevens in de toegewezen SQL-pool verkennen

1. Ga in Synapse Studio naar de hub **Gegevens**.
1. Ga naar **SQLPOOL1** > **Tabellen**. 
3. Klik met de rechtermuisknop op de tabel **dbo.Trip** en selecteer **Nieuw SQL-script** > **Eerste 100 rijen selecteren**.
4. Wacht tot er een nieuw SQL-script wordt gemaakt en uitgevoerd.
5. U ziet dat bovenaan het SQL-script **Verbinding maken met** automatisch is ingesteld op de SQL-pool met de naam **SQLPOOL1**.
6. Vervang de tekst van het SQL-script door deze code en voer deze uit.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount;
    ```

    Deze query laat zien op welke manier de totale reisafstanden en de gemiddelde reisafstand betrekking hebben op het aantal reizigers.
1. In het resultatenvenster van het SQL-script wijzigt u de **Weergave** in **Grafiek** om een visualisatie van de resultaten weer te geven als een lijndiagram.
    
## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens in een Azure Storage-account analyseren](get-started-analyze-storage.md)
