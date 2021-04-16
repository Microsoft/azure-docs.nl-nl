---
title: Aangepaste rollen maken voor het beheren van bedrijfsapps in Azure Active Directory
description: Aangepaste Azure AD-rollen maken en toewijzen voor toegang tot bedrijfsapps in Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c04afe76ced0abf40abf8e30362005fb269172
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534709"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Aangepaste rollen maken voor het beheren van bedrijfsapps in Azure Active Directory

In dit artikel wordt uitgelegd hoe u een aangepaste rol maakt met machtigingen voor het beheren van zakelijke app-toewijzingen voor gebruikers en groepen in Azure Active Directory (Azure AD). Zie het overzicht van aangepaste rollen voor de elementen van roltoewijzingen en de betekenis van termen zoals subtype, machtiging en [eigenschappenset.](custom-overview.md)

## <a name="enterprise-app-role-permissions"></a>Rolmachtigingen voor bedrijfsapps

Er zijn twee machtigingen voor zakelijke apps die in dit artikel worden besproken. Alle voorbeelden maken gebruik van de updatemachtiging.

* Als u de gebruikers- en groepstoewijzingen in het bereik wilt lezen, verleent u de `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` machtiging
* Als u de gebruikers- en groepstoewijzingen in het bereik wilt beheren, verleent u de `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` machtiging

Door de updatemachtiging te verlenen, kan de toegewezene toewijzingen van gebruikers en groepen aan bedrijfsapps beheren. Het bereik van gebruikers- en/of groepstoewijzingen kan worden verleend voor één toepassing of worden verleend voor alle toepassingen. Als toewijzingen op organisatieniveau worden verleend, kan de toegewezene toewijzingen voor alle toepassingen beheren. Als deze is gemaakt op toepassingsniveau, kan de toegewezene toewijzingen alleen voor de opgegeven toepassing beheren.

Het verlenen van de updatemachtiging wordt in twee stappen uitgevoerd:

1. Een aangepaste rol met machtigingen maken `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Verleen gebruikers of groepen machtigingen voor het beheren van gebruikers- en groepstoewijzingen voor bedrijfsapps. Dit is wanneer u het bereik kunt instellen op het niveau van de hele organisatie of op één toepassing.

## <a name="use-the-azure-ad-admin-center"></a>Het Azure AD-beheercentrum gebruiken

### <a name="create-a-new-custom-role"></a>Een nieuwe aangepaste rol maken

>[!NOTE]
> Aangepaste rollen worden gemaakt en beheerd op organisatieniveau en zijn alleen beschikbaar op de pagina Overzicht van de organisatie.

1. Meld u aan bij het [Azure AD-beheercentrum](https://aad.portal.azure.com) met de machtigingen Beheerder voor bevoorrechte rollen of Globale beheerder in uw organisatie.
1. Selecteer **Azure Active Directory**, selecteer **Rollen en beheerders** en selecteer vervolgens **Nieuwe aangepaste rol**.

    ![Een nieuwe aangepaste rol toevoegen vanuit de lijst met rollen in Azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Geef **op** het tabblad Basisbeginselen 'Gebruikers- en groepstoewijzingen beheren' op als naam van de rol en 'Machtigingen verlenen voor het beheren van gebruikers- en groepstoewijzingen' voor de beschrijving van de rol. Selecteer vervolgens **Volgende.**

    ![Geef een naam en beschrijving op voor de aangepaste rol](./media/custom-enterprise-apps/role-name-and-description.png)

1. Voer  op het tabblad Machtigingen 'microsoft.directory/servicePrincipals/appRoleAssignedTo/update' in het zoekvak in en schakel vervolgens de selectievakjes naast de gewenste machtigingen in en selecteer **Volgende.**

    ![De machtigingen toevoegen aan de aangepaste rol](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Controleer op het tabblad **Beoordelen en maken** de machtigingen en selecteer **Maken**.

    ![U kunt nu de aangepaste rol maken](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>De rol toewijzen aan een gebruiker met behulp van de Azure AD-portal

1. Meld u aan bij het [Azure AD-beheercentrum met](https://aad.portal.azure.com) beheerdersrolmachtigingen voor bevoorrechte rollen.
1. Selecteer **Azure Active Directory** en selecteer vervolgens **Rollen en beheerders**.
1. Selecteer de **rol Machtigingen verlenen om gebruikers- en groepstoewijzingen te** beheren.

    ![Open Rollen en beheerders en zoek naar de aangepaste rol](./media/custom-enterprise-apps/select-custom-role.png)

1. Selecteer **Toewijzing toevoegen,** selecteer de gewenste gebruiker en klik vervolgens op **Selecteren om** roltoewijzing aan de gebruiker toe te voegen.

    ![Een toewijzing voor de aangepaste rol toevoegen aan de gebruiker](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Tips voor toewijzing

* Als u machtigingen wilt verlenen aan toegewezen personen voor het beheren van gebruikers en groepstoegang voor alle bedrijfsapps voor de hele organisatie, begint u vanuit de organisatiebrede lijst Rollen en beheerders op de **overzichtspagina** van Azure **AD** voor uw organisatie.
* Als u machtigingen wilt verlenen aan toegewezen personen voor het beheren van gebruikers en groepstoegang  voor een specifieke bedrijfs-app, gaat u naar die app in Azure AD en opent u in de lijst Rollen en beheerders voor die app. Selecteer de nieuwe aangepaste rol en voltooi de toewijzing van de gebruiker of groep. De toegewezen personen kunnen gebruikers en groepstoegang alleen beheren voor de specifieke app.
* Als u uw aangepaste roltoewijzing wilt testen, meldt  u zich aan als de toegewezene en opent u de pagina Gebruikers en groepen van een toepassing om te controleren of **de** optie Gebruiker toevoegen is ingeschakeld.

    ![De gebruikersmachtigingen controleren](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Azure AD PowerShell gebruiken

Zie Create and assign a custom [role (Een](custom-create.md) aangepaste rol maken en toewijzen) en Assign custom roles with resource scope using PowerShell (Aangepaste rollen toewijzen met [resourcebereik met behulp van PowerShell) voor meer informatie.](custom-assign-powershell.md)

Installeer eerst de Azure AD PowerShell-module vanuit [de PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importeer vervolgens de Azure AD PowerShell preview-module met de volgende opdracht:

```powershell
Import-Module -Name AzureADPreview
```

Als u wilt controleren of de module gereed is voor gebruik, gaat u naar de versie die wordt geretourneerd door de volgende opdracht:

```powershell
Get-Module -Name AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    AzureADPreview               {Add-AzureADAdministrati...}
```

### <a name="create-a-custom-role"></a>Een aangepaste rol maken

Maak een nieuwe rol met behulp van het volgende PowerShell-script:

```PowerShell
# Basic role information
$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction = @("microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>De aangepaste rol toewijzen

Wijs de rol toe met dit PowerShell-script.

```powershell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'chandra@example.com'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Manage user and group assignments'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'My Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

## <a name="use-the-microsoft-graph-api"></a>De Microsoft Graph API gebruiken

Maak een aangepaste rol met behulp van het opgegeven voorbeeld in Microsoft Graph API. Zie Een aangepaste rol maken en [toewijzen en Aangepaste](custom-create.md) beheerdersrollen toewijzen met behulp van de api Microsoft Graph meer [informatie.](custom-assign-graph.md)

HTTP-aanvraag voor het maken van de aangepaste rol.

```HTTP
POST
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitionsIsEnabled $true
{
    "description":"Can manage user and group assignments for Applications.",
    "displayName":" Manage user and group assignments",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>De aangepaste rol toewijzen met behulp Microsoft Graph API

De roltoewijzing combineert een beveiligingsprincipaal-id (die een gebruiker of service-principal kan zijn), een roldefinitie-id en een Azure AD-resourcebereik. Zie het overzicht van aangepaste rollen voor meer informatie over de elementen van een [roltoewijzing](custom-overview.md)

HTTP-aanvraag om een aangepaste rol toe te wijzen.

```HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments

{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>Volgende stappen

* [De beschikbare aangepaste rolmachtigingen voor bedrijfsapps verkennen](custom-enterprise-app-permissions.md)
