---
title: Een database kopiëren
description: Maak een transactioneel consistente kopie van een bestaande database in Azure SQL Database op dezelfde server of op een andere server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sashan
ms.reviewer: wiassaf
ms.date: 03/10/2021
ms.openlocfilehash: b7084ef045d14b9715c41bb9ffa483d1f2f7bedf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865095"
---
# <a name="copy-a-transactionally-consistent-copy-of-a-database-in-azure-sql-database"></a>Kopieer een transactioneel consistente kopie van een database in Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database biedt verschillende methoden voor het maken van een kopie van een bestaande [database](single-database-overview.md) op dezelfde server of op een andere server. U kunt een database kopiëren met behulp Azure Portal, PowerShell, Azure CLI of T-SQL.

## <a name="overview"></a>Overzicht

Een databasekopie is een transactioneel consistente momentopname van de brondatabase vanaf een bepaald tijdstip nadat de kopieeraanvraag is gestart. U kunt dezelfde server of een andere server voor de kopie selecteren. U kunt er ook voor kiezen om de redundantie van de back-up, de servicelaag en de rekengrootte van de brondatabase te behouden, of een andere redundantie en/of rekenkracht voor back-upopslag binnen dezelfde of een andere servicelaag te gebruiken. Nadat het kopiëren is voltooid, wordt het een volledig functionele, onafhankelijke database. De aanmeldingen, gebruikers en machtigingen in de gekopieerde database worden onafhankelijk van de brondatabase beheerd. De kopie wordt gemaakt met behulp van de geo-replicatietechnologie. Zodra de replica-seeding is voltooid, wordt de geo-replicatiekoppeling automatisch beëindigd. Alle vereisten voor het gebruik van geo-replicatie zijn van toepassing op de kopieerbewerking van de database. Zie [Overzicht van actieve geo-replicatie](active-geo-replication-overview.md) voor meer informatie.

> [!NOTE]
> Azure SQL Database configureerbare redundantie voor back-upopslag is momenteel beschikbaar als openbare preview-versie in Brazilië - zuid algemeen beschikbaar in de Azure-regio Azië - zuidoost. Als in de preview de brondatabase wordt gemaakt met lokaal redundante of zone-redundante back-upopslag redundantie, wordt het kopiëren van de database naar een server in een andere Azure-regio niet ondersteund. 

## <a name="logins-in-the-database-copy"></a>Aanmeldingen in de databasekopie

Wanneer u een database naar dezelfde server kopieert, kunnen dezelfde aanmeldingen worden gebruikt voor beide databases. De beveiligingsprincipaal die u gebruikt om de database te kopiëren, wordt de database-eigenaar van de nieuwe database.

Wanneer u een database naar een andere server kopieert, wordt de beveiligingsprincipaal die de kopieerbewerking op de doelserver heeft gestart de eigenaar van de nieuwe database.

Ongeacht de doelserver worden alle databasegebruikers, hun machtigingen en hun beveiligings-id's (SID's) gekopieerd naar de databasekopie. Het [gebruik van ingesloten databasegebruikers](logins-create-manage.md) voor gegevenstoegang zorgt ervoor dat de gekopieerde database dezelfde gebruikersreferenties heeft, zodat u deze onmiddellijk met dezelfde referenties kunt openen nadat het kopiëren is voltooid.

Als u aanmeldingen op serverniveau gebruikt voor gegevenstoegang en de database naar een andere server kopieert, werkt de op aanmelding gebaseerde toegang mogelijk niet. Dit kan gebeuren omdat de aanmeldingen niet bestaan op de doelserver of omdat hun wachtwoorden en beveiligings-id's (SID's) verschillen. Zie How to manage Azure SQL Database security after disaster recovery (Beveiliging na noodherstel beheren) voor meer informatie over het beheren van aanmeldingen wanneer u een database naar een andere server [kopieert.](active-geo-replication-security-configure.md) Nadat de kopieerbewerking naar een andere server is geslaagd en voordat andere gebruikers opnieuw worden toegevoegd, kan alleen de aanmelding die is gekoppeld aan de database-eigenaar of de serverbeheerder zich aanmelden bij de gekopieerde database. Zie Aanmeldingen oplossen om aanmeldingen op te lossen en gegevenstoegang tot stand te stellen nadat de kopieerbewerking is [voltooid.](#resolve-logins)

## <a name="copy-using-the-azure-portal"></a>Kopiëren met Azure Portal

Als u een database wilt kopiëren met behulp van Azure Portal, opent u de pagina voor uw database en klikt u vervolgens op **Kopiëren.**

   ![Database-kopie](./media/database-copy/database-copy.png)

## <a name="copy-using-powershell-or-the-azure-cli"></a>Kopiëren met Behulp van PowerShell of de Azure CLI

Gebruik de volgende voorbeelden om een database te kopiëren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik voor PowerShell de [cmdlet New-AzSqlDatabaseCopy.](/powershell/module/az.sql/new-azsqldatabasecopy)

> [!IMPORTANT]
> De Module PowerShell Azure Resource Manager (RM) wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "<resourceGroup>" -ServerName $sourceserver -DatabaseName "<databaseName>" `
    -CopyResourceGroupName "myResourceGroup" -CopyServerName $targetserver -CopyDatabaseName "CopyOfMySampleDatabase"
```

Het kopiëren van de database is een asynchrone bewerking, maar de doeldatabase wordt onmiddellijk gemaakt nadat de aanvraag is geaccepteerd. Als u de kopieerbewerking wilt annuleren terwijl deze nog wordt uitgevoerd, verwijdert u de doeldatabase met de cmdlet [Remove-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase)

Zie Een database kopiëren naar een nieuwe server voor een [volledig PowerShell-script.](scripts/copy-database-to-new-server-powershell.md)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az sql db copy --dest-name "CopyOfMySampleDatabase" --dest-resource-group "myResourceGroup" --dest-server $targetserver `
    --name "<databaseName>" --resource-group "<resourceGroup>" --server $sourceserver
```

Het kopiëren van de database is een asynchrone bewerking, maar de doeldatabase wordt onmiddellijk gemaakt nadat de aanvraag is geaccepteerd. Als u de kopieerbewerking wilt annuleren terwijl deze nog wordt uitgevoerd, verwijdert u de doeldatabase met de [opdracht az sql db delete.](/cli/azure/sql/db#az_sql_db_delete)

* * *

## <a name="copy-using-transact-sql"></a>Kopiëren met Transact-SQL

Meld u aan bij de hoofddatabase met de aanmeldgegevens van de serverbeheerder of de aanmelding die de database heeft gemaakt die u wilt kopiëren. Aanmeldingen die niet de serverbeheerder zijn, moeten lid zijn van de rol om het kopiëren van de database te `dbmanager` laten slagen. Zie Aanmeldingen beheren voor meer informatie over aanmeldingen en het maken van verbinding [met de server.](logins-create-manage.md)

Begin met het kopiëren van de brondatabase met [de CREATE DATABASE ... AS COPY](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true#copy-a-database) OF-instructie. De T-SQL-instructie blijft actief totdat de databasekopiebewerking is voltooid.

> [!NOTE]
> Als u de T-SQL-instructie beëindigt, wordt de databasekopiebewerking niet beëindigd. Als u de bewerking wilt beëindigen, zet u de doeldatabase neer.
>

> [!IMPORTANT]
> Redundantie van back-upopslag selecteren bij gebruik van T-SQL CREATE DATABASE... AS COPY OF opdracht wordt nog niet ondersteund. 

### <a name="copy-to-the-same-server"></a>Kopiëren naar dezelfde server

Meld u aan bij de hoofddatabase met de aanmelding van de serverbeheerder of de aanmelding die de database heeft gemaakt die u wilt kopiëren. Aanmeldingen die niet de serverbeheerder zijn, moeten lid zijn van de rol om het kopiëren van de database te `dbmanager` laten slagen.

Met deze opdracht wordt Database1 gekopieerd naar een nieuwe database met de naam Database2 op dezelfde server. Afhankelijk van de grootte van uw database kan het enige tijd duren om de kopieerbewerking te voltooien.

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE Database2 AS COPY OF Database1;
   ```

### <a name="copy-to-an-elastic-pool"></a>Kopiëren naar een elastische pool

Meld u aan bij de hoofddatabase met de aanmelding van de serverbeheerder of de aanmelding die de database heeft gemaakt die u wilt kopiëren. Aanmeldingen die niet de serverbeheerder zijn, moeten lid zijn van de rol om het kopiëren van de database te `dbmanager` laten slagen.

Met deze opdracht wordt Database1 gekopieerd naar een nieuwe database met de naam Database2 in een elastische pool met de naam pool1. Afhankelijk van de grootte van uw database kan het enige tijd duren om de kopieerbewerking te voltooien.

Database1 kan een individuele of pooldatabase zijn. Kopiëren tussen verschillende laaggroepen wordt ondersteund, maar sommige kopieën in meerdere lagen slagen niet. U kunt bijvoorbeeld één of elastische standard db kopiëren naar een groep voor algemeen gebruik, maar u kunt geen standaard elastische database kopiëren naar een Premium-pool. 

   ```sql
   -- Execute on the master database to start copying
   CREATE DATABASE "Database2"
   AS COPY OF "Database1"
   (SERVICE_OBJECTIVE = ELASTIC_POOL( name = "pool1" ) );
   ```

### <a name="copy-to-a-different-server"></a>Kopiëren naar een andere server

Meld u aan bij de hoofddatabase van de doelserver waar de nieuwe database moet worden gemaakt. Gebruik een aanmelding met dezelfde naam en hetzelfde wachtwoord als de database-eigenaar van de brondatabase op de bronserver. De aanmelding op de doelserver moet ook lid zijn van de rol of de `dbmanager` aanmeldingsgegevens van de serverbeheerder zijn.

Met deze opdracht wordt Database1 op server1 gekopieerd naar een nieuwe database met de naam Database2 op server2. Afhankelijk van de grootte van uw database kan het enige tijd duren om de kopieerbewerking te voltooien.

```sql
-- Execute on the master database of the target server (server2) to start copying from Server1 to Server2
CREATE DATABASE Database2 AS COPY OF server1.Database1;
```

> [!IMPORTANT]
> De firewalls van beide servers moeten worden geconfigureerd om binnenkomende verbindingen toe te staan vanaf het IP-adres van de client die de T-SQL CREATE DATABASE ... ALS KOPIE VAN de opdracht.

### <a name="copy-to-a-different-subscription"></a>Kopiëren naar een ander abonnement

U kunt de stappen in de sectie [Een SQL Database](#copy-to-a-different-server) kopiëren naar een andere server gebruiken om uw database te kopiëren naar een server in een ander abonnement met behulp van T-SQL. Zorg ervoor dat u een aanmelding gebruikt met dezelfde naam en hetzelfde wachtwoord als de database-eigenaar van de brondatabase. Daarnaast moet de aanmelding lid zijn van de rol of `dbmanager` een serverbeheerder, op zowel de bron- als de doelserver.

```sql
--Step# 1
--Create login and user in the master database of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx'
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 2
--Create the user in the source database and grant dbowner permission to the database.

CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE db_owner ADD MEMBER loginname;
GO

--Step# 3
--Capture the SID of the user "loginname" from master database

SELECT [sid] FROM sysusers WHERE [name] = 'loginname';

--Step# 4
--Connect to Destination server.
--Create login and user in the master database, same as of the source server.

CREATE LOGIN loginname WITH PASSWORD = 'xxxxxxxxx', SID = [SID of loginname login on source server];
GO
CREATE USER [loginname] FOR LOGIN [loginname] WITH DEFAULT_SCHEMA=[dbo];
GO
ALTER ROLE dbmanager ADD MEMBER loginname;
GO

--Step# 5
--Execute the copy of database script from the destination server using the credentials created

CREATE DATABASE new_database_name
AS COPY OF source_server_name.source_database_name;
```

> [!NOTE]
> De [Azure Portal,](https://portal.azure.com)PowerShell en de Azure CLI bieden geen ondersteuning voor het kopiëren van databases naar een ander abonnement.

> [!TIP]
> Databasekopie met behulp van T-SQL ondersteunt het kopiëren van een database uit een abonnement in een andere Azure-tenant. Dit wordt alleen ondersteund wanneer u een SQL-verificatie-aanmelding gebruikt om u aan te melden bij de doelserver.

## <a name="monitor-the-progress-of-the-copying-operation"></a>De voortgang van de kopieerbewerking controleren

Controleer het kopieerproces door een query uit te voeren [op de weergaven sys.databases,](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) [sys.dm_database_copies](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-copies-azure-sql-database)en [sys.dm_operation_status.](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) Terwijl het kopiëren wordt uitgevoerd, wordt **state_desc** kolom van de weergave sys.databases voor de nieuwe database ingesteld op **KOPIËREN.**

* Als het kopiëren mislukt, wordt **state_desc** kolom van de weergave sys.databases voor de nieuwe database ingesteld op **SUSPECT**. Voer de instructie DROP uit op de nieuwe database en probeer het later opnieuw.
* Als het kopiëren is geslaagd, wordt **state_desc** kolom van de weergave sys.databases voor de nieuwe database ingesteld op **ONLINE.** Het kopiëren is voltooid en de nieuwe database is een reguliere database die onafhankelijk van de brondatabase kan worden gewijzigd.

> [!NOTE]
> Als u besluit het kopiëren te annuleren terwijl het wordt uitgevoerd, voert u de [instructie DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql) uit op de nieuwe database.

> [!IMPORTANT]
> Als u een kopie moet maken met een aanzienlijk kleinere servicedoelstelling dan de bron, heeft de doeldatabase mogelijk niet voldoende resources om het seeding-proces te voltooien en kan de kopieerbewerking mislukken. In dit scenario gebruikt u een aanvraag voor geo-herstel om een kopie te maken in een andere server en/of een andere regio. Zie Recover an Azure SQL Database using database backups (Een [back-up](recovery-using-backups.md#geo-restore) van een database herstellen) voor meer informatie.

## <a name="azure-rbac-roles-and-permissions-to-manage-database-copy"></a>Azure RBAC-rollen en -machtigingen voor het beheren van databasekopie

Als u een databasekopie wilt maken, moet u de volgende rollen hebben

* Abonnementseigenaar of
* SQL Server rol Inzender of
* Aangepaste rol voor de bron- en doeldatabases met de volgende machtiging:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Als u een databasekopie wilt annuleren, moet u de volgende rollen hebben

* Abonnementseigenaar of
* SQL Server rol Inzender of
* Aangepaste rol voor de bron- en doeldatabases met de volgende machtiging:

   Microsoft.Sql/servers/databases/read Microsoft.Sql/servers/databases/write

Als u het kopiëren van de database wilt Azure Portal, hebt u ook de volgende machtigingen nodig:

   Microsoft.Resources/subscriptions/resources/read Microsoft.Resources/subscriptions/resources/write Microsoft.Resources/deployments/read Microsoft.Resources/deployments/write Microsoft.Resources/deployments/operationstatuses/read

Als u de bewerkingen onder implementaties in de resourcegroep op de portal, bewerkingen voor meerdere resourceproviders, inclusief SQL-bewerkingen, wilt zien, hebt u de volgende aanvullende machtigingen nodig:

   Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read

## <a name="resolve-logins"></a>Aanmeldingen oplossen

Nadat de nieuwe database online is op de doelserver, gebruikt u de instructie [ALTER USER](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-current&preserve-view=true) om de gebruikers uit de nieuwe database opnieuw toe te wijsen aan aanmeldingen op de doelserver. Zie Problemen met zwevende gebruikers oplossen als u zwevende [gebruikers wilt oplossen.](/sql/sql-server/failover-clusters/troubleshoot-orphaned-users-sql-server) Zie ook How to manage Azure SQL Database security after disaster recovery (Beveiliging na [noodherstel beheren).](active-geo-replication-security-configure.md)

Alle gebruikers in de nieuwe database behouden de machtigingen die ze in de brondatabase hadden. De gebruiker die de databasekopie heeft geïnitieerd, wordt de database-eigenaar van de nieuwe database. Nadat het kopiëren is geslaagd en voordat andere gebruikers opnieuw worden toegevoegd, kan alleen de database-eigenaar zich aanmelden bij de nieuwe database.

Zie How to manage Azure SQL Database security after disaster recovery (Beveiliging na noodherstel beheren) voor meer informatie over het beheren van gebruikers en aanmeldingen wanneer u een database naar een andere server [kopieert.](active-geo-replication-security-configure.md)

## <a name="database-copy-errors"></a>Fouten bij het kopiëren van databases

De volgende fouten kunnen optreden tijdens het kopiëren van een database in Azure SQL Database. Zie [Een Azure SQL Database kopiëren](database-copy.md) voor meer informatie.

| Foutcode | Ernst | Beschrijving |
| ---:| ---:|:--- |
| 40635 |16 |Client met IP-adres %.&#x2a;ls is tijdelijk uitgeschakeld. |
| 40637 |16 |Databasekopie maken is momenteel uitgeschakeld. |
| 40561 |16 |Het kopiëren van de database is mislukt. De bron- of doeldatabase bestaat niet. |
| 40562 |16 |Het kopiëren van de database is mislukt. De brondatabase is verwijderd. |
| 40563 |16 |Het kopiëren van de database is mislukt. De doeldatabase is verwijderd. |
| 40564 |16 |Het kopiëren van de database is mislukt vanwege een interne fout. Zet de doeldatabase neer en probeer het opnieuw. |
| 40565 |16 |Het kopiëren van de database is mislukt. Er is niet meer dan 1 gelijktijdige databasekopie uit dezelfde bron toegestaan. Zet de doeldatabase neer en probeer het later opnieuw. |
| 40566 |16 |Het kopiëren van de database is mislukt vanwege een interne fout. Zet de doeldatabase neer en probeer het opnieuw. |
| 40567 |16 |Het kopiëren van de database is mislukt vanwege een interne fout. Zet de doeldatabase neer en probeer het opnieuw. |
| 40568 |16 |Het kopiëren van de database is mislukt. De brondatabase is niet meer beschikbaar. Zet de doeldatabase neer en probeer het opnieuw. |
| 40569 |16 |Het kopiëren van de database is mislukt. De doeldatabase is niet meer beschikbaar. Zet de doeldatabase neer en probeer het opnieuw. |
| 40570 |16 |Het kopiëren van de database is mislukt vanwege een interne fout. Zet de doeldatabase neer en probeer het later opnieuw. |
| 40571 |16 |Het kopiëren van de database is mislukt vanwege een interne fout. Zet de doeldatabase neer en probeer het later opnieuw. |

## <a name="next-steps"></a>Volgende stappen

* Zie Aanmeldingen beheren [](logins-create-manage.md) en Beveiliging na noodherstel beheren voor meer informatie [Azure SQL Database aanmeldingen.](active-geo-replication-security-configure.md)
* Zie De database exporteren naar een BACPAC als u een database [wilt exporteren.](database-export.md)
