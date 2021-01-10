---
title: Aan de slag met query's voor meerdere data bases
description: query's voor Elastic Data Base gebruiken met verticaal gepartitioneerde data bases
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 90f89e72193c26b71859076b99c448a6e2d4c4c6
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060081"
---
# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Aan de slag met query's voor meerdere data bases (verticaal partitioneren) (preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Met Elastic data base query (preview) voor Azure SQL Database kunt u T-SQL-query's uitvoeren die meerdere data bases omvatten met behulp van één verbindings punt. Dit artikel is van toepassing op [verticaal gepartitioneerde data bases](elastic-query-vertical-partitioning.md).  

Wanneer u klaar bent, kunt u informatie over het configureren en gebruiken van een Azure SQL Database voor het uitvoeren van query's die meerdere gerelateerde data bases omvatten.

Zie  [Azure SQL database overzicht van Elastic data base](elastic-query-overview.md)voor meer informatie over de functie voor Elastic data base-query's.

## <a name="prerequisites"></a>Vereisten

U moet een machtiging voor externe gegevens bronnen wijzigen. Deze machtiging is opgenomen in de machtiging ALTER DATABASE. Machtigingen voor ALTER ANY EXTERNAL DATA SOURCE zijn nodig om te verwijzen naar de onderliggende gegevensbron.

## <a name="create-the-sample-databases"></a>De voorbeeld databases maken

U kunt beginnen met het maken van twee data bases, **klanten** en **Orders**, op dezelfde server of op verschillende servers.

Voer de volgende query's uit op de Data Base **Orders** om de tabel **OrderInformation** te maken en de voorbeeld gegevens in te voeren.

```tsql
CREATE TABLE [dbo].[OrderInformation](
    [OrderID] [int] NOT NULL,
    [CustomerID] [int] NOT NULL
    )
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1)
INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8)
```

Voer nu de volgende query uit op de **klanten** database om de tabel **CustomerInformation** te maken en de voorbeeld gegevens in te voeren.

```tsql
CREATE TABLE [dbo].[CustomerInformation](
    [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NULL,
    [Company] [varchar](50) NULL
    CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC)
)
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ')
INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO')
```

## <a name="create-database-objects"></a>Database objecten maken

### <a name="database-scoped-master-key-and-credentials"></a>Data base-bereik hoofd sleutel en referenties

1. Open SQL Server Management Studio of SQL Server Data Tools in Visual Studio.
2. Maak verbinding met de data base orders en voer de volgende T-SQL-opdrachten uit:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';
    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';  
    ```

    De master_key_password is een sterk wacht woord van uw keuze om de verbindings referenties te versleutelen. 
    De gebruikers naam en het wacht woord moeten de gebruikers naam en het wacht woord zijn die worden gebruikt om u aan te melden bij de klanten database.
    Verificatie via Azure Active Directory met elastische query's wordt momenteel niet ondersteund.

### <a name="external-data-sources"></a>Externe gegevensbronnen

Als u een externe gegevens bron wilt maken, voert u de volgende opdracht uit in de data base orders:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = RDBMS,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'Customers',
    CREDENTIAL = ElasticDBQueryCred,
) ;
```

### <a name="external-tables"></a>Externe tabellen

Maak een externe tabel in de data base orders die overeenkomt met de definitie van de CustomerInformation-tabel:

```tsql
CREATE EXTERNAL TABLE [dbo].[CustomerInformation]
( [CustomerID] [int] NOT NULL,
    [CustomerName] [varchar](50) NOT NULL,
    [Company] [varchar](50) NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc)
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Een voor beeld van een Elastic data base-T-SQL-query uitvoeren

Wanneer u uw externe gegevens bron en uw externe tabellen hebt gedefinieerd, kunt u nu T-SQL gebruiken om een query uit te zoeken op uw externe tabellen. Deze query uitvoeren op de orders database:

```tsql
SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company
FROM OrderInformation
INNER JOIN CustomerInformation
ON CustomerInformation.CustomerID = OrderInformation.CustomerID
```

## <a name="cost"></a>Kosten

Op dit moment is de functie voor Elastic data base-query's opgenomen in de kosten van uw Azure SQL Database.  

Zie [SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database)voor prijs informatie.

## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van elastische query's](elastic-query-overview.md)voor een overzicht van elastische query's.
* Zie [Query's uitvoeren op verticaal gepartitioneerde gegevens](elastic-query-vertical-partitioning.md) voor de syntaxis van en voorbeeldquery's voor verticaal gepartitioneerde gegevens
* Zie [Aan de slag met elastische query's voor horizontale partitionering (sharding)](elastic-query-getting-started.md) voor een zelfstudie over horizontale partitionering (sharding).
* Zie [Query's uitvoeren op horizontaal gepartitioneerde gegevens](elastic-query-horizontal-partitioning.md) voor de syntaxis van en voorbeeldquery's voor horizontaal gepartitioneerde gegevens
* Zie [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) voor een opgeslagen procedure waarmee een Transact-SQL-instructie wordt uitgevoerd op één externe Azure SQL-database of een aantal databases die als shards fungeren in een schema voor horizontale partitionering.
