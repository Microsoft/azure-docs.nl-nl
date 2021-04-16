---
title: Gebruik Azure Stream Analytics in toegewezen SQL-pool
description: Tips voor het gebruik Azure Stream Analytics met toegewezen SQL-pool in Azure Synapse voor het ontwikkelen van realtime-oplossingen.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 0c7f139b50cd43e3e8862fda3f5401a853ced8d0
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566576"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Gebruik Azure Stream Analytics toegewezen SQL-pool in Azure Synapse Analytics

Azure Stream Analytics is een volledig beheerde service met lage latentie en een zeer beschikbare, schaalbare complexe gebeurtenisverwerking via streaminggegevens in de cloud. U kunt de basisbeginselen leren door [Inleiding tot de Azure Stream Analytics.](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) U kunt vervolgens leren hoe u een end-to-end-oplossing maakt met Stream Analytics door de zelfstudie Aan de slag [met](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Azure Stream Analytics volgen.

In dit artikel leert u hoe u uw toegewezen SQL-pool gebruikt als uitvoer-sink voor gegevensinvoer met hoge doorvoer Azure Stream Analytics taken.

## <a name="prerequisites"></a>Vereisten

* Azure Stream Analytics taak: volg de stappen in de zelfstudie Aan de slag met Azure Stream Analytics om een [Azure Stream Analytics-taak](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) te maken:  

    1. Een Event Hub-invoer maken
    2. Gebeurtenisgeneratortoepassing configureren en starten
    3. Een Stream Analytics inrichten
    4. Taakinvoer en -query opgeven
* Toegewezen SQL-pool: volg de stappen in de [quickstart: Een toegewezen SQL-pool maken](../quickstart-create-sql-pool-portal.md)om een nieuwe toegewezen SQL-pool te maken.

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>Streaming-uitvoer opgeven die naar uw toegewezen SQL-pool moet wijzen

### <a name="step-1"></a>Stap 1

Ga vanuit Azure Portal naar uw Stream Analytics taak en klik **onder** het **menu Taaktopologie** op Uitvoer.

### <a name="step-2"></a>Stap 2

Klik op de **knop** Toevoegen en kies **Azure Synapse Analytics** in de vervolgkeuzelijst.

![Kies Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Stap 3

Voer de volgende waarden in:

* *Uitvoeralias:* voer een gebruiksvriendelijke naam in voor deze taakuitvoer.
* *Abonnement*:
  * Als uw toegewezen SQL-pool zich in hetzelfde abonnement als de Stream Analytics-taak, klikt u op Azure Synapse Analytics ***selecteren in uw abonnementen.***
  * Als uw toegewezen SQL-pool zich in een ander abonnement heeft, klikt u op Azure Synapse Analytics handmatig opgeven.
* *Database:* selecteer de doeldatabase in de vervolgkeuzelijst.
* *Gebruikersnaam:* geef de gebruikersnaam op van een account met schrijfmachtigingen voor de database.
* *Wachtwoord:* geef het wachtwoord voor het opgegeven gebruikersaccount op.
* *Tabel:* geef de naam op van de doeltabel in de database.
* klik op de **knop** Opslaan

![Ingevuld Azure Synapse Analytics formulier](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Stap 4

Voordat u een test kunt uitvoeren, moet u de tabel maken in uw toegewezen SQL-pool.  Voer het volgende script voor het maken van de tabel SQL Server Management Studio (SSMS) of een queryhulpprogramma naar keuze uit.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>Stap 5

Klik op Azure Portal voor Stream Analytics taak op de naam van uw taak.  Klik op de knop ***Test** _ in het deelvenster _ *_Uitvoerdetails_** .

![Testknop op Outpout-details Wanneer de verbinding met de ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) database is geslaagd, ziet u een melding in de portal.

### <a name="step-6"></a>Stap 6

Klik op het menu ***Query** _ onder _*_Taaktopologie_*_ en wijzig de query om gegevens in te voegen in de Stream-uitvoer die u hebt gemaakt.  Klik op de _*_knop Geselecteerde query testen_*_ om uw query te testen.  Klik op de *_knop _ Query_* opslaan * wanneer de querytest is geslaagd.

![Query opslaan](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Stap 7

Start de Azure Stream Analytics taak.  Klik op de knop ***Start** _ in het menu _ *_Overzicht_** .

![Stream Analytics-taak starten](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klik op ***de knop*** Start in het deelvenster Taak starten.

![Klik op Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Volgende stappen

Zie Andere services integreren voor een overzicht [van de integratie.](sql-data-warehouse-overview-integrate.md)
Zie Ontwerpbeslissingen en coderingstechnieken voor toegewezen [SQL-pool](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkeling.
