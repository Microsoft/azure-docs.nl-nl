---
title: Verbinding maken met Synapse SQL met Azure Data Studio
description: Gebruik Azure Data Studio om verbinding te maken met en query's uit te voeren op Synapse SQL in Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: ff7b07cb1a4f480907bb51e741a05deeaae8e66b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116480"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio"></a>Verbinding maken met Synapse SQL met Azure Data Studio

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

U kunt [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om verbinding te maken met en query's uit te voeren op Synapse SQL in Azure Synapse Analytics. 

## <a name="connect"></a>Verbinding maken

Als u verbinding wilt maken met Synapse SQL, opent u Azure Data Studio en selecteert u **Nieuwe verbinding**.

![Azure Data Studio openen](./media/get-started-azure-data-studio/1-start.png)

Kies **Microsoft SQL Server** als **verbindingstype**.

Voor de verbinding zijn de volgende parameters vereist:

* **Server:** Server in de notatie `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net
* **Database:** Databasenaam

> [!NOTE]
> Als u een **serverloze SQL-pool** wilt gebruiken, moet de URL er als volgt uitzien:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Als u een **toegewezen SQL-pool** wilt gebruiken, moet de URL er als volgt uitzien:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Kies **Windows-verificatie**, **Azure Active Directory** of **SQL-aanmelding** als **verificatietype**.

Als u gebruik wilt maken van **SQL-aanmelding** als verificatietype, moet u de gebruikersnaam- en wachtwoordparameters toevoegen:

* **Gebruiker:** Gebruiker van de server in de notatie `<User>`
* **Wachtwoord:** Wachtwoord dat is gekoppeld aan de gebruiker.

Als u Azure Active Directory wilt gebruiken, moet u het gewenste verificatietype kiezen.

![Microsoft Azure Active Directory-verificatie](./media/get-started-azure-data-studio/3-aad-auth.png)

De volgende schermopname toont de **verbindingsgegevens** voor **Windows-verificatie**:

![Windows-verificatie](./media/get-started-azure-data-studio/3-windows-auth.png)

De volgende schermopname toont de **verbindingsgegevens** via **SQL-aanmelding**:

![SQL-aanmelding](./media/get-started-azure-data-studio/2-database-details.png)

Na een geslaagde aanmelding ziet u een dashboard zoals hieronder: ![Dashboard](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Query’s uitvoeren

Wanneer verbinding is gemaakt, kunt u met behulp van ondersteunde [Transact-SQL (T-SQL)](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)-instructies query's op Synapse SQL uitvoeren aan de hand van het exemplaar. Selecteer **Nieuwe query** in de dashboardweergave om aan de slag te gaan.

![Nieuwe query](./media/get-started-azure-data-studio/5-new-query.png)

U kunt bijvoorbeeld de volgende Transact-SQL-instructie gebruiken om een [query uit te voeren op de Parquet-bestanden ](query-parquet-files.md) met behulp van een serverloze SQL-pool:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Volgende stappen 
Verken andere manieren om verbinding te maken met Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)

Ga naar [Azure Data Studio gebruiken om verbinding te maken en query's op gegevens kunt uitvoeren met behulp van een toegewezen SQL-pool in Azure Synapse Analytics](/sql/azure-data-studio/quickstart-sql-dw) voor meer informatie.