---
title: SQL-verificatie
description: Meer informatie over SQL-verificatie in Azure Synapse Analytics.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: efa160eb422658aeeb2eea3ad3c1d305b4b9f8be
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462403"
---
# <a name="sql-authentication"></a>SQL-verificatie

Azure Synapse Analytics heeft twee SQL-formulierfactoren waarmee u het gebruik van resources kunt beheren. In dit artikel wordt uitgelegd hoe gebruikersverificatie wordt beheerd met de twee formulierfactoren.

Om Synapse SQL te beheren, kunt u gebruikmaken van twee verificatietypen:

- Azure Active Directory-verificatie
- SQL-verificatie

Azure Active Directory-verificatie biedt één centrale plaats voor gebruikersbeheer. Met SQL-verificatie kunnen oudere toepassingen Synapse SQL op vertrouwde wijze gebruiken.

## <a name="administrative-accounts"></a>Beheerdersaccounts

Er zijn twee beheerdersaccounts (**serverbeheerder** en **Active Directory-beheerder**) die als beheerder fungeren. Als u deze beheerdersaccounts voor uw SQL-server wilt identificeren, opent u Azure Portal en gaat u naar het tabblad Eigenschappen van uw Synapse SQL.

![SQL Server-beheerders](./media/sql-authentication/sql-admins.png)

- **Serverbeheerder**

  Wanneer u een Azure Synapse Analytics maakt, moet u de **aanmeldgegevens van de serverbeheerder** opgeven. De SQL-server maakt het account vervolgens als een aanmelding in de hoofddatabase. Dit account maakt verbinding met behulp van SQL Server-verificatie (gebruikersnaam en wachtwoord). Er kan slechts één van deze accounts bestaan.

- **Azure Active Directory-beheerder**

  Ook één Azure Active Directory-account (een afzonderlijk account of het account van een beveiligingsgroep) kan als beheerder worden geconfigureerd. Configuratie van een Azure AD-beheerder is optioneel, maar er **moet** een Azure AD-beheerder worden geconfigureerd als u via Azure AD-accounts verbinding wilt maken met Synapse SQL.

De accounts van de **serverbeheerder** en de **Azure AD-beheerder** hebben de volgende kenmerken:

- Dit zijn de enige accounts die automatisch verbinding kunnen maken met elke SQL-database op de server. (Andere accounts die verbinding willen maken met een gebruikersdatabase, moeten eigenaar van de database zijn of een gebruikersaccount in de database hebben.)
- Deze accounts worden in gebruikersdatabases beschouwd als de `dbo`-gebruiker en beschikken over alle machtigingen. (De eigenaar van een gebruikersdatabase wordt in de database ook als `dbo`-gebruiker beschouwd.)
- Deze accounts moeten in de `master`-database niet als `dbo`-gebruiker worden beschouwd en ze hebben beperkte machtigingen in de hoofddatabase.
- Deze accounts zijn **geen** lid van de vaste standaardserverrol `sysadmin` van SQL Server, die niet beschikbaar is in SQL Database.  
- Deze accounts kunnen databases, aanmeldingen en gebruikers in de hoofddatabase, en IP-firewallregels op serverniveau maken, wijzigen en verwijderen.
- Deze accounts kunnen leden aan de rollen `dbmanager` en `loginmanager` toevoegen en verwijderen.
- Deze accounts kunnen de `sys.sql_logins`-systeemtabel weergeven.

## <a name="serverless-sql-pool"></a>[Serverloze SQL-pool](#tab/serverless)

U kunt de volgende instructies gebruiken om de gebruikers te beheren die toegang hebben tot serverloze SQL-pools.

Als u een aanmelding voor een serverloze SQL-pool wilt maken, gebruikt u de volgende syntaxis:

```sql
CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
-- or
CREATE LOGIN Mary@domainname.net FROM EXTERNAL PROVIDER;
```
Zodra de aanmelding bestaat, kunt u gebruikers maken in de afzonderlijke databases binnen het eindpunt voor de serverloze SQL-pool, en de vereiste machtigingen verlenen aan deze gebruikers. Als u een gebruiker wilt maken, kunt u de volgende syntaxis gebruiken:
```sql
CREATE USER Mary FROM LOGIN Mary;
-- or
CREATE USER Mary FROM LOGIN Mary@domainname.net;
-- or
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

Zodra de aanmelding en de gebruiker zijn gemaakt, kunt u de reguliere syntaxis voor SQL Server gebruiken om rechten te verlenen.

## <a name="sql-pool"></a>[SQL-pool](#tab/provisioned)

### <a name="administrator-access-path"></a>Toegangspad beheerder

Als de firewall op serverniveau correct is geconfigureerd, kunnen de **SQL-serverbeheerder** en de **Azure Active Directory-beheerder** verbinding maken met clienthulpprogramma's zoals SQL Server Management Studio en SQL Server Data Tools. Alleen de nieuwste hulpprogramma's bieden alle functies en mogelijkheden. 

Het volgende diagram toont een standaardconfiguratie voor de twee beheerdersaccounts:
 
![configuratie van de twee beheerdersaccounts](./media/sql-authentication/1sql-db-administrator-access.png)

Wanneer u een open poort in de firewall op serverniveau gebruikt, kunnen beheerders verbinding maken met elke SQL-database.

### <a name="database-creators"></a>Databasemakers

Een van deze beheerdersrollen is de rol **dbmanager**. Leden van deze rol kunnen nieuwe databases maken. Voor het gebruik van deze rol maakt u een gebruiker in de `master`-database en voegt u deze gebruiker vervolgens toe aan de databaserol **dbmanager**. 

Om een database te maken, moet de gebruiker een gebruiker zijn op basis van een SQL Server-aanmelding in de `master`-database of een gebruiker van een ingesloten database op basis van een Azure Active Directory-gebruiker.

1. Gebruik een beheerdersaccount om verbinding te maken met de `master`-database.
2. Maak een aanmelding voor SQL Server-verificatie met de instructie [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Voorbeeldinstructie:

   ```sql
   CREATE LOGIN Mary WITH PASSWORD = '<strong_password>';
   ```

   > [!NOTE]
   > Gebruik een sterk wachtwoord bij het maken van aanmeldgegevens of een ingesloten databasegebruiker. Zie [Sterke wachtwoorden](/sql/relational-databases/security/strong-passwords?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie.

   Voor betere prestaties worden aanmeldingen (principals op serverniveau) tijdelijk in het cachegeheugen op databaseniveau opgeslagen. Zie [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) als u de verificatiecache wilt vernieuwen.

3. Maak een databasegebruiker met behulp van de instructie [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). De gebruiker kan een ingesloten databasegebruiker op basis van Azure Active Directory-verificatie zijn (als u uw omgeving hebt geconfigureerd voor Azure AD-verificatie), maar ook een ingesloten databasegebruiker op basis van SQL Server-verificatie of een gebruiker op basis van SQL Server-verificatie met aanmelding voor SQL Server-verificatie (gemaakt in de vorige stap). Voorbeeldinstructies:

   ```sql
   CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER; -- To create a user with Azure Active Directory
   CREATE USER Ann WITH PASSWORD = '<strong_password>'; -- To create a SQL Database contained database user
   CREATE USER Mary FROM LOGIN Mary;  -- To create a SQL Server user based on a SQL Server authentication login
   ```

4. Voeg de nieuwe gebruiker toe aan de databaserol **dbmanager** in `master` met behulp van de procedure [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=azure-sqldw-latest) (de instructie [ALTER ROLE](/sql/t-sql/statements/alter-role-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) wordt niet ondersteund in ingerichte SQL). Voorbeeldinstructies:

   ```sql
   EXEC sp_addrolemember 'dbmanager', 'Mary'; 
   EXEC sp_addrolemember 'dbmanager', 'mike@contoso.com]'; 
   ```

   > [!NOTE]
   > Dbmanager is een databaserol in de hoofddatabase, zodat u alleen een databasegebruiker aan de rol dbmanager kunt toevoegen. U kunt geen aanmelding op serverniveau toevoegen aan een rol op databaseniveau .

5. U kunt zo nodig een firewallregel configureren, zodat de nieuwe gebruiker verbinding kan maken. (De nieuwe gebruiker kan worden gedekt door een bestaande firewallregel.)

Nu kan de gebruiker verbinding maken met de `master`-database en nieuwe databases maken. Het account dat de database maakt, wordt eigenaar van de database.

### <a name="login-managers"></a>Aanmelding managers

De andere beheerdersrol is de rol voor aanmeldingsbeheerder. Leden van deze rol kunnen nieuwe aanmeldingen maken in de hoofddatabase. Desgewenst kunt u dezelfde stappen doorlopen (een aanmelding maken, een gebruiker maken en een gebruiker toevoegen aan de rol **loginmanager**), zodat een gebruiker nieuwe aanmeldingen kan maken in de hoofddatabase. Gewoonlijk zijn aanmeldingen niet nodig omdat Microsoft het gebruik aanbeveelt van gebruikers van ingesloten databases. Hiervoor wordt verificatie op databaseniveau gebruikt, in plaats van gebruik te maken van gebruikers op basis van aanmelding. Zie [Ingesloten databasegebruikers: een draagbare database maken](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie.

---

## <a name="non-administrator-users"></a>Niet-beheerders

Niet-beheerdersaccounts hebben doorgaans geen toegang nodig tot de hoofddatabase. Maak ingesloten databasegebruikers op databaseniveau met de instructie [CREATE USER (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx). 

De gebruiker kan een ingesloten databasegebruiker op basis van Azure Active Directory-verificatie zijn (als u uw omgeving hebt geconfigureerd voor Azure AD-verificatie), maar ook een ingesloten databasegebruiker op basis van SQL Server-verificatie of een gebruiker op basis van SQL Server-verificatie met aanmelding voor SQL Server-verificatie (gemaakt in de vorige stap).  

Als u gebruikers wilt maken, maakt u verbinding met de database en voert u de instructies uit die in de volgende voorbeelden worden getoond:

```sql
CREATE USER Mary FROM LOGIN Mary;
CREATE USER [mike@contoso.com] FROM EXTERNAL PROVIDER;
```

In eerste instantie kan slechts een van de beheerders of de eigenaar van de database gebruikers maken. Om extra gebruikers te machtigen voor het maken van nieuwe gebruikers, verleent u de geselecteerde gebruikers de `ALTER ANY USER`-toestemming met behulp van een instructie zoals:

```sql
GRANT ALTER ANY USER TO Mary;
```

Om extra gebruikers volledig beheer van de database te geven, moet u ze lid maken van de vaste databaserol **db_owner**.

Gebruik in Azure SQL Database of Synapse zonder server de instructie `ALTER ROLE`.

```sql
ALTER ROLE db_owner ADD MEMBER Mary;
```

Gebruik [EXEC sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) in de toegewezen SQL-pool.

```sql
EXEC sp_addrolemember 'db_owner', 'Mary';
```

> [!NOTE]
> Een veelvoorkomende reden voor het maken van een databasegebruiker op basis van een serveraanmelding is voor gebruikers die toegang nodig hebben tot meerdere databases. Aangezien ingesloten databasegebruikers individuele entiteiten zijn, heeft elke database zijn eigen gebruiker en eigen wachtwoord. Dit kan overhead veroorzaken, omdat de gebruiker zijn eigen wachtwoord voor elke database moet onthouden en dit erg ingewikkeld kan zijn als ze meerdere wachtwoorden in vele databases moeten wijzigen. Wanneer u echter gebruikmaakt van SQL Server-aanmeldingen en hoge beschikbaarheid (actieve geo-replicatie en failover-groepen), moeten de SQL Server-aanmeldingen handmatig worden ingesteld op elke server. Anders wordt de databasegebruiker niet meer toegewezen aan de serveraanmelding nadat een failover is uitgevoerd en heeft de gebruiker geen toegang meer tot de database na failover. 

Zie [De Azure SQL Database-beveiliging configureren en beheren voor geografisch herstel en failovers](../../azure-sql/database/active-geo-replication-security-configure.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie over de configuratie van aanmeldingen voor geo-replicatie.

### <a name="configuring-the-database-level-firewall"></a>De firewall op databaseniveau configureren

U doet er verstandig aan niet-beheerders alleen via de firewall toegang te verlenen tot de databases die ze gebruiken. In plaats van het machtigen van hun IP-adressen via de firewall op serverniveau en hun toegang te verlenen tot alle databases, kunt u de instructie [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om de firewall op databaseniveau te configureren. De firewall op databaseniveau kan niet worden geconfigureerd met behulp van de portal.

### <a name="non-administrator-access-path"></a>Toegangspad niet-beheerder

Wanneer de firewall op databaseniveau correct is geconfigureerd, kunnen databasegebruikers verbinding maken met de hulp van clienthulpprogramma's zoals SQL Server Management Studio of SQL Server Data Tools. Alleen de nieuwste hulpprogramma's bieden alle functies en mogelijkheden. Het volgende diagram toont een standaardtoegangspad voor niet-beheerders.

![Toegangspad niet-beheerder](./media/sql-authentication/2sql-db-nonadmin-access.png)

## <a name="groups-and-roles"></a>Groepen en rollen

Voor efficiënt toegangsbeheer gebruikt u machtigingen die zijn toegewezen aan groepen en rollen in plaats van aan individuele gebruikers.

- Als u Azure Active Directory-verificatie gebruikt, plaatst u Azure Active Directory-gebruikers in een Azure Active Directory-groep. Maak voor de groep een ingesloten databasegebruiker. Plaats een of meer databasegebruikers in een [databaserol](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en wijs vervolgens [machtigingen](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) toe aan de databaserol.

- Als u SQL Server-verificatie gebruikt, maakt u gebruikers van ingesloten databases in de database. Plaats een of meer databasegebruikers in een [databaserol](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) en wijs vervolgens [machtigingen](/sql/relational-databases/security/permissions-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) toe aan de databaserol.

Bij de databaserollen kan het gaan om de ingebouwde rollen als **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** en **db_denydatareader**. **db_owner** wordt doorgaans gebruikt voor het verlenen van volledige machtigingen aan slechts enkele gebruikers. De andere vaste databaserollen zijn handig voor het snel verkrijgen van een eenvoudige database voor ontwikkeldoeleinden, maar worden niet aanbevolen voor de meeste productiedatabases. 

De vaste databaserol **db_datareader** verleent bijvoorbeeld leestoegang tot alle tabellen in de database, wat doorgaans meer is dan strikt noodzakelijk. 

Het is veel beter de instructie [CREATE ROLE](https://msdn.microsoft.com/library/ms187936.aspx) te gebruiken om uw eigen gebruikergedefinieerde databaserollen te maken en zorgvuldig elke rol de minimale machtigingen te verlenen die nodig zijn voor de gerelateerde zakelijke behoeften. Als een gebruiker lid is van meerdere rollen, worden de machtigingen van alle rollen samengevoegd.

## <a name="permissions"></a>Machtigingen

Er zijn meer dan 100 machtigingen die afzonderlijk kunnen worden verleend of geweigerd in SQL Database. Veel van deze machtigingen zijn genest. De machtiging `UPDATE` voor een schema bevat bijvoorbeeld de machtiging `UPDATE` voor elke tabel binnen dat schema. Net als bij de meeste machtigingssystemen gaat de weigering van een machtiging vóór toestemming. 

Vanwege de geneste aard en het aantal machtigingen kan een nauwkeurig onderzoek nodig zijn om een geschikt machtigingssysteem te ontwerpen voor een goede bescherming van uw database. 

Start met de lijst van machtigingen in [Machtigingen (Database-engine)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) en controleer de [afbeelding op postergrootte](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) van de machtigingen.

### <a name="considerations-and-restrictions"></a>Overwegingen en beperkingen

Bij het beheren van aanmeldingen en gebruikers in SQL Database, moet u de volgende zaken overwegen:

- U moet zijn verbonden met de **hoofd** database bij het uitvoeren van de `CREATE/ALTER/DROP DATABASE`-instructies.
- De databasegebruiker die overeenkomt met de aanmelding van de **Serverbeheerder**, kan niet worden gewijzigd of verwijderd.
- Amerikaans Engels is de standaardtaal van de aanmelding van de **serverbeheerder**.
- Alleen de beheerders (aanmelding van **serverbeheerder** of Azure AD-beheerder) en de leden van de databaserol **dbmanager** in de **hoofddatabase** zijn gemachtigd om de instructies `CREATE DATABASE` en `DROP DATABASE` uit te voeren.
- U moet zijn verbonden met de hoofddatabase bij het uitvoeren van de `CREATE/ALTER/DROP LOGIN`-instructies. Het gebruik van aanmeldingen wordt echter afgeraden. Gebruik in plaats daarvan ingesloten databasegebruikers.
- Om verbinding te maken met een gebruikersdatabase, moet u de naam van de database in de verbindingsreeks opgeven.
- Alleen de principal-aanmelding op serverniveau en de leden van de databaserol **loginmanager** in de **hoofd** database zijn gemachtigd om `CREATE LOGIN`-, `ALTER LOGIN`- en `DROP LOGIN`-instructies uit te voeren.
- Bij het uitvoeren van de `CREATE/ALTER/DROP LOGIN`- en `CREATE/ALTER/DROP DATABASE`-instructies in een ADO.NET-toepassing is het gebruik van geparametriseerde opdrachten is niet toegestaan. Zie [Opdrachten en parameters](/dotnet/framework/data/adonet/commands-and-parameters?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie.
- Bij het uitvoeren van de `CREATE/ALTER/DROP DATABASE`- en `CREATE/ALTER/DROP LOGIN`-instructies moet elk van deze instructies de enige instructie in een Transact-SQL-batch zijn. Als deze niet overeenkomen, treedt er een fout op. De volgende Transact-SQL controleert bijvoorbeeld of de database bestaat. Als deze bestaat, wordt een `DROP DATABASE`-instructie aangeroepen om de database te verwijderen. Omdat de `DROP DATABASE`-instructie niet de enige instructie in de batch is, leidt het uitvoeren van de volgende Transact-SQL-instructie tot een fout.

  ```sql
  IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
  DROP DATABASE [database_name];
  GO
  ```
  
  Gebruik in plaats daarvan de volgende Transact-SQL-instructie:
  
  ```sql
  DROP DATABASE IF EXISTS [database_name]
  ```

- Bij het uitvoeren van de `CREATE USER`-instructie met de optie `FOR/FROM LOGIN` moet deze de enige instructie in een Transact-SQL-batch zijn.
- Bij het uitvoeren van de `ALTER USER`-instructie met de optie `WITH LOGIN` moet deze de enige instructie in een Transact-SQL-batch zijn.
- Voor `CREATE/ALTER/DROP` heeft een gebruiker de `ALTER ANY USER`-machtiging voor de database nodig.
- Wanneer de eigenaar van een databaserol probeert om een andere databasegebruiker toe te voegen aan of te verwijderen uit die databaserol, kan de volgende fout optreden: **Gebruiker of rol Naam bestaat niet in deze database.** Deze fout treedt op omdat de gebruiker niet zichtbaar is voor de eigenaar. Om dit probleem op te lossen, verleent u de roleigenaar de `VIEW DEFINITION`-machtiging voor de gebruiker. 

## <a name="next-steps"></a>Volgende stappen

Zie [Ingesloten databasegebruikers: een draagbare database maken](https://msdn.microsoft.com/library/ff929188.aspx) voor meer informatie.
 
