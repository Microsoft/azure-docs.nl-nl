---
title: Verbinding maken met een toegewezen SQL-groep (voorheen SQL DW) met VSTS
description: Een exclusieve SQL-groep (voorheen SQL DW) opvragen in azure Synapse Analytics met Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: c55f8483ba54ecf9778693b364603d642ddb3deb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452319"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Verbinding maken met een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics met Visual Studio en SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Gebruik Visual Studio om binnen een paar minuten een specifieke SQL-groep (voorheen SQL DW) in azure Synapse op te vragen. Deze methode maakt gebruik van de uitbrei ding SQL Server Data Tools (SSDT) in Visual Studio 2019. 

## <a name="prerequisites"></a>Vereisten
Voor deze zelfstudie hebt u het volgende nodig:

* Een bestaande exclusieve SQL-groep (voorheen SQL DW). Zie [een toegewezen SQL-groep maken (voorheen SQL DW)](create-data-warehouse-portal.md)om er een te maken.
* SSDT voor Visual Studio. Als u Visual Studio hebt, hebt u waarschijnlijk al SSDT voor Visual Studio. Voor installatie-instructies en -opties raadpleegt u [Visual Studio en SSDT installeren](sql-data-warehouse-install-visual-studio.md).
* De volledig gekwalificeerde SQL-servernaam. Zie [verbinding maken met een toegewezen SQL-groep (voorheen SQL DW)](sql-data-warehouse-connect-overview.md)om deze informatie te vinden.

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. Maak verbinding met uw toegewezen SQL-groep (voorheen SQL DW)
1. Open Visual Studio 2019.
2. Open SQL Server-objectverkenner door SQL Server-objectverkenner **weer geven** te selecteren  >  **SQL Server Object Explorer**.
   
    ![SQL Server-objectverkenner](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Klik op het pictogram **SQL Server toevoegen**.
   
    ![SQL Server toevoegen](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Vul de velden in het venster Connect to Server (Verbinding maken met server) in.
   
    ![Verbinding maken met server](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Server naam**. Voer de eerder vastgestelde **servernaam** in.
   * **Verificatie**. Selecteer **SQL Server Authentication** (SQL Server-verificatie) of **Active Directory Integrated Authentication** (Geïntegreerde Active Directory-verificatie).
   * **Gebruikers naam** en **wacht woord**. Voer de gebruikersnaam en het wachtwoord in als u hierboven SQL Server-verificatie hebt geselecteerd.
   * Klik op **Verbinden**.
5. U kunt de Azure SQL-server uitvouwen als u deze wilt verkennen. U kunt de databases weergeven die aan de server zijn gekoppeld. Vouw AdventureWorksDW uit als u de tabellen in de voorbeelddatabase wilt zien.
   
    ![AdventureWorksDW verkennen](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Voer een voorbeeld query uit
Nu er een verbinding met uw database is ingesteld, gaat u een query schrijven.

1. Klik met de rechtermuisknop op de database in SQL Server-objectverkenner.
2. Selecteer **New Query** (Nieuwe query). Een nieuwe queryvenster wordt geopend.
   
    ![Nieuwe query](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Kopieer de volgende T-SQL-query in het queryvenster:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Voer de query uit door op de groene pijl te klikken of de volgende snelkoppeling te gebruiken: `CTRL` + `SHIFT` + `E` .
   
    ![Query uitvoeren](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Bekijk de resultaten van de query. In dit voorbeeld heeft de tabel FactInternetSales 60398 rijen.
   
    ![Queryresultaten](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Volgende stappen
Nu u weet hoe u verbinding maakt en een query uitvoert, kunt u proberen [de gegevens te visualiseren met Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md).

Zie verifiëren voor een [toegewezen SQL-groep (voorheen SQL DW)](sql-data-warehouse-authentication.md)om uw omgeving te configureren voor Azure Active Directory-verificatie.
