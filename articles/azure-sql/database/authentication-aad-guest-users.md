---
title: Azure AD-gast gebruikers maken
description: Azure AD-gast gebruikers maken en deze instellen als Azure AD-beheerder zonder Azure AD-groepen te gebruiken in Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 07/27/2020
ms.openlocfilehash: 7a4d9fb9f803a497e84fa189d9a89c2d9097bb70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92675061"
---
# <a name="create-azure-ad-guest-users-and-set-as-an-azure-ad-admin"></a>Azure AD-gastgebruikers maken en instellen als Azure AD-beheerder

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Dit artikel is beschikbaar in de **openbare preview-versie**.

Gast gebruikers in Azure Active Directory (Azure AD) zijn gebruikers die zijn geïmporteerd in de huidige Azure AD van andere Azure Active Directory-mappen of buiten de Directory. Gast gebruikers kunnen bijvoorbeeld gebruikers van andere Azure Active Directory-mappen of van accounts zoals *\@ Outlook.com*, *\@ Hotmail.com*, *\@ Live.com* of *\@ Gmail.com* toevoegen. In dit artikel wordt uitgelegd hoe u een Azure AD-gast gebruiker maakt en die gebruiker instelt als een Azure AD-beheerder voor de logische Azure SQL-Server, zonder dat deze gast gebruiker deel moet uitmaken van een groep in azure AD.

## <a name="feature-description"></a>Functiebeschrijving

Met deze functie wordt de huidige beperking opgeheven waarmee gast gebruikers alleen verbinding kunnen maken met Azure SQL Database, een door SQL beheerd exemplaar of Azure Synapse Analytics wanneer ze lid zijn van een groep die is gemaakt in azure AD. De groep die hand matig moet worden toegewezen aan een gebruiker met behulp van de instructie [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) in een bepaalde data base. Zodra een database gebruiker is gemaakt voor de Azure AD-groep met de gast gebruiker, kan de gast gebruiker zich aanmelden bij de data base met behulp van Azure Active Directory MFA-verificatie. Als onderdeel van deze **open bare preview** kunnen gast gebruikers worden gemaakt en rechtstreeks verbinding maken met SQL database, SQL Managed instance of Azure Synapse zonder dat ze eerst aan een Azure AD-groep moeten worden toegevoegd en vervolgens een database gebruiker maken voor die Azure AD-groep.

Als onderdeel van deze functie hebt u ook de mogelijkheid om de Azure AD-gast gebruiker rechtstreeks als AD-beheerder in te stellen voor de logische Azure SQL-Server. De bestaande functionaliteit waarbij de gast gebruiker deel kan uitmaken van een Azure AD-groep en die groep vervolgens kan worden ingesteld als de Azure AD-beheerder voor de logische Azure SQL-Server wordt niet beïnvloed. Gast gebruikers in de-data base die deel uitmaken van een Azure AD-groep, worden ook niet beïnvloed door deze wijziging.

Zie [using multi-factor Azure Active Directory Authentication](authentication-mfa-ssms-overview.md)(Engelstalig) voor meer informatie over bestaande ondersteuning voor gast gebruikers met Azure ad-groepen.

## <a name="prerequisite"></a>Vereiste

- De module [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) of hoger is vereist wanneer u Power shell gebruikt om een gast gebruiker in te stellen als een Azure AD-beheerder voor de logische Azure SQL-Server.

## <a name="create-database-user-for-azure-ad-guest-user"></a>Een database gebruiker maken voor de Azure AD-gast gebruiker 

Volg deze stappen om een database gebruiker te maken met behulp van een Azure AD-gast gebruiker.

### <a name="create-guest-user-in-sql-database-and-azure-synapse"></a>Een gast gebruiker maken in SQL Database en Azure Synapse

1. Zorg ervoor dat de gast gebruiker (bijvoorbeeld `user1@gmail.com` ) al is toegevoegd aan uw Azure AD en dat er een Azure AD-beheerder is ingesteld voor de database server. Een Azure AD-beheerder is vereist voor Azure Active Directory-verificatie.

1. Maak verbinding met de SQL database als de Azure AD-beheerder of een Azure AD-gebruiker met voldoende SQL-machtigingen voor het maken van gebruikers en voer de onderstaande opdracht uit in de data base waar de gast gebruiker moet worden toegevoegd:

    ```sql
    CREATE USER [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Er moet nu een database gebruiker zijn gemaakt voor de gast gebruiker `user1@gmail.com` .

1. Voer de onderstaande opdracht uit om te controleren of de database gebruiker is gemaakt:

    ```sql
    SELECT * FROM sys.database_principals
    ```

1. Verbreek de verbinding met en meld u aan bij de Data Base als de gast gebruiker `user1@gmail.com` die gebruikmaakt van [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) met behulp van de verificatie methode **Azure Active Directory-Universal met MFA**. Zie [using multi-factor Azure Active Directory-verificatie](authentication-mfa-ssms-overview.md)voor meer informatie.

### <a name="create-guest-user-in-sql-managed-instance"></a>Gast gebruiker maken in SQL-beheerd exemplaar

> [!NOTE]
> SQL Managed instance ondersteunt aanmeldingen voor Azure AD-gebruikers, evenals Azure AD data base-gebruikers. In de onderstaande stappen ziet u hoe u een aanmelding en gebruiker maakt voor een Azure AD-gast gebruiker in een SQL Managed instance. U kunt er ook voor kiezen om een [Inge sloten database gebruiker](/sql/relational-databases/security/contained-database-users-making-your-database-portable) te maken in een SQL-beheerd exemplaar met behulp van de methode in de sectie [gast gebruiker maken in SQL database en Azure Synapse](#create-guest-user-in-sql-database-and-azure-synapse) .

1. Zorg ervoor dat de gast gebruiker (bijvoorbeeld `user1@gmail.com` ) al is toegevoegd aan uw Azure AD en dat er een Azure AD-beheerder is ingesteld voor de SQL Managed instance server. Een Azure AD-beheerder is vereist voor Azure Active Directory-verificatie.

1. Maak verbinding met de SQL Managed instance server als de Azure AD-beheerder of een Azure AD-gebruiker met voldoende SQL-machtigingen voor het maken van gebruikers, en voer de volgende opdracht uit op de `master` Data Base om een aanmelding voor de gast gebruiker te maken:

    ```sql
    CREATE LOGIN [user1@gmail.com] FROM EXTERNAL PROVIDER
    ```

1. Er moet nu een aanmelding worden gemaakt voor de gast gebruiker `user1@gmail.com` in de `master` Data Base.

1. Voer de onderstaande opdracht uit om te controleren of de aanmelding is gemaakt:

    ```sql
    SELECT * FROM sys.server_principals
    ```

1. Voer de onderstaande opdracht uit in de data base waar de gast gebruiker moet worden toegevoegd: 

    ```sql
    CREATE USER [user1@gmail.com] FROM LOGIN [user1@gmail.com]
    ```

1. Er moet nu een database gebruiker zijn gemaakt voor de gast gebruiker `user1@gmail.com` .

1. Verbreek de verbinding met en meld u aan bij de Data Base als de gast gebruiker `user1@gmail.com` die gebruikmaakt van [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) met behulp van de verificatie methode **Azure Active Directory-Universal met MFA**. Zie [using multi-factor Azure Active Directory-verificatie](authentication-mfa-ssms-overview.md)voor meer informatie.

## <a name="setting-a-guest-user-as-an-azure-ad-admin"></a>Een gast gebruiker instellen als een Azure AD-beheerder

Volg deze stappen om een Azure AD-gast gebruiker in te stellen als de Azure AD-beheerder voor de logische SQL-Server.

### <a name="set-azure-ad-admin-for-sql-database-and-azure-synapse"></a>Azure AD-beheerder instellen voor SQL Database en Azure Synapse

1. Zorg ervoor dat de gast gebruiker (bijvoorbeeld `user1@gmail.com` ) al is toegevoegd aan uw Azure AD.

1. Voer de volgende Power shell-opdracht uit om de gast gebruiker toe te voegen als Azure AD-beheerder voor uw logische Azure SQL-Server:

    - Vervang door de `<ResourceGroupName>` naam van uw Azure-resource groep die de logische Azure SQL-Server bevat.
    - Vervang door `<ServerName>` de naam van uw logische Azure SQL-Server. Als de servernaam `myserver.database.windows.net` is, vervangt u `<Server Name>` door `myserver`.
    - Vervang door `<DisplayNameOfGuestUser>` de gebruikers naam van uw gast.

    ```powershell
    Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -ServerName <ServerName> -DisplayName <DisplayNameOfGuestUser>
    ```

    U kunt ook de Azure CLI-opdracht [AZ SQL Server AD-admin](/cli/azure/sql/server/ad-admin) gebruiken om de gast gebruiker in te stellen als een Azure AD-beheerder voor uw logische Azure SQL-Server.

### <a name="set-azure-ad-admin-for-sql-managed-instance"></a>Azure AD-beheerder instellen voor SQL-beheerd exemplaar

1. Zorg ervoor dat de gast gebruiker (bijvoorbeeld `user1@gmail.com` ) al is toegevoegd aan uw Azure AD.

1. Ga naar de [Azure Portal](https://portal.azure.com)en ga naar uw **Azure Active Directory** -resource. Ga onder **beheren** naar het deel venster **gebruikers** . Selecteer uw gast gebruiker en noteer de `Object ID` . 

1. Voer de volgende Power shell-opdracht uit om de gast gebruiker toe te voegen als de Azure AD-beheerder voor uw SQL Managed instance:

    - Vervang door de `<ResourceGroupName>` naam van uw Azure-resource groep die het SQL Managed instance bevat.
    - Vervang door `<ManagedInstanceName>` de naam van uw door SQL beheerde exemplaar.
    - Vervang door `<DisplayNameOfGuestUser>` de gebruikers naam van uw gast.
    - Vervang door `<AADObjectIDOfGuestUser>` de `Object ID` eerder verzamelde versie.

    ```powershell
    Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName <ResourceGroupName> -InstanceName "<ManagedInstanceName>" -DisplayName <DisplayNameOfGuestUser> -ObjectId <AADObjectIDOfGuestUser>
    ```

    U kunt ook de Azure CLI-opdracht [AZ SQL mi AD-admin](/cli/azure/sql/mi/ad-admin) gebruiken om de gast gebruiker in te stellen als een Azure AD-beheerder voor uw SQL Managed instance.

## <a name="limitations"></a>Beperkingen

Er is een beperking voor de Azure Portal waarmee wordt voor komen dat een Azure AD-gast gebruiker wordt geselecteerd als Azure AD-beheerder voor een door SQL beheerd exemplaar. Voor gast accounts buiten uw Azure AD, zoals *\@ Outlook.com*, *\@ Hotmail.com*, *\@ Live.com* of *\@ Gmail.com*, worden deze accounts in de AD-beheer kiezer weer gegeven, maar ze zijn grijs en kunnen niet worden geselecteerd. Gebruik de hierboven vermelde [Power shell-of cli-opdrachten](#setting-a-guest-user-as-an-azure-ad-admin) om de Azure AD-beheerder in te stellen. Een Azure AD-groep met de gast gebruiker kan ook worden ingesteld als de Azure AD-beheerder voor het SQL Managed instance.

Deze functionaliteit wordt ingeschakeld voor SQL Managed instance vóór de algemene Beschik baarheid van deze functie.

## <a name="next-steps"></a>Volgende stappen

- [Azure AD-verificatie configureren en beheren met Azure SQL](authentication-aad-configure.md)
- [Multi-factor Azure Active Directory-verificatie gebruiken](authentication-mfa-ssms-overview.md)
- [GEBRUIKER maken (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)