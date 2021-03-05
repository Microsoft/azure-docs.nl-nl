---
title: Azure Active Directory-service-principal met Azure SQL
description: Azure AD-toepassingen (Service-principals) ondersteunen het maken van Azure AD-gebruikers in Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 15cc935457f76fb1d2fe4e8d699db831ebacc357
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181751"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory-service-principal met Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ondersteuning voor het maken van Azure Active Directory (Azure AD) in Azure SQL Database (SQL DB) en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) namens Azure AD-toepassingen (Service-principals) zijn momenteel beschikbaar als **open bare preview**.

> [!NOTE]
> Deze functionaliteit wordt al ondersteund voor SQL Managed instance.

## <a name="service-principal-azure-ad-applications-support"></a>Service-Principal (Azure AD-toepassingen) ondersteunen

Dit artikel is van toepassing op toepassingen die zijn geïntegreerd met Azure AD en die deel uitmaken van de Azure AD-registratie. Voor deze toepassingen is vaak verificatie-en autorisatie toegang tot Azure SQL vereist om verschillende taken uit te voeren. Met deze functie in **open bare preview** kunnen service-principals Azure AD-gebruikers maken in SQL database en Azure Synapse. Er is een beperking opgetreden bij het maken van Azure AD-objecten uit naam van Azure AD-toepassingen die zijn verwijderd.

Wanneer een Azure AD-toepassing is geregistreerd met behulp van de Azure Portal of een Power shell-opdracht, worden er twee objecten gemaakt in de Azure AD-Tenant:

- Een toepassingsobject
- Een service-principal-object

Zie voor meer informatie over Azure AD-toepassingen [toepassings-en Service-Principal-objecten in azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) en [een Azure-service-principal maken met Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

SQL Database, Azure Synapse en SQL Managed instance ondersteunen de volgende Azure AD-objecten:

- Azure AD-gebruikers (beheerd, federatief en gast)
- Azure AD-groepen (beheerd en federatief)
- Azure AD-toepassingen 

De T-SQL-opdracht namens `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` een Azure AD-toepassing wordt nu ondersteund voor SQL database en Azure Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Functionaliteit van het maken van Azure AD-gebruikers met Service-principals

Ondersteuning van deze functionaliteit is handig in azure AD-toepassingen voor automatisering waarbij Azure AD-objecten worden gemaakt en onderhouden in SQL Database en Azure Synapse zonder menselijke interactie. Service-principals kunnen een Azure AD-beheerder zijn voor de logische SQL-Server, als onderdeel van een groep of een afzonderlijke gebruiker. De toepassing kan het maken van Azure AD-objecten in SQL Database en Azure Synapse automatiseren wanneer deze wordt uitgevoerd als een systeem beheerder en vereist geen aanvullende SQL-bevoegdheden. Dit biedt een volledige automatisering van het maken van een database gebruiker. Deze functie wordt ook ondersteund voor door het systeem toegewezen beheerde identiteit en door de gebruiker toegewezen beheerde identiteit. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Service-principals inschakelen voor het maken van Azure AD-gebruikers

Als u het maken van een Azure AD-object in SQL Database en Azure Synapse namens een Azure AD-toepassing wilt inschakelen, zijn de volgende instellingen vereist:

1. Wijs de server identiteit toe. De toegewezen server identiteit vertegenwoordigt de beheerde systeem identiteit (MSI). Op dit moment biedt de server identiteit voor Azure SQL geen ondersteuning voor door de gebruiker beheerde identiteit (UMI).
    - Voor een nieuwe logische Azure SQL-Server voert u de volgende Power shell-opdracht uit:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Zie de opdracht [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) voor meer informatie.

    - Voor bestaande logische Azure SQL-servers voert u de volgende opdracht uit:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Zie de opdracht [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) voor meer informatie.

    - Als u wilt controleren of de server identiteit is toegewezen aan de server, voert u de Get-AzSqlServer opdracht uit.

    > [!NOTE]
    > U kunt ook de server identiteit toewijzen met behulp van CLI-opdrachten. Zie [AZ SQL Server Create](/cli/azure/sql/server#az-sql-server-create) en [AZ SQL Server Update](/cli/azure/sql/server#az-sql-server-update)(Engelstalig) voor meer informatie.

2. Verleen de Azure AD- [**adreslijst lezers**](../../active-directory/roles/permissions-reference.md#directory-readers) toestemming voor de server identiteit die is gemaakt of toegewezen aan de server.
    - Als u deze machtiging wilt verlenen, volgt u de beschrijving die wordt gebruikt voor SQL Managed instance die beschikbaar is in het volgende artikel: [Azure AD-beheerder (SQL Managed instance) inrichten](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - De Azure AD-gebruiker die deze machtiging verleent, moet deel uitmaken van de beheerdersrol Azure AD **Global Administrator** of **privileged roles** .

> [!IMPORTANT]
> Stap 1 en 2 moeten in de bovenstaande volg orde worden uitgevoerd. Eerst moet u de server identiteit maken of toewijzen, gevolgd door de machtiging voor het verlenen van de [**Directory lezers**](../../active-directory/roles/permissions-reference.md#directory-readers) . Als een van deze stappen wordt wegge laten, of beide een uitvoerings fout veroorzaken tijdens het maken van een Azure AD-object in Azure SQL namens een Azure AD-toepassing.
>
> Als u de Service-Principal gebruikt om de Azure AD-beheerder in te stellen of op te heffen, moet de toepassing ook over de [Directory. alle](/graph/permissions-reference#application-permissions-18) API-machtigingen voor toepassingen in azure AD beschikken. Zie [zelf studie: Azure AD-gebruikers maken met Azure AD-toepassingen](authentication-aad-service-principal-tutorial.md)voor meer informatie over de [vereiste machtigingen voor het instellen van een Azure AD-beheerder](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)en stapsgewijze instructies voor het maken van een Azure AD-gebruiker namens een Azure AD-toepassing.
>
> In de **open bare preview** kunt u de rol van de **Directory lezers** toewijzen aan een groep in azure AD. De groeps eigenaren kunnen vervolgens de beheerde identiteit toevoegen als lid van deze groep, waardoor de beheerder van een **globale beheerder** of **bevoorrechte** rol niet nodig is om de functie **Directory lezers** te verlenen. Zie [Rol Directory Readers in Azure Active Directory voor Azure SQL](authentication-aad-directory-readers-role.md) voor meer informatie over deze functie.

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Problemen oplossen en beperkingen voor open bare preview-versie

- Bij het maken van Azure AD-objecten in Azure SQL namens een Azure AD-toepassing zonder de server identiteit in te scha kelen en **Directory lezers** toestemming te verlenen, mislukt de bewerking met de volgende mogelijke fouten. In het volgende voor beeld wordt een Power shell-opdracht uitgevoerd voor het maken van een SQL Database gebruiker `myapp` in de zelf studie over het artikel [: Azure AD-gebruikers maken met Azure AD-toepassingen](authentication-aad-service-principal-tutorial.md).
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - Voor de bovenstaande fout volgt u de stappen voor het [toewijzen van een identiteit aan de logische Azure SQL-Server](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) en het toewijzen van [Directory lezers machtigingen voor de id van de logische SQL-Server](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > De hierboven vermelde fout berichten worden gewijzigd voordat het onderdeel GA de ontbrekende installatie vereisten voor de ondersteuning van Azure AD-toepassingen duidelijk te identificeren.
- Het instellen van de Azure AD-toepassing als een Azure AD-beheerder voor SQL Managed instance wordt alleen ondersteund met de CLI-opdracht en de Power shell-opdracht met [AZ. SQL 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) of hoger. Zie de opdrachten [AZ SQL mi AD-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) and [set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) voor meer informatie. 
    - Als u de Azure Portal voor SQL Managed instance wilt gebruiken om de Azure AD-beheerder in te stellen, is een mogelijke oplossing een Azure AD-groep te maken. Voeg vervolgens de Service-Principal (Azure AD-toepassing) toe aan deze groep en stel deze groep in als een Azure AD-beheerder voor het SQL Managed instance.
    - Het instellen van de Service-Principal (Azure AD-toepassing) als een Azure AD-beheerder voor SQL Database en Azure Synapse wordt ondersteund met behulp van de Azure Portal-, [Power shell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)-en [cli](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse) -opdrachten.
- Het gebruik van een Azure AD-toepassing met Service-Principal van een andere Azure AD-Tenant mislukt wanneer toegang wordt verkregen tot SQL Database of een SQL-beheerd exemplaar dat in een andere Tenant is gemaakt. Een service-principal die is toegewezen aan deze toepassing moet afkomstig zijn van dezelfde Tenant als de logische SQL-Server of het beheerde exemplaar.
- De module [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) of hoger is vereist, wanneer u PowerShell gebruikt om een afzonderlijke Azure AD-toepassing in te stellen als Azure AD-beheerder voor Azure SQL. Zorg ervoor dat u een upgrade uitvoert naar de nieuwste module.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure AD-gebruikers maken met behulp van Azure AD-toepassingen](authentication-aad-service-principal-tutorial.md)