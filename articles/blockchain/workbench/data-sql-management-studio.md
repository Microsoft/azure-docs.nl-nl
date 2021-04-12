---
title: Query's uitvoeren op Azure Block Chain Workbench-gegevens met behulp van SQL Server Management Studio
description: Leer hoe u vanuit SQL Server Management Studio verbinding maakt met een SQL-database van Azure Blockchain Workbench.
ms.date: 11/20/2019
ms.topic: how-to
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: d34affa93af65acf5fd7d6558e2d8077a2efc360
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625157"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Gegevens van Azure Blockchain Workbench gebruiken met SQL Server Management Studio

Microsoft SQL Server Management Studio biedt de mogelijkheid om snel query's te schrijven en te testen op de SQL-data base van Azure Block Chain Workbench. In deze sectie vindt u stapsgewijze instructies voor het maken van verbinding met de SQL Database van Azure Block Chain Workbench vanuit SQL Server Management Studio.

## <a name="prerequisites"></a>Vereisten

* Down load [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>SQL Server Management Studio verbinden met gegevens in Azure Blockchain Workbench

1. Open SQL Server Management Studio en selecteer **Connect**.
2. Selecteer **Database Engine**.

    ![Database-engine](./media/data-sql-management-studio/database-engine.png)

3. Voer in het dialoogvenster **Verbinding maken met server** de naam van de server en uw databasereferenties in.

    Als u de referenties gebruikt die zijn gemaakt tijdens de implementatie van Azure Blockchain Workbench, is de gebruikersnaam **dbadmin** en het wachtwoord het wachtwoord dat u hebt opgegeven tijdens de implementatie.

    ![SQL-referenties invoeren](./media/data-sql-management-studio/sql-creds.png)

   1. In SQL Server Management Studio ziet u de lijst met databases, databaseweergaven en opgeslagen procedures uit de Azure Blockchain Workbench-database.

      ![Databaselijst](./media/data-sql-management-studio/db-list.png)

5. Als u de gegevens wilt bekijken die aan een databaseweergave zijn gekoppeld, kunt u met de volgende stappen automatisch een select-instructie genereren.
6. Klik met de rechter muisknop op een van de database weergaven in het Objectverkenner.
7. Selecteer **Script View as**.
8. Kies **SELECT to**.
9. Selecteer **New Query Editor Window**.
10. U kunt een nieuwe query maken door **New Query** te selecteren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Databaseweergaven in Azure Blockchain Workbench](database-views.md)
