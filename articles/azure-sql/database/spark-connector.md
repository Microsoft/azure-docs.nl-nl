---
title: De Spark-connector gebruiken met Microsoft Azure SQL en SQL Server
description: Meer informatie over het gebruik van de Spark-connector met Azure SQL Database, Azure SQL Managed instance en SQL Server.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 09/02/2020
ms.openlocfilehash: 2e01e74f5086f7f1eb7e85661fbd35f452d8dae8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790199"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Big data analyse in realtime versnellen met behulp van de Spark-connector
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Vanaf sep 2020 wordt deze connector niet actief onderhouden. [Apache Spark connector voor SQL Server en Azure SQL](/sql/connect/spark/connector) is nu wel beschikbaar, met ondersteuning voor python-en R-bindingen, een gebruiks vriendelijke interface voor het bulksgewijs invoegen van gegevens en vele andere verbeteringen. We raden u ten zeerste aan de nieuwe connector te evalueren en gebruiken in plaats van deze. De informatie over de oude connector (deze pagina) wordt alleen voor archiverings doeleinden bewaard.

De Spark-connector maakt data bases in Azure SQL Database, Azure SQL Managed instance en SQL Server om te fungeren als invoer gegevens bron of uitvoer gegevens filter voor Spark-taken. U kunt hiermee realtime transactionele gegevens gebruiken in big data Analytics en de resultaten voor ad-hoc-query's of-rapporten behouden. Vergeleken met de ingebouwde JDBC-connector biedt deze connector de mogelijkheid om gegevens bulksgewijs in te voegen in uw data base. Hiermee kan leverde rij-voor-rij worden ingevoegd met 10x voor snellere prestaties van 20x. De Spark-connector ondersteunt Azure Active Directory-verificatie (Azure AD) om verbinding te maken met Azure SQL Database en Azure SQL Managed instance, zodat u verbinding kunt maken tussen uw data base en Azure Databricks met uw Azure AD-account. Het biedt vergelijk bare interfaces met de ingebouwde JDBC-connector. Het is eenvoudig om uw bestaande Spark-taken te migreren om deze nieuwe connector te gebruiken.

## <a name="download-and-build-a-spark-connector"></a>Een Spark-connector downloaden en bouwen

De GitHub-opslag plaats voor de oude connector die eerder aan deze pagina is gekoppeld, wordt niet actief onderhouden. In plaats daarvan raden we u ten zeerste aan de [nieuwe connector](https://github.com/microsoft/sql-spark-connector)te evalueren en te gebruiken.

### <a name="official-supported-versions"></a>Officiële ondersteunde versies

| Onderdeel                             | Versie                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 of hoger           |
| Scala                                 | 2,10 of hoger            |
| Microsoft JDBC-stuurprogramma voor SQL Server  | 6,2 of hoger             |
| Microsoft SQL Server                  | SQL Server 2008 of hoger |
| Azure SQL Database                    | Ondersteund                |
| Azure SQL Managed Instance            | Ondersteund                |

De Spark-connector maakt gebruik van het micro soft JDBC-stuur programma voor SQL Server om gegevens tussen Spark-werk knooppunten en data bases te verplaatsen:

De gegevens stroom is als volgt:

1. Het Spark-hoofd knooppunt maakt verbinding met data bases in SQL Database of SQL Server en laadt gegevens uit een specifieke tabel of gebruikt een specifieke SQL-query.
2. Het Spark-hoofd knooppunt distribueert gegevens naar worker-knoop punten voor trans formatie.
3. Het worker-knoop punt maakt verbinding met data bases die verbinding maken met SQL Database en SQL Server en schrijft gegevens naar de data base. De gebruiker kan ervoor kiezen om rijen in te voegen of bulksgewijs in te voegen.

In het volgende diagram ziet u de gegevens stroom.

   ![Diagram toont de beschreven stroom, met een hoofd knooppunt dat rechtstreeks verbinding maakt met de data base en verbinding maakt met drie werk knooppunten die verbinding maken met de data base.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>De Spark-connector bouwen

Op dit moment gebruikt het connector project maven. Als u de connector zonder afhankelijkheden wilt maken, kunt u het volgende uitvoeren:

- MVN opschone pakket
- De nieuwste versie van het JAR-bestand downloaden vanuit de map uitgeven
- Het SQL Database Spark JAR insluiten

## <a name="connect-and-read-data-using-the-spark-connector"></a>Verbinding maken en gegevens lezen met behulp van de Spark-connector

U kunt verbinding maken met data bases in SQL Database en SQL Server vanuit een Spark-taak om gegevens te lezen of te schrijven. U kunt ook een DML-of DDL-query uitvoeren in data bases in SQL Database en SQL Server.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Gegevens lezen van Azure SQL en SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>Gegevens lezen uit Azure SQL en SQL Server met de opgegeven SQL-query

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>Gegevens schrijven naar Azure SQL en SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>DML-of DDL-query uitvoeren in Azure SQL en SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Verbinding maken vanuit Spark met behulp van Azure AD-verificatie

U kunt verbinding maken met Azure SQL Database en SQL Managed instance met behulp van Azure AD-verificatie. Gebruik Azure AD-verificatie om identiteiten van database gebruikers centraal en als alternatief voor SQL Server-verificatie te beheren.

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Verbinding maken met behulp van de ActiveDirectoryPassword-verificatie modus

#### <a name="setup-requirement"></a>Installatie vereiste

Als u de ActiveDirectoryPassword-verificatie modus gebruikt, moet u [Azure-ActiveDirectory-Library-voor-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) en de bijbehorende afhankelijkheden downloaden en in het pad Java build toevoegen.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-an-access-token"></a>Verbinding maken met behulp van een toegangs token

#### <a name="setup-requirement"></a>Installatie vereiste

Als u de verificatie modus op basis van toegangs tokens gebruikt, moet u [Azure-ActiveDirectory-library-for-Java](https://github.com/AzureAD/azure-activedirectory-library-for-java) en de bijbehorende afhankelijkheden downloaden en in het pad Java build toevoegen.

Zie [Azure Active Directory verificatie gebruiken voor verificatie voor](authentication-aad-overview.md) meer informatie over het verkrijgen van een toegangs token voor uw data base in Azure SQL database of Azure SQL Managed instance.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-using-bulk-insert"></a>Gegevens schrijven met bulksgewijs invoegen

De traditionele JDBC-connector schrijft gegevens in uw data base met behulp van rijen per rij. U kunt de Spark-connector gebruiken om gegevens te schrijven naar Azure SQL en SQL Server met bulksgewijs invoegen. De schrijf prestaties worden aanzienlijk verbeterd bij het laden van grote gegevens sets of het laden van gegevens in tabellen waarin een column store-index wordt gebruikt.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/**
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Volgende stappen

Als u dat nog niet hebt gedaan, downloadt u de Spark-connector van de [Azure-sqldb-Spark github-opslag plaats](https://github.com/Azure/azure-sqldb-spark) en bekijkt u de aanvullende bronnen in de opslag plaats:

- [Voor beeld Azure Databricks-notebooks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Voorbeeld scripts (scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Mogelijk wilt u ook de [Apache Spark SQL-, DataFrames-en gegevens sets gids](https://spark.apache.org/docs/latest/sql-programming-guide.html) en de [Azure Databricks documentatie](/azure/azure-databricks/)bekijken.