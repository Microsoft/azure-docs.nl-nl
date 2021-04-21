---
title: Azure Active Directory-service-principal met Azure SQL
description: Azure AD-toepassingen (service-principals) bieden ondersteuning voor het maken van Azure AD-gebruikers in Azure SQL Database, Azure SQL Managed Instance en Azure Synapse Analytics
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 02/11/2021
ms.openlocfilehash: 17e846c7435e2f1cc77c5915f7e0b308c3706f96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775417"
---
# <a name="azure-active-directory-service-principal-with-azure-sql"></a>Azure Active Directory-service-principal met Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Ondersteuning voor Azure Active Directory (Azure AD) maken van gebruikers in Azure SQL Database (SQL DB) en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) namens Azure AD-toepassingen (service-principals) zijn momenteel in **openbare preview.**

> [!NOTE]
> Deze functionaliteit wordt al ondersteund voor SQL Managed Instance.

## <a name="service-principal-azure-ad-applications-support"></a>Ondersteuning voor service-principals (Azure AD-toepassingen)

Dit artikel is van toepassing op toepassingen die zijn geïntegreerd met Azure AD en die deel uitmaken van Azure AD-registratie. Deze toepassingen hebben vaak verificatie- en autorisatietoegang tot Azure SQL om verschillende taken uit te voeren. Met deze functie in **openbare preview** kunnen service-principals nu Azure AD-gebruikers maken in SQL Database en Azure Synapse. Er is een beperking waardoor azure AD-objecten niet meer kunnen worden gemaakt namens Azure AD-toepassingen die zijn verwijderd.

Wanneer een Azure AD-toepassing wordt geregistreerd met de Azure Portal of een PowerShell-opdracht, worden er twee objecten gemaakt in de Azure AD-tenant:

- Een toepassingsobject
- Een service-principal-object

Zie Application [and service principal objects in Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md) and Create an Azure service principal with Azure PowerShell (Een [Azure-service-principal](/powershell/azure/create-azure-service-principal-azureps)maken met Azure PowerShell) voor meer informatie over Azure AD-toepassingen.

SQL Database, Azure Synapse en SQL Managed Instance de volgende Azure AD-objecten ondersteunen:

- Azure AD-gebruikers (beheerd, federatief en gast)
- Azure AD-groepen (beheerd en federatief)
- Azure AD-toepassingen 

De T-SQL-opdracht namens een Azure AD-toepassing wordt nu ondersteund voor SQL Database `CREATE USER [Azure_AD_Object] FROM EXTERNAL PROVIDER` en Azure Synapse.

## <a name="functionality-of-azure-ad-user-creation-using-service-principals"></a>Functionaliteit van het maken van Azure AD-gebruikers met behulp van service-principals

Het ondersteunen van deze functionaliteit is handig in Azure AD-processen voor toepassingsautomatisering waarbij Azure AD-objecten worden gemaakt en onderhouden in SQL Database en Azure Synapse zonder menselijke tussenkomst. Service-principals kunnen een Azure AD-beheerder voor de logische SQL-server zijn als onderdeel van een groep of een afzonderlijke gebruiker. De toepassing kan het maken van Azure AD-objecten in SQL Database en Azure Synapse automatiseren wanneer deze wordt uitgevoerd als systeembeheerder en vereist geen extra SQL-bevoegdheden. Hierdoor kunt u een databasegebruiker volledig automatiseren. Deze functie wordt ook ondersteund voor door het systeem toegewezen beheerde identiteit en door de gebruiker toegewezen beheerde identiteit. Zie Wat zijn beheerde identiteiten [voor Azure-resources? voor meer informatie.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="enable-service-principals-to-create-azure-ad-users"></a>Service-principals inschakelen om Azure AD-gebruikers te maken

Als u het maken van een Azure AD-object in SQL Database en Azure Synapse namens een Azure AD-toepassing wilt inschakelen, zijn de volgende instellingen vereist:

1. Wijs de serveridentiteit toe. De toegewezen serveridentiteit vertegenwoordigt de Managed Service Identity (MSI). Momenteel biedt de serveridentiteit voor Azure SQL geen ondersteuning voor door de gebruiker beheerde identiteit (UMI).
    - Voer voor een Azure SQL logische server de volgende PowerShell-opdracht uit:
    
    ```powershell
    New-AzSqlServer -ResourceGroupName <resource group> -Location <Location name> -ServerName <Server name> -ServerVersion "12.0" -SqlAdministratorCredentials (Get-Credential) -AssignIdentity
    ```

    Zie de opdracht [New-AzSqlServer voor meer](/powershell/module/az.sql/new-azsqlserver) informatie.

    - Voer voor Azure SQL logische servers de volgende opdracht uit:
    
    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <Server name> -AssignIdentity
    ```

    Zie de opdracht [Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver) voor meer informatie.

    - Als u wilt controleren of de serveridentiteit is toegewezen aan de server, voert u de Get-AzSqlServer uit.

    > [!NOTE]
    > Serveridentiteit kan ook worden toegewezen met CLI-opdrachten. Zie az [sql server create](/cli/azure/sql/server#az_sql_server_create) en az sql server update voor meer [informatie.](/cli/azure/sql/server#az_sql_server_update)

2. Verleen de Azure AD [**Directory Readers toestemming**](../../active-directory/roles/permissions-reference.md#directory-readers) voor de serveridentiteit die is gemaakt of toegewezen aan de server.
    - Als u deze machtiging wilt verlenen, volgt u de beschrijving die wordt gebruikt voor SQL Managed Instance die beschikbaar is in het volgende artikel: [Azure AD-beheerder inrichten (SQL Managed Instance)](authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-managed-instance)
    - De Azure AD-gebruiker die deze machtiging verleent, moet deel uitmaken van de rol Globale beheerder van Azure **AD** of **Beheerder met bevoorrechte** rollen.

> [!IMPORTANT]
> Stap 1 en 2 moeten in de bovenstaande volgorde worden uitgevoerd. Maak eerst de serveridentiteit of wijs deze toe, gevolgd door het verlenen van de [**machtiging Adreslijstlezers.**](../../active-directory/roles/permissions-reference.md#directory-readers) Als u een van deze stappen weglaten, veroorzaakt dit een uitvoeringsfout tijdens het maken van een Azure AD-object in Azure SQL namens een Azure AD-toepassing.
>
> Als u de service-principal gebruikt om de Azure AD-beheerder in of uit te stellen, moet de toepassing ook de machtiging [Directory.Read.All](/graph/permissions-reference#application-permissions-18) Application API hebben in Azure AD. Zie [Zelfstudie: Azure AD-gebruikers](authentication-aad-service-principal-tutorial.md)maken met Behulp van Azure AD-toepassingen voor meer informatie over machtigingen die vereist zijn om een [Azure AD-beheerder](authentication-aad-service-principal-tutorial.md#permissions-required-to-set-or-unset-the-azure-ad-admin)in te stellen en stapsgewijs instructies voor het maken van een Azure AD-gebruiker namens een Azure AD-toepassing.
>
> In **de openbare preview** kunt u de rol Directory Readers **toewijzen** aan een groep in Azure AD. De groepseigenaren kunnen vervolgens de beheerde identiteit toevoegen als lid van  deze groep, waardoor het niet nodig is dat een globale beheerder of beheerder met bevoorrechte rollen de rol **Directory Readers** verleent.  Zie [Rol Directory Readers in Azure Active Directory voor Azure SQL](authentication-aad-directory-readers-role.md) voor meer informatie over deze functie.

## <a name="troubleshooting-and-limitations-for-public-preview"></a>Problemen met en beperkingen voor openbare preview oplossen

- Bij het maken van Azure AD-objecten in Azure SQL namens een Azure AD-toepassing zonder serveridentiteit in te stellen en directory **readers** toestemming te verlenen, mislukt de bewerking met de volgende mogelijke fouten. De onderstaande voorbeeldfout is voor het uitvoeren van een PowerShell-opdracht voor het maken van een SQL Database gebruiker in het artikel Zelfstudie: Azure AD-gebruikers maken met behulp van `myapp` [Azure AD-toepassingen.](authentication-aad-service-principal-tutorial.md)
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "'myapp' is not a valid login or you do not have permission. Cannot find the user 'myapp', because it does not exist, or you do not have permission."`
    - `Exception calling "ExecuteNonQuery" with "0" argument(s): "Principal 'myapp' could not be resolved.`
    - `User or server identity does not have permission to read from Azure Active Directory.`
      - Voor de bovenstaande fout volgt u de stappen om een identiteit toe te wijzen aan de [logische Azure SQL-server](authentication-aad-service-principal-tutorial.md#assign-an-identity-to-the-azure-sql-logical-server) en de machtiging Maplezers toewijzen aan de [logische SQL-serveridentiteit](authentication-aad-service-principal-tutorial.md#assign-directory-readers-permission-to-the-sql-logical-server-identity).
    > [!NOTE]
    > De hierboven beschreven foutberichten worden vóór de functie-GA gewijzigd om duidelijk de ontbrekende installatievereiste voor ondersteuning van Azure AD-toepassingen te identificeren.
- Het instellen van de Azure AD-toepassing als een Azure AD-beheerder voor SQL Managed Instance wordt alleen ondersteund met behulp van de CLI-opdracht en PowerShell-opdracht met [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) of hoger. Zie de opdrachten [az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az_sql_mi_ad_admin_create) en [Set-AzSqlInstanceActiveDirectoryAdministrator voor](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) meer informatie. 
    - Als u de azure ad Azure Portal voor SQL Managed Instance wilt gebruiken om de Azure AD-beheerder in te stellen, is een mogelijke tijdelijke oplossing om een Azure AD-groep te maken. Voeg vervolgens de service-principal (Azure AD-toepassing) toe aan deze groep en stel deze groep in als een Azure AD-beheerder voor de SQL Managed Instance.
    - Het instellen van de service-principal (Azure AD-toepassing) als een Azure AD-beheerder voor SQL Database en Azure Synapse wordt ondersteund met behulp van de opdrachten Azure Portal, [PowerShell](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)en [CLI.](authentication-aad-configure.md?tabs=azure-cli#powershell-for-sql-database-and-azure-synapse)
- Het gebruik van een Azure AD-toepassing met een service-principal van een andere Azure AD-tenant mislukt bij het openen van SQL Database of SQL Managed Instance gemaakt in een andere tenant. Een service-principal die aan deze toepassing is toegewezen, moet afkomstig zijn van dezelfde tenant als de logische SQL-server of het beheerde exemplaar.
- De module [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) of hoger is vereist, wanneer u PowerShell gebruikt om een afzonderlijke Azure AD-toepassing in te stellen als Azure AD-beheerder voor Azure SQL. Zorg ervoor dat u een upgrade uitvoert naar de nieuwste module.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure AD-gebruikers maken met behulp van Azure AD-toepassingen](authentication-aad-service-principal-tutorial.md)
