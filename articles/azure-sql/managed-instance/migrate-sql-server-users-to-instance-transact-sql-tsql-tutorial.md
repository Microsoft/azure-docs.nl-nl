---
title: SQL Server Windows-gebruikers en -groepen naar een beheerd SQL-exemplaar migreren met T-SQL
description: Meer informatie over het migreren van Windows-gebruikers en -groepen in een SQL Server-exemplaar naar een beheerd Azure SQL-exemplaar
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: f2dd34ab7c6ee5be26836e4abb86960605ee44ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84708650"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>Zelfstudie: Windows-gebruikers en -groepen in een SQL Server-exemplaar migreren naar een beheerd Azure SQL-exemplaar met de T-SQL DDL-syntaxis
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> De syntaxis die in dit artikel wordt gebruikt voor het migreren van gebruikers en groepen naar beheerde SQL-exemplaren is een **openbare preview**-versie.

In dit artikel vindt u meer informatie over het proces voor het migreren van uw on-premises Windows-gebruikers en -groepen van uw SQL-server naar een beheerd Azure SQL-exemplaar met de T-SQL-syntaxis.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Aanmeldingen voor SQL-server maken
> - Een testdatabase maken voor migratie
> - Aanmeldingen, gebruikers en rollen maken
> - Back-up en herstel van uw database naar een beheerd SQL-exemplaar
> - Gebruikers handmatig migreren naar een beheerd exemplaar met de ALTER USER-syntax
> - Verificatie testen met de nieuwe toegewezen gebruikers

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie gelden de volgende vereisten:

- Het Windows-domein is federatief met Azure Active Directory (Azure AD).
- Toegang tot Active Directory om gebruikers/groepen te maken.
- Een bestaande SQL-server in uw on-premises omgeving.
- Een bestaand beheerd SQL-exemplaar. Zie [Quickstart: Een beheerd SQL-exemplaar maken](instance-create-quickstart.md).
  - Er moet een `sysadmin` in het beheerde SQL-exemplaar worden gebruikt voor het maken van Azure AD-aanmeldingen.
- [Maak een Azure AD-beheerder voor een beheerd SQL-exemplaar](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).
- U kunt in uw netwerk verbinding maken met uw beheerde SQL-exemplaar. Zie de volgende artikelen voor aanvullende informatie:
  - [Uw toepassing verbinden met het beheerde Azure SQL-exemplaar](connect-application-instance.md)
  - [Snelstart: Een punt-naar-site-verbinding configureren naar een beheerd Azure SQL-exemplaar vanaf on-premises](point-to-site-p2s-configure.md)
  - [Openbaar eindpunt configureren in een beheerd Azure SQL-exemplaar](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>T-SQL DDL-syntaxis

Hieronder vindt u de T-SQL DDL-syntaxis die wordt gebruikt om de migratie van Windows-gebruikers en -groepen van een SQL Server-exemplaar naar een beheerd SQL-exemplaar met Azure AD-verificatie te ondersteunen.

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>Argumenten

_domainName_</br>
Hiermee geeft u de domeinnaam van de gebruiker op.

_userName_</br>
Hiermee geeft u de naam van de gebruiker op die in de database is geïdentificeerd.

_= loginName\@domainName.com_</br>
Hiermee wordt een gebruiker opnieuw toegewezen aan de Azure AD-aanmelding

_groupName_</br>
Hiermee geeft u de naam van de groep op die in de database is geïdentificeerd.

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>Deel 1: Aanmeldingen maken in SQL Server voor Windows-gebruikers en -groepen

> [!IMPORTANT]
> Met de volgende syntaxis maakt u een gebruikers- en een groepsaanmelding in SQL Server U moet ervoor zorgen dat de gebruiker en groep in uw Active Directory (AD) bestaan voordat u de onderstaande syntaxis uitvoert. </br> </br>
> Gebruikers: testUser1, testGroupUser </br>
> Groep: migratie - testGroupUser moet deel uitmaken van de migratiegroep in AD

In het onderstaande voorbeeld wordt een aanmelding in SQL Server gemaakt voor een account met de naam _testUser1_ onder het domein _aadsqlmi_.

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases

use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;
go;

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser].
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;
go;


-- Check logins were created
select * from sys.server_principals;
go;
```

Maak een database voor deze test.

```sql
-- Create a database called [migration]
create database migration
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>Deel 2: Windows-gebruikers en -groepen maken en vervolgens rollen en machtigingen toevoegen

Gebruik de volgende syntaxis om de testgebruiker te maken.

```sql
use migration;  
go

-- Create Windows user [aadsqlmi\testUser1] with login
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1];
go
```

Controleer de gebruikersmachtigingen:

```sql
-- Check the user in the Metadata
select * from sys.database_principals;
go

-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions;
go
```

Maak een rol en wijs uw testgebruiker aan deze rol toe:

```sql
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole;
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole;
go

alter role UserMigrationRole add member [aadsqlmi\testUser1];
go
```

Gebruik de volgende query om gebruikersnamen weer te geven die zijn toegewezen aan een specifieke rol:

```sql
-- Display user name assigned to a specific role
SELECT DP1.name AS DatabaseRoleName,
   isnull (DP2.name, 'No members') AS DatabaseUserName
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name;
```

Gebruik de volgende syntaxis om een groep te maken. Voeg de groep vervolgens toe aan de rol `db_owner`.

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration];
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration';
go

--Check the db_owner role for 'aadsqlmi\migration' group
select is_rolemember('db_owner', 'aadsqlmi\migration')
go
-- Output  ( 1 means YES)
```

Maak een testtabel en voeg gegevens toe met behulp van de volgende syntaxis:

```sql
-- Create a table and add data
create table test ( a int, b int);
go

insert into test values (1,10)
go

-- Check the table values
select * from test;
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>Deel 3: Back-up maken en herstellen van de afzonderlijke gebruikersdatabase naar een beheerd SQL-exemplaar

Maak een back-up van de migratiedatabase met behulp van het artikel [Databases kopiëren met back-up en herstel](/sql/relational-databases/databases/copy-databases-with-backup-and-restore), of gebruik de volgende syntaxis:

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

Volg onze [quickstart: Een database herstellen naar een beheerd SQL-exemplaar](restore-sample-database-quickstart.md).

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>Deel 4: Gebruikers migreren naar een beheerd SQL-exemplaar

Voer de opdracht ALTER USER uit om het migratieproces op het beheerde SQL-exemplaar te voltooien.

1. Meld u aan bij uw beheerde SQL-exemplaar met het Azure AD-beheerdersaccount voor beheerde SQL-exemplaren. Maak vervolgens uw Azure AD-aanmelding in het beheerde SQL-exemplaar met behulp van de volgende syntaxis. Zie [Zelfstudie: Beveiliging van een beheerd exemplaar in Azure SQL Database met behulp van Azure AD-server-principals (aanmeldingen)](aad-security-configure-tutorial.md).

    ```sql
    use master
    go

    -- Create login for AAD user [testUser1@aadsqlmi.net]
    create login [testUser1@aadsqlmi.net] from external provider
    go

    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net]
    create login [migration] from external provider
    go

    --Check the two new logins
    select * from sys.server_principals
    go
    ```

1. Controleer of de migratie de juiste database, tabel en principals heeft.

    ```sql
    -- Switch to the database migration that is already restored for MI
    use migration;
    go

    --Check if the restored table test exist and contain a row
    select * from test;
    go

    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals;
    go
    -- the old user aadsqlmi\testUser1 should be there
    -- the old group aadsqlmi\migration should be there
    ```

1. Gebruik de ALTER USER-syntaxis om de on-premises gebruiker toe te wijzen aan de Azure AD-aanmelding.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net];
    go

    -- Check the principal
    select * from sys.database_principals;
    go
    -- New user testUser1@aadsqlmi.net should be there instead
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    -- Check a specific role
    -- Display Db user name assigned to a specific role
    SELECT DP1.name AS DatabaseRoleName,
    isnull (DP2.name, 'No members') AS DatabaseUserName
    FROM sys.database_role_members AS DRM
    RIGHT OUTER JOIN sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
    LEFT OUTER JOIN sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
    WHERE DP1.type = 'R'
    ORDER BY DP1.name;
    ```

1. Gebruik de ALTER USER-syntaxis om de on-premises groep toe te wijzen aan de Azure AD-aanmelding.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration];
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals;
    go

    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    --Check the db_owner role for 'aadsqlmi\migration' user
    select is_rolemember('db_owner', 'migration')
    go
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>Deel 5: Azure AD-gebruikers- of -groepsverificatie testen

Test de verificatie bij het beheerde SQL-exemplaar met behulp van de gebruiker die eerder is toegewezen aan de Azure AD-aanmelding met de ALTER USER-syntaxis.

1. Meld u aan bij de federatieve VM met uw abonnement voor beheerde exemplaren van Azure SQL als `aadsqlmi\testUser1`
1. Gebruik SQL Server Management Studio (SSMS) om u aan te melden bij uw beheerde SQL-exemplaar met geïntegreerde **Active Directory**-verificatie en maak verbinding met de database`migration`.
    1. U kunt zich ook aanmelden met de testUser1@aadsqlmi.net aanmeldingsgegevens met de SSMS-optie **Active Directory - Universeel met MFA-ondersteuning**. In dit geval kunt u het mechanisme voor eenmalige aanmelding echter niet gebruiken en moet u een wachtwoord invoeren. U hoeft geen federatieve VM te gebruiken om u aan te melden bij uw beheerde SQL-exemplaar.
1. Als onderdeel van het rollid **SELECTEREN**, kunt u uit de tabel `test` kiezen

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

Test de verificatie bij een beheerd SQL-exemplaar met behulp van een lid van een Windows-groep `migration`. De gebruiker `aadsqlmi\testGroupUser` moet zijn toegevoegd aan de groep `migration` vóór de migratie.

1. Meld u aan bij de federatieve VM met uw abonnement voor beheerde exemplaren van Azure SQLals `aadsqlmi\testGroupUser`
1. Gebruik SSMS met geïntegreerde **Active Directory**-verificatie om verbinding maken met de server van het beheerde Azure SQL-exemplaar en de database `migration`
    1. U kunt zich ook aanmelden met de testGroupUser@aadsqlmi.net aanmeldingsgegevens met de SSMS-optie **Active Directory - Universeel met MFA-ondersteuning**. In dit geval kunt u het mechanisme voor eenmalige aanmelding echter niet gebruiken en moet u een wachtwoord invoeren. U hoeft geen federatieve VM te gebruiken om u aan te melden bij uw beheerde SQL-exemplaar.
1. Als onderdeel van de `db_owner`-rol kunt u een nieuwe tabel maken.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> Als gevolg van een bekend ontwerpprobleem voor Azure SQL Database, mislukt het maken van een tabelinstructie die wordt uitgevoerd als lid van een groep met de volgende fout: </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> De huidige tijdelijke oplossing is het maken van een tabel met een bestaand schema in bovenstaand geval <dbo.new>

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: SQL Server migreren naar een offline, beheerd Azure SQL-exemplaar met DMS](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
