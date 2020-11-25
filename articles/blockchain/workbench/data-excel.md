---
title: Gegevens van Azure Blockchain Workbench gebruiken in Microsoft Excel
description: Meer informatie over het laden en weer geven van Azure Block Chain Workbench preview SQL DB-gegevens in micro soft Excel.
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 4dd941abdded6a93510f4a71119769cd73855bcc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000161"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Gegevens van Azure Blockchain Workbench weergeven met Microsoft Excel

U kunt Microsoft Excel gebruiken om gegevens in een SQL-database van Azure Blockchain Workbench weer te geven. Dit artikel bevat de stappen die nodig zijn voor deze bewerkingen:

* Verbinding maken met een database van Blockchain Workbench vanuit Microsoft Excel
* Databasetabellen en -weergaven van Blockchain Workbench bekijken
* Gegevens van Blockchain Workbench laden in Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Verbinding maken met een database van Blockchain Workbench

U kunt als volgt verbinding maken met een database van Blockchain Workbench:

1. Open Microsoft Excel.
2. Ga naar het tabblad **Gegevens** en kies **Gegevens ophalen**.
3. Selecteer **Uit Azure** en selecteer vervolgens **Uit Azure SQL Database**.

   ![Verbinding maken met Azure SQL Database](./media/data-excel/connect-sql-db.png)

4. Ga als volgt te werk in het dialoogvenster **SQL Server-database**:

    * Voer bij **Server** de naam van de Blockchain Workbench-server in.
    * Voer bij **Database (optioneel)** de naam van de database in.

   ![Databaseserver en database opgeven](./media/data-excel/provide-server-db.png)

5. Selecteer **Database** in de navigatiebalk van het dialoogvenster **SQL Server-database**. Geef bij **Gebruikersnaam** en **Wachtwoord** uw gebruikersnaam en wachtwoord op en selecteer vervolgens **Verbinding maken**.

    > [!NOTE]
    > Als u de referenties gebruikt die zijn gemaakt tijdens de implementatie van Azure Blockchain Workbench, is `dbadmin` de **gebruikersnaam**. Gebruik voor **Wachtwoord** het wachtwoord dat u hebt gemaakt tijdens de implementatie van Blockchain Workbench.
    
   ![Referenties opgeven voor toegang tot de database](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Databasetabellen en -weergaven bekijken

Het dialoogvenster Navigator van Excel wordt geopend zodra er verbinding is met de database. Gebruik dit dialoogvenster om de tabellen en weergaven in de database te bekijken. De weergaven zijn ontworpen voor rapportagedoeleinden en hun namen worden voorafgegaan door **vw**.

   ![Voorbeeld van een weergave in Excel Navigator](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Gegevens van een weergave laden in een Excel-werkmap

Het volgende voorbeeld laat zien hoe u gegevens vanuit een weergave kunt laden in een Excel-werkmap.

1. Selecteer op de schuifbalk van **Navigator** de weergave **vwContractAction**. In het voorbeeld van **vwContractAction** worden alle acties weergegeven die zijn gerelateerd aan een contract in de database van Blockchain Workbench.
2. Selecteer **Laden** om alle gegevens in de weergave op te halen en deze over te brengen naar de Excel-werkmap.

   ![Gegevens geladen uit een weergave](./media/data-excel/view-data.png)

Nu de gegevens geladen zijn, kunt u functies van Excel gebruiken om uw eigen rapporten te maken met behulp van de metagegevens en transactiegegevens uit de database van Azure Blockchain Workbench.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Databaseweergaven in Azure Blockchain Workbench](database-views.md)