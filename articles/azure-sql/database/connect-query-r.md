---
title: R gebruiken met Azure SQL Database Machine Learning Services (preview) om een query uit te voeren op een database
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In dit artikel wordt uitgelegd hoe u een R-script gebruikt met Azure SQL Database Machine Learning Services om verbinding te maken met een database in Azure SQL Database en een query op de database kunt uitvoeren met behulp van Transact-SQL-instructies.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: sqldbrb=2
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, sstein
manager: cgronlun
ms.date: 05/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2e32a4abeae78aa7105f21ecffbb18c2eae841a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185620"
---
# <a name="quickstart-use-r-with-azure-sql-database-machine-learning-services-preview-to-query-a-database"></a>Quickstart: R gebruiken met Azure SQL Database Machine Learning Services (preview) om een query uit te voeren op een database 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In deze snelstart gebruikt u R met Azure SQL Database Machine Learning Services om verbinding te maken met een database in Azure SQL Database en gebruikt u Transact-SQL-instructies om een query op gegevens uit te voeren.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een [Azure SQL-database](single-database-create-quickstart.md)
- [Machine Learning Services](../managed-instance/machine-learning-services-overview.md) met R ingeschakeld.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

> [!IMPORTANT]
> De scripts in dit artikel zijn geschreven voor gebruik met de **Adventure Works**-database.

Machine Learning Services met R is een functie van Azure SQL Database die wordt gebruikt voor het uitvoeren van in-database R-scripts. Zie [R-project](https://www.r-project.org/) voor meer informatie.

## <a name="get-the-sql-server-connection-information"></a>De SQL Server-verbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de database in Azure SQL Database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL-databases** of **Met SQL beheerde exemplaren**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor een database in Azure SQL Database, of de volledig gekwalificeerde servernaam naast **Host** voor een beheerd exemplaar in Azure SQL Managed Instance. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

## <a name="create-code-to-query-your-database"></a>Code maken om query's uit te voeren op uw database

1. Open **SQL Server Management Studio** en maak verbinding met de database.

   Als u hulp nodig hebt bij het tot stand brengen van een verbinding, raadpleegt u [quickstart: SQL Server Management Studio gebruiken om verbinding te maken en query's uit te voeren op een database in Azure SQL Database](connect-query-ssms.md).

1. Geef het volledige R-script door aan de [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) opgeslagen procedure.

   Het script wordt doorgegeven via het argument `@script`. Alles binnen het argument `@script` moet geldige R-code zijn.
   
   >[!IMPORTANT]
   >Voor de code in dit voorbeeld worden de voorbeeldgegevens gebruikt van AdventureWorksLT, die u als bron kunt kiezen bij het maken van uw database. Als in uw database andere gegevens staan, kunt u tabellen uit uw eigen database gebruiken in de SELECT-query. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Als er fouten optreden, kan dit zijn omdat de openbare preview van Machine Learning Services (met R) niet is ingeschakeld voor uw database. Zie het gedeelte [Vereisten](#prerequisites) hierboven.

## <a name="run-the-code"></a>De code uitvoeren

1. Voer de opgeslagen procedure [sp_execute_external_script](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) uit.

1. Controleer of de bovenste twintig rijen Categorie/Product worden geretourneerd in het venster **Berichten**.

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste database ontwerpen in Azure SQL Database](design-first-database-tutorial.md)
- [Azure SQL Database Machine Learning Services (met R)](../managed-instance/machine-learning-services-overview.md)
- [Eenvoudige R-scripts maken en uitvoeren in Azure SQL Database Machine Learning Services (preview)](/sql/machine-learning/tutorials/quickstart-r-create-script?context=%2fazure%2fazure-sql%2fmanaged-instance%2fcontext%2fml-context)