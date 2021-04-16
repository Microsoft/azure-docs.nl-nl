---
title: Azure Active Directory-verificatie configureren
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Leer hoe u verbinding maakt met SQL Database, SQL Managed Instance en Azure Synapse Analytics met behulp van Azure Active Directory-verificatie nadat u Azure AD hebt geconfigureerd.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: 5894defca5a90f1d8cd7f312f47a37df6495ccd3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376168"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Azure AD-verificatie configureren en beheren met Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In dit artikel wordt beschreven hoe u een Azure Active Directory-exemplaar (Azure AD) maakt en vult en vervolgens Azure AD gebruikt met [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Zie Verificatie Azure Active Directory [voor een overzicht.](authentication-aad-overview.md)

## <a name="azure-ad-authentication-methods"></a>Azure AD-verificatiemethoden

Azure AD-verificatie ondersteunt de volgende verificatiemethoden:

- Azure AD-identiteiten in de cloud
- Hybride Azure AD-identiteiten die ondersteuning bieden voor:
  - Cloudverificatie met twee opties in combinatie met naadloze eenmalige aanmelding (SSO)
    - Verificatie van Azure AD-wachtwoordhash
    - Pass-through-verificatie in Azure AD
  - Federatieve verificatie

Zie Choose the right authentication method for your Azure Active Directory hybrid identity solution (De juiste verificatiemethode kiezen voor uw hybride identiteitsoplossing) voor meer informatie [over Azure AD-verificatiemethoden en welke u moet kiezen.](../../active-directory/hybrid/choose-ad-authn.md)

Zie voor meer informatie over hybride Identiteiten, installatie en synchronisatie van Azure AD:

- Wachtwoordhashverificatie: [synchronisatie van wachtwoordhashsynchronisatie met Azure AD Connect implementeren](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Pass-through-verificatie [- Azure Active Directory pass-through-verificatie](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federatieverificatie: [implementatie van Active Directory Federation Services in Azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) en Azure AD Connect en [federatie](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Een Azure AD-exemplaar maken en vullen

Maak een Azure AD-exemplaar en vul dit met gebruikers en groepen. Azure AD kan het eerste beheerde Azure AD-domein zijn. Azure AD kan ook een on-premises Active Directory Domain Services zijn die federatief is met Azure AD.

Zie [Uw on-premises identiteiten integreren met Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Uw domeinnaam toevoegen in Azure AD](../../active-directory/fundamentals/add-custom-domain.md), [Microsoft Azure ondersteunt nu federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Uw Azure AD-adreslijst beheren](../../active-directory/fundamentals/active-directory-whatis.md), [Azure AD beheren met Windows PowerShell](/powershell/azure/) en [Poorten en protocollen waarvoor hybride identiteit is vereist](../../active-directory/hybrid/reference-connect-ports.md) voor meer informatie.

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Een Azure-abonnement aan Azure Active Directory koppelen of toevoegen

1. Koppel uw Azure-abonnement aan Azure Active Directory door van de directory een vertrouwde directory te maken voor het Azure-abonnement dat als host voor de database wordt gebruikt. Zie Een [Azure-abonnement koppelen of toevoegen aan uw](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)Azure Active Directory tenant voor meer informatie.

2. Gebruik de directorywisselaar in de Azure Portal over te schakelen naar het abonnement dat is gekoppeld aan het domein.

   > [!IMPORTANT]
   > Voor elk Azure-abonnement is er een vertrouwensrelatie met een Azure AD-exemplaar. Dit betekent dat er op die directory wordt vertrouwd voor het verifiëren van gebruikers, services en apparaten. Meerdere abonnementen kunnen dezelfde directory vertrouwen, maar een abonnement vertrouwt slechts één directory. De vertrouwensrelatie die een abonnement heeft met een directory is anders dan de relatie die een abonnement heeft met andere resources in Azure (websites, databases, enzovoort); deze resources lijken meer op onderliggende resources van een abonnement. Als een abonnement is verlopen, wordt toegang tot de andere resources die zijn gekoppeld aan het abonnement ook geblokkeerd. De directory blijft echter wel aanwezig in Azure, en u kunt er een ander abonnement aan koppelen om de directorygebruikers te blijven beheren. Zie Inzicht in resourcetoegang in Azure voor [meer informatie over resources.](../../active-directory/external-identities/add-users-administrator.md) Zie How to associate or add an Azure subscription to Azure Active Directory (Een Azure-abonnement koppelen of toevoegen aan een Azure Active Directory) voor meer informatie [over deze vertrouwde relatie.](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Azure AD-beheerder met een server in SQL Database

Elke [server](logical-servers.md) in Azure (die als host SQL Database of Azure Synapse) begint met één serverbeheerdersaccount dat de beheerder van de hele server is. Maak een tweede beheerdersaccount als Een Azure AD-account. Deze principal wordt gemaakt als een ingesloten databasegebruiker in de hoofddatabase van de server. Beheerdersaccounts zijn lid van **de db_owner** in elke gebruikersdatabase en voeren elke gebruikersdatabase in als **de dbo-gebruiker.** Zie Databases en aanmeldingen beheren voor meer informatie [over beheerdersaccounts.](logins-create-manage.md)

Wanneer u Azure Active Directory geo-replicatie gebruikt, moet de Azure Active Directory-beheerder worden geconfigureerd voor zowel de primaire als de secundaire servers. Als een server geen Azure Active Directory heeft, Azure Active Directory aanmeldingen en ontvangen gebruikers een foutmelding `Cannot connect` bij de server.

> [!NOTE]
> Gebruikers die niet zijn gebaseerd op een Azure AD-account (inclusief het serverbeheerdersaccount), kunnen geen op Azure AD gebaseerde gebruikers maken, omdat ze geen machtigingen hebben om voorgestelde databasegebruikers te valideren met De Azure AD.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Azure AD-beheerder inrichten (SQL Managed Instance)

> [!IMPORTANT]
> Volg deze stappen alleen als u een Azure SQL Managed Instance. Deze bewerking kan alleen worden uitgevoerd door een globale beheerder of een beheerder met bevoorrechte rol in Azure AD.
>
> In **de openbare preview** kunt u de rol Directory Readers **toewijzen** aan een groep in Azure AD. De groepseigenaren kunnen vervolgens de identiteit van het beheerde exemplaar toevoegen als lid van deze groep, zodat u een Azure AD-beheerder kunt inrichten voor de SQL Managed Instance. Zie [Rol Directory Readers in Azure Active Directory voor Azure SQL](authentication-aad-directory-readers-role.md) voor meer informatie over deze functie.

Uw SQL Managed Instance machtigingen nodig om Azure AD te lezen om taken uit te voeren, zoals verificatie van gebruikers via lidmaatschap van beveiligingsgroep of het maken van nieuwe gebruikers. Om dit te laten werken, moet u de gebruiker SQL Managed Instance om Azure AD te lezen. U kunt dit doen met behulp van Azure Portal of PowerShell.

### <a name="azure-portal"></a>Azure Portal

Als u uw SQL Managed Instance Azure AD-leesmachtiging wilt verlenen met behulp van de Azure Portal, meldt u zich aan als globale beheerder in Azure AD en volgt u deze stappen:

1. Selecteer in [Azure Portal](https://portal.azure.com)in de rechterbovenhoek uw verbinding in een vervolgkeuzelijst met mogelijke Active Directories.

2. Kies de juiste Active Directory als de standaard-Azure AD.

   Met deze stap koppelt u het abonnement dat is gekoppeld aan Active Directory aan de SQL Managed Instance, om ervoor te zorgen dat hetzelfde abonnement wordt gebruikt voor zowel het Azure AD-exemplaar als het SQL Managed Instance.

3. Navigeer naar de SQL Managed Instance u wilt gebruiken voor Azure AD-integratie.

   ![Schermopname van de Azure Portal met de pagina Active Directory-beheerder die is geopend voor het geselecteerde beheerde SQL-exemplaar.](./media/authentication-aad-configure/aad.png)

4. Selecteer de banner boven aan de pagina Active Directory-beheerder en verleen toestemming aan de huidige gebruiker.

    ![Schermopname van het dialoogvenster voor het verlenen van machtigingen aan een met SQL beheerd exemplaar voor toegang tot Active Directory. De knop Machtigingen verlenen is geselecteerd.](./media/authentication-aad-configure/grant-permissions.png)

5. Nadat de bewerking is geslaagd, wordt de volgende melding in de rechterbovenhoek weer geven:

    ![Schermopname van een melding die bevestigt dat de leesmachtigingen voor Active Directory zijn bijgewerkt voor het beheerde exemplaar.](./media/authentication-aad-configure/success.png)

6. U kunt nu uw Azure AD-beheerder kiezen voor uw SQL Managed Instance. Selecteer op de pagina Active Directory-beheerder de optie **Beheeropdracht** instellen.

    ![Schermopname van de gemarkeerde opdracht Beheerder instellen op de pagina Active Directory-beheerder voor het geselecteerde beheerde SQL-exemplaar.](./media/authentication-aad-configure/set-admin.png)

7. Zoek op de pagina Azure AD-beheerder naar een gebruiker, selecteer de gebruiker of groep die beheerder moet worden en selecteer **vervolgens Selecteren.**

   Op de pagina Active Directory-beheerder worden alle leden en groepen van uw Active Directory weergegeven. Gebruikers of groepen die niet beschikbaar zijn, kunnen niet worden geselecteerd omdat ze niet worden ondersteund als Azure AD-beheerders. Zie de lijst met ondersteunde beheerders in Functies en beperkingen [van Azure AD.](authentication-aad-overview.md#azure-ad-features-and-limitations) Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) is alleen van toepassing op de Azure Portal en wordt niet doorgegeven aan SQL Database, SQL Managed Instance of Azure Synapse.

    ![Een Azure Active Directory toevoegen](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. Selecteer bovenaan de pagina Active Directory-beheerder de optie **Opslaan**.

    ![Schermopname van de Active Directory-beheerderspagina met de knop Opslaan in de bovenste rij naast de knoppen Beheerder instellen en Beheerder verwijderen.](./media/authentication-aad-configure/save.png)

    Het wijzigen van de beheerder kan enkele minuten duren. Vervolgens wordt de nieuwe beheerder weergegeven in het vak Active Directory-beheerder.

Nadat u een Azure AD-beheerder voor uw SQL Managed Instance hebt ingericht, kunt u beginnen met het maken van Azure AD-server-principals (aanmeldingen) met de [syntaxis CREATE LOGIN.](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) Zie overzicht SQL Managed Instance [voor meer informatie.](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)

> [!TIP]
> Als u later een beheerder wilt verwijderen, selecteert u bovenaan de pagina Active Directory-beheerder de optie **Beheerder** verwijderen en selecteert u **vervolgens Opslaan.**

### <a name="powershell"></a>PowerShell

Als u uw SQL Managed Instance Azure AD-leesmachtiging wilt verlenen met behulp van PowerShell, moet u dit script uitvoeren:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Global Administrator" or "Privileged Role Administrator" type of user.

$aadTenant = "<YourTenantId>" # Enter your tenant ID
$managedInstanceName = "MyManagedInstance"

# Get Azure AD role "Directory Users" and create if it doesn't exist
$roleName = "Directory Readers"
$role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
if ($role -eq $null) {
    # Instantiate an instance of the role template
    $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
    Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
}

# Get service principal for your SQL Managed Instance
$roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
$roleMember.Count
if ($roleMember -eq $null) {
    Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1)) {
    Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}

# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

if ($selDirReader -eq $null) {
    # Add principal to readers role
    Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

    #Write-Output "Dumping service principal '$($managedInstanceName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else {
    Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
}
```

### <a name="powershell-for-sql-managed-instance"></a>PowerShell voor SQL Managed Instance

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u PowerShell-cmdlets wilt uitvoeren, moet Azure PowerShell geïnstalleerd en uitgevoerd. Zie [How to install and configure Azure PowerShell](/powershell/azure/) (Azure PowerShell installeren en configureren) voor gedetailleerde informatie.

> [!IMPORTANT]
> De PowerShell Azure Resource Manager -module (RM) wordt nog steeds ondersteund door Azure SQL Managed Instance, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

Als u een Azure AD-beheerder wilt inrichten, voert u de volgende Azure PowerShell uit:

- Connect-AzAccount
- Select-AzSubscription

De cmdlets die worden gebruikt voor het inrichten en beheren van de Azure AD-beheerder SQL Managed Instance worden vermeld in de volgende tabel:

| Naam van cmdlet | Description |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Er wordt een Azure AD-beheerder voor de SQL Managed Instance in het huidige abonnement. (Moet van het huidige abonnement zijn)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Hiermee verwijdert u een Azure AD-beheerder voor de SQL Managed Instance in het huidige abonnement. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Retourneert informatie over een Azure AD-beheerder voor de SQL Managed Instance in het huidige abonnement.|

De volgende opdracht haalt informatie op over een Azure AD-beheerder voor een SQL Managed Instance met de naam ManagedInstance01 die is gekoppeld aan een resourcegroep met de naam ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Met de volgende opdracht wordt een Azure AD-beheerdersgroep met de naam DDE's voor de SQL Managed Instance met de naam ManagedInstance01. Deze server is gekoppeld aan de resourcegroep ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Met de volgende opdracht verwijdert u de Azure AD-beheerder voor de SQL Managed Instance met de naam ManagedInstanceName01 die is gekoppeld aan de resourcegroep ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt ook een Azure AD-beheerder voor de SQL Managed Instance door de volgende CLI-opdrachten aan te roepen:

| Opdracht | Beschrijving |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Een Azure Active Directory beheerder in voor de SQL Managed Instance (moet van het huidige abonnement zijn). |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Hiermee verwijdert u Azure Active Directory beheerder voor de SQL Managed Instance. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Retourneert informatie over een Azure Active Directory die momenteel is geconfigureerd voor de SQL Managed Instance. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Werkt de Active Directory-beheerder voor de SQL Managed Instance. |

Zie az sql mi voor meer informatie [over CLI-opdrachten.](/cli/azure/sql/mi)

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Azure AD-beheerder inrichten (SQL Database)

> [!IMPORTANT]
> Volg deze stappen alleen als u een [server inrichten](logical-servers.md) voor SQL Database of Azure Synapse.

De volgende twee procedures laten zien hoe u een Azure Active Directory-beheerder voor uw server in de Azure Portal en met behulp van PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Ga naar de [Azure-portal](https://portal.azure.com/) en selecteer in de rechterbovenhoek uw verbinding om een lijst met mogelijke Active Directories weer te geven. Kies de juiste Active Directory als de standaard-Azure AD. Met deze stap koppelt u de aan het abonnement gekoppelde Active Directory aan de server om ervoor te zorgen dat hetzelfde abonnement wordt gebruikt voor zowel Azure AD als de server.

2. Zoek en selecteer **SQL Server.**

    ![SQL-servers zoeken en selecteren](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Voordat u op deze pagina **SQL-servers**  selecteert, kunt  u de ster naast de naam selecteren om de categorie aan uw favorieten toe te voegen en **SQL-servers** toe te voegen aan de linkernavigatiebalk.

3. Selecteer op **SQL Server** pagina Active **Directory-beheerder.**

4. Selecteer op **de pagina Active Directory-beheerder** de optie **Beheerder instellen.**

    ![SQL-servers stellen Active Directory-beheerder in](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. Zoek op de pagina **Beheerder toevoegen** een gebruiker. Selecteer de gebruiker of groep die beheerder moet zijn en selecteer **Selecteren**. (Op de pagina Active Directory-beheerder ziet u alle leden en groepen van uw Active Directory.) Gebruikers of groepen die grijs zijn gekleurd, kunnen niet worden geselecteerd omdat ze niet worden ondersteund als beheerders voor Azure AD. (Zie de lijst met ondersteunde beheerders in de sectie Functies en beperkingen van **Azure AD** [Azure Active Directory-verificatie](authentication-aad-overview.md)gebruiken voor verificatie met SQL Database of Azure Synapse .) Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) is alleen van toepassing op de portal en wordt niet doorgegeven aan SQL Server.

    ![Selecteer Azure Active Directory beheerder](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. Selecteer bovenaan de pagina **Active Directory-beheerder** de optie **Opslaan**.

    ![beheerder opslaan](./media/authentication-aad-configure/save-admin.png)

Het wijzigen van de beheerder kan enkele minuten duren. Vervolgens wordt de nieuwe beheerder weergegeven in het vak **Active Directory-beheerder**.

   > [!NOTE]
   > Bij het instellen van de Azure AD-beheerder kan de nieuwe beheerdersnaam (gebruiker of groep) niet als serververificatiegebruiker al aanwezig zijn in de virtuele hoofddatabase. Indien wel aanwezig, mislukt het instellen van de Azure AD-beheerder, waarna het maken ervan wordt teruggedraaid en er wordt aangegeven dat deze beheerder (naam) al bestaat. Omdat een dergelijke serververificatiegebruiker geen deel uitmaakt van Azure AD, kan er geen verbinding worden gemaakt met de server met behulp van Azure AD-verificatie.

Als u later een beheerder wilt verwijderen, selecteert u bovenaan de **pagina Active Directory-beheerder** de optie **Beheerder** verwijderen en selecteert u **vervolgens Opslaan.**

### <a name="powershell-for-sql-database-and-azure-synapse"></a>PowerShell voor SQL Database en Azure Synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u PowerShell-cmdlets wilt uitvoeren, moet Azure PowerShell geïnstalleerd en uitgevoerd. Zie [How to install and configure Azure PowerShell](/powershell/azure/) (Azure PowerShell installeren en configureren) voor gedetailleerde informatie. Als u een Azure AD-beheerder wilt inrichten, voert u de volgende Azure PowerShell uit:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets die worden gebruikt om de Azure AD-beheerder in te SQL Database en Azure Synapse:

| Naam van cmdlet | Description |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Een beheerder Azure Active Directory voor de server die als host SQL Database of Azure Synapse. (Moet van het huidige abonnement zijn) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Hiermee verwijdert u een Azure Active Directory-beheerder voor de server die als host SQL Database of Azure Synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retourneert informatie over een Azure Active Directory die momenteel is geconfigureerd voor de server die als host SQL Database of Azure Synapse. |

Gebruik PowerShell-opdracht get-help voor meer informatie over elk van deze opdrachten. Bijvoorbeeld `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Met het volgende script wordt een Azure AD-beheerdersgroep met de **naam DBA_Group** (object-id ) voor de `40b79501-b343-44ed-9ce7-da4c8cc7353f` **demo_server-server** in een resourcegroep met de naam **Group-23 gemaakt:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

De **invoerparameter DisplayName** accepteert de Weergavenaam van Azure AD of de User Principal Name. Bijvoorbeeld ``DisplayName="John Smith"`` en ``DisplayName="johns@contoso.com"``. Voor Azure AD-groepen wordt alleen de Weergavenaam van Azure AD ondersteund.

> [!NOTE]
> De Azure PowerShell voorkomt ```Set-AzSqlServerActiveDirectoryAdministrator``` niet dat u Azure AD-beheerders kunt inrichten voor niet-ondersteunde gebruikers. Een niet-ondersteunde gebruiker kan worden ingericht, maar kan geen verbinding maken met een database.

In het volgende voorbeeld wordt de optionele **ObjectID gebruikt:**

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> De Azure AD **ObjectID** is vereist als **de DisplayName** niet uniek is. Als u de **waarden ObjectID** en **DisplayName** wilt ophalen, gebruikt u de sectie Active Directory van de klassieke Azure-portal en bekijkt u de eigenschappen van een gebruiker of groep.

Het volgende voorbeeld retourneert informatie over de huidige Azure AD-beheerder voor de server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

In het volgende voorbeeld wordt een Azure AD-beheerder verwijderd:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U kunt een Azure AD-beheerder inrichten door de volgende CLI-opdrachten aan te roepen:

| Opdracht | Beschrijving |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Een beheerder Azure Active Directory voor de server die als host SQL Database of Azure Synapse. (Moet afkomstig zijn van het huidige abonnement) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Hiermee verwijdert u een Azure Active Directory-beheerder voor de server die als host SQL Database of Azure Synapse. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Retourneert informatie over een Azure Active Directory die momenteel is geconfigureerd voor de server die als host SQL Database of Azure Synapse. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Werkt de Active Directory-beheerder bij voor de server die als host SQL Database of Azure Synapse. |

Zie az sql server voor meer informatie [over CLI-opdrachten.](/cli/azure/sql/server)

* * *

> [!NOTE]
> U kunt ook een beheerder Azure Active Directory met behulp van de REST API's. Zie Service Management REST API [Reference and Operations for Azure SQL Database Operations for Azure SQL Database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Uw clientcomputers configureren

Op alle clientmachines, van waaruit uw toepassingen of gebruikers verbinding maken met SQL Database of Azure Synapse azure AD-identiteiten, moet u de volgende software installeren:

- .NET Framework 4.6 of hoger van [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- Azure Active Directory Authentication Library for SQL Server (*ADAL.DLL*). Hieronder vindt u de downloadkoppelingen om het meest recente SSMS-, ODBC- en OLE DB-stuurprogramma met de *ADAL.DLL* installeren.
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC-stuurprogramma 17 voor SQL Server](/sql/connect/odbc/download-odbc-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)
  - [OLE DB stuurprogramma 18 voor SQL Server](/sql/connect/oledb/download-oledb-driver-for-sql-server?view=sql-server-ver15&preserve-view=true)

U kunt aan deze vereisten voldoen door:

- Het installeren van de nieuwste versie van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) of [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) voldoet aan .NET Framework 4.6-vereiste.
  - SSMS installeert de x86-versie *vanADAL.DLL*.
  - SSDT installeert de amd64-versie van *ADAL.DLL*.
  - De meest recente Visual Studio van [Visual Studio Downloads voldoet](https://www.visualstudio.com/downloads/download-visual-studio-vs) aan de .NET Framework 4.6-vereiste, maar installeert niet de vereiste amd64-versie *vanADAL.DLL*.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Ingesloten gebruikers maken die zijn toegewezen aan Azure AD-identiteiten

Omdat SQL Managed Instance ondersteuning biedt voor Azure AD-server-principals (aanmeldingen), is het niet vereist om ingesloten databasegebruikers te gebruiken. Met Azure AD-serverprincipals (aanmeldingen) kunt u aanmeldingen maken van Azure AD-gebruikers, -groepen of -toepassingen. Dit betekent dat u kunt verifiëren met uw SQL Managed Instance met behulp van de aanmeldingsgegevens van de Azure AD-server in plaats van een ingesloten databasegebruiker. Zie overzicht van SQL Managed Instance [voor meer informatie.](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration) Zie CREATE LOGIN voor syntaxis voor het maken van Azure AD-server-principals [(aanmeldingen).](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true)

Het gebruik van Azure Active Directory verificatie met SQL Database en Azure Synapse vereist echter het gebruik van ingesloten databasegebruikers op basis van een Azure AD-identiteit. Een ingesloten databasegebruiker heeft geen aanmelding in de hoofddatabase en wordt gekoppeld aan een identiteit in Azure AD die is gekoppeld aan de database. De Azure AD-identiteit kan een individueel gebruikersaccount of een groep zijn. Zie [Contained Database Users- Making Your Database Portable](/sql/relational-databases/security/contained-database-users-making-your-database-portable) (Gebruikers van ingesloten databases: een draagbare database maken) voor meer informatie over gebruikers van ingesloten databases.

> [!NOTE]
> Databasegebruikers kunnen niet in de Azure-portal worden gemaakt (met uitzondering van beheerders). Azure-rollen worden niet doorgegeven aan de database in SQL Database, de SQL Managed Instance of Azure Synapse. Azure-rollen worden gebruikt voor het beheren van Azure-resources en zijn niet van toepassing op databasemachtigingen. De rol **Inzender SQL Server** verleent bijvoorbeeld geen toegang om verbinding te maken met de database in SQL Database, de SQL Managed Instance of Azure Synapse. De toestemming tot het verlenen van toegang moet rechtstreeks plaatsvinden in de database met behulp van Transact-SQL-instructies.

> [!WARNING]
> Speciale tekens zoals dubbele punt of en-teken wanneer ze zijn opgenomen als gebruikersnamen `:` in de T-SQL en instructies `&` worden niet `CREATE LOGIN` `CREATE USER` ondersteund.

Als u een op Azure AD gebaseerde databasegebruiker wilt maken (anders dan de serverbeheerder die eigenaar is van de database), maakt u als gebruiker met ten minste de machtiging **ALTER ANY USER** verbinding met de database met een Azure AD-identiteit. Gebruik vervolgens de volgende Transact-SQL-syntaxis:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kan de user principal name van een Azure AD-gebruiker of de weergavenaam voor een Azure AD-groep zijn.

**Voorbeelden:** Een ingesloten databasegebruiker maken die een federatief of beheerd domeingebruiker van Azure AD vertegenwoordigt:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Als u een ingesloten databasegebruiker wilt maken die een Azure AD- of federatief domeingroep vertegenwoordigt, geeft u de weergavenaam van een beveiligingsgroep op:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Een ingesloten databasegebruiker maken die een toepassing vertegenwoordigt die verbinding maakt met behulp van een Azure AD-token:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Voor deze opdracht moet SQL toegang hebben tot Azure AD (de 'externe provider') namens de aangemelde gebruiker. Soms ontstaan er omstandigheden die ertoe leiden dat Azure AD een uitzondering terug naar SQL retournt. In dergelijke gevallen ziet de gebruiker SQL-fout 33134, die het azure AD-specifieke foutbericht moet bevatten. In de meeste tijd wordt in de fout gezegd dat de toegang wordt geweigerd of dat de gebruiker zich moet inschrijven voor MFA om toegang te krijgen tot de resource, of dat de toegang tussen toepassingen van de eerste partij moet worden afgehandeld via vooraf-autorisatie. In de eerste twee gevallen wordt het probleem meestal veroorzaakt door beleid voor voorwaardelijke toegang dat is ingesteld in de Azure AD-tenant van de gebruiker: hiermee wordt voorkomen dat de gebruiker toegang heeft tot de externe provider. Het bijwerken van het beleid voor voorwaardelijke toegang om toegang tot de toepassing '00000002-0000-0000-c000-0000000000000' (de toepassings-id van de Azure AD-Graph API) toe te staan, moet het probleem oplossen. In het geval dat de fout meldt dat de toegang tussen toepassingen van de eerste partij moet worden afgehandeld via vooraf-autorisatie, is het probleem omdat de gebruiker is aangemeld als een service-principal. De opdracht slaagt als deze wordt uitgevoerd door een gebruiker.

> [!TIP]
> U kunt niet rechtstreeks een gebruiker maken van een Azure Active Directory dan de Azure Active Directory die is gekoppeld aan uw Azure-abonnement. Leden van andere Active Directory's die zijn geïmporteerde gebruikers in de gekoppelde Active Directory (ook wel externe gebruikers genoemd) kunnen echter worden toegevoegd aan een Active Directory-groep in de tenant Active Directory. Door een ingesloten databasegebruiker voor die AD-groep te maken, kunnen de gebruikers van de externe Active Directory toegang krijgen tot SQL Database.

Zie CREATE [USER (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql)voor meer informatie over het maken van ingesloten databasegebruikers op basis van Azure Active Directory-identiteiten.

> [!NOTE]
> Als u de Azure Active Directory voor de server verwijdert, voorkomt u dat een Azure AD-verificatiegebruiker verbinding maakt met de server. Indien nodig kunnen onwerkbare Azure AD-gebruikers handmatig worden uitgevallen door een SQL Database beheerder.

> [!NOTE]
> Als u een **Time-out voor verbinding** verlopen ontvangt, moet u mogelijk de parameter van de connection string `TransparentNetworkIPResolution` ingesteld op false. Zie Verbindings [time-outprobleem met .NET Framework 4.6.1 - TransparentNetworkIPResolution voor meer informatie.](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution)

Wanneer u een databasegebruiker maakt, ontvangt die gebruiker de **machtiging CONNECT** en kan deze verbinding maken met die database als lid van de **rol PUBLIC.** De enige machtigingen die voor de gebruiker beschikbaar zijn, zijn in eerste instantie machtigingen die zijn verleend aan de rol **PUBLIC** of machtigingen die worden verleend aan Azure AD-groepen waar ze lid van zijn. Zodra u een op Azure AD gebaseerde ingesloten databasegebruiker hebt ingericht, kunt u de gebruiker aanvullende machtigingen verlenen, op dezelfde manier als u machtigingen verleent aan elk ander type gebruiker. Verleen doorgaans machtigingen aan databaserollen en voeg gebruikers toe aan rollen. Zie Basisbeginselen van [database-enginemachtigingen voor meer informatie.](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx) Zie Databases en aanmeldingen beheren in SQL Database voor meer informatie over [speciale Azure SQL Database.](logins-create-manage.md)
Een federatief domeingebruikersaccount dat als externe gebruiker in een beheerd domein wordt geïmporteerd, moet de beheerde domeinidentiteit gebruiken.

> [!NOTE]
> Azure AD-gebruikers worden in de metagegevens van de database gemarkeerd met het type E (EXTERNAL_USER) en voor groepen met het type X (EXTERNAL_GROUPS). Zie [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql) voor meer informatie.

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Verbinding maken met de database met behulp van SSMS of SSDT  

Als u wilt controleren of de Azure AD-beheerder juist is ingesteld, maakt u verbinding met de **hoofddatabase** met behulp van het Azure AD-beheerdersaccount.
Als u een op Azure AD gebaseerde ingesloten databasegebruiker wilt inrichten (anders dan de serverbeheerder die eigenaar is van de database), maakt u verbinding met de database met een Azure AD-identiteit die toegang heeft tot de database.

> [!IMPORTANT]
> Ondersteuning voor Azure Active Directory verificatie is beschikbaar in [SQL Server 2016](/sql/ssms/download-sql-server-management-studio-ssms) Management Studio en [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) in Visual Studio 2015. De release van augustus 2016 van SSMS biedt ook ondersteuning voor universele Active Directory-verificatie, waarmee beheerders Multi-Factor Authentication kunnen vereisen met behulp van een telefoongesprek, sms-bericht, smartcards met pincode of meldingen via een mobiele app.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Een Azure AD-identiteit gebruiken om verbinding te maken met behulp van SSMS of SSDT

De volgende procedures laten zien hoe u verbinding maakt met SQL Database met een Azure AD-identiteit met behulp van SQL Server Management Studio of SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Geïntegreerde Active Directory-verificatie

Gebruik deze methode als u bent aangemeld bij Windows met behulp van uw Azure Active Directory-referenties van een federatief domein of een beheerd domein dat is geconfigureerd voor naadloze een aanmelding voor pass-through- en wachtwoordhashverificatie. Zie naadloze een Azure Active Directory [voor meer informatie.](../../active-directory/hybrid/how-to-connect-sso.md)

1. Start Management Studio of Data Tools en selecteer in het dialoogvenster Verbinding maken met  **server** (of Verbinding maken met **database-engine)** in het vak Verificatie de **optie Azure Active Directory - Geïntegreerd.** Er is geen wachtwoord nodig of kan worden ingevoerd omdat uw bestaande referenties voor de verbinding worden weergegeven.

   ![Geïntegreerde AD-verificatie selecteren][11]

2. Selecteer de **knop Opties**  en typ op de pagina Verbindingseigenschappen in het vak Verbinding maken met **database** de naam van de gebruikersdatabase waarmee u verbinding wilt maken. Zie het artikel Multi-Factor Azure AD auth on the differences between the Connection Properties for SSMS 17.x and 18.x [(Multi-Factor Azure AD-authenticatie](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) over de verschillen tussen de verbindingseigenschappen voor SSMS 17.x en 18.x) voor meer informatie.

   ![Selecteer de naam van de database][13]

### <a name="active-directory-password-authentication"></a>Active Directory-wachtwoordverificatie

Gebruik deze methode wanneer u verbinding maakt met een Principal Name van Azure AD met behulp van het beheerde Azure AD-domein. U kunt deze ook gebruiken voor federatief accounts zonder toegang tot het domein, bijvoorbeeld wanneer u op afstand werkt.

Gebruik deze methode om te verifiëren bij de database in SQL Database of de SQL Managed Instance met azure AD-cloudidentiteitsgebruikers of degenen die gebruikmaken van hybride Azure AD-identiteiten. Deze methode ondersteunt gebruikers die hun Windows-referenties willen gebruiken, maar hun lokale computer niet is verbonden met het domein (bijvoorbeeld via externe toegang). In dit geval kan een Windows-gebruiker zijn of haar domeinaccount en wachtwoord opgeven en zich verifiëren bij de database in SQL Database, de SQL Managed Instance of Azure Synapse.

1. Start Management Studio of Data Tools en selecteer in het dialoogvenster Verbinding maken met  **server** (of Verbinding maken met **database-engine)** in het vak Verificatie de optie **Azure Active Directory - Wachtwoord.**

2. Typ in **het vak Gebruikersnaam** uw gebruikersnaam Azure Active Directory in de notatie gebruikersnaam **\@ domain.com**. Gebruikersnamen moeten een account zijn van Azure Active Directory of een account van een beheerd of federatief domein met Azure Active Directory.

3. Typ in **het vak** Wachtwoord uw gebruikerswachtwoord voor het Azure Active Directory of het beheerde/federatief domeinaccount.

    ![AD-wachtwoordverificatie selecteren][12]

4. Selecteer de **knop** Opties  en typ op de pagina Verbindingseigenschappen in het vak Verbinding maken met **database** de naam van de gebruikersdatabase waarmee u verbinding wilt maken. (Zie de afbeelding in de vorige optie.)

### <a name="active-directory-interactive-authentication"></a>Interactieve verificatie van Active Directory

Gebruik deze methode voor interactieve verificatie met of zonder Multi-Factor Authentication (MFA), waarbij het wachtwoord interactief wordt aangevraagd. Deze methode kan worden gebruikt voor verificatie bij de database in SQL Database, de SQL Managed Instance en Azure Synapse voor identiteitsgebruikers in de cloud van Azure AD of gebruikers die gebruikmaken van hybride Azure AD-identiteiten.

Zie [Multi-Factor Azure AD-verificatie gebruiken met SQL Database en Azure Synapse (SSMS-ondersteuning voor MFA) voor meer informatie.](authentication-mfa-ssms-overview.md)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Een Azure AD-identiteit gebruiken om verbinding te maken vanuit een clienttoepassing

De volgende procedures laten zien hoe u vanuit een clienttoepassing verbinding maakt met een SQL Database met een Azure AD-identiteit.

### <a name="active-directory-integrated-authentication"></a>Geïntegreerde Active Directory-verificatie

Als u geïntegreerde Windows-verificatie wilt gebruiken, moet de Active Directory van uw domein zijn ge federeerd met Azure Active Directory, of moet het een beheerd domein zijn dat is geconfigureerd voor naadloze een aanmelding voor pass-through- of wachtwoordhashverificatie. Zie naadloze een Azure Active Directory [voor meer informatie.](../../active-directory/hybrid/how-to-connect-sso.md)

> [!NOTE]
> [MSAL.NET (Microsoft.Identity.Client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) voor geïntegreerde Windows-verificatie wordt niet ondersteund voor naadloze een aanmelding voor pass-through- en wachtwoordhashverificatie.

Uw clienttoepassing (of een service) die verbinding maakt met de database moet worden uitgevoerd op een computer die lid is van een domein onder de domeinreferenties van een gebruiker.

Als u verbinding wilt maken met een database met behulp van geïntegreerde verificatie en een Azure AD-identiteit, moet het trefwoord Verificatie in de database connection string ingesteld op `Active Directory Integrated` . In het volgende C#-codevoorbeeld wordt ADO .NET gebruikt.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Het connection string `Integrated Security=True` sleutelwoord wordt niet ondersteund om verbinding te maken met Azure SQL Database. Wanneer u een ODBC-verbinding maakt, moet u spaties verwijderen en Verificatie instellen op ActiveDirectoryIntegrated.

### <a name="active-directory-password-authentication"></a>Active Directory-wachtwoordverificatie

Als u verbinding wilt maken met een database met behulp van gebruikersaccounts voor identiteiten in de cloud van Azure AD of gebruikers die gebruikmaken van hybride Azure AD-identiteiten, moet het trefwoord Verificatie zijn ingesteld op `Active Directory Password` . De connection string moeten de trefwoorden en waarden User ID/UID en Password/PWD bevatten. In het volgende C#-codevoorbeeld wordt ADO .NET gebruikt.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Meer informatie over Azure AD-verificatiemethoden met behulp van de democodevoorbeelden die beschikbaar zijn [op Azure AD Authentication GitHub Demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD-token

Met deze verificatiemethode kunnen services in de middelste laag [JSON-webtokens (JWT)](../../active-directory/develop/id-tokens.md) verkrijgen om verbinding te maken met de database in SQL Database, de SQL Managed Instance of Azure Synapse door een token te verkrijgen van Azure AD. Deze methode maakt verschillende toepassingsscenario's mogelijk, waaronder service-id's, service-principals en toepassingen die gebruikmaken van verificatie op basis van certificaten. U moet vier basisstappen uitvoeren om Azure AD-tokenverificatie te gebruiken:

1. Registreer uw toepassing bij Azure Active Directory en haal de client-id voor uw code op.
2. Maak een databasegebruiker die de toepassing vertegenwoordigt. (Eerder in stap 6 voltooid.)
3. Maak een certificaat op de clientcomputer en voert de toepassing uit.
4. Voeg het certificaat toe als sleutel voor uw toepassing.

Voorbeeld van connection string:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Zie voor meer informatie [SQL Server Security Blog](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth). Zie Aan de slag met verificatie op basis van certificaten in Azure Active Directory voor meer informatie over [het toevoegen Azure Active Directory.](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)

### <a name="sqlcmd"></a>sqlcmd

De volgende instructies maken verbinding met behulp van versie 13.1 van sqlcmd, die beschikbaar is via [het Downloadcentrum.](https://www.microsoft.com/download/details.aspx?id=53591)

> [!NOTE]
> `sqlcmd` met de `-G` opdracht werkt niet met systeemidentiteiten en vereist een user principal-aanmelding.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Problemen met Azure AD-verificatie oplossen

In de volgende blog vindt u richtlijnen voor het oplossen van problemen met Azure AD-verificatie: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Volgende stappen

- Zie [Aanmeldingen, gebruikers, databaserollen](logins-create-manage.md)en gebruikersaccounts voor een overzicht van aanmeldingen, gebruikers, databaserollen en machtigingen in SQL Database.
- Zie [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine) voor meer informatie over database-principals.
- Zie [Databaserollen](/sql/relational-databases/security/authentication-access/database-level-roles) voor meer informatie over databaserollen.
- Zie [SQL Database-firewallregels](firewall-configure.md) voor meer informatie over de firewallregels in SQL Database.
- Zie Azure AD-gastgebruikers maken en instellen als Een Azure AD-beheerder voor meer informatie over het instellen van een Azure [AD-gastgebruiker als Azure AD-beheerder.](authentication-aad-guest-users.md)
- Zie Create Azure AD users using Azure AD applications (Azure AD-gebruikers maken met behulp van Azure AD-toepassingen) voor meer informatie over het Azure SQL van [service-principals](authentication-aad-service-principal-tutorial.md)

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
