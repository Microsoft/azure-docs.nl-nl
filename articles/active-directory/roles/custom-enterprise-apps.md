---
title: Aangepaste rollen maken voor het beheren van zakelijke apps in Azure Active Directory
description: Aangepaste Azure AD-rollen voor toegang tot bedrijfs-apps maken en toewijzen in Azure Active Directory
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a76e2d37e9dcdd285a8608fdbfd715bfb834eb8
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467745"
---
# <a name="create-custom-roles-to-manage-enterprise-apps-in-azure-active-directory"></a>Aangepaste rollen maken voor het beheren van zakelijke apps in Azure Active Directory

In dit artikel wordt uitgelegd hoe u een aangepaste rol maakt met machtigingen voor het beheren van zakelijke app-toewijzingen voor gebruikers en groepen in Azure Active Directory (Azure AD). Zie [overzicht van aangepaste functies](custom-overview.md)voor de elementen van functie toewijzingen en de betekenis van termen zoals subtype, machtiging en eigenschappenset.

## <a name="enterprise-app-role-permissions"></a>Machtigingen voor de rol van de Enter prise-app

Er zijn twee machtigingen voor de Enter prise-app die in dit artikel worden besproken. Alle voor beelden gebruiken de update machtiging.

* Als u de gebruikers-en groeps toewijzingen in het bereik wilt lezen, moet u de `microsoft.directory/servicePrincipals/appRoleAssignedTo/read` machtiging verlenen
* Als u de gebruikers-en groeps toewijzingen in het bereik wilt beheren, moet u de `microsoft.directory/servicePrincipals/appRoleAssignedTo/update` machtiging verlenen

Het verlenen van de machtigingen voor de update heeft tot gevolg dat de toegewezen gebruiker toewijzingen van gebruikers en groepen kan beheren voor zakelijke apps. Het bereik van gebruikers-en/of groeps toewijzingen kan worden verleend voor één toepassing of worden toegekend voor alle toepassingen. Als er op organisatie niveau wordt toegekend, kan de toegewezen gebruiker toewijzingen voor alle toepassingen beheren. Als u op toepassings niveau hebt gemaakt, kunnen de toegewezen toewijzingen alleen voor de opgegeven toepassing worden beheerd.

Het verlenen van de update machtiging geschiedt in twee stappen:

1. Een aangepaste rol maken met machtiging `microsoft.directory/servicePrincipals/appRoleAssignedTo/update`
1. Gebruikers of groepen machtigingen verlenen voor het beheren van gebruikers-en groeps toewijzingen voor zakelijke apps. Dit is het geval wanneer u het bereik kunt instellen op het niveau van de organisatie of op één toepassing.

## <a name="use-the-azure-ad-admin-center"></a>Het Azure AD-beheer centrum gebruiken

### <a name="create-a-new-custom-role"></a>Een nieuwe aangepaste rol maken

>[!NOTE]
> Aangepaste rollen worden gemaakt en beheerd op basis van een organisatie niveau en zijn alleen beschikbaar op de overzichts pagina van de organisatie.

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met privileged Role Administrator of Global Administrator Permissions in uw organisatie.
1. Selecteer **Azure Active Directory**, selecteer **Rollen en beheerders** en selecteer vervolgens **Nieuwe aangepaste rol**.

    ![Een nieuwe aangepaste rol toevoegen vanuit de lijst rollen in azure AD](./media/custom-enterprise-apps/new-custom-role.png)

1. Op het tabblad **basis beginselen** voert u ' gebruikers-en groeps toewijzingen beheren ' in voor de naam van de rol en ' machtigingen verlenen voor het beheren van gebruikers-en groeps toewijzingen ' voor de functie beschrijving, en selecteer vervolgens **volgende**.

    ![Geef een naam en beschrijving op voor de aangepaste rol](./media/custom-enterprise-apps/role-name-and-description.png)

1. Op het tabblad **machtigingen** voert u ' micro soft. Directory/ServicePrincipals/appRoleAssignedTo/update ' in het zoekvak in en selecteert u vervolgens de selectie vakjes naast de gewenste machtigingen en selecteert u vervolgens **volgende**.

    ![De machtigingen voor de aangepaste rol toevoegen](./media/custom-enterprise-apps/role-custom-permissions.png)

1. Controleer op het tabblad **Beoordelen en maken** de machtigingen en selecteer **Maken**.

    ![U kunt nu de aangepaste rol maken](./media/custom-enterprise-apps/role-custom-create.png)

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>De rol toewijzen aan een gebruiker met behulp van de Azure AD-Portal

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met privileged Role Administrator role permissions.
1. Selecteer **Azure Active Directory** en selecteer vervolgens **Rollen en beheerders**.
1. Selecteer de rol **machtigingen geven om de gebruikers-en groeps toewijzingen te beheren** .

    ![Rollen en beheerders openen en zoeken naar de aangepaste rol](./media/custom-enterprise-apps/select-custom-role.png)

1. Selecteer **toewijzing toevoegen**, selecteer de gewenste gebruiker en klik vervolgens op **selecteren** om roltoewijzing toe te voegen aan de gebruiker.

    ![Een toewijzing voor de aangepaste rol toevoegen aan de gebruiker](./media/custom-enterprise-apps/assign-user-to-role.png)

#### <a name="assignment-tips"></a>Tips voor de toewijzing

* Om machtigingen toe te kennen aan gebruikers en groeps toegang voor alle zakelijke apps organisatie-breed, start u vanuit de lijst met **rollen en beheerders** in de hele organisatie op de pagina **overzicht** van Azure AD voor uw organisatie.
* Als u machtigingen wilt verlenen aan gebruikers en groeps toegang voor een specifieke bedrijfs-app, gaat u naar die app in azure AD en opent u deze in de lijst **rollen en Administrators** voor die app. Selecteer de nieuwe aangepaste rol en voltooi de gebruikers-of groeps toewijzing. De toegewezen gebruiker kan gebruikers en groeps toegang alleen voor de specifieke app beheren.
* Als u uw aangepaste roltoewijzing wilt testen, meldt u zich aan als de toegewezen persoon en opent u de pagina **gebruikers en groepen** van een toepassing om te controleren of de optie **gebruiker toevoegen** is ingeschakeld.

    ![De gebruikers machtigingen controleren](./media/custom-enterprise-apps/verify-user-permissions.png)

## <a name="use-azure-ad-powershell"></a>Azure AD Power shell gebruiken

Zie [een aangepaste rol maken en toewijzen](custom-create.md) en [aangepaste rollen toewijzen aan resource bereik met behulp van Power shell](custom-assign-powershell.md)voor meer informatie.

Installeer eerst de Azure AD Power shell-module van [de PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17). Importeer vervolgens de Azure AD PowerShell preview-module met de volgende opdracht:

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
$allowedResourceAction =@( "microsoft.directory/servicePrincipals/appRoleAssignedTo/update")
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role"></a>De aangepaste rol toewijzen

Wijs de rol toe met dit Power shell-script.

```powershell
PowerShell
# Basic role information

$description = "Manage user and group assignments"
$displayName = "Can manage user and group assignments for Applications"
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/servicePrincipals/appRoleAssignedTo/update"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

## <a name="use-the-microsoft-graph-api"></a>De Microsoft Graph API gebruiken

Maak een aangepaste rol met behulp van het voor beeld in de Microsoft Graph-API. Zie [een aangepaste rol maken en toewijzen](custom-create.md) en [aangepaste beheerders rollen toewijzen met behulp van de Microsoft Graph-API](custom-assign-graph.md)voor meer informatie.

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

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>De aangepaste rol toewijzen met behulp van Microsoft Graph-API

De roltoewijzing combineert een beveiligings-principal-ID (die een gebruiker of service-principal kan zijn), een roldefinitie-ID en een Azure AD-resource bereik. Zie [overzicht van aangepaste functies](custom-overview.md) voor meer informatie over de elementen van een roltoewijzing.

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

* [De beschik bare aangepaste rolmachtigingen voor zakelijke apps verkennen](custom-enterprise-app-permissions.md)
