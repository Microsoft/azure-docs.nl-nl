---
title: SQL Managed Instance-beveiliging met behulp van Azure AD-serverprincipals (aanmeldingen)
description: Informatie over technieken en functies voor het beveiligen van een beheerd exemplaar van Azure SQL en Azure AD-serverprincipals (aanmeldingen) gebruiken
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 11/06/2019
ms.openlocfilehash: 50544507f9d83c216bb6c18e004c5ce7ad1ca346
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639856"
---
# <a name="tutorial-security-in-azure-sql-managed-instance-using-azure-ad-server-principals-logins"></a>Zelfstudie: Beveiliging van Azure SQL Managed Instance met behulp van Azure AD-serverprincipals (aanmeldingen)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance biedt bijna alle beveiligingsfuncties die de meest recente SQL Server (Enterprise Edition) Database Engine ook heeft:

- Toegang beperken in een geïsoleerde omgeving
- Verificatiemechanismen gebruiken waarvoor identiteit vereist is: Azure Active Directory (Azure AD) en SQL-verificatie
- Autorisatie via op rollen gebaseerde lidmaatschappen en machtigingen
- Beveiligingsfuncties inschakelen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> - Een AD-serverprincipal (aanmelding) maken voor een beheerd exemplaar
> - Machtigingen verlenen aan Azure AD-server-principals (aanmeldingen) in een beheerd exemplaar
> - Azure AD-gebruikers maken op basis van Azure AD-server-principals (aanmeldingen)
> - Machtigingen aan Azure AD-gebruikers toewijzen en databasebeveiliging beheren
> - Imitatie gebruiken voor Azure AD-gebruikers
> - Databaseoverschrijdende query’s gebruiken voor Azure AD-gebruikers
> - Leren over beveiligingsfuncties, zoals beveiliging tegen bedreigingen, controle, gegevensmaskering en versleuteling

Zie [Overzicht van Azure SQL Managed Instance](sql-managed-instance-paas-overview.md) voor meer informatie. 

## <a name="prerequisites"></a>Vereisten

Zorg dat u over het volgende beschikt als u de zelfstudie wilt uitvoeren:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Een beheerd exemplaar
  - Volg dit artikel: [Snelstart: Een beheerd exemplaar maken](instance-create-quickstart.md)
- Toegang krijgen tot uw beheerde exemplaar en [het inrichten van een Azure AD-beheerder voor het beheerde exemplaar](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). Voor meer informatie zie:
  - [Uw toepassing verbinding laten maken met een beheerd exemplaar](connect-application-instance.md)
  - [Connectiviteitsarchitectuur van SQL Managed Instance](connectivity-architecture-overview.md)
  - [Verificatie van Azure Active Directory configureren en beheren met SQL](../database/authentication-aad-configure.md)

## <a name="limit-access"></a>Toegang beperken 

Beheerde exemplaren kunnen worden geopend via een privé-IP-adres. Net als bij een geïsoleerde SQL Server-omgeving hebben toepassingen of gebruikers toegang nodig tot het SQL Managed Instance Network (VNet) voordat er een verbinding tot stand kan worden gebracht. Raadpleeg [Uw toepassing verbinden met SQL Managed Instance](connect-application-instance.md) voor meer informatie.

Het is ook mogelijk om een service-eindpunt te configureren op een beheerd exemplaar, waarmee openbare verbindingen kunnen worden ingesteld op dezelfde manier als voor Azure SQL Database.
Raadpleeg [Openbaar eindpunt configureren in Azure SQL Managed Instance](public-endpoint-configure.md) voor meer informatie.

> [!NOTE]
> Ook als service-eindpunten zijn ingeschakeld, zijn [Azure SQL Database-firewallregels](../database/firewall-configure.md) niet van toepassing. Azure SQL Managed Instance heeft een eigen [ingebouwde firewall](management-endpoint-verify-built-in-firewall.md) voor het beheren van de verbinding.

## <a name="create-an-azure-ad-server-principal-login-using-ssms"></a>Een Azure AD-serverprincipal (aanmelding) maken met SSMS

De eerste Azure AD-serverprincipal (aanmelding) kan worden gemaakt door het standaardaccount voor SQL-beheerders (niet-Azure AD) dat een `sysadmin` is, of de Azure AD-beheerder voor het beheerde exemplaar dat tijdens het inrichtingsproces is gemaakt. Raadpleeg [Een Azure Active Directory-beheerder inrichten voor SQL Managed Instance](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) voor meer informatie.

Zie de volgende artikelen voor voorbeelden van het maken van een verbinding met SQL Managed Instance:

- [Snelstart: Azure VM configureren om verbinding te maken met SQL Managed Instance](connect-vm-instance-configure.md)
- [Snelstart: Vanuit on-premises een punt-naar-site-verbinding configureren naar SQL Managed Instance](point-to-site-p2s-configure.md)

1. Meld u aan bij uw beheerde exemplaar met behulp van een standaard SQL-aanmeldingsaccount (niet-Azure AD) dat een `sysadmin` is of een Azure AD-beheerder voor SQL Managed Instance, met behulp van [SQL Server Management Studio](point-to-site-p2s-configure.md#connect-with-ssms).

2. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.

3. Gebruik in het queryvenster de volgende syntaxis om een aanmelding te maken voor een lokaal Azure AD-account:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    In dit voorbeeld maakt u een aanmelding voor het account nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. Selecteer **Uitvoeren** op de werkbalk om de aanmelding te maken.

5. Controleer de zojuist toegevoegde aanmelding door de volgende T-SQL-opdracht uit te voeren:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![Schermopname van het tabblad Resultaten in de S S M S Objectverkenner met de naam, de principal_id, de sid, het type en het type_desc van de zojuist toegevoegde aanmelding.](./media/aad-security-configure-tutorial/native-login.png)

Zie [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) voor meer informatie.

## <a name="grant-permissions-to-create-logins"></a>Machtigingen verlenen om aanmeldingen te maken

Voor het maken van andere Azure AD-server-principals (aanmeldingen), moeten SQL Server-rollen of -machtigingen aan de principal (SQL of Azure AD) worden verleend.

### <a name="sql-authentication"></a>SQL-verificatie

- Als de aanmelding een SQL-principal is, kunnen alleen aanmeldingen die deel uitmaken van de rol `sysadmin` de opdracht create gebruiken om aanmeldingen te maken voor een Azure AD-account.

### <a name="azure-ad-authentication"></a>Azure Active Directory-verificatie

- Om ervoor te zorgen dat de zojuist gemaakte Azure AD-server-principal (aanmelding) andere aanmeldingen voor andere Azure AD-gebruikers, -groepen of -toepassingen kan maken, verleent u de aanmelding de `sysadmin`- of `securityadmin`-serverrol.
- Minimaal moet de machtiging **ALTER ANY LOGIN** worden toegewezen aan de Azure AD-server-principal (aanmelding) om andere Azure AD-server-principals (aanmeldingen) te kunnen maken.
- De machtiging die aan de zojuist gemaakte Azure AD-server-principals (aanmeldingen) in de master wordt verleend, is standaard: **CONNECT SQL** en **VIEW ANY DATABASE**.
- De `sysadmin`-serverrol kan worden verleend aan veel Azure AD-server-principals (aanmeldingen) in een beheerd exemplaar.

Ga als volgt te werk om de aanmelding toe te voegen aan de `sysadmin`-serverrol:

1. Meld u opnieuw aan bij het beheerde exemplaar of gebruik de bestaande verbinding met de Azure AD-beheerder of SQL-principal die een `sysadmin` is.

1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.

1. Verleen de Azure AD-server-principal (aanmelding) de `sysadmin`-serverrol met behulp van de volgende T-SQL-syntaxis:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    In het volgende voorbeeld wordt de `sysadmin`-serverrol verleend aan de aanmelding nativeuser@aadsqlmi.onmicrosoft.com

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Extra Azure AD-server-principals (aanmeldingen) maken met behulp van SSMS

Nadat de Azure AD-server-principal (aanmelding) is gemaakt en is voorzien van `sysadmin`-bevoegdheden, kan die aanmelding extra aanmeldingen maken met behulp van de component **FROM EXTERNAL PROVIDER** met **CREATE LOGIN**.

1. Maak verbinding met het beheerde exemplaar met de Azure AD-server-principal (aanmelding), met behulp van SQL Server Management Studio. Voer de hostnaam van uw SQL Managed Instance in. Voor verificatie in SSMS zijn er drie opties waaruit u kunt kiezen wanneer u zich aanmeldt bij een Azure AD-account:

   - Active Directory - Universal met ondersteuning voor MFA
   - Active Directory - wachtwoord
   - Active Directory - geïntegreerd </br>

     ![Schermopname van het dialoogvenster Verbinding maken met server in S S M S met Active Directory - Universal met MFA-ondersteuning geselecteerd in de vervolgkeuzelijst Verificatie.](./media/aad-security-configure-tutorial/ssms-login-prompt.png)

     Zie [Universele verificatie (SSMS-ondersteuning voor Multi-Factor Authentication)](../database/authentication-mfa-ssms-overview.md) voor meer informatie.

1. Selecteer **Active Directory - Universal met ondersteuning voor MFA**. Hiermee wordt een MFA-aanmeldingsvenster geopend. Meld u aan met uw Azure AD-wachtwoord.

    ![Schermopname van het Multi-Factor Authentication-aanmeldingsvenster met de cursor in het veld Wachtwoord invoeren.](./media/aad-security-configure-tutorial/mfa-login-prompt.png)

1. Klik in SSMS **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.
1. Gebruik in het queryvenster de volgende syntaxis om een aanmelding te maken voor een ander Azure AD-account:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    In dit voorbeeld maakt u een aanmelding voor de Azure AD-gebruiker bob@aadsqlmi.net, wiens domein aadsqlmi.net is gefedereerd met het Azure AD-domein aadsqlmi.onmicrosoft.com.

    Voer de volgende T-SQL-opdracht uit. Gefedereerde Azure AD-accounts zijn de SQL Managed Instance-vervangingen voor on-premises Windows-aanmeldingen en -gebruikers.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Maak een database in het beheerd exemplaar met behulp van de [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current&preserve-view=true)-syntaxis. Deze database wordt gebruikt voor het testen van aanmeldingen in de volgende sectie.
    1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.
    1. Gebruik in het queryvenster de volgende syntaxis om een database te maken met de naam **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Maak een aanmelding voor een SQL Managed Instance voor een groep in Azure AD. De groep moet aanwezig zijn in Azure AD voordat u de aanmelding aan SQL Managed Instance kunt toevoegen. Zie [Een basisgroep maken en leden toevoegen met Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Maak een groep met de naam _mygroup_ en voeg leden toe aan deze groep.

1. Open een nieuw queryvenster in SQL Server Management Studio.

    In dit voorbeeld wordt ervan uitgegaan dat er in Azure AD een groep bestaat met de naam _mygroup_. Voer de volgende opdracht uit:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Als test meldt u zich aan bij het beheerde exemplaar met de zojuist gemaakte aanmelding of groep. Open een nieuwe verbinding naar het beheerde exemplaar en gebruik de nieuwe aanmelding voor verificatie.
1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query** als nieuwe verbinding.
1. Controleer servermachtigingen voor de zojuist gemaakte Azure AD-server-principal (aanmelding) door de volgende opdracht uit te voeren:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Ook gastgebruikers van Azure AD worden ondersteund voor aanmeldingen bij SQL Managed Instance, maar alleen wanneer ze zijn toegevoegd als onderdeel van een Azure AD-groep. Een Azure AD-gastgebruiker is een account dat vanuit een andere Azure AD-instantie wordt uitgenodigd voor de Azure AD-instantie waarvan het beheerde exemplaar deel uitmaakt. joe@contoso.com (Azure AD-account) of steve@outlook.com (Microsoft-account) kunnen bijvoorbeeld worden toegevoegd aan een groep in de Azure AD-instantie aadsqlmi. Zodra de gebruikers aan een groep zijn toegevoegd, kan er een aanmelding worden gemaakt in de **hoofd**-database van de SQL Managed Instance voor de groep met de syntaxis **CREATE LOGIN**. Gastgebruikers die lid zijn van deze groep, kunnen verbinding maken met het beheerde exemplaar met behulp van hun huidige aanmeldingsgegevens (bijvoorbeeld joe@contoso.com of steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login"></a>Een Azure AD-gebruiker maken op basis van de Azure AD-serverprincipal (aanmelding)

Autorisatie voor afzonderlijke databases werkt in SQL Managed Instance nagenoeg hetzelfde als voor databases in SQL Server. Er kan een gebruiker worden gemaakt op basis van een bestaande aanmelding in een database en worden voorzien van machtigingen voor die database, of worden toegevoegd aan een databaserol.

Nu we een database met de naam **MyMITestDB** hebben gemaakt plus een aanmelding met alleen standaardmachtigingen, bestaat de volgende stap uit het maken van een gebruiker op basis van deze aanmelding. Op dit moment kan de aanmelding verbinding maken met het beheerde exemplaar en alle databases zien, maar er kan niet met de databases worden gecommuniceerd. Als u zich aanmeldt met het Azure AD-account waarvoor de standaardmachtigingen gelden en u de zojuist gemaakte database probeert uit te vouwen, krijgt u de volgende fout te zien:

![Schermopname van een foutbericht van de S S M S Objectverkenner: 'De database MyMITestDB is niet toegankelijk. (Objectverkenner)'.](./media/aad-security-configure-tutorial/ssms-db-not-accessible.png)

Zie [Aan de slag met machtigingen voor database-engines](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions) voor meer informatie over het verlenen van databasemachtigingen.

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Een Azure AD-gebruiker en een voorbeeldtabel maken

1. Meld u aan bij uw beheerde exemplaar met behulp van een `sysadmin`-account via SQL Server Management Studio.
1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.
1. Gebruik in het queryvenster de volgende syntaxis om een Azure AD-gebruiker te maken op basis van een Azure AD-server-principal (aanmelding):

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    In het volgende voorbeeld wordt een gebruiker bob@aadsqlmi.net gemaakt op basis van de aanmelding bob@aadsqlmi.net:

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. Er kan ook een Azure AD-gebruiker worden gemaakt op basis van een Azure AD-server-principal (aanmelding) voor een groep.

    In het volgende voorbeeld maakt u een aanmelding voor de Azure AD-groep _mygroup_ die in uw Azure AD-instantie bestaat.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Alle gebruikers die deel uitmaken van *mygroup*, hebben toegang tot de **MyMITestDB**-database.

    > [!IMPORTANT]
    > Bij het maken van een **USER** op basis van een Azure AD-server-principal (aanmelding) is de user_name hetzelfde als de login_name van **LOGIN**.

    Zie [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true) voor meer informatie.

1. Maak in een nieuw queryvenster een testtabel met de volgende T-SQL-opdracht:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Maak een verbinding in SSMS met de gebruiker die is gemaakt. U merkt dat de tabel **TestTable** die eerder door de `sysadmin` werd gemaakt, niet is te zien. De gebruiker heeft machtigingen nodig voor het lezen van gegevens uit de database.

1. U kunt nagaan wat de huidige machtigingen van de gebruiker zijn door de volgende opdracht uit te voeren:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Gebruikers toevoegen aan rollen op databaseniveau

Om ervoor te zorgen dat de gebruiker de gegevens in de database kan zien, geven we [rollen op databaseniveau](/sql/relational-databases/security/authentication-access/database-level-roles) aan de gebruiker.

1. Meld u aan bij uw beheerde exemplaar met behulp van een `sysadmin`-account via SQL Server Management Studio.

1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.

1. Geef de Azure AD-gebruiker de databaserol `db_datareader` met behulp van de volgende T-SQL-syntaxis:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    In het volgende voorbeeld krijgen de gebruiker bob@aadsqlmi.net en de groep _mygroup_`db_datareader` machtigingen voor de **MyMITestDB**-database:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Controleer de Azure AD-gebruiker die in de database werd gemaakt door de volgende opdracht uit te voeren:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Maak een nieuwe verbinding naar het beheerd exemplaar met de gebruiker die is toegevoegd aan de `db_datareader`-rol.
1. Vouw de database in **Objectverkenner** uit om de tabel weer te geven.

    ![Schermopname van de S S M S Objectverkenner met de mapstructuur voor Tabellen in MyMITestDB. De map dbo.TestTable is uitgelicht.](./media/aad-security-configure-tutorial/ssms-test-table.png)

1. Open een nieuw queryvenster en voer de volgende SELECT-instructie uit:

    ```sql
    SELECT *
    FROM TestTable
    ```

    Kunt u gegevens uit de tabel zien? De kolommen zouden moeten worden geretourneerd.

    ![Schermopname van het tabblad Resultaten in de S S M S Objectverkenner die de tabelkolommen AccountNum, Plaats, Naam en Status weergeeft.](./media/aad-security-configure-tutorial/ssms-test-table-query.png)

## <a name="impersonate-azure-ad-server-level-principals-logins"></a>Principals op Azure AD-serverniveau (aanmeldingen) imiteren

SQL Managed Instance biedt ondersteuning voor het imiteren van principals op Azure AD-serverniveau (aanmeldingen).

### <a name="test-impersonation"></a>Imitatie testen

1. Meld u aan bij uw beheerde exemplaar met behulp van een `sysadmin`-account via SQL Server Management Studio.

1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.

1. Voer in het queryvenster de volgende opdracht uit om een opgeslagen procedure te maken:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Gebruik de volgende opdracht om weer te geven dat de gebruiker die u imiteert bij het uitvoeren van de opgeslagen procedure **bob\@aadsqlmi.net** is.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Test de imitatie met behulp van de instructie EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Alleen de principals op SQL-serverniveau (aanmeldingen) die deel uitmaken van de `sysadmin`-rol kunnen de volgende bewerkingen voor Azure AD-principals uitvoeren:
>
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="use-cross-database-queries"></a>Query's tussen databases gebruiken

Query's tussen meerdere databases worden ondersteund voor Azure AD-accounts met Azure AD-server-principals (aanmeldingen). Als u een query tussen meerdere databases met een Azure AD-groep wilt testen, moeten we een andere database en tabel maken. U kunt het maken van een andere database en tabel overslaan als die al bestaan.

1. Meld u aan bij uw beheerde exemplaar met behulp van een `sysadmin`-account via SQL Server Management Studio.
1. Klik in **Objectverkenner** met de rechtermuisknop op de server en kies **Nieuwe query**.
1. Gebruik in het queryvenster de volgende opdracht om een database te maken met de naam **MyMITestDB2** en een tabel met de naam **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. Voer in een nieuw queryvenster de volgende opdracht uit om de gebruiker _mygroup_ te maken in de nieuwe database **MyMITestDB2** en SELECT-machtigingen voor die database te verlenen aan _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Meld u aan bij het beheerde exemplaar via SQL Server Management Studio als een lid van de Azure AD-groep _mygroup_. Open een nieuw queryvenster en voer de volgende SELECT-instructie tussen meerdere databases uit:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    U krijgt dan de tabelresultaten uit **TestTable2** te zien.

## <a name="additional-supported-scenarios"></a>Aanvullende ondersteunde scenario's

- Beheer van SQL Agent en taakuitvoeringen worden ondersteund voor Azure AD-server-principals (aanmeldingen).
- Database-back-up en herstelbewerkingen kunnen worden uitgevoerd door Azure AD-server-principals (aanmeldingen).
- [Controle](auditing-configure.md) van alle instructies met betrekking tot Azure AD-server-principal (aanmelding) en verificatiegebeurtenissen.
- Exclusieve beheerdersverbinding voor Azure AD-server-principals (aanmeldingen) die lid zijn van de `sysadmin`-serverrol.
- Azure AD-server-principals (aanmeldingen) worden ondersteund bij gebruik van het [sqlcmd-hulpprogramma](/sql/tools/sqlcmd-utility) en [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
- Aanmeldingstriggers worden ondersteund voor aanmeldingsgebeurtenissen die afkomstig zijn van Azure AD-server-principal s(aanmeldingen).
- Service Broker en DB-mail kunnen worden ingesteld voor gebruik van Azure AD-server-principals (aanmeldingen).

## <a name="next-steps"></a>Volgende stappen

### <a name="enable-security-features"></a>Beveiligingsfuncties inschakelen

Zie het artikel [Beveiligingsfuncties van SQL Managed Instance](sql-managed-instance-paas-overview.md#security-features) voor een uitgebreide lijst met manieren om uw database te beveiligen. De volgende beveiligingsfuncties worden besproken:

- [SQL Managed Instance-controle](auditing-configure.md)
- [Altijd versleuteld](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detectie van bedreigingen](threat-detection-configure.md)
- [Dynamische gegevensmaskering](/sql/relational-databases/security/dynamic-data-masking)
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security)
- [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>Mogelijkheden van SQL Managed Instance

Een volledig overzicht van de mogelijkheden van SQL Managed Instance is hier te vinden:

> [!div class="nextstepaction"]
> [Mogelijkheden van SQL Managed Instance](sql-managed-instance-paas-overview.md)