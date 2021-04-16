---
title: Verbinding maken met toegewezen SQL-pool (voorheen SQL DW) met VSTS
description: Query's uitvoeren op toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics met Visual Studio.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 926e95887f8d6aa164908a4107656074142a969e
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566457"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Verbinding maken met toegewezen SQL-pool (voorheen SQL DW) in Azure Synapse Analytics met Visual Studio ssdt

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Gebruik Visual Studio in slechts enkele minuten een query uit te voeren op een toegewezen SQL-pool (voorheen SQL DW) Azure Synapse te voeren. Deze methode gebruikt de extensie SQL Server Data Tools (SSDT) in Visual Studio 2019. 

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een bestaande toegewezen SQL-pool (voorheen SQL DW). Zie Een toegewezen [SQL-pool maken (voorheen SQL DW) als](create-data-warehouse-portal.md)u er een wilt maken.
* SSDT voor Visual Studio. Als u een Visual Studio hebt, hebt u waarschijnlijk al SSDT voor Visual Studio. Voor installatie-instructies en -opties raadpleegt u [Visual Studio en SSDT installeren](sql-data-warehouse-install-visual-studio.md).
* De volledig gekwalificeerde SQL-servernaam. Zie Verbinding maken met een toegewezen [SQL-pool (voorheen SQL DW)](sql-data-warehouse-connect-overview.md)voor meer informatie.

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. Verbinding maken met uw toegewezen SQL-pool (voorheen SQL DW)
1. Open Visual Studio 2019.
2. Open SQL Server-objectverkenner door **Weergave**  >  **SQL Server-objectverkenner.**
   
    ![SQL Server-objectverkenner](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Klik op het pictogram **SQL Server toevoegen**.
   
    ![SQL Server toevoegen](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.
   
    ![Verbinding maken met server](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Servernaam**. Voer de eerder vastgestelde **servernaam** in.
   * **Verificatie**. Selecteer **SQL Server Authentication** (SQL Server-verificatie) of **Active Directory Integrated Authentication** (Geïntegreerde Active Directory-verificatie).
   * **Gebruikersnaam en** **wachtwoord.** Voer de gebruikersnaam en het wachtwoord in als u hierboven SQL Server-verificatie hebt geselecteerd.
   * Klik op **Verbinden**.
5. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.
   
    ![AdventureWorksDW verkennen](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Een voorbeeldquery uitvoeren
Nu er een verbinding met uw database is ingesteld, gaat u een query schrijven.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Kopieer de volgende T-SQL-query in het queryvenster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit door op de groene pijl te klikken of gebruik de volgende snelkoppeling: `CTRL` + `SHIFT` + `E` .
   
    ![Query uitvoeren](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u verbinding maakt en een query uitvoert, kunt u proberen [de gegevens te visualiseren met Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect).

Zie Verifiëren bij toegewezen [SQL-pool (Azure Active Directory](sql-data-warehouse-authentication.md)SQL DW) voor het configureren van uw omgeving voor verificatie.