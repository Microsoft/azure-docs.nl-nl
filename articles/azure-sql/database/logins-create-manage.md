---
title: Server-en database toegang autoriseren met behulp van aanmeldingen en gebruikers accounts
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Meer informatie over hoe Azure SQL Database, SQL Managed instance en Azure Synapse gebruikers voor toegang verifieert met behulp van aanmeldingen en gebruikers accounts. Meer informatie over het verlenen van database rollen en expliciete machtigingen om aanmeldingen en gebruikers te autoriseren om acties uit te voeren en gegevens op te vragen.
keywords: sql-databasebeveiliging,beheer databasebeveiliging,aanmeldingsbeveiliging,databasebeveiliging,databasetoegang
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: 6383c0fa3aad15da8abaea79136a75732ce9ea01
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605753"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Databasetoegang verlenen tot SQL Database, SQL Managed Instance, en Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In dit artikel vindt u meer informatie over:

- Opties voor het configureren van Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics om gebruikers in staat te stellen beheer taken uit te voeren en toegang te krijgen tot de gegevens die zijn opgeslagen in deze data bases.
- De configuratie van toegang en autorisatie na het maken van een nieuwe server.
- Aanmeldingen en gebruikers accounts toevoegen aan de hoofd database en gebruikers accounts en vervolgens deze accounts beheer machtigingen verlenen.
- Het toevoegen van gebruikers accounts in gebruikers databases, hetzij gekoppeld aan aanmeldingen of als opgenomen gebruikers accounts.
- Gebruikers accounts met machtigingen in gebruikers databases configureren met behulp van database rollen en expliciete machtigingen.

> [!IMPORTANT]
> Data bases in Azure SQL Database, Azure SQL Managed instance en Azure Synapse worden gezamenlijk in de rest van dit artikel vermeld als data bases en de server verwijst naar de [Server](logical-servers.md) die de data bases beheert voor Azure SQL database en Azure Synapse.

## <a name="authentication-and-authorization"></a>Verificatie en autorisatie

[**Verificatie**](security-overview.md#authentication) is het proces waarbij de gebruiker wordt geclaimd. Een gebruiker maakt verbinding met een Data Base met behulp van een gebruikers account.
Wanneer een gebruiker probeert verbinding te maken met een Data Base, bieden ze een gebruikers account en verificatie gegevens. De gebruiker wordt geverifieerd met een van de volgende twee verificatie methoden:

- [SQL-verificatie](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Met deze verificatie methode verzendt de gebruiker de naam van een gebruikers account en het bijbehorende wacht woord om een verbinding tot stand te brengen. Dit wacht woord wordt opgeslagen in de hoofd database voor gebruikers accounts die zijn gekoppeld aan een aanmelding of worden opgeslagen in de data base met de gebruikers accounts die *niet* aan een aanmelding zijn gekoppeld.
- [Azure Active Directory-verificatie](authentication-aad-overview.md)

  Met deze verificatie methode verzendt de gebruiker de naam van een gebruikers account en vraagt de service de referentie gegevens te gebruiken die zijn opgeslagen in Azure Active Directory (Azure AD).

**Aanmeldingen en gebruikers**: een gebruikers account in een Data Base kan worden gekoppeld aan een aanmelding die is opgeslagen in de hoofd database of kan een gebruikers naam zijn die is opgeslagen in een afzonderlijke data base.

- Een **aanmelding** is een afzonderlijk account in de hoofd database, waaraan een gebruikers account in een of meer data bases kan worden gekoppeld. Bij een aanmelding worden de aanmeldingsgegevens voor het gebruikersaccount bij de aanmelding opgeslagen.
- Een **gebruikers account** is een afzonderlijk account in een Data Base die mogelijk wel, maar niet is gekoppeld aan een aanmelding. Bij een gebruikersaccount dat niet is gekoppeld aan een aanmelding, worden de aanmeldingsgegevens opgeslagen met het gebruikersaccount.

[**Autorisatie**](security-overview.md#authorization) voor toegang tot gegevens en het uitvoeren van verschillende acties wordt beheerd met behulp van database rollen en expliciete machtigingen. Autorisatie verwijst naar de machtigingen die zijn toegewezen aan een gebruiker en bepaalt wat die gebruiker mag doen. Autorisatie wordt beheerd door de databaserol [lidmaatschappen](/sql/relational-databases/security/authentication-access/database-level-roles) en [object machtigingen](/sql/relational-databases/security/permissions-database-engine)van uw gebruikers account. Het wordt aanbevolen om gebruikers de minimaal benodigde bevoegdheden te verlenen.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Bestaande aanmeldingen en gebruikers accounts na het maken van een nieuwe data base

Wanneer u Azure SQL voor het eerst implementeert, geeft u een beheerders aanmelding en een bijbehorend wacht woord op voor die aanmelding. Dit beheerders account wordt **Server beheerder** genoemd. De volgende configuratie van aanmeldingen en gebruikers in de hoofd-en gebruikers databases vindt plaats tijdens de implementatie:

- Er wordt een SQL-aanmelding met Administrator bevoegdheden gemaakt met behulp van de aanmeldings naam die u hebt opgegeven. Een [aanmelding](/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) is een afzonderlijke gebruikers account waarmee u zich kunt aanmelden bij SQL database, SQL Managed instance en Azure Synapse.
- Aan deze aanmelding worden volledige beheerders machtigingen voor alle data bases verleend als [principal op server niveau](/sql/relational-databases/security/authentication-access/principals-database-engine). De aanmelding heeft alle beschik bare machtigingen en kan niet worden beperkt. In een SQL Managed instance wordt deze aanmelding toegevoegd aan de [vaste serverrol sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) (deze rol bestaat niet in Azure SQL database).
- Een [gebruikers account](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) met de naam `dbo` wordt gemaakt voor deze aanmelding in elke gebruikers database. De [dbo](/sql/relational-databases/security/authentication-access/principals-database-engine) -gebruiker heeft alle database machtigingen in de data base en is toegewezen aan de `db_owner` vaste databaserol. Aanvullende vaste database rollen worden verderop in dit artikel besproken.

Als u de beheerders accounts voor een Data Base wilt identificeren, opent u de Azure Portal en navigeert u naar het tabblad **Eigenschappen** van de server of het beheerde exemplaar.

![SQL Server-beheerders](./media/logins-create-manage/sql-admins.png)

![Scherm afbeelding die de menu optie Eigenschappen markeert.](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> De aanmeldings naam van de beheerder kan niet worden gewijzigd nadat deze is gemaakt. Als u het wacht woord voor de server beheerder opnieuw wilt instellen, gaat u naar de [Azure Portal](https://portal.azure.com), klikt u op **SQL-servers**, selecteert u de server in de lijst en klikt u vervolgens op **wacht woord opnieuw instellen**. Als u het wacht woord voor het beheerde exemplaar van SQL wilt herstellen, gaat u naar de Azure Portal, klikt u op het exemplaar en klikt u op **wacht woord opnieuw instellen**. U kunt ook Power shell of de Azure CLI gebruiken.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Extra aanmeldingen en gebruikers met beheerders machtigingen maken

Op dit punt wordt de server of het beheerde exemplaar alleen geconfigureerd voor toegang met behulp van één SQL-aanmeldings-en-gebruikers account. Als u extra aanmeldingen wilt maken met volledige of gedeeltelijke beheerders machtigingen, hebt u de volgende opties (afhankelijk van de implementatie modus):

- **Een Azure Active Directory beheerders account met volledige beheerders machtigingen maken**

  Schakel Azure Active Directory-verificatie in en maak een aanmelding voor Azure AD-beheerders. Een Azure Active Directory-account kan worden geconfigureerd als beheerder van de Azure SQL-implementatie met volledige beheerders machtigingen. Dit account kan een individuele account of een beveiligings groep zijn. Een Azure AD-beheerder **moet** worden geconfigureerd als u Azure AD-accounts wilt gebruiken om verbinding te maken met SQL database, een door SQL beheerd exemplaar of Azure Synapse. Voor gedetailleerde informatie over het inschakelen van Azure AD-verificatie voor alle Azure SQL-implementatie typen raadpleegt u de volgende artikelen:

  - [Azure Active Directory authenticatie gebruiken voor verificatie met SQL](authentication-aad-overview.md)
  - [Verificatie van Azure Active Directory configureren en beheren met SQL](authentication-aad-configure.md)

- **Maak in SQL Managed instance SQL-aanmeldingen met volledige beheerders machtigingen**

  - Maak een extra SQL-aanmelding in de hoofd database.
  - Voeg de aanmelding toe aan de [vaste serverrol sysadmin](/sql/relational-databases/security/authentication-access/server-level-roles) met de instructie [ALTER server Role](/sql/t-sql/statements/alter-server-role-transact-sql) . Deze aanmelding heeft volledige beheerders machtigingen.
  - U kunt ook een [Azure AD-aanmelding](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) maken met de syntaxis voor het maken van een [aanmelding](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) .

- **Maak in SQL Database SQL-aanmeldingen met beperkte beheerders machtigingen**

  - Maak een extra SQL-aanmelding in de hoofd database.
  - Maak een gebruikers account in de hoofd database die is gekoppeld aan deze nieuwe aanmelding.
  - Voeg het gebruikers account toe aan de `dbmanager` , de `loginmanager` rol of beide in de- `master` Data Base met behulp van de instructie [ALTER Role](/sql/t-sql/statements/alter-role-transact-sql) (voor Azure Synapse gebruikt u de instructie [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > `dbmanager` en `loginmanager` rollen zijn **niet** van toepassing op IMPLEMENTATIEs van SQL-beheerde exemplaren.

  Leden van deze [speciale Master database rollen](/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) voor Azure SQL database hebben toestemming voor het maken en beheren van data bases of voor het maken en beheren van aanmeldingen. In data bases die zijn gemaakt door een gebruiker die lid is van de `dbmanager` rol, wordt het lid toegewezen aan de `db_owner` vaste databaserol en kan die data base worden aangemeld en beheerd met behulp van het `dbo` gebruikers account. Deze rollen hebben geen expliciete machtigingen buiten de hoofd database.

  > [!IMPORTANT]
  > U kunt geen extra SQL-aanmelding met volledige beheerders machtigingen maken in SQL Database.

## <a name="create-accounts-for-non-administrator-users"></a>Accounts maken voor gebruikers die geen beheerder zijn

U kunt met een van de volgende twee methoden accounts maken voor gebruikers die geen beheerder zijn:

- **Een aanmelding maken**

  Maak een SQL-aanmelding in de hoofd database. Maak vervolgens een gebruikers account in elke Data Base waartoe die gebruiker toegang nodig heeft en koppel het gebruikers account aan die aanmelding. Deze methode verdient de voor keur wanneer de gebruiker toegang moet krijgen tot meerdere data bases en u de wacht woorden gesynchroniseerd wilt laten blijven. Deze aanpak heeft echter complexer wanneer deze wordt gebruikt met geo-replicatie, aangezien de aanmelding moet worden gemaakt op zowel de primaire server als op de secundaire server (s). Zie [Azure SQL database beveiliging configureren en beheren voor geo-herstel of failover](active-geo-replication-security-configure.md)voor meer informatie.
- **Een gebruikersaccount maken**

  Maak een gebruikers account in de data base waartoe een gebruiker toegang nodig heeft (ook wel een [Inge sloten gebruiker](/sql/relational-databases/security/contained-database-users-making-your-database-portable)genoemd).

  - Met SQL Database kunt u altijd dit type gebruikers account maken.
  - Met SQL Managed instance die ondersteuning biedt voor [Azure ad server-principals](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities), kunt u gebruikers accounts maken om te verifiëren bij het SQL Managed instance zonder dat database gebruikers moeten worden gemaakt als Inge sloten database gebruiker.

  Met deze methode worden de verificatie gegevens van de gebruiker opgeslagen in elke Data Base en automatisch gerepliceerd naar geo-gerepliceerde data bases. Als hetzelfde account echter bestaat in meerdere data bases en u Azure SQL-verificatie gebruikt, moet u de wacht woorden hand matig synchroniseren. Als een gebruiker ook een account in verschillende data bases met verschillende wacht woorden heeft, kan het onthouden van die wacht woorden een probleem worden.

> [!IMPORTANT]
> Als u Inge sloten gebruikers wilt maken die zijn toegewezen aan Azure AD-identiteiten, moet u zijn aangemeld met een Azure AD-account dat een beheerder is in de data base in Azure SQL Database. In SQL Managed instance kan een SQL-aanmelding met `sysadmin` machtigingen ook een Azure AD-aanmelding of-gebruiker maken.

Voor voor beelden van het maken van aanmeldingen en gebruikers raadpleegt u:

- [Aanmelding voor Azure SQL Database maken](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Aanmelding maken voor Azure SQL Managed instance](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Aanmelding voor Azure Synapse maken](/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Create user](/sql/t-sql/statements/create-user-transact-sql#examples)
- [In azure AD opgenomen gebruikers maken](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Zie [zelf studie: beveiligde Azure SQL database](secure-database-tutorial.md)voor een beveiligings zelfstudie waarbij u gebruikers maakt in Azure SQL database.

## <a name="using-fixed-and-custom-database-roles"></a>Vaste en aangepaste database rollen gebruiken

Nadat u een gebruikers account in een Data Base hebt gemaakt, op basis van een aanmelding of als een Inge sloten gebruiker, kunt u die gebruiker machtigen om verschillende acties uit te voeren en toegang te krijgen tot gegevens in een bepaalde data base. U kunt de volgende methoden gebruiken om toegang te verlenen:

- **Vaste database rollen**

  Voeg het gebruikers account toe aan een [vaste databaserol](/sql/relational-databases/security/authentication-access/database-level-roles). Er zijn negen vaste database rollen, elk met een gedefinieerde set machtigingen. De meest voorkomende vaste database rollen zijn: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter** en **db_denydatareader**. **db_owner** wordt doorgaans gebruikt voor het verlenen van volledige machtigingen aan slechts enkele gebruikers. De andere vaste databaserollen zijn handig voor het snel verkrijgen van een eenvoudige database voor ontwikkeldoeleinden, maar worden niet aanbevolen voor de meeste productiedatabases. Met de **db_datareader** vaste databaserol wordt bijvoorbeeld lees toegang verleend aan elke tabel in de data base, wat meer is dan strikt nood zakelijk is.

  - Een gebruiker toevoegen aan een vaste databaserol:

    - Gebruik in Azure SQL Database de instructie [ALTER Role](/sql/t-sql/statements/alter-role-transact-sql) . Zie voor voor beelden [ALTER Role-voor beelden](/sql/t-sql/statements/alter-role-transact-sql#examples)
    - Gebruik de [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) -instructie om Azure Synapse te gebruiken. Zie [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql#examples)-voor beelden voor voor beelden.

- **Aangepaste databaserol**

  Maak een aangepaste databaserol met behulp van de instructie [Create Role](/sql/t-sql/statements/create-role-transact-sql) . Met een aangepaste rol kunt u uw eigen door de gebruiker gedefinieerde database rollen maken en elke rol altijd de minste machtigingen verlenen die nodig zijn voor de bedrijfs behoefte. U kunt vervolgens gebruikers toevoegen aan de aangepaste rol. Als een gebruiker lid is van meerdere rollen, worden de machtigingen van alle rollen samengevoegd.
- **Machtigingen rechtstreeks verlenen**

  Verleen de [machtigingen](/sql/relational-databases/security/permissions-database-engine) van het gebruikers account rechtstreeks. Er zijn meer dan 100 machtigingen die afzonderlijk kunnen worden verleend of geweigerd in SQL Database. Veel van deze machtigingen zijn genest. De machtiging `UPDATE` voor een schema bevat bijvoorbeeld de machtiging `UPDATE` voor elke tabel binnen dat schema. Net als bij de meeste machtigingssystemen gaat de weigering van een machtiging vóór toestemming. Vanwege de geneste aard en het aantal machtigingen kan een nauwkeurig onderzoek nodig zijn om een geschikt machtigingssysteem te ontwerpen voor een goede bescherming van uw database. Start met de lijst van machtigingen in [Machtigingen (Database-engine)](/sql/relational-databases/security/permissions-database-engine) en controleer de [afbeelding op postergrootte](/sql/relational-databases/security/media/database-engine-permissions.png) van de machtigingen.

## <a name="using-groups"></a>Groepen gebruiken

Efficiënt toegangs beheer maakt gebruik van machtigingen die zijn toegewezen aan Active Directory beveiligings groepen en vaste of aangepaste rollen in plaats van aan individuele gebruikers.

- Wanneer u Azure Active Directory-verificatie gebruikt, moet u Azure Active Directory gebruikers in een Azure Active Directory beveiligings groep plaatsen. Maak voor de groep een ingesloten databasegebruiker. Voeg een of meer database gebruikers als lid toe aan aangepaste of ingebouwde database rollen met de specifieke machtigingen die geschikt zijn voor die groep gebruikers.

- Bij het gebruik van SQL-verificatie maakt u Inge sloten database gebruikers in de-data base. Plaats een of meer database gebruikers in een aangepaste databaserol met specifieke machtigingen die geschikt zijn voor die groep gebruikers.

  > [!NOTE]
  > U kunt ook groepen gebruiken voor niet-Inge sloten database gebruikers.

Zorg ervoor dat u de volgende functies kunt gebruiken voor het beperken of het verhogen van machtigingen:

- [Imitatie](/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) en [module-ondertekening](/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) kunnen worden gebruikt om machtigingen tijdelijk veilig te verhogen.
- [Beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) kan worden gebruikt om de rijen waartoe een gebruiker toegang heeft te beperken.
- [Gegevensmaskering](dynamic-data-masking-overview.md) kan worden gebruikt om de weergave van gevoelige gegevens te beperken.
- [Opgeslagen procedures](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) kunnen worden gebruikt om de acties die op de database kunnen worden uitgevoerd te beperken.

## <a name="next-steps"></a>Volgende stappen

Zie [Security Overview](security-overview.md)(Engelstalig) voor een overzicht van alle beveiligings functies van Azure SQL database en SQL Managed instance.
