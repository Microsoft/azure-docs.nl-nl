---
title: Azure Stream Analytics gebruiken
description: Tips voor het gebruik van Azure Stream Analytics met uw data warehouse in azure Synapse voor het ontwikkelen van real-time oplossingen.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 3ead3393218255808eb67983251fcf9f2561c82c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020177"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Azure Stream Analytics met Azure Synapse Analytics gebruiken

Azure Stream Analytics is een volledig beheerde service met lage latentie en een Maxi maal beschik bare, schaal bare complexe gebeurtenis verwerking via streaming-gegevens in de Cloud. Lees de [Inleiding tot Azure stream Analytics voor](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)meer informatie over de basis principes. U kunt vervolgens leren hoe u een end-to-end-oplossing maakt met Stream Analytics door de zelf studie [aan de slag met Azure stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) te volgen.

In dit artikel leert u hoe u uw data warehouse kunt gebruiken als een uitvoer filter voor gegevens opname met hoge door Voer met Azure Stream Analytics-taken.

## <a name="prerequisites"></a>Vereisten

* Azure Stream Analytics taak: als u een Azure Stream Analytics taak wilt maken, volgt u de stappen in de zelf studie [aan de slag met Azure stream Analytics](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om het volgende te doen:  

    1. Een event hub-invoer maken
    2. Toepassing voor gebeurtenis Generator configureren en starten
    3. Een Stream Analytics-taak inrichten
    4. Taak invoer en-query opgeven
* Synapse toegewezen SQL-groep voor uw data warehouse: als u een nieuw data warehouse wilt maken, volgt u de stappen in de [Quick Start om een nieuw data warehouse te maken](create-data-warehouse-portal.md).

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>Streaming-uitvoer opgeven zodat deze naar uw data warehouse verwijst

### <a name="step-1"></a>Stap 1

Ga vanuit het Azure Portal naar uw Stream Analytics-taak en klik op **uitvoer** onder het menu **taak topologie** .

### <a name="step-2"></a>Stap 2

Klik op de knop **toevoegen** en kies **Azure Synapse Analytics** in de vervolg keuzelijst.

![Kies Azure Synapse Analytics](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>Stap 3

Voer de volgende waarden in:

* *Uitvoer alias*: Voer een beschrijvende naam in voor deze taak uitvoer.
* *Abonnement*:
  * Als uw data warehouse zich in hetzelfde abonnement bevindt als de Stream Analytics taak, klikt u op ***Selecteer Azure Synapse Analytics van uw abonnementen** _.
  _ Als uw data warehouse zich in een ander abonnement bevindt, klikt u op on hand matig Azure Synapse Analytics-instellingen opgeven.
* *Data Base*: Selecteer de doel database in de vervolg keuzelijst.
* *Gebruikers naam*: Geef de gebruikers naam op van een account met schrijf machtigingen voor de data base.
* *Wacht woord*: Geef het wacht woord op voor het opgegeven gebruikers account.
* *Tabel*: Geef de naam op van de doel tabel in de data base.
* Klik op de knop **Opslaan**

![Azure Synapse Analytics-formulier is voltooid](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>Stap 4

Voordat u een test kunt uitvoeren, moet u de tabel in uw data warehouse maken.  Voer het volgende script uit om de tabel te maken met behulp van SQL Server Management Studio (SSMS) of de keuze van een query programma.

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

Klik in de Azure Portal voor Stream Analytics taak op de naam van uw taak.  Klik op de knop **_toets_* _ in het deel venster _*_uitvoer Details_*_ .

![De knop testen op Details van Outpout ](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) als de verbinding met de data base slaagt, wordt er een melding weer geven in de portal.

### <a name="step-6"></a>Stap 6

Klik op het _*_query_*_ menu onder _*_taak topologie_*_ en wijzig de query om gegevens in te voegen in de stream-uitvoer die u hebt gemaakt.  Klik op de knop _*_geselecteerde query testen_*_ om uw query te testen.  Klik op de knop _*_query opslaan_*_ wanneer de query test is voltooid.

![Query opslaan](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>Stap 7

Start de Azure Stream Analytics taak.  Klik op de knop _*_Start_*_ in het menu _*_overzicht_*_ .

![Stream Analytics-taak starten](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

Klik op de knop _ *_Start_** in het deel venster taak starten.

![Klik op Start](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor een overzicht van integratie [andere services integreren](sql-data-warehouse-overview-integrate.md).
Zie [ontwerp beslissingen en coderings technieken voor data warehouses](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
