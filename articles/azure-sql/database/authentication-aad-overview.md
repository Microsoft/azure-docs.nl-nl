---
title: Verificatie via Azure Active Directory
description: Meer informatie over het gebruik van Azure Active Directory voor verificatie met Azure SQL Database, Azure SQL Managed instance en Synapse SQL in azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 04/23/2020
ms.openlocfilehash: a636c0e2a41b636f30ada14d4f16a022f2890b71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454300"
---
# <a name="use-azure-active-directory-authentication"></a>Azure Active Directory-verificatie gebruiken

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure Active Directory-verificatie (Azure AD) is een mechanisme voor het maken van verbinding met [Azure SQL database](sql-database-paas-overview.md), [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md)en [Synapse SQL in azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) met behulp van identiteiten in azure AD.

> [!NOTE]
> Dit artikel is van toepassing op Azure SQL Database, SQL Managed instance en Azure Synapse Analytics.

Met Azure AD-verificatie kunt u de identiteit van databasegebruikers en andere Microsoft-services op één centrale locatie beheren. Centraal identiteitsbeheer biedt één plek voor het beheren van databasegebruikers en vereenvoudigt het machtigingenbeheer. Dit biedt verschillende voordelen, zoals:

- Het biedt een alternatief voor SQL Server-verificatie.
- Het helpt bij het stoppen van de verspreiding van gebruikers identiteiten op meerdere servers.
- Hiermee kan het draaien van wacht woorden op één plek plaatsvinden.
- Klanten kunnen database machtigingen beheren met externe groepen (Azure AD).
- Wachtwoorden hoeven niet meer te worden opgeslagen door het inschakelen van geïntegreerde Windows-verificatie en andere vormen van authenticatie die worden ondersteund door Azure Active Directory.
- Voor Azure AD-verificatie worden de Inge sloten database gebruikers gebruikt voor het verifiëren van identiteiten op database niveau.
- Azure AD biedt ondersteuning voor verificatie op basis van tokens voor toepassingen die verbinding maken met SQL Database en SQL Managed instance.
- Azure AD-verificatie ondersteunt:
  - Alleen Cloud-id's van Azure AD.
  - Hybride Azure AD-identiteiten die ondersteuning bieden voor:
    - Cloud verificatie met twee opties in combi natie met naadloze eenmalige aanmelding (SSO) **Pass-Through-** verificatie en verificatie van **wacht woord-hashes** .
    - Federatieve authenticatie.
  - Zie het volgende artikel voor meer informatie over Azure AD-verificatie methoden en welke u er één kunt kiezen:
    - [Kies de juiste verificatie methode voor uw Azure Active Directory hybride identiteits oplossing](../../active-directory/hybrid/choose-ad-authn.md)

- Azure AD biedt ondersteuning voor verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory universele verificatie, waaronder Multi-Factor Authentication. Multi-Factor Authentication bevat sterke verificatie met een scala aan eenvoudige verificatie opties: telefoon oproep, SMS-bericht, Smart Cards met pincode of mobiele app-melding. Zie voor meer informatie [SSMS-ondersteuning voor Azure AD-multi-factor Authentication met Azure SQL database, SQL Managed instance en Azure Synapse](authentication-mfa-ssms-overview.md)

- Azure AD biedt ondersteuning voor vergelijkbare verbindingen van SQL Server Data Tools (SSDT) die gebruikmaken van Active Directory Interactive Authentication. Zie [Azure Active Directory-ondersteuning in SQL Server Data tools (SSDT)](/sql/ssdt/azure-active-directory) voor meer informatie.

> [!NOTE]  
> Verbinding maken met een SQL-serverexemplaar dat wordt uitgevoerd op een Azure VM (virtual machine) wordt niet ondersteund bij gebruik van een Azure Active Directory-account. Gebruik in plaats hiervan een Active Directory-domeinaccount.  

De configuratiestappen omvatten de volgende procedures voor het configureren en gebruiken van Azure Active Directory-verificatie.

1. Maak en vul Azure AD.
2. Optioneel: koppel of wijzig de Active Directory die momenteel aan uw Azure-abonnement is gekoppeld.
3. Maak een Azure Active Directory-beheerder.
4. Configureer uw client computers.
5. Maak Inge sloten database gebruikers in uw data base die zijn toegewezen aan Azure AD-identiteiten.
6. Maak verbinding met uw data base met behulp van Azure AD-identiteiten.

> [!NOTE]
> Zie [Azure AD met Azure SQL database configureren](authentication-aad-configure.md)voor meer informatie over het maken en vullen van Azure AD en het configureren van Azure ad met Azure SQL database, SQL Managed instance en Synapse SQL in azure Synapse Analytics.

## <a name="trust-architecture"></a>Architectuur van vertrouwensrelatie

- Alleen het Cloud gedeelte van Azure AD, SQL Database, SQL Managed instance en Azure Synapse worden beschouwd als ondersteuning voor native gebruikers wachtwoorden van Azure AD.
- Als u referenties voor eenmalige aanmelding (of gebruiker/wacht woord voor Windows-referenties) voor Windows wilt ondersteunen, gebruikt u Azure Active Directory referenties van een federatief of beheerd domein dat is geconfigureerd voor naadloze eenmalige aanmelding voor Pass-Through-en wacht woord-hash-verificatie. Zie [Azure Active Directory naadloze eenmalige aanmelding](../../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.
- Voor de ondersteuning van federatieve aanmelding (of gebruiker/wachtwoord voor Windows-referenties), is de communicatie met het ADFS-blok vereist.

Raadpleeg de volgende artikelen voor meer informatie over hybride Azure AD-identiteiten, de instellingen en synchronisatie:

- Wachtwoord verificatie voor wacht woord- [hash-synchronisatie implementeren met Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Pass-Through-verificatie- [Azure Active Directory Pass-Through-verificatie](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federatieve verificatie- [Active Directory Federation Services implementeren in azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) en [Azure AD Connect en Federatie](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

Zie het onderstaande diagram voor een voor beeld Federated Authentication met ADFS-infra structuur (of gebruiker/wacht woord voor Windows-referenties). De pijlen geven communicatiepaden aan.

![aad-verificatiediagram][1]

In het volgende diagram worden de federatie-, vertrouwens en hostingrelaties aangegeven waarmee een client verbinding kan maken met een database door een token in te dienen. Het token wordt geverifieerd door een Azure AD en wordt vertrouwd door de database. Klant 1 kan een Azure Active Directory met systeemeigen gebruikers of een Azure AD met federatieve gebruikers vertegenwoordigen. Klant 2 is een mogelijke oplossing met inbegrip van geïmporteerde gebruikers, in dit voor beeld, afkomstig van een federatieve Azure Active Directory met ADFS wordt gesynchroniseerd met Azure Active Directory. Het is belangrijk om te begrijpen dat toegang tot een database met behulp van Azure AD-verificatie vereist dat het hostingabonnement is gekoppeld aan Azure AD. Hetzelfde abonnement moet worden gebruikt om de Azure SQL Database, het SQL Managed instance of Azure Synapse-resources te maken.

![abonnementsrelatie][2]

## <a name="administrator-structure"></a>Beheerdersstructuur

Wanneer u Azure AD-verificatie gebruikt, zijn er twee beheerders accounts: de oorspronkelijke Azure SQL Database beheerder en de Azure AD-beheerder. Dezelfde concepten zijn van toepassing op Azure Synapse. Alleen de beheerder op basis van een Azure AD-account kan de eerste Azure AD-databasegebruiker in een gebruikersdatabase maken. De aanmelding van de Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Wanneer de beheerder een groeps account is, kan dit worden gebruikt door elk groepslid om meerdere Azure AD-beheerders voor de server in te scha kelen. Het gebruik van groeps account als beheerder verbetert de beheer baarheid, zodat u groeps leden in azure AD centraal kunt toevoegen en verwijderen zonder de gebruikers of machtigingen in SQL Database of Azure Synapse te wijzigen. Er kan maar één Azure AD-beheerder (een gebruiker of groep) tegelijk worden geconfigureerd.

![beheerdersstructuur][3]

## <a name="permissions"></a>Machtigingen

Als u nieuwe gebruikers wilt maken, moet u de machtiging `ALTER ANY USER` in de database hebben. De machtiging `ALTER ANY USER` kan aan elke databasegebruiker worden verleend. De machtiging `ALTER ANY USER` is ook verleend aan de beheerdersaccounts van de server, databasegebruikers met de machtiging `CONTROL ON DATABASE`of `ALTER ON DATABASE` voor die database en leden van de databaserol `db_owner`.

Als u een Inge sloten database gebruiker wilt maken in Azure SQL Database, SQL Managed instance of Azure Synapse, moet u verbinding maken met de data base of het exemplaar met behulp van een Azure AD-identiteit. Om de eerste gebruiker voor een ingesloten database te maken, moet u gebruikmaken van een Azure AD-beheerder (die de eigenaar is van de database) om verbinding te maken met de database. Dit wordt geïllustreerd in [Azure Active Directory authenticatie configureren en beheren met SQL database of Azure Synapse](authentication-aad-configure.md). Azure AD-verificatie is alleen mogelijk als de Azure AD-beheerder is gemaakt voor Azure SQL Database, een beheerd exemplaar van SQL of Azure Synapse. Als de Azure Active Directory-beheerder van de server is verwijderd, kunnen bestaande Azure Active Directory gebruikers die eerder in SQL Server zijn gemaakt, geen verbinding meer maken met de data base met behulp van hun Azure Active Directory referenties.

## <a name="azure-ad-features-and-limitations"></a>Functies en beperkingen van Azure AD

- De volgende leden van Azure AD kunnen worden ingericht voor Azure SQL Database:

  - Systeemeigen leden: Een lid dat is gemaakt in Azure AD in het beheerde domein of in een klantdomein. Zie [Uw eigen domeinnaam toevoegen aan Azure AD](../../active-directory/fundamentals/add-custom-domain.md) voor meer informatie.
  - Leden van een Active Directory domein Federated met Azure Active Directory op een beheerd domein dat is geconfigureerd voor naadloze eenmalige aanmelding met pass-through of wacht woord-hash-verificatie. Zie [Microsoft Azure nu ondersteuning biedt voor Federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/windows-azure-now-supports-federation-with-windows-server-active-directory//) en [Azure Active Directory naadloze eenmalige aanmelding](../../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.
  - Geïmporteerde leden van andere Azure AD's die systeemeigen leden of leden van een federatief domein zijn.
  - Active Directory-groepen die zijn gemaakt als beveiligingsgroepen.

- Azure AD-gebruikers die deel uitmaken van een groep met `db_owner` serverrol, kunnen de **[referentie Create Data Base scoped CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)** niet gebruiken voor Azure SQL database en Azure Synapse. U ziet de volgende fout:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Verleen de rol `db_owner` rechtstreeks aan de individuele Azure AD-gebruiker om het probleem met **CREATE DATABASE SCOPED CREDENTIAL** te beperken.

- Deze systeemfuncties retourneren NULL-waarden wanneer ze worden uitgevoerd onder Azure AD-principals:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

### <a name="sql-managed-instance"></a>SQL Managed Instance

- Azure AD-server-principals (aanmeldingen) en gebruikers worden ondersteund voor door [SQL beheerd exemplaar](../managed-instance/sql-managed-instance-paas-overview.md).
- Het instellen van Azure AD-server principals (aanmeldingen) die zijn toegewezen aan een Azure AD-groep als de data base-eigenaar, wordt niet ondersteund in een [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md).
  - Een uitbrei ding hiervan is dat wanneer een groep wordt toegevoegd als onderdeel van de `dbcreator` Server functie, gebruikers van deze groep verbinding kunnen maken met het SQL Managed instance en nieuwe data bases kunnen maken, maar geen toegang tot de data base hebben. Dit is omdat de nieuwe data base-eigenaar SA is en niet de Azure AD-gebruiker. Dit probleem treedt niet op als de afzonderlijke gebruiker wordt toegevoegd aan de `dbcreator` Server functie.
- De uitvoering van SQL Agent-beheer en-taken wordt ondersteund voor Azure AD-server-principals (aanmeldingen).
- Database-back-up en herstelbewerkingen kunnen worden uitgevoerd door Azure AD-server-principals (aanmeldingen).
- Controle van alle-instructies met betrekking tot Azure AD-server-principals (aanmeldingen) en verificatie gebeurtenissen wordt ondersteund.
- Exclusieve beheerders verbinding voor Azure AD-server-principals (aanmeldingen) die lid zijn van de serverrol sysadmin wordt ondersteund.
  - Ondersteund via SQLCMD-hulp programma en SQL Server Management Studio.
- Aanmeldingstriggers worden ondersteund voor aanmeldingsgebeurtenissen die afkomstig zijn van Azure AD-server-principal s(aanmeldingen).
- Service Broker en DB-mail kunnen worden ingesteld met behulp van een Azure AD server-principal (aanmelden).

## <a name="connect-by-using-azure-ad-identities"></a>Verbinding maken met behulp van Azure AD-identiteiten

Azure Active Directory-verificatie ondersteunt de volgende methoden om verbinding te maken met een database met behulp van Azure AD-identiteiten:

- Azure Active Directory – wachtwoord
- Azure Active Directory – geïntegreerd
- Azure Active Directory universeel met Multi-Factor Authentication
- Verificatie van toepassingstoken gebruiken

De volgende verificatiemethoden worden ondersteund voor Azure AD-server-principals (aanmeldingen):

- Azure Active Directory – wachtwoord
- Azure Active Directory – geïntegreerd
- Azure Active Directory universeel met Multi-Factor Authentication

### <a name="additional-considerations"></a>Aanvullende overwegingen

- Voor een betere beheerbaarheid raden wij u aan een toegewezen Azure AD-groep in te richten als beheerder.
- Er kan slechts één Azure AD-beheerder (een gebruiker of groep) op elk gewenst moment worden geconfigureerd voor een server in SQL Database of Azure Synapse.
  - Het toevoegen van Azure AD-server-principals (aanmeldingen) voor een SQL Managed instance biedt de mogelijkheid om meerdere Azure AD server-principals (aanmeldingen) te maken die kunnen worden toegevoegd aan de `sysadmin` rol.
- Alleen een Azure AD-beheerder voor de server kan voor het eerst verbinding maken met de server of het beheerde exemplaar met behulp van een Azure Active Directory-account. De Active Directory-beheerder kan vervolgens nieuwe Azure AD-databasegebruikers configureren.
- Het is raadzaam om de time-out voor de verbinding in te stellen op 30 seconden.
- SQL Server 2016 Management Studio en SQL Server Data Tools voor Visual Studio 2015 (versie 14.0.60311.1 van april 2016 of later) ondersteunen Azure Active Directory-verificatie. (Azure AD-verificatie wordt ondersteund door de **.NET Framework-gegevensprovider voor SqlServer**; minimaal .NET Framework versie 4.6). Daarom kunnen de nieuwste versies van deze hulpprogram ma's en gegevenslaag toepassingen (DAC en BACPAC) gebruikmaken van Azure AD-verificatie.
- Vanaf versie 15.0.1, [sqlcmd utility](/sql/tools/sqlcmd-utility) en [bcp Utility](/sql/tools/bcp-utility) ondersteunen Active Directory interactieve verificatie met multi-factor Authentication.
- SQL Server Data Tools voor Visual Studio 2015 vereist minimaal de versie van april 2016 van de hulpmiddelen voor gegevens (versie 14.0.60311.1). Op dit moment worden Azure AD-gebruikers niet weer gegeven in SSDT Objectverkenner. Als tijdelijke oplossing kunt u de gebruikers weergeven in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).
- [Microsoft JDBC Driver 6.0 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) ondersteunt Azure AD-verificatie. Zie ook [De verbindingseigenschappen instellen](/sql/connect/jdbc/setting-the-connection-properties).
- Poly Base kan niet worden geverifieerd met behulp van Azure AD-verificatie.
- Azure AD-verificatie wordt ondersteund voor Azure SQL Database en Azure Synapse via de Blade Azure Portal **Data Base importeren** en **Data Base exporteren** . Importeren en exporteren met behulp van Azure AD-verificatie wordt ook ondersteund vanuit een Power shell-opdracht.
- Azure AD-verificatie wordt ondersteund voor SQL Database, SQL Managed instance en Azure Synapse met behulp van de CLI. Zie [Azure AD-verificatie configureren en beheren met SQL database of Azure Synapse](authentication-aad-configure.md) en [SQL Server-AZ SQL Server](/cli/azure/sql/server)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het maken en vullen van een Azure AD-exemplaar en het configureren met Azure SQL Database, SQL Managed instance of Azure Synapse de [Azure Active Directory-verificatie configureren en beheren met SQL database, SQL Managed instance of Azure Synapse](authentication-aad-configure.md).
- Voor een zelf studie over het gebruik van Azure AD server-principals (aanmeldingen) met SQL Managed instance raadpleegt u [Azure ad server-principals (aanmeldingen) met SQL Managed instance](../managed-instance/aad-security-configure-tutorial.md)
- Zie [aanmeldingen, gebruikers, database rollen en machtigingen](logins-create-manage.md)voor een overzicht van aanmeldingen, gebruikers, database rollen en machtigingen in SQL database.
- Zie [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine) voor meer informatie over database-principals.
- Zie [Databaserollen](/sql/relational-databases/security/authentication-access/database-level-roles) voor meer informatie over databaserollen.
- Zie  [login maken](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)voor de syntaxis voor het maken van Azure ad-server principals (aanmeldingen) voor een door SQL beheerd exemplaar.
- Zie [SQL Database-firewallregels](firewall-configure.md) voor meer informatie over de firewallregels in SQL Database.

<!--Image references-->
[1]: ./media/authentication-aad-overview/1aad-auth-diagram.png
[2]: ./media/authentication-aad-overview/2subscription-relationship.png
[3]: ./media/authentication-aad-overview/3admin-structure.png
