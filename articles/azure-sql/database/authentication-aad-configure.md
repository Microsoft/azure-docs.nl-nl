---
title: Azure Active Directory-verificatie configureren
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Meer informatie over hoe u verbinding maakt met SQL Database, SQL Managed instance en Azure Synapse Analytics met behulp van Azure Active Directory-verificatie, nadat u Azure AD hebt geconfigureerd.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse, has-adal-ref, sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, sstein
ms.date: 08/17/2020
ms.openlocfilehash: 5558480f568e802637deb30c9f1b41c00826c9b5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96454492"
---
# <a name="configure-and-manage-azure-ad-authentication-with-azure-sql"></a>Azure AD-verificatie configureren en beheren met Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In dit artikel wordt beschreven hoe u een Azure Active Directory-exemplaar (Azure AD) maakt en vult en vervolgens Azure AD gebruikt met [Azure SQL database](sql-database-paas-overview.md), [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md)en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Zie [Azure Active Directory-verificatie](authentication-aad-overview.md)voor een overzicht.

## <a name="azure-ad-authentication-methods"></a>Azure AD-verificatie methoden

Azure AD-verificatie ondersteunt de volgende verificatie methoden:

- Alleen Azure AD-Cloud-identiteiten
- Hybride Azure AD-identiteiten die ondersteuning bieden voor:
  - Cloud verificatie met twee opties in combi natie met naadloze eenmalige aanmelding (SSO)
    - Verificatie van wacht woord-hash voor Azure AD
    - Pass-Through-verificatie van Azure AD
  - Federatieve verificatie

Zie [de juiste verificatie methode kiezen voor uw Azure Active Directory hybride identiteits oplossing](../../active-directory/hybrid/choose-ad-authn.md)voor meer informatie over Azure AD-verificatie methoden en welke u er een kunt kiezen.

Zie voor meer informatie over de hybride identiteiten, het instellen en synchroniseren van Azure AD:

- Wachtwoord verificatie voor wacht woord- [hash-synchronisatie implementeren met Azure AD Connect Sync](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- Pass-Through-verificatie- [Azure Active Directory Pass-Through-verificatie](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)
- Federatieve verificatie- [Active Directory Federation Services implementeren in azure](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) en [Azure AD Connect en Federatie](../../active-directory/hybrid/how-to-connect-fed-whatis.md)

## <a name="create-and-populate-an-azure-ad-instance"></a>Een Azure AD-exemplaar maken en vullen

Een Azure AD-exemplaar maken en vullen met gebruikers en groepen. Azure AD kan het eerste door Azure AD beheerde domein zijn. Azure AD kan ook een on-premises Active Directory Domain Services zijn die federatief is voor Azure AD.

Zie [Uw on-premises identiteiten integreren met Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [Uw domeinnaam toevoegen in Azure AD](../../active-directory/fundamentals/add-custom-domain.md), [Microsoft Azure ondersteunt nu federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Uw Azure AD-adreslijst beheren](../../active-directory/fundamentals/active-directory-whatis.md), [Azure AD beheren met Windows PowerShell](/powershell/azure/) en [Poorten en protocollen waarvoor hybride identiteit is vereist](../../active-directory/hybrid/reference-connect-ports.md) voor meer informatie.

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Een Azure-abonnement aan Azure Active Directory koppelen of toevoegen

1. Koppel uw Azure-abonnement aan Azure Active Directory door de Directory een vertrouwde Directory te maken voor het Azure-abonnement dat als host fungeert voor de data base. Zie [een Azure-abonnement koppelen aan of toevoegen aan uw Azure Active Directory-Tenant](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)voor meer informatie.

2. Gebruik de Directory wisselaar in de Azure Portal om over te scha kelen naar het abonnement dat is gekoppeld aan het domein.

   > [!IMPORTANT]
   > Voor elk Azure-abonnement is er een vertrouwensrelatie met een Azure AD-exemplaar. Dit betekent dat er op die directory wordt vertrouwd voor het verifiëren van gebruikers, services en apparaten. Meerdere abonnementen kunnen dezelfde directory vertrouwen, maar een abonnement vertrouwt slechts één directory. De vertrouwensrelatie die een abonnement heeft met een directory is anders dan de relatie die een abonnement heeft met andere resources in Azure (websites, databases, enzovoort); deze resources lijken meer op onderliggende resources van een abonnement. Als een abonnement is verlopen, wordt toegang tot de andere resources die zijn gekoppeld aan het abonnement ook geblokkeerd. De directory blijft echter wel aanwezig in Azure, en u kunt er een ander abonnement aan koppelen om de directorygebruikers te blijven beheren. Zie informatie over de [toegang tot resources in azure](../../active-directory/external-identities/add-users-administrator.md)voor meer informatie over resources. Zie [een Azure-abonnement koppelen aan of toevoegen aan Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)voor meer informatie over deze vertrouwde relatie.

## <a name="azure-ad-admin-with-a-server-in-sql-database"></a>Azure AD-beheerder met een server in SQL Database

Elke [Server](logical-servers.md) in azure (die als host fungeert voor SQL database of Azure Synapse) begint met één beheerders account van de server die de beheerder van de volledige server is. Maak een tweede beheerders account als een Azure AD-account. Deze principal is gemaakt als een Inge sloten database gebruiker in de hoofd database van de server. Beheerders accounts zijn leden van de **db_owner** rol in elke gebruikers database en voeren elke gebruikers database in als de **dbo** -gebruiker. Zie [data bases en aanmeldingen beheren](logins-create-manage.md)voor meer informatie over beheerders accounts.

Bij het gebruik van Azure Active Directory met geo-replicatie moet de Azure Active Directory-beheerder worden geconfigureerd voor zowel de primaire als de secundaire server. Als een server geen Azure Active Directory-beheerder heeft, Azure Active Directory aanmeldingen en gebruikers een `Cannot connect` Server fout ontvangen.

> [!NOTE]
> Gebruikers die niet zijn gebaseerd op een Azure AD-account (met inbegrip van het beheerders account van de server) kunnen geen gebruikers op basis van Azure AD maken, omdat ze geen toestemming hebben om voorgestelde database gebruikers te valideren met de Azure AD.

## <a name="provision-azure-ad-admin-sql-managed-instance"></a>Een Azure AD-beheerder (SQL Managed instance) inrichten

> [!IMPORTANT]
> Volg deze stappen alleen als u een door Azure SQL beheerd exemplaar inricht. Deze bewerking kan alleen worden uitgevoerd door de beheerder globaal/bedrijf of een bevoegde beheerdersrol in azure AD.
>
> In de **open bare preview** kunt u de rol van de **Directory lezers** toewijzen aan een groep in azure AD. De groeps eigenaren kunnen de identiteit van het beheerde exemplaar vervolgens toevoegen als lid van deze groep, zodat u een Azure AD-beheerder kunt inrichten voor het SQL Managed instance. Zie [Rol Directory Readers in Azure Active Directory voor Azure SQL](authentication-aad-directory-readers-role.md) voor meer informatie over deze functie.

Uw SQL Managed instance heeft machtigingen nodig voor het lezen van Azure AD om taken zoals de verificatie van gebruikers via lidmaatschap van een beveiligings groep of het maken van nieuwe gebruikers te kunnen uitvoeren. Om dit te laten werken, moet u de SQL Managed instance machtigen om Azure AD te lezen. U kunt dit doen met behulp van de Azure Portal of Power shell.

### <a name="azure-portal"></a>Azure Portal

Als u uw SQL Managed instance Azure AD-Lees machtiging wilt verlenen met de Azure Portal, meldt u zich aan als globaal/bedrijfs beheerder in azure AD en voert u de volgende stappen uit:

1. Selecteer in het [Azure Portal](https://portal.azure.com)in de rechter bovenhoek uw verbinding in een vervolg keuzelijst met mogelijke actieve directory's.

2. Kies de juiste Active Directory als de standaard-Azure AD.

   Met deze stap koppelt u het abonnement dat is gekoppeld aan Active Directory aan het beheerde exemplaar van SQL, en zorgt u ervoor dat hetzelfde abonnement wordt gebruikt voor zowel het Azure AD-exemplaar als het SQL Managed instance.

3. Navigeer naar het SQL-beheerde exemplaar dat u wilt gebruiken voor Azure AD-integratie.

   ![Scherm afbeelding van de Azure Portal waarop de Active Directory-beheer pagina wordt geopend voor het geselecteerde exemplaar van SQL Managed.](./media/authentication-aad-configure/aad.png)

4. Selecteer de banner boven op de pagina Active Directory beheer en Ken machtigingen toe aan de huidige gebruiker.

    ![Scherm afbeelding van het dialoog venster voor het verlenen van machtigingen aan een SQL Managed instance voor toegang tot Active Directory. De knop machtigingen verlenen is geselecteerd.](./media/authentication-aad-configure/grant-permissions.png)

5. Nadat de bewerking is voltooid, wordt in de rechter bovenhoek de volgende melding weer gegeven:

    ![Scherm opname van een melding waarin wordt bevestigd dat de Lees machtigingen van Active Directory zijn bijgewerkt voor het beheerde exemplaar.](./media/authentication-aad-configure/success.png)

6. U kunt nu uw Azure AD-beheerder kiezen voor uw SQL Managed instance. Selecteer op de pagina Active Directory-beheer de optie admin-opdracht **instellen** .

    ![Scherm opname van de opdracht set admin gemarkeerd op de pagina Active Directory-beheer voor het geselecteerde exemplaar van SQL Managed.](./media/authentication-aad-configure/set-admin.png)

7. Zoek op de pagina Azure AD-beheerder naar een gebruiker, selecteer de gebruiker of groep die u als beheerder wilt en selecteer vervolgens **selecteren**.

   Op de pagina Active Directory beheer worden alle leden en groepen van uw Active Directory weer gegeven. Gebruikers of groepen die grijs worden weer gegeven, kunnen niet worden geselecteerd, omdat ze niet worden ondersteund als Azure AD-Administrators. Zie de lijst met ondersteunde beheerders in [Azure AD-functies en-beperkingen](authentication-aad-overview.md#azure-ad-features-and-limitations). Azure RBAC (op rollen gebaseerd toegangs beheer) is alleen van toepassing op de Azure Portal en wordt niet door gegeven aan SQL Database, een beheerd exemplaar van SQL of Azure Synapse.

    ![Azure Active Directory beheerder toevoegen](./media/authentication-aad-configure/add-azure-active-directory-admin.png)

8. Selecteer bovenaan de pagina Active Directory-beheerder de optie **Opslaan**.

    ![Scherm afbeelding van de pagina Active Directory-beheer met de knop Opslaan in de bovenste rij naast de knoppen beheerder instellen en beheerder verwijderen.](./media/authentication-aad-configure/save.png)

    Het wijzigen van de beheerder kan enkele minuten duren. Vervolgens wordt de nieuwe beheerder weergegeven in het vak Active Directory-beheerder.

Nadat u een Azure AD-beheerder hebt ingericht voor uw SQL Managed instance, kunt u beginnen met het maken van Azure AD-server principals (aanmeldingen) met de syntaxis voor het maken van de <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">aanmelding</a> . Zie [overzicht van SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)voor meer informatie.

> [!TIP]
> Als u later een beheerder wilt verwijderen, selecteert u aan de bovenkant van de pagina Active Directory-beheer de optie **beheerder verwijderen** en selecteert u vervolgens **Opslaan**.

### <a name="powershell"></a>PowerShell

Als u uw SQL Managed instance Azure AD-Lees machtiging wilt verlenen met behulp van Power shell, voert u dit script uit:

```powershell
# Gives Azure Active Directory read permission to a Service Principal representing the SQL Managed Instance.
# Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

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

### <a name="powershell-for-sql-managed-instance"></a>Power shell voor door SQL beheerd exemplaar

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u Power shell-cmdlets wilt uitvoeren, moet Azure PowerShell zijn geïnstalleerd en worden uitgevoerd. Zie [How to install and configure Azure PowerShell](/powershell/azure/) (Azure PowerShell installeren en configureren) voor gedetailleerde informatie.

> [!IMPORTANT]
> De module Power shell Azure Resource Manager (RM) wordt nog steeds ondersteund door Azure SQL Managed instance, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

Voer de volgende Azure PowerShell opdrachten uit om een Azure AD-beheerder in te richten:

- Connect-AzAccount
- Select-AzSubscription

De cmdlets die worden gebruikt voor het inrichten en beheren van Azure AD-beheerder voor uw SQL Managed instance worden weer gegeven in de volgende tabel:

| Naam van cmdlet | Beschrijving |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Richt een Azure AD-beheerder in voor het beheerde exemplaar van SQL in het huidige abonnement. (Moet afkomstig zijn van het huidige abonnement)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Hiermee verwijdert u een Azure AD-beheerder voor het beheerde exemplaar van SQL in het huidige abonnement. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Retourneert informatie over een Azure AD-beheerder voor het beheerde exemplaar van SQL in het huidige abonnement.|

Met de volgende opdracht wordt informatie opgehaald over een Azure AD-beheerder voor een SQL Managed instance met de naam ManagedInstance01 die is gekoppeld aan een resource groep met de naam ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

Met de volgende opdracht wordt een Azure AD-Beheerders groep met de naam Dba's voor het SQL Managed instance met de naam ManagedInstance01 ingericht. Deze server is gekoppeld aan de ResourceGroup01 van de resource groep.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

Met de volgende opdracht wordt de Azure AD-beheerder voor het SQL Managed instance met de naam ManagedInstanceName01 die is gekoppeld aan de resource groep ResourceGroup01, verwijderd.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

U kunt ook een Azure AD-beheerder inrichten voor het beheerde exemplaar van SQL door de volgende CLI-opdrachten aan te roepen:

| Opdracht | Beschrijving |
| --- | --- |
|[AZ SQL mi AD-admin Create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | Voorziet in een Azure Active Directory beheerder voor het SQL Managed instance (moet afkomstig zijn van het huidige abonnement). |
|[AZ SQL mi AD-admin Delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | Hiermee verwijdert u een Azure Active Directory beheerder voor het SQL Managed instance. |
|[AZ SQL mi AD-administrator list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | Retourneert informatie over een Azure Active Directory-beheerder die momenteel is geconfigureerd voor het SQL Managed instance. |
|[AZ SQL mi AD-Admin Update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | Hiermee wordt de Active Directory-beheerder voor het beheerde exemplaar van SQL bijgewerkt. |

Zie [AZ SQL mi](/cli/azure/sql/mi)voor meer informatie over cli-opdrachten.

* * *

## <a name="provision-azure-ad-admin-sql-database"></a>Een Azure AD-beheerder (SQL Database) inrichten

> [!IMPORTANT]
> Volg deze stappen alleen als u een [Server](logical-servers.md) inricht voor SQL database of Azure Synapse.

In de volgende twee procedures wordt uitgelegd hoe u een Azure Active Directory beheerder voor uw server inricht in de Azure Portal en met behulp van Power shell.

### <a name="azure-portal"></a>Azure Portal

1. Ga naar de [Azure-portal](https://portal.azure.com/) en selecteer in de rechterbovenhoek uw verbinding om een lijst met mogelijke Active Directories weer te geven. Kies de juiste Active Directory als de standaard-Azure AD. Met deze stap koppelt u de Active Directory die aan het abonnement is gekoppeld aan de server om ervoor te zorgen dat hetzelfde abonnement wordt gebruikt voor zowel Azure AD als de server.

2. Zoek en selecteer **SQL Server**.

    ![SQL-servers zoeken en selecteren](./media/authentication-aad-configure/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > Op deze pagina kunt u, voordat u **SQL-servers** selecteert, de **ster** naast de naam selecteren om de categorie te *favoriet* en **SQL-servers** aan de linkernavigatiebalk toe te voegen.

3. Selecteer op de pagina **SQL Server** **Active Directory beheerder**.

4. Selecteer op de pagina **Active Directory-beheerder** de optie **beheerder instellen**.

    ![SQL-servers Active Directory beheerder instellen](./media/authentication-aad-configure/sql-servers-set-active-directory-admin.png)  

5. Zoek op de pagina **Beheerder toevoegen** een gebruiker. Selecteer de gebruiker of groep die beheerder moet zijn en selecteer **Selecteren**. (Op de pagina Active Directory-beheerder ziet u alle leden en groepen van uw Active Directory.) Gebruikers of groepen die grijs zijn gekleurd, kunnen niet worden geselecteerd omdat ze niet worden ondersteund als beheerders voor Azure AD. (Zie de lijst met ondersteunde beheerders in de sectie **functies en beperkingen van Azure AD** van het [gebruik van Azure Active Directory verificatie voor verificatie met SQL database of Azure Synapse](authentication-aad-overview.md).) Azure RBAC (op rollen gebaseerd toegangs beheer) is alleen van toepassing op de portal en wordt niet door gegeven aan SQL Server.

    ![Azure Active Directory beheerder selecteren](./media/authentication-aad-configure/select-azure-active-directory-admin.png)  

6. Selecteer boven aan de pagina **Active Directory-beheer** de optie **Opslaan**.

    ![beheerder opslaan](./media/authentication-aad-configure/save-admin.png)

Het wijzigen van de beheerder kan enkele minuten duren. Vervolgens wordt de nieuwe beheerder weergegeven in het vak **Active Directory-beheerder**.

   > [!NOTE]
   > Bij het instellen van de Azure AD-beheerder kan de nieuwe beheerders naam (gebruiker of groep) niet al aanwezig zijn in de virtuele hoofd database als server authenticatie gebruiker. Indien wel aanwezig, mislukt het instellen van de Azure AD-beheerder, waarna het maken ervan wordt teruggedraaid en er wordt aangegeven dat deze beheerder (naam) al bestaat. Omdat een dergelijke server authenticatie gebruiker geen deel uitmaakt van Azure AD, mislukt de poging om verbinding te maken met de server met behulp van Azure AD-verificatie.

Als u later een beheerder wilt verwijderen, selecteert u aan de bovenkant van de pagina **Active Directory-beheer** de optie **beheerder verwijderen** en selecteert u vervolgens **Opslaan**.

### <a name="powershell-for-sql-database-and-azure-synapse"></a>Power shell voor SQL Database en Azure Synapse

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u Power shell-cmdlets wilt uitvoeren, moet Azure PowerShell zijn geïnstalleerd en worden uitgevoerd. Zie [How to install and configure Azure PowerShell](/powershell/azure/) (Azure PowerShell installeren en configureren) voor gedetailleerde informatie. Voer de volgende Azure PowerShell opdrachten uit om een Azure AD-beheerder in te richten:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets die worden gebruikt voor het inrichten en beheren van Azure AD-beheerder voor SQL Database en Azure Synapse:

| Naam van cmdlet | Beschrijving |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Richt een Azure Active Directory beheerder in voor de server die als host fungeert voor SQL Database of Azure Synapse. (Moet afkomstig zijn van het huidige abonnement) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Hiermee verwijdert u een Azure Active Directory beheerder voor de server die als host fungeert voor SQL Database of Azure Synapse.|
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retourneert informatie over een Azure Active Directory-beheerder die momenteel is geconfigureerd voor de server die als host fungeert voor SQL Database of Azure Synapse. |

Gebruik de Power shell-opdracht Get-Help voor meer informatie over elk van deze opdrachten. Bijvoorbeeld `get-help Set-AzSqlServerActiveDirectoryAdministrator`.

Met het volgende script wordt een Azure AD-Beheerders groep met de naam **DBA_Group** (object-id `40b79501-b343-44ed-9ce7-da4c8cc7353f` ) ingericht voor de **demo_server** -server in een resource groep met de naam **groep-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

De para meter **DisplayName** invoer accepteert de Azure AD-weergave naam of de principal-naam van de gebruiker. Bijvoorbeeld ``DisplayName="John Smith"`` en ``DisplayName="johns@contoso.com"``. Voor Azure AD-groepen wordt alleen de Azure AD-weergave naam ondersteund.

> [!NOTE]
> De Azure PowerShell opdracht ```Set-AzSqlServerActiveDirectoryAdministrator``` voor komt niet dat u Azure AD-beheerders kunt inrichten voor niet-ondersteunde gebruikers. Een niet-ondersteunde gebruiker kan worden ingericht, maar kan geen verbinding maken met een Data Base.

In het volgende voor beeld wordt de optionele **ObjectID** gebruikt:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> De **ObjectID** voor Azure AD is vereist wanneer de **DisplayName** niet uniek is. Als u de waarden voor **ObjectID** en **DisplayName** wilt ophalen, gebruikt u de sectie Active Directory van klassieke Azure-Portal en bekijkt u de eigenschappen van een gebruiker of groep.

In het volgende voor beeld wordt informatie gegeven over de huidige Azure AD-beheerder voor de server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

In het volgende voor beeld wordt een Azure AD-beheerder verwijderd:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

U kunt een Azure AD-beheerder inrichten door de volgende CLI-opdrachten aan te roepen:

| Opdracht | Beschrijving |
| --- | --- |
|[AZ SQL Server AD-admin Create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Richt een Azure Active Directory beheerder in voor de server die als host fungeert voor SQL Database of Azure Synapse. (Moet afkomstig zijn van het huidige abonnement) |
|[AZ SQL Server AD-admin Delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Hiermee verwijdert u een Azure Active Directory beheerder voor de server die als host fungeert voor SQL Database of Azure Synapse. |
|[AZ SQL Server AD-administrator list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | Retourneert informatie over een Azure Active Directory-beheerder die momenteel is geconfigureerd voor de server die als host fungeert voor SQL Database of Azure Synapse. |
|[AZ SQL Server AD-Admin Update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Hiermee wordt de Active Directory beheerder voor de server die als host fungeert voor SQL Database of Azure Synapse. |

Zie [AZ SQL Server](/cli/azure/sql/server)(Engelstalig) voor meer informatie over cli-opdrachten.

* * *

> [!NOTE]
> U kunt ook een Azure Active Directory beheerder inrichten met behulp van de REST Api's. Zie voor meer informatie [Service Management rest API naslag informatie en bewerkingen voor Azure SQL database bewerkingen voor Azure SQL database](/rest/api/sql/)

## <a name="configure-your-client-computers"></a>Uw client computers configureren

Op alle client computers, van waaruit uw toepassingen of gebruikers verbinding maken met SQL Database of Azure Synapse met behulp van Azure AD-identiteiten, moet u de volgende software installeren:

- .NET Framework 4,6 of hoger van [https://msdn.microsoft.com/library/5a4x27ek.aspx](/dotnet/framework/install/guide-for-developers) .
- Azure Active Directory-verificatie bibliotheek voor SQL Server (*ADAL.DLL*). Hieronder vindt u de Download koppelingen voor het installeren van het nieuwste SSMS-, ODBC-en OLE DB-stuur programma dat de *ADAL.DLL* -bibliotheek bevat.
  - [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
  - [ODBC-stuur programma 17 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
  - [OLE DB stuur programma 18 voor SQL Server](https://www.microsoft.com/download/details.aspx?id=56730)

U kunt aan de volgende vereisten voldoen:

- Het installeren van de nieuwste versie van [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) of [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) voldoet aan de .NET Framework 4,6-vereiste.
  - SSMS installeert de x86-versie van *ADAL.DLL*.
  - SSDT installeert de amd64-versie van *ADAL.DLL*.
  - De nieuwste Visual Studio- [down loads van Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) voldoen aan de vereiste van .NET Framework 4,6, maar installeert de vereiste amd64-versie van *ADAL.DLL* niet.

## <a name="create-contained-users-mapped-to-azure-ad-identities"></a>Opgenomen gebruikers maken die zijn toegewezen aan Azure AD-identiteiten

Omdat SQL Managed instance Azure AD server-principals (aanmeldingen) ondersteunt, is het gebruik van Inge sloten database gebruikers niet vereist. Met Azure AD server-principals (aanmeldingen) kunt u aanmeldingen maken van Azure AD-gebruikers,-groepen of-toepassingen. Dit betekent dat u bij uw SQL Managed Instance kunt verifiëren met behulp van de Azure AD-Server aanmelding in plaats van een Inge sloten database gebruiker. Zie [overzicht van SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md#azure-active-directory-integration)voor meer informatie. Zie <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">login maken</a>voor de syntaxis voor het maken van Azure ad-server principals (aanmeldingen).

Voor het gebruik van Azure Active Directory-verificatie met SQL Database en Azure Synapse moet Inge sloten database gebruikers echter worden gebruikt op basis van een Azure AD-identiteit. Een Inge sloten database gebruiker heeft geen aanmelding in de hoofd database en wijst toe aan een identiteit in azure AD die is gekoppeld aan de data base. De Azure AD-identiteit kan een individueel gebruikersaccount of een groep zijn. Zie [Contained Database Users- Making Your Database Portable](/sql/relational-databases/security/contained-database-users-making-your-database-portable) (Gebruikers van ingesloten databases: een draagbare database maken) voor meer informatie over gebruikers van ingesloten databases.

> [!NOTE]
> Databasegebruikers kunnen niet in de Azure-portal worden gemaakt (met uitzondering van beheerders). Azure-rollen worden niet door gegeven aan de data base in SQL Database, het SQL Managed instance of Azure Synapse. Azure-rollen worden gebruikt voor het beheer van Azure-resources en zijn niet van toepassing op database machtigingen. De rol **SQL Server Inzender** verleent bijvoorbeeld geen toegang om verbinding te maken met de data base in SQL database, het SQL Managed instance of Azure Synapse. De toestemming tot het verlenen van toegang moet rechtstreeks plaatsvinden in de database met behulp van Transact-SQL-instructies.

> [!WARNING]
> Speciale tekens zoals dubbele punten `:` of ampersands `&` wanneer ze worden opgenomen als gebruikers namen in de T-SQL- `CREATE LOGIN` en- `CREATE USER` instructies worden niet ondersteund.

Als u een Inge sloten database gebruiker op basis van Azure AD wilt maken (met uitzonde ring van de server beheerder die eigenaar is van de data base), maakt u verbinding met de data base met een Azure AD-identiteit, als een gebruiker met ten minste de machtiging **wijzigen van een gebruiker** . Gebruik vervolgens de volgende Transact-SQL-syntaxis:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* kan de User Principal name van een Azure AD-gebruiker of de weergave naam voor een Azure AD-groep zijn.

**Voor beelden:** Een Inge sloten database gebruiker maken die een Azure AD Federated-of beheerde domein gebruiker vertegenwoordigt:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Als u een Inge sloten database gebruiker wilt maken die een Azure AD-of federatieve domein groep vertegenwoordigt, geeft u de weergave naam van een beveiligings groep op:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Een Inge sloten database gebruiker maken die een toepassing vertegenwoordigt die verbinding maakt met behulp van een Azure AD-token:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Voor deze opdracht is vereist dat SQL-toegang tot Azure AD (de externe provider) namens de aangemelde gebruiker is. Soms ontstaan er omstandigheden die ertoe leiden dat Azure AD een uitzonde ring terugstuurt naar SQL. In deze gevallen ziet de gebruiker SQL-fout 33134, die het specifieke Azure AD-fout bericht moet bevatten. In de meeste gevallen geeft de fout aan dat de toegang wordt geweigerd of dat de gebruiker zich moet inschrijven in MFA om toegang te krijgen tot de resource, of dat toegang tussen toepassingen van de eerste partij moet worden afgehandeld via pre-autorisatie. In de eerste twee gevallen wordt het probleem meestal veroorzaakt door het beleid voor voorwaardelijke toegang dat is ingesteld in de Azure AD-Tenant van de gebruiker: Hiermee wordt voor komen dat de gebruiker toegang heeft tot de externe provider. Het bijwerken van het CA-beleid om toegang tot de toepassing ' 00000002-0000-0000-C000-000000000000 te gebruiken ' toe te staan (de toepassings-ID van de Azure AD-Graph API) moet het probleem oplossen. In het geval dat de fout toegang geeft tussen toepassingen van de eerste partij, moet worden verwerkt via pre-autorisatie. het probleem is dat de gebruiker is aangemeld als service-principal. De opdracht moet slagen als deze door een gebruiker wordt uitgevoerd.

> [!TIP]
> U kunt een gebruiker niet rechtstreeks maken op basis van een andere Azure Active Directory dan de Azure Active Directory die aan uw Azure-abonnement is gekoppeld. Leden van andere Active Directory-gebruikers die zijn geïmporteerd in de gekoppelde Active Directory (ook wel externe gebruikers genoemd) kunnen wel worden toegevoegd aan een Active Directory groep in de Tenant Active Directory. Door een Inge sloten database gebruiker te maken voor die AD-groep, kunnen de gebruikers van de externe Active Directory toegang krijgen tot SQL Database.

Zie [Create User (Transact-SQL) (Engelstalig)](/sql/t-sql/statements/create-user-transact-sql)voor meer informatie over het maken van Inge sloten database gebruikers op basis van Azure Active Directory identiteiten.

> [!NOTE]
> Als u de Azure Active Directory beheerder voor de server verwijdert, kan de gebruiker van Azure AD-verificatie geen verbinding maken met de server. Zo nodig kunnen niet-herbruikbare Azure AD-gebruikers hand matig worden verwijderd door een SQL Database beheerder.

> [!NOTE]
> Als u een **time-out** voor de verbinding hebt ontvangen, moet u mogelijk de `TransparentNetworkIPResolution` para meter van de Connection String instellen op ONWAAR. Zie voor meer informatie [verbindingstime-out probleem met .NET Framework 4.6.1-TransparentNetworkIPResolution](/archive/blogs/dataaccesstechnologies/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution).

Wanneer u een database gebruiker maakt, ontvangt die gebruiker de machtiging **verbinden** en kan deze verbinding maken met die data base als lid van de **open bare** rol. In eerste instantie zijn er machtigingen verleend aan de **open bare** rol of machtigingen die zijn verleend aan een Azure AD-groep waarvan ze lid zijn. Wanneer u een Inge sloten database gebruiker op basis van Azure AD hebt ingericht, kunt u de gebruiker extra machtigingen verlenen, op dezelfde manier als u de machtiging voor elk ander type gebruiker toewijst. Verleen doorgaans machtigingen aan database rollen en voeg gebruikers toe aan rollen. Zie [basis beginselen van machtigingen voor data base-engine](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)voor meer informatie. Zie [data bases en aanmeldingen beheren in Azure SQL database](logins-create-manage.md)voor meer informatie over speciale SQL database rollen.
Een federatieve domein gebruikers account dat in een beheerd domein als een externe gebruiker wordt geïmporteerd, moet de identiteit van het beheerde domein gebruiken.

> [!NOTE]
> Azure AD-gebruikers worden in de metagegevens van de database gemarkeerd met het type E (EXTERNAL_USER) en voor groepen met het type X (EXTERNAL_GROUPS). Zie [sys.database_principals](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql) voor meer informatie.

## <a name="connect-to-the-database-using-ssms-or-ssdt"></a>Verbinding maken met de data base met behulp van SSMS of SSDT  

Als u wilt controleren of de Azure AD-beheerder juist is ingesteld, maakt u verbinding met de **hoofd** database met behulp van het Azure AD-beheerders account.
Als u een Inge sloten database gebruiker op basis van Azure AD wilt inrichten (met uitzonde ring van de server beheerder die eigenaar is van de data base), maakt u verbinding met de data base met een Azure AD-identiteit die toegang heeft tot de data base.

> [!IMPORTANT]
> Ondersteuning voor Azure Active Directory-verificatie is beschikbaar met [SQL Server 2016 Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) in Visual Studio 2015. De release van de 2016 van de augustus van SSMS bevat ook ondersteuning voor Active Directory universele verificatie, waarmee beheerders Multi-Factor Authentication kunnen vereisen met behulp van een telefoon gesprek, tekst bericht, Smart Cards met een pincode of een melding voor mobiele apps.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Een Azure AD-identiteit gebruiken om verbinding te maken met behulp van SSMS of SSDT

De volgende procedures laten zien hoe u verbinding kunt maken met SQL Database met een Azure AD-identiteit met behulp van SQL Server Management Studio-of SQL Server database Hulpprogramma's.

### <a name="active-directory-integrated-authentication"></a>Geïntegreerde verificatie Active Directory

Gebruik deze methode als u bent aangemeld bij Windows met uw Azure Active Directory referenties van een federatief domein of een beheerd domein dat is geconfigureerd voor naadloze eenmalige aanmelding voor Pass-Through-en wacht woord-hash-verificatie. Zie [Azure Active Directory naadloze eenmalige aanmelding](../../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.

1. Start Management Studio of Hulpprogram Ma's voor gegevens en selecteer in het dialoog venster **verbinding maken met server** (of **verbinding maken met data base-engine**) in het vak **verificatie** de optie **Azure Active Directory geïntegreerd**. Er is geen wacht woord nodig of het kan worden ingevoerd omdat uw bestaande referenties worden weer gegeven voor de verbinding.

   ![Geïntegreerde AD-verificatie selecteren][11]

2. Selecteer de knop **Opties** en typ op de pagina **verbindings eigenschappen** in het vak **verbinding maken met data base** de naam van de gebruikers database waarmee u verbinding wilt maken. Zie het artikel [multi-factor Azure AD auth](authentication-mfa-ssms-overview.md#azure-ad-domain-name-or-tenant-id-parameter) op de verschillen tussen de verbindings eigenschappen voor SSMS 17. x en 18. x voor meer informatie.

   ![De database naam selecteren][13]

### <a name="active-directory-password-authentication"></a>Wachtwoord verificatie Active Directory

Gebruik deze methode wanneer u verbinding maakt met een principal-naam van Azure AD met behulp van het door Azure AD beheerde domein. U kunt dit ook gebruiken voor federatieve accounts zonder toegang tot het domein, bijvoorbeeld wanneer u extern werkt.

Gebruik deze methode om u te verifiëren bij de data base in SQL Database of het SQL Managed instance met Azure AD-Cloud identiteits gebruikers, of klanten die Azure AD Hybrid-identiteiten gebruiken. Deze methode ondersteunt gebruikers die hun Windows-referentie willen gebruiken, maar hun lokale machine is niet gekoppeld aan het domein (bijvoorbeeld via externe toegang). In dit geval kan een Windows-gebruiker zijn of haar domein account en wacht woord aanduiden en kan worden geverifieerd bij de data base in SQL Database, het door SQL beheerde exemplaar of Azure Synapse.

1. Start Management Studio of Hulpprogram Ma's voor gegevens en selecteer in het dialoog venster **verbinding maken met server** (of **verbinding maken met data base-engine**) in het vak **verificatie** de optie **Azure Active Directory-wacht woord**.

2. Typ in het vak **gebruikers naam** uw Azure Active Directory gebruikers naam in de notatie **username \@ Domain.com**. Gebruikers namen moeten een account zijn uit Azure Active Directory of een account van een beheerd of Federated domein met Azure Active Directory.

3. Typ in het vak **wacht woord** het wacht woord van uw gebruiker voor de Azure Active Directory account of het beheerde/federatieve domein account.

    ![AD-wachtwoord verificatie selecteren][12]

4. Selecteer de knop **Opties** en typ op de pagina **verbindings eigenschappen** in het vak **verbinding maken met data base** de naam van de gebruikers database waarmee u verbinding wilt maken. (Zie de afbeelding in de vorige optie.)

### <a name="active-directory-interactive-authentication"></a>Interactieve verificatie Active Directory

Gebruik deze methode voor interactieve verificatie met of zonder Multi-Factor Authentication (MFA), waarbij een wacht woord interactief wordt aangevraagd. Deze methode kan worden gebruikt om te verifiëren bij de data base in SQL Database, het SQL Managed instance en Azure Synapse voor Azure AD-Cloud-only-ID-gebruikers of klanten die Azure AD Hybrid-identiteiten gebruiken.

Zie [using multi-factor Azure AD Authentication with SQL database en Azure Synapse (SSMS-ondersteuning voor MFA)](authentication-mfa-ssms-overview.md)voor meer informatie.

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Een Azure AD-identiteit gebruiken om verbinding te maken vanuit een client toepassing

De volgende procedures laten zien hoe u verbinding maakt met een SQL Database met een Azure AD-identiteit van een client toepassing.

### <a name="active-directory-integrated-authentication"></a>Geïntegreerde verificatie Active Directory

Als u geïntegreerde Windows-verificatie wilt gebruiken, moet de Active Directory van uw domein federatief zijn met Azure Active Directory of moet een beheerd domein zijn dat is geconfigureerd voor naadloze eenmalige aanmelding voor Pass-Through of wacht woord-hash-verificatie. Zie [Azure Active Directory naadloze eenmalige aanmelding](../../active-directory/hybrid/how-to-connect-sso.md)voor meer informatie.

> [!NOTE]
> [MSAL.net (micro soft. Identity. client)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap) voor geïntegreerde Windows-verificatie wordt niet ondersteund voor naadloze eenmalige aanmelding voor Pass-Through en wacht woord-hash-verificatie.

Uw client toepassing (of een service) die verbinding maakt met de data base moet worden uitgevoerd op een computer die lid is van een domein, onder de domein referenties van de gebruiker.

Als u verbinding wilt maken met een Data Base met behulp van geïntegreerde verificatie en een Azure AD-identiteit, moet het sleutel woord voor verificatie in de data base connection string worden ingesteld op `Active Directory Integrated` . Het volgende C#-code voorbeeld maakt gebruik van ADO .NET.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Het sleutel woord connection string `Integrated Security=True` wordt niet ondersteund om verbinding te maken met Azure SQL database. Wanneer u een ODBC-verbinding maakt, moet u spaties verwijderen en de verificatie instellen op ' ActiveDirectoryIntegrated '.

### <a name="active-directory-password-authentication"></a>Wachtwoord verificatie Active Directory

Als u verbinding wilt maken met een Data Base met behulp van Azure AD-Cloud gebruikers accounts met alleen identiteiten, of klanten die Azure AD Hybrid-identiteiten gebruiken, moet het sleutel woord Authentication worden ingesteld op `Active Directory Password` . Het connection string moet gebruikers-ID/UID en wacht woord/PWD tref woorden en waarden bevatten. Het volgende C#-code voorbeeld maakt gebruik van ADO .NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Meer informatie over Azure AD-verificatie methoden met behulp van de voorbeeld code voorbeelden die beschikbaar zijn in [Azure AD Authentication github demo](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Azure AD-token

Met deze verificatie methode kunnen middelste-laag Services [JSON-Webtokens (JWT)](../../active-directory/develop/id-tokens.md) verkrijgen om verbinding te maken met de data base in SQL database, het SQL Managed instance of Azure Synapse door een token van Azure ad te verkrijgen. Met deze methode kunnen verschillende toepassings scenario's, waaronder service-identiteiten, service-principals en toepassingen, gebruikmaken van verificatie op basis van certificaten. U moet vier basis stappen volt ooien voor het gebruik van Azure AD-token verificatie:

1. Registreer uw toepassing bij Azure Active Directory en haal de client-ID op voor uw code.
2. Maak een database gebruiker die de toepassing vertegenwoordigt. (Eerder in stap 6 voltooid.)
3. Een certificaat op de client computer maken, wordt de toepassing uitgevoerd.
4. Voeg het certificaat toe als sleutel voor uw toepassing.

Voorbeeld connection string:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Zie [SQL Server Security Blog](/archive/blogs/sqlsecurity/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth)(Engelstalig) voor meer informatie. Zie [aan de slag met verificatie op basis van certificaten in azure Active Directory](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)voor meer informatie over het toevoegen van een certificaat.

### <a name="sqlcmd"></a>sqlcmd

De volgende instructies zijn een verbinding met versie 13,1 van Sqlcmd, die beschikbaar is via het [Download centrum](https://www.microsoft.com/download/details.aspx?id=53591).

> [!NOTE]
> `sqlcmd` met de `-G` opdracht werkt niet met systeem identiteiten en is een gebruikers-principal-aanmelding vereist.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshoot-azure-ad-authentication"></a>Problemen met Azure AD-verificatie oplossen

Meer informatie over het oplossen van problemen met Azure AD-verificatie vindt u in de volgende blog: <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>Volgende stappen

- Zie [aanmeldingen, gebruikers, database rollen en gebruikers accounts](logins-create-manage.md)voor een overzicht van aanmeldingen, gebruikers, database rollen en machtigingen in SQL database.
- Zie [Principals](/sql/relational-databases/security/authentication-access/principals-database-engine) voor meer informatie over database-principals.
- Zie [Databaserollen](/sql/relational-databases/security/authentication-access/database-level-roles) voor meer informatie over databaserollen.
- Zie [SQL Database-firewallregels](firewall-configure.md) voor meer informatie over de firewallregels in SQL Database.
- Zie voor meer informatie over het instellen van een Azure AD-gast gebruiker als de Azure AD-beheerder [Azure AD-gast gebruikers maken en instellen als een Azure AD-beheerder](authentication-aad-guest-users.md).
- Zie [Azure AD-gebruikers maken met Azure AD-toepassingen](authentication-aad-service-principal-tutorial.md) voor meer informatie over het gebruik van service-principals met Azure SQL

<!--Image references-->

[11]: ./media/authentication-aad-configure/active-directory-integrated.png
[12]: ./media/authentication-aad-configure/12connect-using-pw-auth2.png
[13]: ./media/authentication-aad-configure/13connect-to-db2.png
