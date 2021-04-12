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
ms.openlocfilehash: 4588eee721a58a7e4f3366d0d325b48de0f56ae5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259809"
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
    IF NOT EXISTS (SELECT * FROM sys.objects O JOIN sys.schemas S ON O.schema_id = S.schema_id WHERE O.NAME = 'NYCTaxiTripSmall' AND O.TYPE = 'U' AND S.NAME = 'dbo')
    CREATE TABLE dbo.NYCTaxiTripSmall
        (
         [DateID] int,
         [MedallionID] int,
         [HackneyLicenseID] int,
         [PickupTimeID] int,
         [DropoffTimeID] int,
         [PickupGeographyID] int,
         [DropoffGeographyID] int,
         [PickupLatitude] float,
         [PickupLongitude] float,
         [PickupLatLong] nvarchar(4000),
         [DropoffLatitude] float,
         [DropoffLongitude] float,
         [DropoffLatLong] nvarchar(4000),
         [PassengerCount] int,
         [TripDurationSeconds] int,
         [TripDistanceMiles] float,
         [PaymentType] nvarchar(4000),
         [FareAmount] numeric(19,4),
         [SurchargeAmount] numeric(19,4),
         [TaxAmount] numeric(19,4),
         [TipAmount] numeric(19,4),
         [TollsAmount] numeric(19,4),
         [TotalAmount] numeric(19,4)
        )
    WITH
        (
        DISTRIBUTION = ROUND_ROBIN,
         CLUSTERED COLUMNSTORE INDEX
         -- HEAP
        )
    GO

    --Uncomment the 4 lines below to create a stored procedure for data pipeline orchestration
    --CREATE PROC bulk_load_NYCTaxiTripSmall
    --AS
    --BEGIN
    COPY INTO dbo.NYCTaxiTripSmall
    (DateID 1, MedallionID 2, HackneyLicenseID 3, PickupTimeID 4, DropoffTimeID 5, PickupGeographyID 6, DropoffGeographyID 7, PickupLatitude 8, PickupLongitude 9, PickupLatLong 10, DropoffLatitude 11, DropoffLongitude 12, DropoffLatLong 13, PassengerCount 14, TripDurationSeconds 15, TripDistanceMiles 16, PaymentType 17, FareAmount 18, SurchargeAmount 19, TaxAmount 20, TipAmount 21, TollsAmount 22, TotalAmount 23)
    FROM 'https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet'
    WITH
    (
        FILE_TYPE = 'PARQUET'
        ,MAXERRORS = 0
        ,IDENTITY_INSERT = 'OFF'
    )
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
