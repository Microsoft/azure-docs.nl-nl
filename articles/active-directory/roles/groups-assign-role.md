---
title: Een rol toewijzen aan een Cloud groep in Azure Active Directory | Microsoft Docs
description: Wijs een Azure AD-rol toe aan een door een functie toewijs bare groep in de Azure Portal, Power shell of Graph API.
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
ms.openlocfilehash: 816009abb688525cd7663311c79300a6d12cf146
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742944"
---
# <a name="assign-a-role-to-a-cloud-group-in-azure-active-directory"></a>Een rol toewijzen aan een Cloud groep in Azure Active Directory

In deze sectie wordt beschreven hoe een IT-beheerder Azure Active Directory-rol (Azure AD) kan toewijzen aan een Azure AD-groep.

## <a name="using-azure-ad-admin-center"></a>Het Azure AD-beheer centrum gebruiken

Het toewijzen van een groep aan een Azure AD-rol is vergelijkbaar met het toewijzen van gebruikers en service-principals, met uitzonde ring van alleen groepen die rollen kunnen worden toegewezen. In de Azure Portal worden alleen groepen weer gegeven die rollen kunnen worden toegewezen.

1. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met privileged Role Administrator of Global Administrator Permissions in de Azure AD-organisatie.

1. Selecteer **Azure Active Directory**  >  **rollen en beheerders**, en selecteer de rol die u wilt toewijzen.

1. Selecteer op de pagina **functie naam** _ de optie > _ * toewijzing toevoegen * *.

   ![De nieuwe roltoewijzing toevoegen](./media/groups-assign-role/add-assignment.png)

1. Selecteer de groep. Alleen de groepen die kunnen worden toegewezen aan Azure AD-rollen, worden weer gegeven.

    [![Voor een nieuwe roltoewijzing worden alleen groepen weer gegeven die kunnen worden toegewezen.](./media/groups-assign-role/eligible-groups.png "Voor een nieuwe roltoewijzing worden alleen groepen weer gegeven die kunnen worden toegewezen.")](./media/groups-assign-role/eligible-groups.png#lightbox)

1. Selecteer **Toevoegen**.

Zie [beheerders en niet-beheerders rollen toewijzen aan gebruikers](../fundamentals/active-directory-users-assign-role-azure-portal.md)voor meer informatie over het toewijzen van rolmachtigingen.

## <a name="using-powershell"></a>PowerShell gebruiken

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Een groep maken die kan worden toegewezen aan een rol

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true 
```

### <a name="get-the-role-definition-for-the-role-you-want-to-assign"></a>De roldefinitie ophalen voor de rol die u wilt toewijzen

```powershell
$roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Helpdesk Administrator'" 
```

### <a name="create-a-role-assignment"></a>Een roltoewijzing maken

```powershell
$roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id 
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph-API gebruiken

### <a name="create-a-group-that-can-be-assigned-azure-ad-role"></a>Een groep maken waaraan de Azure AD-rol kan worden toegewezen

```
POST https://graph.microsoft.com/beta/groups
{
"description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
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

```
GET https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions?$filter = displayName eq ‘Helpdesk Administrator’
```

### <a name="create-the-role-assignment"></a>De roltoewijzing maken

```
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
{
"principalId":"<Object Id of Group>",
"roleDefinitionId":"<ID of role definition>",
"directoryScopeId":"/"
}
```
## <a name="next-steps"></a>Volgende stappen

- [Cloudgroepen gebruiken om roltoewijzingen te beheren](groups-concept.md)
- [Problemen met rollen die zijn toegewezen aan cloudgroepen oplossen](groups-faq-troubleshooting.md)
