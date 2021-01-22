---
title: Verbinding maken met Synapse SQL via sqlcmd
description: Gebruik het opdrachtregelprogramma sqlcmd om verbinding te maken met serverloze SQL-pools en toegewezen SQL-pools, en er query's op uit te voeren.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: b78c2d5c03c95249c7f708f2d660d32c834f123e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676092"
---
# <a name="connect-to-synapse-sql-with-sqlcmd"></a>Verbinding maken met Synapse SQL via sqlcmd

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

U kunt het opdrachtregelprogramma [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) binnen Synapse SQL gebruiken om verbinding te maken met serverloze SQL-pools en toegewezen SQL-pools, en er query's op uit te voeren.  

## <a name="1-connect"></a>1. Verbinding maken
U gaat als volgt aan de slag met [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true): open de opdrachtprompt en voer **sqlcmd** in, gevolgd door de verbindingstekenreeks voor uw Synapse SQL-database. De verbindingstekenreeks moet de volgende parameters bevatten:

* **Server (-S):** Server in de notatie `<`Servernaam`>`.database.windows.net
* **Database (-d):** Databasenaam
* **Id's met aanhalingstekens inschakelen (-I):** Id's met aanhalingstekens moeten zijn ingeschakeld om verbinding te maken met een Synapse SQL-instantie

Als u gebruik wilt maken van SQL Server-verificatie, moet u de gebruikersnaam- en wachtwoordparameters toevoegen:

* **Gebruiker (-U):** Gebruiker van de server in de notatie `<`Gebruiker`>`
* **Wachtwoord (-P):** Wachtwoord dat is gekoppeld aan de gebruiker.

Uw verbindingstekenreeks kan er als volgt uitzien:

**Serverloze SQL-pool**

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
```

**Toegewezen SQL-pool**

```
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Als u geïntegreerde verificatie van Azure Active Directory wilt gebruiken, moet u de Azure Active Directory-parameters toevoegen:

* **Azure Active Directory Authentication (-G):** Azure Active Directory gebruiken voor verificatie

Uw verbindingstekenreeks kan er als volgt uitzien:

**Serverloze SQL-pool**

```
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -G -I
```

**Toegewezen SQL-pool**

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> U moet [Azure Active Directory Authentication inschakelen](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) om te verifiëren met Active Directory.

## <a name="2-query"></a>2. Query’s uitvoeren

### <a name="use-dedicated-sql-pool"></a>Een toegewezen SQL-pool gebruiken

Wanneer verbinding is gemaakt, kunt u elke ondersteunde [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-instructie (T-SQL) voor het exemplaar uitvoeren. In dit voorbeeld worden query's in de interactieve modus verzonden.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Voor een toegewezen SQL-pool ziet u in de volgende voorbeelden hoe u query's in de batchmodus uitvoert met behulp van de optie -Q of door uw SQL naar sqlcmd te sluizen:

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

### <a name="use-serverless-sql-pool"></a>Serverloze SQL-pools gebruiken

Wanneer verbinding is gemaakt, kunt u elke ondersteunde [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)-instructie (T-SQL) voor het exemplaar uitvoeren.  In het volgende voorbeeld worden query's in de interactieve modus verzonden.

```sql
C:\>sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P Enter_Your_Password_Here -I
1> SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')
2> GO
3> QUIT
```

Voor een serverloze SQL-pool ziet u in de volgende voorbeelden hoe u query's in de batchmodus uitvoert met behulp van de optie -Q of door uw SQL naar sqlcmd te sluizen:

```sql
sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I -Q "SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')"
```

```sql
"SELECT COUNT(*) FROM  OPENROWSET(BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', FORMAT='PARQUET')" | sqlcmd -S partyeunrt.database.windows.net -d demo -U Enter_Your_Username_Here -P 'Enter_Your_Password_Here' -I > ./tables.out
```

## <a name="next-steps"></a>Volgende stappen

Zie de [sqlcmd-documentatie](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor meer informatie over sqlcmd-opties.
