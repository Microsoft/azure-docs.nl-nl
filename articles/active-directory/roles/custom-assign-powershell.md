---
title: Aangepaste rollen toewijzen met behulp van Azure AD Power shell-Azure AD | Microsoft Docs
description: Leden van een aangepaste rol van Azure AD-beheerder beheren met Azure AD Power shell.
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
ms.openlocfilehash: 7828313844b8f95b2bac5bff37022a822686ab33
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740241"
---
# <a name="assign-custom-roles-with-resource-scope-using-powershell-in-azure-active-directory"></a>Aangepaste rollen met resource bereik toewijzen met behulp van Power shell in Azure Active Directory

In dit artikel wordt beschreven hoe u een roltoewijzing maakt op organisatie bereik in Azure Active Directory (Azure AD). Voor het toewijzen van een rol in het bereik van de organisatie wordt toegang verleend via de Azure AD-organisatie. Zie [een aangepaste rol maken en deze toewijzen aan](custom-create.md)de hand van een resource bereik voor het maken van een roltoewijzing met een bereik van één Azure AD-resource. In dit artikel wordt gebruikgemaakt van de [Azure Active Directory module Power shell versie 2](/powershell/module/azuread/#directory_roles) .

Zie [beheerders rollen toewijzen in azure Active Directory](permissions-reference.md)voor meer informatie over Azure AD-beheerders rollen.

## <a name="required-permissions"></a>Vereiste machtigingen

Maak verbinding met uw Azure AD-organisatie met behulp van een algemeen beheerders account om rollen toe te wijzen of te verwijderen.

## <a name="prepare-powershell"></a>PowerShell voorbereiden

Installeer de Azure AD Power shell-module vanuit het [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureADPreview). Importeer vervolgens de Azure AD PowerShell preview-module met de volgende opdracht:

``` PowerShell
Import-Module AzureADPreview
```

Als u wilt controleren of de module gereed is voor gebruik, gaat u naar de versie die wordt geretourneerd door de volgende opdracht:

``` PowerShell
Get-Module AzureADPreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADMSAdministrati...}
```

U kunt nu beginnen met het gebruik van de cmdlets in de module. Zie voor een volledige beschrijving van de cmdlets in de Azure AD-module de online referentie documentatie voor [Azure ad preview-module](https://www.powershellgallery.com/packages/AzureADPreview).

## <a name="assign-a-directory-role-to-a-user-or-service-principal-with-resource-scope"></a>Een directory-rol toewijzen aan een gebruiker of Service-Principal met resource bereik

1. Laad de module Azure AD Power shell (preview).
1. Meld u aan door de opdracht uit te voeren `Connect-AzureAD` .
1. Maak een nieuwe rol met behulp van het volgende Power shell-script.

``` PowerShell
## Assign a role to a user or service principal with resource scope
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

Gebruik de cmdlet [Get-AzureADMSServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) om de rol toe te wijzen aan een Service-Principal in plaats van een gebruiker.

## <a name="role-definitions"></a>Roldefinities

Role definition-objecten bevatten de definitie van de ingebouwde of aangepaste rol, samen met de machtigingen die worden verleend door die roltoewijzing. Deze resource bevat zowel aangepaste roldefinities als ingebouwde Directory rollen (die worden weer gegeven in een roleDefinition-equivalent formulier). Momenteel kunnen er voor een Azure AD-organisatie Maxi maal 30 unieke aangepaste roldefinities worden gedefinieerd.

### <a name="create-a-role-definition"></a>Een roldefinitie maken

``` PowerShell
# Basic information
$description = "Can manage credentials of application registrations"
$displayName = "Application Registration Credential Administrator"
$templateId = (New-Guid).Guid

# Set of actions to include
$rolePermissions = @{
    "allowedResourceActions" = @(
        "microsoft.directory/applications/standard/read",
        "microsoft.directory/applications/credentials/update"
    )
}

# Create new custom directory role
$customAdmin = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="read-and-list-role-definitions"></a>Definities van rollen lezen en weer geven

``` PowerShell
# Get all role definitions
Get-AzureADMSRoleDefinitions

# Get single role definition by ID
Get-AzureADMSRoleDefinition -Id 86593cfc-114b-4a15-9954-97c3494ef49b

# Get single role definition by templateId
Get-AzureADMSRoleDefinition -Filter "templateId eq 'c4e39bd9-1100-46d3-8c65-fb160da0071f'"
```

### <a name="update-a-role-definition"></a>Een roldefinitie bijwerken

``` PowerShell
# Update role definition
# This works for any writable property on role definition. You can replace display name with other
# valid properties.
Set-AzureADMSRoleDefinition -Id c4e39bd9-1100-46d3-8c65-fb160da0071f -DisplayName "Updated DisplayName"
```

### <a name="delete-a-role-definition"></a>Een roldefinitie verwijderen

``` PowerShell
# Delete role definition
Remove-AzureADMSRoleDefinitions -Id c4e39bd9-1100-46d3-8c65-fb160da0071f
```

## <a name="role-assignments"></a>Roltoewijzingen

Roltoewijzingen bevatten informatie die een bepaalde beveiligingsprincipal (een gebruiker of een toepassings service-principal) koppelt aan een roldefinitie. Indien nodig kunt u een bereik van één Azure AD-resource voor de toegewezen machtigingen toevoegen.  Het beperken van het bereik van een roltoewijzing wordt ondersteund voor ingebouwde en aangepaste rollen.

### <a name="create-a-role-assignment"></a>Een roltoewijzing maken

``` PowerShell
# Get the user and role definition you want to link
$user = Get-AzureADUser -Filter "userPrincipalName eq 'cburl@f128.info'"
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Application Support Administrator'"

# Get app registration and construct resource scope for assignment.
$appRegistration = Get-AzureADApplication -Filter "displayName eq 'f/128 Filter Photos'"
$resourceScope = '/' + $appRegistration.objectId

# Create a scoped role assignment
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope $resourceScope -RoleDefinitionId $roleDefinition.Id -PrincipalId $user.objectId
```

### <a name="read-and-list-role-assignments"></a>Lees-en lijst roltoewijzingen

``` PowerShell
# Get role assignments for a given principal
Get-AzureADMSRoleAssignment -Filter "principalId eq '27c8ca78-ab1c-40ae-bd1b-eaeebd6f68ac'"

# Get role assignments for a given role definition 
Get-AzureADMSRoleAssignment -Filter "roleDefinitionId eq '355aed8a-864b-4e2b-b225-ea95482e7570'"
```

### <a name="delete-a-role-assignment"></a>Een roltoewijzing verwijderen

``` PowerShell
# Delete role assignment
Remove-AzureADMSRoleAssignment -Id 'qiho4WOb9UKKgng_LbPV7tvKaKRCD61PkJeKMh7Y458-1'
```

## <a name="next-steps"></a>Volgende stappen

- Delen met ons op het [forum Azure AD Administrator-rollen](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)
- Zie [beheerders rollen toewijzen](permissions-reference.md) voor meer informatie over rollen en toewijzingen van Azure AD-beheerdersrol
- Zie voor de standaard gebruikers machtigingen een [vergelijking van standaard machtigingen voor gast-en gebruikers accounts](../fundamentals/users-default-permissions.md)
