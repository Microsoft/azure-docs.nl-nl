---
title: Verbinding maken met Synapse SQL en query's uitvoeren met Visual Studio en SSDT
description: Gebruik Visual Studio om een specifieke SQL-pool op te vragen met behulp van Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: ef8e2a3d1a6b78e8f2b6b9a900ed2485c1a4a5d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96451585"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Verbinding maken met Synapse SQL via Visual Studio en SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Gebruik Visual Studio om een specifieke SQL-pool op te vragen met behulp van Azure Synapse Analytics. Deze methode maakt gebruik van de uitbrei ding SQL Server Data Tools (SSDT) in Visual Studio 2019. 

> [!NOTE]
> Een serverloze SQL-groep wordt niet ondersteund door SSDT.

## <a name="prerequisites"></a>Vereisten

Als u deze zelf studie wilt gebruiken, moet u beschikken over de volgende onderdelen:

* Een bestaande exclusieve SQL-groep. Als u er nog geen hebt, raadpleegt u [een toegewezen SQL-groep maken](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) om dit vereiste te volt ooien.
* SSDT voor Visual Studio. Als u Visual Studio hebt, beschikt u waarschijnlijk al over dit onderdeel. Voor installatie-instructies en -opties raadpleegt u [Visual Studio en SSDT installeren](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* De volledig gekwalificeerde SQL-servernaam. Zie [verbinding maken met een toegewezen SQL-groep](connect-overview.md)om deze server naam te vinden.

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. Maak verbinding met een toegewezen SQL-groep
1. Open Visual Studio 2019.
2. Open de SQL Server-objectverkenner door **weer gave**  >  **SQL Server-objectverkenner** te selecteren.
   
    ![SQL Server-objectverkenner](./media/get-started-visual-studio/open-ssdt.png)
3. Klik op het pictogram **SQL Server toevoegen**.
   
    ![SQL Server toevoegen](./media/get-started-visual-studio/add-server.png)
4. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.
   
    ![Verbinding maken met server](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Servernaam**: Voer de eerder vastgestelde **servernaam** in.
   * **Verificatie**: Selecteer **SQL Server verificatie** of **Active Directory geïntegreerde verificatie**:
   * **Gebruikersnaam** en **Wachtwoord**: Voer uw gebruikersnaam en wachtwoord in als u hierboven SQL Server-verificatie hebt geselecteerd.
   * Klik op **Verbinden**.
5. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.
   
    ![AdventureWorksDW verkennen](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Voer een voorbeeld query uit
Nu er een verbinding tot stand is gebracht met uw data base, schrijft u een query.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](./media/get-started-visual-studio/new-query2.png)
3. Kopieer de volgende T-SQL-query in het queryvenster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit door op de groene pijl te klikken of de volgende snelkoppeling te gebruiken: `CTRL` + `SHIFT` + `E` .
   
    ![Query uitvoeren](./media/get-started-visual-studio/run-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u verbinding maakt en een query uitvoert, kunt u proberen [de gegevens te visualiseren met Power BI](get-started-power-bi-professional.md).
Zie [verifiëren voor exclusieve SQL-groep](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)om uw omgeving te configureren voor Azure Active Directory-verificatie.
 