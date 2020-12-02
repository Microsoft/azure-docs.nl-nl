---
title: Verbinding maken met een SQL-groep in azure Synapse
description: Maak verbinding met de SQL-groep.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: azure-synapse, seo-lt-2019, devx-track-csharp
ms.openlocfilehash: 3483fa03e8c05101b88bde5c964b74b382d32a52
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462844"
---
# <a name="connect-to-a-sql-pool-in-azure-synapse"></a>Verbinding maken met een SQL-groep in azure Synapse 

Maak verbinding met een SQL-groep in azure Synapse.

## <a name="find-your-server-name"></a>Uw servernaam vinden

De naam van de server in het volgende voor beeld is sqlpoolservername.database.windows.net. Ga als volgt te werk om de volledig gekwalificeerde servernaam te vinden:

1. Ga naar de [Azure Portal](https://portal.azure.com).
2. Klik op **Azure Synapse Analytics**.
3. Klik op de SQL-groep waarmee u verbinding wilt maken.
4. Zoek de volledige servernaam.

   ![Volledige servernaam](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>Ondersteunde stuurprogramma's en verbindingsreeksen

De SQL-groep ondersteunt [ADO.net](/dotnet/framework/data/adonet?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), [ODBC](/sql/connect/odbc/windows/microsoft-odbc-driver-for-sql-server-on-windows?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [php](/sql/connect/php/overview-of-the-php-sql-driver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)en [JDBC](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Klik op een van de bovenstaande stuurprogramma's om de meest recente versie en documentatie te vinden.

Voor het automatisch genereren van de verbindingsreeks voor het stuurprogramma dat u gebruikt vanuit de Azure-portal, klikt u op de optie **Databaseverbindingsreeksen tonen** uit het voorgaande voorbeeld. Hier volgen ook enkele voorbeelden van hoe een verbindingsreeks er voor elk stuurprogramma uitziet.

> [!NOTE]
> Overweeg de verbindingstime-out in te stellen op 300 seconden. De verbinding blijft dan in stand tijdens korte perioden van niet-beschikbaarheid.

### <a name="adonet-connection-string-example"></a>Voorbeeld van ADO.NET-verbindingsreeks

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Voorbeeld van ODBC-verbindingsreeks

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Voorbeeld van PHP-verbindingsreeks

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Voorbeeld van JDBC-verbindingsreeks

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Verbindingsinstellingen

De SQL-pool standaardisert enkele instellingen tijdens het maken van verbinding en objecten. Deze instellingen kunnen niet worden overschreven, en omvatten:

| Instelling van SQL-groep | Waarde |
|:--- |:--- |
| [ANSI_NULLS](/sql/t-sql/statements/set-ansi-nulls-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |AAN |
| [QUOTED_IDENTIFIERS](/sql/t-sql/statements/set-quoted-identifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |AAN |
| [DATEFORMAT](/sql/t-sql/statements/set-dateformat-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |mdy |
| [DATEFIRST](/sql/t-sql/statements/set-datefirst-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |7 |

## <a name="next-steps"></a>Volgende stappen

Zie [Query’s uitvoeren bij Visual Studio](sql-data-warehouse-query-visual-studio.md) als u verbinding wilt maken en een query wilt uitvoeren met Visual Studio. Zie [verificatie voor Azure Synapse Analytics](sql-data-warehouse-authentication.md)voor meer informatie over verificatie opties.
