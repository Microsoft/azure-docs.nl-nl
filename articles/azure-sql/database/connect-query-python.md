---
title: Python gebruiken om een query uit te voeren op een database
description: In dit onderwerp ziet u hoe u Python gebruikt om een programma te maken dat is verbonden met een database in Azure SQL Database, en hoe u een query voor deze database uitvoert met behulp van Transact-SQL-instructies.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, devx-track-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/19/2020
ms.openlocfilehash: 8fb6d319cacf85630b2c400cd18d14487725f925
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97703961"
---
# <a name="quickstart-use-python-to-query-a-database"></a>Quickstart: Python gebruiken om een query uit te voeren op een database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

In deze quickstart gebruikt u Python om verbinding te maken met Azure SQL Database, Azure SQL Managed Instance, of Synapse SQL Database, en gebruikt u Transact-SQL-instructies om een query uit te voeren op gegevens.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze quickstart te voltooien:

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

- Een database waarin u een query gaat uitvoeren.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

- [Python](https://python.org/downloads) 3 en gerelateerde software

  # <a name="macos"></a>[MacOS](#tab/macos)

  Als u Homebrew en Python, het ODBC-stuurprogramma en SQLCMD, en het Python-stuurprogramma voor SQL Server wilt installeren, gebruikt u de stappen **1.2**, **1.3** en **2.1** in [Python-apps maken met SQL Server op macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Zie [ODBC-stuurprogramma van Microsoft op macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server) voor meer informatie.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Gebruik `sudo apt-get install python python-pip gcc g++ build-essential` om Python en andere vereiste pakketten te installeren.

  Zie [Een omgeving configureren voor pyodbc Python-ontwikkeling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux) als u het ODBC-stuurprogramma, SQLCMD en het Python-stuurprogramma voor SQL Server wilt installeren.

  Zie [ODBC-stuurprogramma van Microsoft op Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server) voor meer informatie.

  # <a name="windows"></a>[Windows](#tab/windows)

  Zie [Een omgeving configureren voor pyodbc Python-ontwikkeling](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows) als u Python, het ODBC-stuurprogramma en SQLCMD, en het Python-stuurprogramma voor SQL Server wilt installeren.

  Zie [ODBC-stuurprogramma van Microsoft](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server) voor meer informatie.

---
Als u Python en de database in Azure SQL Database verder wilt verkennen, raadpleegt u [Azure SQL Database-bibliotheken voor Python](/python/api/overview/azure/sql), de [pyodbc-opslagplaats](https://github.com/mkleehammer/pyodbc/wiki/) en een [pyodbc-voorbeeld](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="create-code-to-query-your-database"></a>Code maken om query's uit te voeren op uw database 

1. Maak in een teksteditor een nieuw bestand met de naam *sqltest.py*.  
   
1. Voeg de volgende code toe. Haal de verbindingsgegevens op uit de sectie met vereisten, en vervang \<server>, \<database>, \<username> en \<password> door uw eigen waarden.
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
           cursor.execute("SELECT TOP 3 name, collation_name FROM sys.databases")
           row = cursor.fetchone()
           while row:
               print (str(row[0]) + " " + str(row[1]))
               row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>De code uitvoeren

1. Voer de volgende opdracht uit op een opdrachtprompt:

   ```cmd
   python sqltest.py
   ```

1. Controleer of de databases en de bijbehorende sorteringen zijn geretourneerd. Sluit vervolgens het opdrachtvenster.

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste database ontwerpen in Azure SQL Database](design-first-database-tutorial.md)
- [Microsoft Python-stuurprogramma's voor SQL Server](/sql/connect/python/python-driver-for-sql-server/)
- [Python-ontwikkelaarscentrum](https://azure.microsoft.com/develop/python/?v=17.23h)
