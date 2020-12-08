---
title: Verbinding maken met Synapse SQL met behulp van Power BI Professional
description: In deze zelfstudie voert u stappen uit om te leren hoe u Power BI Desktop kunt verbinden met een serverloze SQL-pool.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: b33edb99109b7516e1792497a936031cf954bc15
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451610"
---
# <a name="connect-to-serverless-sql-pool-with-power-bi-professional"></a>Verbinding maken met een serverloze SQL-pool met behulp van Power BI Professional

> [!div class="op_single_selector"]
>
> - [Azure Data Studio](get-started-azure-data-studio.md)
> - [Power BI](get-started-power-bi-professional.md)
> - [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> - [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> - [SSMS](get-started-ssms.md)

In deze zelfstudie voert u stappen uit om te leren hoe u Power BI Desktop kunt verbinden met een serverloze SQL-pool.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende hulpprogramma's nodig om query's uit te voeren:

- Gewenste SQL-client:

  - Azure Data Studio
  - SQL Server Management Studio

- Power BI Desktop geïnstalleerd

Parameters:

| Parameter                                 | Beschrijving                                                   |
| ----------------------------------------- | ------------------------------------------------------------- |
| Eindpuntadres van de service voor serverloze SQL-pools    | Zal worden gebruikt als servernaam                                   |
| Eindpuntregio van de service voor serverloze SQL-pools     | Zal worden gebruikt om te bepalen welke opslag wordt gebruikt in voorbeelden |
| Gebruikersnaam en wachtwoord voor eindpunttoegang | Zal worden gebruikt om toegang te krijgen tot het eindpunt                               |
| De database die u gaat gebruiken om weergaven te maken       | Deze database zal worden gebruikt als uitgangspunt in voorbeelden       |

## <a name="first-time-setup"></a>De eerste installatie

Er zijn twee stappen voorafgaand aan het gebruik van voorbeelden:

1. Database voor uw weergaven maken
2. Referenties maken die een serverloze SQL-pool kan gebruiken voor toegang tot bestanden in opslag

### <a name="create-database"></a>Database maken

Voor dit aan de slag-artikel moet u uw eigen database maken om als demo te gebruiken. Er is een database nodig voor het maken van weergaven. U gebruikt deze database in enkele van de voorbeeldquery's in deze documentatie.

> [!NOTE]
> De databases worden alleen gebruikt voor het weergeven van metagegevens, niet voor de werkelijke gegevens.
>
> Noteer de naam van de database die u gebruikt. U hebt deze later nodig.

```sql
DROP DATABASE IF EXISTS demo;
```

### <a name="create-credentials"></a>Referenties maken

U moet referenties maken voordat u query's kunt uitvoeren. Deze referenties worden gebruikt voor de service voor serverloze SQL-pools om toegang te krijgen tot bestanden in opslag.

> [!NOTE]
> U moet referenties maken voor toegang tot het opslagaccount. Hoewel een serverloze SQL-pool toegang kan krijgen tot opslagaccounts uit verschillende regio's, zijn de prestaties beter als de opslag en de Azure Synapse-werkruimte zich in dezelfde regio bevinden.

**Codefragment voor het maken van referenties voor Census-gegevenscontainers** uitvoeren:

```sql
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer];
GO

-- Create credentials for Census Data container which resides in a azure open data storage account
-- There is no secret. We are using public storage account which doesn't need secret
CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = '';
GO
```

## <a name="create-a-power-bi-desktop-report"></a>Een Power BI Desktop-rapport maken

Open de toepassing Power BI Desktop en selecteer de optie **Gegevens ophalen**.

![Open de toepassing Power BI Desktop en selecteer Gegevens ophalen.](./media/get-started-power-bi-professional/step-0-open-powerbi.png)

### <a name="step-1---select-data-source"></a>Stap 1 - gegevensbron selecteren

Selecteer **Azure** in het menu en vervolgens **Azure SQL Database**.
![Selecteer de gegevensbron.](./media/get-started-power-bi-professional/step-1-select-data-source.png)

### <a name="step-2---select-database"></a>Step 2 - database selecteren

Schrijf de URL voor de database en de naam van de database waarin de weergave zich bevindt.
![Selecteer database op het eindpunt.](./media/get-started-power-bi-professional/step-2-db.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar [Queryopslagbestanden](get-started-azure-data-studio.md) om te leren hoe u verbinding kunt maken met een serverloze SQL-pool via Azure Data Studio.