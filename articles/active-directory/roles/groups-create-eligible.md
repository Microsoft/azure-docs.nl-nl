---
title: Een groep maken voor het toewijzen van rollen in Azure Active Directory | Microsoft Docs
description: Meer informatie over het maken van een door een functie toewijs bare groep in azure AD. Beheer Azure-rollen in Azure Portal, PowerShell of Graph API.
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
ms.openlocfilehash: d69662ca24ab135db549ee108e93f9276e154e58
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106905"
---
# <a name="create-a-role-assignable-group-in-azure-active-directory"></a>Een door een functie toewijs bare groep maken in Azure Active Directory

U kunt alleen een rol toewijzen aan een groep die is gemaakt met de eigenschap isAssignableToRole ingesteld op True, of is gemaakt in de Azure AD-Portal met **Azure AD-rollen kunnen worden toegewezen aan de groep** ingeschakeld. Dit groeps kenmerk maakt de groep een die kan worden toegewezen aan een rol in Azure Active Directory (Azure AD). In dit artikel wordt beschreven hoe u dit speciale soort groep maakt. **Opmerking:** Een groep waarvoor de eigenschap isAssignableToRole is ingesteld op True, kan niet van het dynamische lidmaatschaps type zijn. Zie [een groep gebruiken om toewijzingen van Azure AD-rollen te beheren](groups-concept.md)voor meer informatie.

## <a name="using-azure-ad-admin-center"></a>Het Azure AD-beheer centrum gebruiken

1. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met privileged Role Administrator of Global Administrator Permissions in de Azure AD-organisatie.
1. Selecteer **groepen**  >  **alle groepen**  >  **nieuwe groep**.

    [![Open Azure Active Directory en maak een nieuwe groep.](./media/groups-create-eligible/new-group.png "Open Azure Active Directory en maak een nieuwe groep.")](./media/groups-create-eligible/new-group.png#<lightbox>)

1. Geef op het tabblad **nieuwe groep** groeps type, naam en beschrijving op.
1. Het inschakelen van **Azure AD-rollen kan worden toegewezen aan de groep**. Deze schakel optie is alleen zichtbaar voor beheerders van bevoegde rollen en globale beheerders, omdat dit slechts twee rollen zijn die de switch kunnen instellen.

    [![De nieuwe groep geschikt maken voor roltoewijzing](./media/groups-create-eligible/eligible-switch.png "De nieuwe groep geschikt maken voor roltoewijzing")](./media/groups-create-eligible/eligible-switch.png#<lightbox>)

1. Selecteer de leden en eigen aars voor de groep. U hebt ook de optie om rollen toe te wijzen aan de groep, maar u hoeft hier geen rol toe te wijzen.

    [![Leden toevoegen aan de groep die kan worden toegewezen en rollen toewijzen.](./media/groups-create-eligible/specify-members.png "Leden toevoegen aan de groep die kan worden toegewezen en rollen toewijzen.")](./media/groups-create-eligible/specify-members.png#<lightbox>)

1. Nadat u de leden en eigen aren hebt opgegeven, selecteert u **maken**.

    [![De knop maken bevindt zich onder aan de pagina.](./media/groups-create-eligible/create-button.png "De knop maken bevindt zich onder aan de pagina.")](./media/groups-create-eligible/create-button.png#<lightbox>)

De groep wordt gemaakt met de rollen die u mogelijk hebt toegewezen.

## <a name="using-powershell"></a>PowerShell gebruiken

### <a name="install-the-azure-ad-preview-module"></a>De Azure AD-preview-module installeren

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Als u wilt controleren of de module gereed is voor gebruik, geeft u de volgende opdracht:

```powershell
Get-Module -Name AzureADPreview
```

### <a name="create-a-group-that-can-be-assigned-to-role"></a>Een groep maken die kan worden toegewezen aan een rol

```powershell
$group = New-AzureADMSGroup -DisplayName "Contoso_Helpdesk_Administrators" -Description "This group is assigned to Helpdesk Administrator built-in role in Azure AD." -MailEnabled $true -SecurityEnabled $true -MailNickName "contosohelpdeskadministrators" -IsAssignableToRole $true
```

Voor dit type groep is `isPublic` altijd False en is `isSecurityEnabled` altijd waar.

### <a name="copy-one-groups-users-and-service-principals-into-a-role-assignable-group"></a>Kopieer de gebruikers en service-principals van één groep in een door een functie toewijs bare groep

```powershell
#Basic set up
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
Get-Module -Name AzureADPreview

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
}
}
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph-API gebruiken

### <a name="create-a-role-assignable-group-in-azure-ad"></a>Een door een functie toewijs bare groep maken in azure AD

```http
POST https://graph.microsoft.com/beta/groups
{
  "description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
  "displayName": "Contoso_Helpdesk_Administrators",
  "groupTypes": [
    "Unified"
  ],
  "isAssignableToRole": true,
  "mailEnabled": true,
  "securityEnabled": true,
  "mailNickname": "contosohelpdeskadministrators",
  "visibility" : "Private"
}
```

Voor dit type groep is `isPublic` altijd False en is `isSecurityEnabled` altijd waar.

## <a name="next-steps"></a>Volgende stappen

- [Een rol toewijzen aan een cloudgroep](groups-assign-role.md)
- [Cloudgroepen gebruiken om roltoewijzingen te beheren](groups-concept.md)
- [Problemen met rollen die zijn toegewezen aan cloudgroepen oplossen](groups-faq-troubleshooting.md)
