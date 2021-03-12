---
title: Roltoewijzingen uit een groep in Azure Active Directory verwijderen | Microsoft Docs
description: Bekijk een voorbeeld van aangepaste Azure AD-rollen voor het delegeren van identiteitsbeheer. Beheer Azure-rollen in Azure Portal, PowerShell of Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78ed23f563fce9760768a99e5bbf58f68500d665
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012017"
---
# <a name="remove-role-assignments-from-a-group-in-azure-active-directory"></a>Roltoewijzingen uit een groep in Azure Active Directory verwijderen

In dit artikel wordt beschreven hoe een IT-beheerder Azure AD-rollen kan verwijderen die aan groepen zijn toegewezen. In de Azure Portal kunt u nu zowel directe als indirecte roltoewijzingen voor een gebruiker verwijderen. Als aan een gebruiker een rol is toegewezen door een groepslid maatschap, verwijdert u de gebruiker uit de groep om de roltoewijzing te verwijderen.

## <a name="using-azure-admin-center"></a>Azure-beheer centrum gebruiken

1. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met privileged Role Administrator of Global Administrator Permissions in de Azure AD-organisatie.

1. Selecteer **rollen en beheerders**  >  **_naam van rol_**.

1. Selecteer de groep waaruit u de roltoewijzing wilt verwijderen en selecteer **toewijzing verwijderen**.

   ![Een roltoewijzing verwijderen uit een geselecteerde groep.](./media/groups-remove-assignment/remove-assignment.png)

1. Wanneer u wordt gevraagd om uw actie te bevestigen, selecteert u **Ja**.

## <a name="using-powershell"></a>PowerShell gebruiken

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Een groep maken die kan worden toegewezen aan een rol

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

### <a name="get-the-role-definition-you-want-to-assign-the-group-to"></a>Haal de roldefinitie op waaraan u de groep wilt toewijzen

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'"
```

### <a name="create-a-role-assignment"></a>Een roltoewijzing maken

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.objectId
```

### <a name="remove-the-role-assignment"></a>De roltoewijzing verwijderen

```powershell
Remove-AzureAdMSRoleAssignment -Id $roleAssignment.Id 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph-API gebruiken

### <a name="create-a-group-that-can-be-assigned-an-azure-ad-role"></a>Een groep maken waaraan een Azure AD-rol kan worden toegewezen

```http
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD",
"displayName": "Contoso_Helpdesk_Administrators",
"groupTypes": [
"Unified"
],
"mailEnabled": true,
"securityEnabled": true
"mailNickname": "contosohelpdeskadministrators",
"isAssignableToRole": true,
}
```

### <a name="get-the-role-definition"></a>De roldefinitie ophalen

```http
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter=displayName+eq+'Helpdesk Administrator'
```

### <a name="create-the-role-assignment"></a>De roltoewijzing maken

```http
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"{object-id-of-group}",
"roleDefinitionId":"{role-definition-id}",
"directoryScopeId":"/"
}
```

### <a name="delete-role-assignment"></a>Roltoewijzing verwijderen

```http
DELETE https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/{role-assignment-id}
```

## <a name="next-steps"></a>Volgende stappen

- [Cloudgroepen gebruiken om roltoewijzingen te beheren](groups-concept.md)
- [Problemen met rollen die zijn toegewezen aan cloudgroepen oplossen](groups-faq-troubleshooting.md)
