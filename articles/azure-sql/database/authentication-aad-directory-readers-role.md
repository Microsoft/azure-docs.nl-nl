---
title: Rol Directory Readers in Azure Active Directory voor Azure SQL
description: Meer informatie over de functie van de adreslijst lezer in azure AD voor Azure SQL.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: 5764a8df862610fc076ce2810fcc0d4bf8dbda3c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094553"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Rol Directory Readers in Azure Active Directory voor Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> Deze functie in dit artikel is beschikbaar als **open bare preview**.

Azure Active Directory (Azure AD) heeft [het gebruik van Cloud groepen geïntroduceerd om roltoewijzingen in azure Active Directory (preview-versie) te beheren](../../active-directory/roles/groups-concept.md). Hierdoor kunnen Azure AD-rollen worden toegewezen aan groepen.

Wanneer u een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) inschakelt voor Azure SQL database, Azure SQL Managed instance of Azure Synapse Analytics, moet de rol van Azure AD- [**adreslijst lezers**](../../active-directory/roles/permissions-reference.md#directory-readers) worden toegewezen aan de identiteit om Lees toegang tot de [Azure AD-Graph API](../../active-directory/develop/active-directory-graph-api.md)toe te staan. De beheerde identiteit van SQL Database en Azure Synapse wordt aangeduid als de server identiteit. De beheerde identiteit van het beheerde exemplaar van SQL wordt aangeduid als de identiteit van het beheerde exemplaar en wordt automatisch toegewezen wanneer het exemplaar wordt gemaakt. Zie [service-principals inschakelen voor het maken van Azure AD-gebruikers](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)voor meer informatie over het toewijzen van een server identiteit aan SQL database of Azure Synapse.

De functie voor het **lezers van mappen** is nodig voor het volgende:

- Azure AD-aanmeldingen voor SQL Managed instance maken
- Azure AD-gebruikers imiteren in Azure SQL
- Migreer SQL Server gebruikers die gebruikmaken van Windows-verificatie naar een SQL Managed instance met Azure AD-verificatie (met behulp van de opdracht [Alter User (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) )
- De Azure AD-beheerder voor het beheerde exemplaar van SQL wijzigen
- [Service-principals (toepassingen)](authentication-aad-service-principal.md) toestaan om Azure AD-gebruikers te maken in Azure SQL

## <a name="assigning-the-directory-readers-role"></a>De rol van de map lezers toewijzen

Als u de rol van de [**Directory lezers**](../../active-directory/roles/permissions-reference.md#directory-readers) wilt toewijzen aan een identiteit, is een gebruiker met Administrator machtigingen voor de rol [globale beheerder](../../active-directory/roles/permissions-reference.md#global-administrator) of [privileged](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) vereist. Gebruikers die vaak SQL Database, een door SQL beheerd exemplaar of Azure Synapse beheren of implementeren, hebben geen toegang tot deze uiterst privilegede rollen. Dit kan vaak leiden tot complicaties voor gebruikers die niet-geplande Azure SQL-resources maken, of hulp nodig hebben van leden met een hoog privilege die vaak niet toegankelijk zijn in grote organisaties.

Voor SQL Managed Instance moet de rol **Directory Readers** zijn toegewezen aan de beheerd-exemplaaridentiteit, voordat u [een Azure AD-beheerder voor het beheerd exemplaar kunt instellen](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

Het is niet vereist de rol **Directory Readers** toe te wijzen aan de serveridentiteit voor SQL Database of Azure Synapse bij het instellen van een Azure AD-beheerder voor de logische server. De rol **Directory Readers** is echter wel vereist om een Azure AD-object te kunnen maken in SQL Database of Azure Synapse namens een Azure AD-toepassing. Als de rol niet is toegewezen aan de SQL-logische-serveridentiteit zal het niet lukken Azure AD-gebruikers te maken in Azure SQL. Raadpleeg [Azure Active Directory-service-principal met Azure SQL](authentication-aad-service-principal.md) voor meer informatie.

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>De rol van de Directory lezers toekennen aan een Azure AD-groep

In de **open bare preview-versie** kunt u nu [](../../active-directory/roles/permissions-reference.md#global-administrator) een Azure AD- [groep maken en](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) de [**Directory lezers**](../../active-directory/roles/permissions-reference.md#directory-readers) machtigingen toewijzen aan de groep. Hiermee krijgt u toegang tot de Azure AD-Graph API voor leden van deze groep. Bovendien mogen Azure AD-gebruikers die eigen aren van deze groep zijn, nieuwe leden voor deze groep toewijzen, met inbegrip van de identiteiten van de logische Azure SQL-servers.

Voor deze oplossing is nog steeds een gebruiker met hoge bevoegdheden (globale beheerder of beheerdersrol) vereist voor het maken van een groep en het toewijzen van gebruikers als een eenmalige activiteit, maar de eigenaar van de Azure AD-groep kan extra leden toewijzen aan de volgende. Dit elimineert de nood zaak om een gebruiker met hoge bevoegdheden in de toekomst te betrekken bij het configureren van alle SQL-data bases, SQL Managed instances of Azure Synapse-servers in hun Azure AD-Tenant.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: De rol van Directory Readers toewijzen aan een Azure AD-groep en roltoewijzingen beheren](authentication-aad-directory-readers-role-tutorial.md)