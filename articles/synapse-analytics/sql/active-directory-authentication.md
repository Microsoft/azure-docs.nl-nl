---
title: Azure Active Directory
description: Meer informatie over hoe u Azure Active Directory gebruikt voor verificatie met SQL Database, Managed Instance en Synapse SQL
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 962e0e734abd73f5a66f9b9ee6067155dd839e5d
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118282"
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-synapse-sql"></a>Azure Active Directory-verificatie gebruiken voor verificatie met Synapse SQL

Azure Active Directory-verificatie is een mechanisme dat verbinding maakt met [Azure Synapse Analytics](../overview-faq.md) door gebruik te maken van identiteiten in Azure Active Directory (Azure AD).

Met Azure AD-verificatie kunt u gebruikersidentiteiten die toegang hebben tot Azure Synapse centraal beheren om het beheer van machtigingen te vereenvoudigen. Dit biedt verschillende voordelen, zoals:

- Het biedt een alternatief voor normale verificatie van gebruikersnaam en wachtwoord.
- Het gaat de verspreiding van gebruikers-id's op servers tegen.
- Maakt rotatie van wachtwoorden op één plek mogelijk.
- Klanten kunnen machtigingen beheren met behulp van externe groepen (van Azure AD).
- Wachtwoorden hoeven niet meer te worden opgeslagen door het inschakelen van geïntegreerde Windows-verificatie en andere vormen van authenticatie die worden ondersteund door Azure Active Directory.
- Azure AD biedt ondersteuning voor verificatie op basis van tokens voor toepassingen die verbinding maken met Azure Synapse.
- Azure AD-verificatie biedt ondersteuning voor ADFS (domeinfederatie) of systeemeigen verificatie met gebruikersnaam/wachtwoord voor een lokale Azure Active Directory zonder domeinsynchronisatie.
- Azure AD ondersteunt verbindingen van SQL Server Management Studio die gebruikmaken van Active Directory Universal-verificatie, waaronder Multi-Factor Authentication (MFA).  MFA omvat krachtige verificatie met een scala aan eenvoudige verificatie-opties, waaronder telefoonoproepen, sms-berichten, smartcards met pincode of meldingen in mobiele apps. Zie [SSMS-ondersteuning voor Azure AD MFA met Synapse SQL](mfa-authentication.md) voor meer informatie.
- Azure AD biedt ondersteuning voor vergelijkbare verbindingen van SQL Server Data Tools (SSDT) die gebruikmaken van Active Directory Interactive Authentication. Zie [Azure Active Directory support in SQL Server Data Tools (SSDT)](/sql/ssdt/azure-active-directory?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (Ondersteuning van Azure Active Directory in SQL Server Data Tools (SSDT)) voor meer informatie.

De configuratiestappen omvatten de volgende procedures voor het configureren en gebruiken van Azure Active Directory-verificatie.

1. Maak en vul Azure AD.
2. Een Azure Active Directory-identiteit maken
3. Een rol aan gemaakte Azure Active Directory-identiteit toewijzen in Synapse-werkruimte
4. Maak verbinding met Synapse Studio met behulp van Azure AD-identiteiten.

## <a name="azure-ad-pass-through-in-azure-synapse-analytics"></a>Azure AD-doorvoer in Azure Synapse Analytics

Met Azure Synapse Analytics kunt u toegang krijgen tot de gegevens in de data lake door gebruik te maken van uw Azure Active Directory-identiteit.

Door toegangsrechten te definiëren voor de bestanden en gegevens die in verschillende gegevensengines worden gerespecteerd, kunt u uw data lake-oplossing vereenvoudigen omdat alle machtigingen op één plaats worden gedefinieerd, in plaats van dat ze op meerdere locaties moeten worden gedefinieerd.

## <a name="trust-architecture"></a>Architectuur van vertrouwensrelatie

In het volgende overzichtsdiagram ziet u een overzicht van de oplossingsarchitectuur van het gebruik van Azure AD-verificatie met Synapse SQL. Ter ondersteuning van het native gebruikerswachtwoord van Azure AD wordt alleen rekening gehouden met het cloudgedeelte en Azure AD/Synapse Synapse SQL. Voor de ondersteuning van federatieve aanmelding (of gebruiker/wachtwoord voor Windows-referenties), is de communicatie met het ADFS-blok vereist. De pijlen geven communicatiepaden aan.

![azure ad-verificatiediagram](./media/aad-authentication/1-active-directory-authentication-diagram.png)

In het volgende diagram worden de federatie-, vertrouwens en hostingrelaties aangegeven waarmee een client verbinding kan maken met een database door een token in te dienen. Het token wordt geverifieerd door een Azure AD en wordt vertrouwd door de database. 

Klant 1 kan een Azure Active Directory met systeemeigen gebruikers of een Azure AD met federatieve gebruikers vertegenwoordigen. Klant 2 vertegenwoordigt een mogelijke oplossing met inbegrip van geïmporteerde gebruikers; in dit voorbeeld afkomstig van een federatieve Azure Active Directory waarbij ADFS wordt gesynchroniseerd met Azure Active Directory. 

Het is belangrijk om te begrijpen dat toegang tot een database met behulp van Azure AD-verificatie vereist dat het hostingabonnement is gekoppeld aan Azure AD. Hetzelfde abonnement moet worden gebruikt voor het maken van de SQL-server die als host fungeert voor de Azure SQL Database of toegewezen SQL-pool.

![abonnementsrelatie](./media/aad-authentication/2-subscription-relationship.png)

## <a name="administrator-structure"></a>Beheerdersstructuur

Wanneer u Azure AD-verificatie gebruikt, zijn er twee beheerdersaccounts voor de Synapse SQL; de oorspronkelijke SQL Server-beheerder en de Azure AD-beheerder. Alleen de beheerder op basis van een Azure AD-account kan de eerste Azure AD-databasegebruiker in een gebruikersdatabase maken. 

De aanmelding van de Azure AD-beheerder kan een Azure AD-gebruiker of een Azure AD-groep zijn. Wanneer de beheerder een groepsaccount is, kan dit worden gebruikt door elk groepslid. Er kunnen dus meerdere Azure AD-beheerders voor het Synapse SQL-exemplaar worden ingeschakeld. 

Het gebruik van een groepsaccount als beheerder verbetert de beheerbaarheid, omdat u groepsleden in Azure AD centraal kunt toevoegen en verwijderen zonder de gebruikers of machtigingen in de Azure Synapse Analytics-werkruimte te wijzigen. Er kan maar één Azure AD-beheerder (een gebruiker of groep) tegelijk worden geconfigureerd.

![beheerdersstructuur](./media/aad-authentication/3-admin-structure.png)

## <a name="permissions"></a>Machtigingen

Als u nieuwe gebruikers wilt maken, moet u de machtiging `ALTER ANY USER` in de database hebben. De machtiging `ALTER ANY USER` kan aan elke databasegebruiker worden verleend. De machtiging `ALTER ANY USER` is ook verleend aan de beheerdersaccounts van de server, databasegebruikers met de machtiging `CONTROL ON DATABASE`of `ALTER ON DATABASE` voor die database en leden van de databaserol `db_owner`.

Als u een gebruiker voor een ingesloten database in Synapse SQL wilt maken, moet u met behulp van een Azure AD-identiteit verbinding maken met de database of het exemplaar. Om de eerste gebruiker voor een ingesloten database te maken, moet u gebruikmaken van een Azure AD-beheerder (die de eigenaar is van de database) om verbinding te maken met de database. 

Een Azure AD-verificatie is alleen mogelijk als de Azure AD-beheerder is gemaakt voor Synapse SQL. Als de Azure Active Directory-beheerder van de server is verwijderd, kunnen bestaande Azure Active Directory-gebruikers die eerder in Synapse SQL zijn gemaakt, geen verbinding meer maken met de database met behulp van hun Azure Active Directory-referenties.
 
## <a name="azure-ad-features-and-limitations"></a>Functies en beperkingen van Azure AD

- De volgende leden van Azure AD kunnen worden ingericht in Synapse SQL:

  - Systeemeigen leden: Een lid dat is gemaakt in Azure AD in het beheerde domein of in een klantdomein. Zie [Uw eigen domeinnaam toevoegen aan Azure AD](../../active-directory/fundamentals/add-custom-domain.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie.
  - Leden van federatief domein: Een lid dat is gemaakt in Azure AD met een federatief domein. Zie [Microsoft Azure ondersteunt nu federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) voor meer informatie.
  - Geïmporteerde leden van andere Azure AD's die systeemeigen leden of leden van een federatief domein zijn.
  - Active Directory-groepen die zijn gemaakt als beveiligingsgroepen.

- Azure AD-gebruikers die deel uitmaken van een groep met serverrol `db_owner`, kunnen de syntaxis **[CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** niet gebruiken tegen Synapse SQL. U ziet de volgende fout:

    `SQL Error [2760] [S0001]: The specified schema name 'user@mydomain.com' either does not exist or you do not have permission to use it.`

    Verleen de rol `db_owner` rechtstreeks aan de individuele Azure AD-gebruiker om het probleem met **CREATE DATABASE SCOPED CREDENTIAL** te beperken.

- Deze systeemfuncties retourneren NULL-waarden wanneer ze worden uitgevoerd onder Azure AD-principals:

  - `SUSER_ID()`
  - `SUSER_NAME(<admin ID>)`
  - `SUSER_SNAME(<admin SID>)`
  - `SUSER_ID(<admin name>)`
  - `SUSER_SID(<admin name>)`

## <a name="connect-using-azure-ad-identities"></a>Verbinding maken met Azure AD-identiteiten

Azure Active Directory-verificatie ondersteunt de volgende methoden om verbinding te maken met een database met behulp van Azure AD-identiteiten:

- Azure Active Directory – wachtwoord
- Azure Active Directory – geïntegreerd
- Azure Active Directory – Universal met MFA
- Verificatie van toepassingstoken gebruiken

De volgende verificatiemethoden worden ondersteund voor Azure AD-server-principals (aanmeldingen):

- Azure Active Directory – wachtwoord
- Azure Active Directory – geïntegreerd
- Azure Active Directory – Universal met MFA

### <a name="additional-considerations"></a>Aanvullende overwegingen

- Voor een betere beheerbaarheid raden wij u aan een toegewezen Azure AD-groep in te richten als beheerder.
- Er kan maar één Azure AD-beheerder (een gebruiker of groep) tegelijk voor een Synapse SQL-pool worden geconfigureerd.
  - Door Azure AD-server-principals (aanmeldingen) voor Synapse SQL toe te voegen, wordt het mogelijk om meerdere Azure AD-server-principals (aanmeldingen) te maken die kunnen worden toegevoegd aan de rol `sysadmin`.
- Alleen een Azure AD-beheerder voor Synapse SQL kan in eerste instantie verbinding maken met Synapse SQL met een Azure Active Directory-account. De Active Directory-beheerder kan vervolgens nieuwe Azure AD-databasegebruikers configureren.
- Het is raadzaam om de time-out voor de verbinding in te stellen op 30 seconden.
- SQL Server 2016 Management Studio en SQL Server Data Tools voor Visual Studio 2015 (versie 14.0.60311.1 van april 2016 of later) ondersteunen Azure Active Directory-verificatie. (Azure AD-verificatie wordt ondersteund door de **.NET Framework-gegevensprovider voor SqlServer**; minimaal .NET Framework versie 4.6). Daardoor kunnen de nieuwste versies van deze hulpprogramma's en toepassingen met een gegevenslaag (DAC en .BACPAC) gebruik maken van Azure AD-verificatie.
- Vanaf versie 15.0.1 ondersteunen de hulpprogramma's [sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) en [bcp](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) Active Directory interactieve verificatie met MFA.
- SQL Server Data Tools voor Visual Studio 2015 vereist minimaal de versie van april 2016 van de hulpmiddelen voor gegevens (versie 14.0.60311.1). Momenteel worden Azure AD-gebruikers niet weergegeven in SSDT Objectverkenner. Als tijdelijke oplossing kunt u de gebruikers weergeven in [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- [Microsoft JDBC Driver 6.0 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) ondersteunt Azure AD-verificatie. Zie ook [De verbindingseigenschappen instellen](/sql/connect/jdbc/setting-the-connection-properties?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="next-steps"></a>Volgende stappen

- Zie [Synapse SQL-toegangsbeheer](../security/synapse-workspace-access-control-overview.md) voor een overzicht van toegang en beheer in Synapse SQL.
- Zie [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor meer informatie over database-principals.
- Zie [Databaserollen](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) voor meer informatie over databaserollen.

