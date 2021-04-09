---
title: Node.js gebruiken om een query uit te voeren op een database
description: Node.js gebruiken om een programma te maken dat is verbonden met een database in Azure SQL Database of een met Azure SQL beheerd exemplaar, en hoe een query hierop uitvoeren met T-SQL-instructies.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2, devx-track-js
ms.openlocfilehash: 44530577972839aacb803d1722fa97716088fa0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325433"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Quickstart: Node.js gebruiken om een query uit te voeren op een database in Azure SQL Database of een met Azure SQL beheerd exemplaar
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

In deze quickstart gebruikt u Node.js om verbinding te maken met een database en een query uit te voeren op gegevens.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

  | Bewerking | SQL Database | SQL Managed Instance | SQL Server op virtuele Azure-machine |
  |:--- |:--- |:---|:---|
  | Maken| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configureren | [IP-firewallregel op serverniveau](firewall-create-server-level-portal-quickstart.md)| [Connectiviteit vanaf een VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Connectiviteit vanaf on-premises](../managed-instance/point-to-site-p2s-configure.md) | [Verbinding maken met een SQL Server-exemplaar](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Gegevens laden|Adventure Works geladen volgens de quickstart|[Wide World Importers herstellen](../managed-instance/restore-sample-database-quickstart.md) | [Wide World Importers herstellen](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works herstellen of importeren uit een [BACPAC](database-import.md)-bestand vanuit [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works herstellen of importeren vanuit een [BACPAC](database-import.md)-bestand vanaf [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||


- [Node.js](https://nodejs.org)-gerelateerde software

  # <a name="macos"></a>[MacOS](#tab/macos)

  Installeer Homebrew en Node.js en installeer het ODBC-stuurprogramma en SQLCMD aan de hand van de stappen **1.2** en **1.3** in [Node.js-apps maken met SQL Server op macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installeer Node.js en installeer het ODBC-stuurprogramma en SQLCMD aan de hand van de stappen **1.2** en **1.3** in [Node.js-apps maken met SQL Server op Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Installeer Chocolatey en Node.js en installeer het ODBC-stuurprogramma en SQLCMD aan de hand van de stappen **1.2** en **1.3** in [Node.js-apps maken met SQL Server op Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> De scripts in dit artikel zijn geschreven voor gebruik met de **Adventure Works**-database.

> [!NOTE]
> U kunt desgewenst kiezen voor het gebruik van een met Azure SQL beheerd exemplaar.
>
> Als u wilt maken en configureren, gebruikt u de [Azure-portal](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md)of de [opdrachtregelinterface](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) en stelt u [on-premises](../managed-instance/point-to-site-p2s-configure.md) of [VM](../managed-instance/connect-vm-instance-configure.md)-connectiviteit in.
>
> Als u gegevens wilt laden, raadpleegt u [Herstellen met BACPAC](database-import.md) met het [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)-bestand of raadpleegt u [De Wide World Imports-database herstellen](../managed-instance/restore-sample-database-quickstart.md).

## <a name="get-server-connection-information"></a>Serververbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de database in Azure SQL Database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Ga naar de pagina **SQL-databases** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een database in Azure SQL Database of de volledig gekwalificeerde servernaam (of het IP-adres) naast **Host** voor een met Azure SQL beheerd exemplaar of SQL Server op de Azure-VM. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

> [!NOTE]
> Zie [Verbinding maken met SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server) voor meer informatie over de verbinding van SQL Server op een Azure-VM.

## <a name="create-the-project"></a>Het project maken

Open een opdrachtprompt en maak een map met de naam *sqltest*. Open de map die u hebt gemaakt en voer de volgende opdracht uit:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>Code toevoegen om een query uit te voeren op de database

1. Maak in uw favoriete teksteditor een nieuw bestand *sqltest.js*.

1. Vervang de inhoud ervan door de volgende code. Voeg vervolgens de juiste waarden toe voor uw server, database, gebruiker en wachtwoord.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });

      connection.execSql(request);
    }
    ```

> [!NOTE]
> In het codevoorbeeld wordt de voorbeelddatabase **AdventureWorksLT** in Azure SQL Database gebruikt.

## <a name="run-the-code"></a>De code uitvoeren

1. Voer het programma uit bij de opdrachtprompt.

    ```bash
    node sqltest.js
    ```

1. Controleer de bovenste 20 rijen die worden geretourneerd en sluit het toepassingsvenster.

## <a name="next-steps"></a>Volgende stappen

- [Microsoft-stuurprogramma Node.js voor SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Verbinding maken en query's uitvoeren in Windows/Linux/macOS met [.NET core](connect-query-dotnet-core.md), [Visual Studio Code](connect-query-vscode.md) of [SSMS](connect-query-ssms.md) (alleen Windows)

- [Aan de slag met .NET Core in Windows/Linux/macOS met behulp van de opdrachtregel](/dotnet/core/tutorials/using-with-xplat-cli)

- Uw eerste database in Azure SQL Database ontwerpen met [.NET](design-first-database-csharp-tutorial.md) of [SSMS](design-first-database-tutorial.md)
