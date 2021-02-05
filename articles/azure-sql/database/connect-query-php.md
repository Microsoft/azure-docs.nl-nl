---
title: PHP gebruiken om een query uit te zoeken
description: Hoe PHP gebruiken om een programma te maken dat is verbonden met een database in Azure SQL Database of Azure SQL Managed Instance, en hoe u een query voor deze database uitvoert met behulp van T-SQL-instructies.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2
ms.openlocfilehash: e0ad8e9e71a0ee2c23412d535fe79955edb39ba8
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576647"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>Quickstart: PHP gebruiken om een query uit te voeren voor een Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In dit artikel ziet u hoe u [PHP](https://php.net/manual/en/intro-whatis.php) gebruikt om verbinding te maken met een database in Azure SQL Database of Azure SQL Managed Instance. Vervolgens kunt u T-SQL-instructies gebruiken om een query uit te voeren voor de gegevens.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een database in Azure SQL Database of Azure SQL Managed Instance. U kunt een van deze quickstarts gebruiken om een database te maken en vervolgens te configureren:

  | Bewerking | SQL Database | SQL Managed Instance | SQL Server op virtuele Azure-machine |
  |:--- |:--- |:---|:---|
  | Maken| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configureren | [IP-firewallregel op serverniveau](firewall-create-server-level-portal-quickstart.md)| [Connectiviteit vanaf een VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Connectiviteit vanaf on-premises](../managed-instance/point-to-site-p2s-configure.md) | [Verbinding maken met een SQL Server-exemplaar](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Gegevens laden|Adventure Works geladen volgens de quickstart|[Wide World Importers herstellen](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers herstellen](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works herstellen of importeren vanuit een [BACPAC](database-import.md)-bestand vanaf [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works herstellen of importeren uit een [BACPAC](database-import.md)-bestand vanuit [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||




  > [!IMPORTANT]
  > De scripts in dit artikel zijn geschreven voor gebruik met de Adventure Works-database. Met een met SQL Managed Instance moet u de Adventure Works-database in een exemplaardatabase importeren of de scripts in dit artikel wijzigen zodat deze de Wide World Importers-database gebruiken.

- U moet aan PHP verwante software hebben geïnstalleerd voor het besturingssysteem:

  - **macOS**: installeer PHP, het ODBC-stuurprogramma en installeer vervolgens het PHP-stuurprogramma voor SQL Server. Zie [stap 1, 2 en 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**: installeer PHP, het ODBC-stuurprogramma en installeer vervolgens het PHP-stuurprogramma voor SQL Server. Zie [stap 1, 2 en 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**, Installeer PHP-en PHP-Stuur Programma's en installeer vervolgens het ODBC-stuur programma en Sqlcmd. Zie [Stap 1.2 en 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-server-connection-information"></a>Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de database in Azure SQL Database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL Databases** of **SQL Managed Instances**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een database in Azure SQL Database, of de volledig gekwalificeerde servernaam (of het IP-adres) naast **Host** voor een Azure SQL Managed Instance of SQL Server op een Azure VM. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

> [!NOTE]
> Zie [Verbinding met een SQL Server-exemplaar](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server) voor meer informatie over de verbinding van SQL Server op Azure VM.

## <a name="add-code-to-query-the-database"></a>Code toevoegen om een query uit te voeren op de database

1. Maak een nieuw bestand in uw favoriete teksteditor *sqltest.php*.  

1. Vervang de inhoud ervan door de volgende code. Voeg vervolgens de juiste waarden toe voor uw server, database, gebruiker en wachtwoord.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>De code uitvoeren

1. Voer de app uit vanaf de opdrachtprompt.

   ```bash
   php sqltest.php
   ```

1. Controleer de bovenste 20 rijen die worden geretourneerd en sluit het app-venster.

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste database ontwerpen in Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft PHP-stuurprogramma's voor SQL Server](https://github.com/Microsoft/msphpsql/)
- [Problemen melden of vragen stellen](https://github.com/Microsoft/msphpsql/issues)
- [Voorbeeld van pogingslogica: Flexibel verbinding maken met Azure SQL via PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
