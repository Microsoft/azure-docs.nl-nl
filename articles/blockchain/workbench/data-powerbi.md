---
title: Gegevens van Azure Blockchain Workbench gebruiken in Microsoft Power BI
description: Leer hoe u gegevens uit een SQL-database van Azure Blockchain Workbench laadt en weergeeft in Microsoft Power BI.
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 7e0e585ce45616c2402972c725b502f4b704d1cd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000144"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Gegevens van Azure Blockchain Workbench weergeven met Microsoft Power BI

Micro soft Power BI biedt de mogelijkheid om eenvoudig krachtige rapporten te genereren van SQL DB-data bases met behulp van Power BI Desktop en vervolgens te publiceren naar [https://www.powerbi.com](https://www.powerbi.com) .

Dit artikel bevat stapsgewijze instructies om vanuit Power BI Desktop verbinding te maken met een SQL-database van Azure Blockchain Workbench, een rapport te maken en dit rapport te implementeren op powerbi.com.

## <a name="prerequisites"></a>Vereisten

* Down load [Power bi Desktop](https://powerbi.microsoft.com/desktop/).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Power BI verbinding maken met gegevens in azure Block Chain workbench

1.  Open Power BI Desktop.
2.  Selecteer **Gegevens ophalen**.

    ![Gegevens ophalen](./media/data-powerbi/get-data.png)
3.  Selecteer **SQL Server** in de lijst met gegevensbronnen.

4.  Geef de naam van de server en de database op in het dialoogvenster. Geef aan of u de gegevens wilt importeren of een **DirectQuery** wilt uitvoeren. Selecteer **OK**.

    ![SQL-server selecteren](./media/data-powerbi/select-sql.png)

5.  Geef de databasereferenties op voor toegang tot Azure Blockchain Workbench. Selecteer **Database** en voer uw referenties in.

    Als u de referenties gebruikt die zijn gemaakt tijdens de implementatie van Azure Blockchain Workbench, is de gebruikersnaam **dbadmin** en het wachtwoord het wachtwoord dat u hebt opgegeven tijdens de implementatie.

    ![Instellingen van SQL-database](./media/data-powerbi/db-settings.png)

6.  Als er verbinding is met de database, ziet u in het dialoogvenster **Navigator** de tabellen en weergaven die beschikbaar zijn in de database. De weergaven zijn ontworpen voor rapportagedoeleinden en herkent u aan het voorvoegsel **vw**.

    ![Scherm opname van Power BI bureau blad met het dialoog venster navigator waarin vwContractAction is geselecteerd.](./media/data-powerbi/navigator.png)

7.  Selecteer de weergaven die u wilt opnemen in het rapport. Voor demonstratie doeleinden bevatten we **vwContractAction**, dat details bevat over de acties die op een contract hebben plaatsgevonden.

    ![Weergaven selecteren](./media/data-powerbi/select-views.png)

U kunt nu rapporten maken en publiceren zoals u dat gewend bent met Power BI.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Databaseweergaven in Azure Blockchain Workbench](database-views.md)