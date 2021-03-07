---
title: Verbinding maken en query's uitvoeren
description: Koppelingen naar quickstarts voor Azure SQL Database waarin wordt beschreven hoe u verbinding met Azure SQL Database en Azure SQL Managed Instance maakt en vervolgens een query uitvoert.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: a9f9e03227bfb75d94ed79cdf858278e2efe4f31
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440391"
---
# <a name="azure-sql-database-and-azure-sql-managed-instance-connect-and-query-articles"></a>Verbinding maken met Azure SQL Database en Azure SQL Managed Instance en query's uitvoeren voor artikelen
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Het volgende document bevat koppelingen naar voorbeelden van Azure die laten zien hoe u verbinding maakt met een Azure SQL Database en Azure SQL Managed Instance en vervolgens een query uitvoert. Zie [TLS-overwegingen voor databaseconnectiviteit](#tls-considerations-for-database-connectivity) voor enkele gerelateerde aanbevelingen voor beveiligingsbinding op transportniveau (Transport Level Security).

## <a name="quickstarts"></a>Snelstartgidsen

| Snelstartgids | Beschrijving |
|---|---|
|[SQL Server Management Studio](connect-query-ssms.md)|In deze quickstart wordt uitgelegd hoe u SSMS gebruikt om verbinding te maken met een database en vervolgens Transact-SQL-instructies gebruikt om gegevens in de database te zoeken, in te voegen, bij te werken en te verwijderen.|
|[Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=%2fazure%2fsql-database%2ftoc.json)|In deze quickstart wordt uitgelegd hoe u Azure Data Studio gebruikt om verbinding te maken met een database, waarna u met behulp van Transact-SQL-instructies (T-SQL) de TutorialDB maakt die wordt gebruikt in de zelfstudies voor Azure Data Studio.|
|[Azure-portal](connect-query-portal.md)|In deze quickstart ziet u hoe u de queryeditor gebruikt om verbinding te maken met een database (alleen Azure SQL Database) en vervolgens Transact-SQL-instructies gebruikt om gegevens in de database te zoeken, in te voegen, bij te werken en te verwijderen.|
|[Visual Studio Code](connect-query-vscode.md)|In deze quickstart ziet u hoe u Visual Studio Code gebruikt om verbinding te maken met een database en vervolgens Transact-SQL-instructies gebruikt om gegevens in de database te zoeken, in te voegen, bij te werken en te verwijderen.|
|[.NET met Visual Studio](connect-query-dotnet-visual-studio.md)|In deze quickstart wordt uitgelegd hoe u .NET Framework gebruikt om een C#-programma te maken met Visual Studio dat verbinding maakt met een database en hoe u Transact-SQL-instructies gebruikt om gegevens te doorzoeken.|
|[.NET Core](connect-query-dotnet-core.md)|In deze quickstart wordt uitgelegd hoe u .NET Core gebruikt in Windows/Linux/macOS om een C#-programma te maken dat verbinding maakt met een database en hoe u Transact-SQL-instructies gebruikt om een query uit te voeren voor de gegevens.|
|[Go](connect-query-go.md)|In deze quickstart ziet u hoe u Go kunt gebruiken om verbinding te maken met een database. Bovendien worden er Transact-SQL-instructies voor het doorzoeken en wijzigen van gegevens beschreven.|
|[Java](connect-query-java.md)|In deze quickstart wordt uitgelegd hoe u Java gebruikt om verbinding te maken met een database en hoe u vervolgens Transact-SQL-instructies gebruikt om een query uit te voeren voor gegevens.|
|[Node.js](connect-query-nodejs.md)|In deze quickstart wordt uitgelegd hoe u Node.js gebruikt om een programma te maken dat verbinding maakt met een database en hoe u Transact-SQL-instructies gebruikt om een query uit te voeren voor gegevens.|
|[PHP](connect-query-php.md)|In deze quickstart wordt uitgelegd hoe u PHP gebruikt om een programma te maken dat verbinding maakt met een database en hoe u Transact-SQL-instructies gebruikt om gegevens te doorzoeken.|
|[Python](connect-query-python.md)|In deze quickstart wordt uitgelegd hoe u Python gebruikt om verbinding te maken met een database en hoe u Transact-SQL-instructies gebruikt om een query uit te voeren voor de gegevens. |
|[Ruby](connect-query-ruby.md)|In deze quickstart wordt uitgelegd hoe u Ruby gebruikt om een programma te maken dat verbinding maakt met een database en hoe u Transact-SQL-instructies gebruikt om gegevens te doorzoeken.|
|[R](connect-query-r.md)|In deze quickstart wordt uitgelegd hoe u R gebruikt met Azure SQL Database Machine Learning Services om een programma te maken waarmee u verbinding met een database in Azure SQL Database kunt maken en Transact-SQL-instructies kunt gebruiken om een query voor gegevens uit te voeren.|
|||

## <a name="get-server-connection-information"></a>Serververbindingsgegevens ophalen

Haal de verbindingsgegevens op die u nodig hebt om verbinding te maken met de database in Azure SQL Database. U hebt de volledig gekwalificeerde servernaam of hostnaam, databasenaam en aanmeldingsgegevens nodig voor de volgende procedures.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Navigeer naar de pagina **SQL Databases** of **SQL Managed Instances**.

3. Bekijk op de pagina **Overzicht** de volledig gekwalificeerde servernaam naast **Servernaam** voor de database in Azure SQL Database, of de volledig gekwalificeerde servernaam (of het IP-adres) naast **Host** voor een met Azure SQL beheerd exemplaar of SQL Server op een Azure-VM. Als u de servernaam of hostnaam wilt kopiëren, plaatst u de muisaanwijzer erboven en selecteert u het pictogram **Kopiëren**.

> [!NOTE]
> Zie [Verbinding met een SQL Server-exemplaar](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server) voor meer informatie over de verbinding van SQL Server op een Azure-VM.

## <a name="get-adonet-connection-information-optional---sql-database-only"></a>ADO.NET-verbindingsgegevens ophalen (optioneel - alleen SQL Database)

1. Ga naar de databaseblade in de Azure-portal en selecteer onder **Instellingen** de optie **Verbindingsreeksen**.

2. Bekijk de volledige **ADO.NET**-verbindingsreeks.

    ![ADO.NET-verbindingsreeks](./media/connect-query-dotnet-core/adonet-connection-string2.png)

3. Kopieer de **ADO.NET**-verbindingsreeks als u van plan bent om deze te gebruiken.

## <a name="tls-considerations-for-database-connectivity"></a>TLS-overwegingen voor de connectiviteit van databases

Transport Layer Security (TLS) wordt gebruikt door alle stuurprogramma's die Microsoft aanbiedt of ondersteunt voor het maken van verbinding met databases in Azure SQL Database of Azure SQL Managed Instance. Er is geen speciale configuratie nodig. Voor alle verbindingen met SQL Server-instantie, een database in Azure SQL Database of een instantie van Azure SQL Managed Instance, raden we aan om de volgende configuraties of een equivalent daarvan in te stellen voor alle toepassingen:

- **Versleutelen = Aan**
- **TrustServerCertificate = Uit**

Sommige systemen gebruiken andere, maar wel vergelijkbare sleutelwoorden voor deze configuratiesleutelwoorden. Deze configuraties zorgen ervoor dat het clientstuurprogramma de identiteit controleert van het TLS-certificaat dat afkomstig is van de server.

We raden u ook aan om TLS 1.1 en 1.0 op de client uit te schakelen als u moet voldoen aan de Payment Card Industry - Data Security Standard (PCI-DSS).

Stuurprogramma's die niet van Microsoft zijn, maken mogelijk niet standaard gebruik van TLS. Dit kan een factor zijn bij het maken van verbinding met Azure SQL Database of Azure SQL Managed Instance. Bij toepassingen met ingesloten stuurprogramma's is het mogelijk niet toegestaan om deze verbindingsinstellingen te beheren. Wij raden u aan om de beveiliging van zulke stuurprogramma’s en toepassingen te controleren voordat u ze gebruikt op systemen die interactie hebben met gevoelige gegevens.

## <a name="libraries"></a>Bibliotheken

U kunt verschillende bibliotheken en frameworks gebruiken om verbinding te maken met Azure SQL Database of Azure SQL Managed Instance. Bekijk onze [Aan de slag-zelfstudies](https://aka.ms/sqldev) om snel aan de slag te gaan met programmeertalen zoals C#, Java, Node.js, PHP en Python. Bouw vervolgens een app met behulp van SQL Server op Linux of Windows of Docker op macOS.

De volgende tabel bevat connectiviteitsbibliotheken of *stuurprogramma's* die clienttoepassingen kunnen gebruiken vanuit een groot aantal talen om verbinding te maken met en gebruik te maken van SQL Server on-premises of in de cloud. U kunt deze gebruiken in Linux, Windows of Docker en om verbinding te maken met Azure SQL Database, Azure SQL Managed Instance en Azure Synapse Analytics.

| Taal | Platform | Aanvullende bronnen | Downloaden | Aan de slag |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET voor SQL Server](/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Downloaden](https://www.microsoft.com/net/download/) | [Aan de slag](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-stuurprogramma voor SQL Server](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server/) | [Downloaden](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server) |  [Aan de slag](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-stuurprogramma voor SQL Server](/sql/connect/php/microsoft-php-driver-for-sql-server) | [Downloaden](/sql/connect/php/download-drivers-php-sql-server) | [Aan de slag](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js-stuurprogramma voor SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server/) | [Installeren](/sql/connect/node-js/step-1-configure-development-environment-for-node-js-development/) |  [Aan de slag](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-stuurprogramma](/sql/connect/python/python-driver-for-sql-server/) | Installatieopties: <br/> \* [pymssql](/sql/connect/python/pymssql/step-1-configure-development-environment-for-pymssql-python-development/) <br/> \* [pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development/) |  [Aan de slag](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby-stuurprogramma voor SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/) | [Installeren](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development/) | [Aan de slag](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-stuurprogramma voor SQL Server](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) | [Downloaden](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server/) |  

De volgende tabel bevat voorbeelden van ORM-frameworks (Object-Relational Mapping) en webframeworks die clienttoepassingen kunnen gebruiken met SQL Server, Azure SQL Database, Azure SQL Managed Instance of Azure Synapse Analytics. U kunt de Frameworks op Linux, Windows of Docker gebruiken.

| Taal | Platform | ORM('s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](/ef)<br>[Entity Framework Core](/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://sequelize.org/) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Volgende stappen

- Ga naar [Connectiviteitsarchitectuur van Azure SQL Database](connectivity-architecture.md) voor informatie over connectiviteitsarchitectuur.
- [SQL Server-stuurprogramma's](/sql/connect/sql-connection-libraries/) zoeken die worden gebruikt om verbinding te maken vanuit clienttoepassingen.
- Verbinding maken met Azure SQL Database of Azure SQL Managed Instance
  - [Verbinding maken en query's uitvoeren met .NET ( C# )](connect-query-dotnet-core.md)
  - [Verbinding maken en query's uitvoeren met PHP](connect-query-php.md)
  - [Verbinding maken en query's uitvoeren met Node. js](connect-query-nodejs.md)
  - [Verbinding maken en query's uitvoeren met Java](connect-query-java.md)
  - [Verbinding maken en query's uitvoeren met Python](connect-query-python.md)
  - [Verbinding maken en query's uitvoeren met Ruby](connect-query-ruby.md)
- Voorbeelden van logische code voor opnieuw proberen:
  - [Flexibel verbinding maken met ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Flexibel verbinding maken met PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php
