---
title: Een rol toewijzen aan een groep met behulp van Privileged Identity Management in azure AD | Microsoft Docs
description: Meer informatie over hoe u een Azure Active Directory (Azure AD)-rol aan een groep kunt toewijzen met behulp van Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 02cd3f54823b80ae201316fee29c02616b9d8502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103012034"
---
# <a name="assign-a-role-to-a-group-using-privileged-identity-management"></a>Een rol toewijzen aan een groep met behulp van Privileged Identity Management

In dit artikel wordt beschreven hoe u een Azure Active Directory-rol (Azure AD) kunt toewijzen aan een groep met behulp van Azure AD Privileged Identity Management (PIM).

> [!NOTE]
> U moet de bijgewerkte versie van Privileged Identity Management gebruiken om een groep aan een Azure AD-rol toe te wijzen met behulp van PIM. Mogelijk maakt u gebruik van een oudere versie van PIM als uw Azure AD-organisatie gebruikmaakt van de Privileged Identity Management-API. Als dit het geval is, neemt u contact op met de alias pim_preview@microsoft.com om uw organisatie te verplaatsen en uw API bij te werken. Meer informatie over [Azure AD-rollen en-functies in PIM](../privileged-identity-management/azure-ad-roles-features.md).

## <a name="using-azure-ad-admin-center"></a>Het Azure AD-beheer centrum gebruiken

1. Meld u aan bij [Azure AD privileged Identity Management](https://ms.portal.azure.com/?Microsoft_AAD_IAM_GroupRoles=true&Microsoft_AAD_IAM_userRolesV2=true&Microsoft_AAD_IAM_enablePimIntegration=true#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) als beheerder van een bevoorrechte rol of als globale beheerder in uw organisatie.

1. **Privileged Identity Management**  >  functies van **Azure AD-rollen** selecteren  >    >  **toewijzingen toevoegen**

1. Selecteer een rol en selecteer vervolgens een groep. Alleen groepen die in aanmerking komen voor roltoewijzing (groepen die kunnen worden toegewezen aan rollen) worden weer gegeven, niet alle groepen.

    ![Scherm opname van de pagina toewijzingen toevoegen met de secties ' rol selecteren ' en ' leden selecteren ' gemarkeerd.](./media/groups-pim-eligible/select-member.png)

1. Selecteer de gewenste lidmaatschaps instelling. Kies **in aanmerking komend** voor rollen die moeten worden geactiveerd. Standaard komt de gebruiker permanent in aanmerking, maar u kunt ook een begin-en eind tijd instellen voor de geschiktheid van de gebruiker. Zodra u klaar bent, klikt u op opslaan en toevoegen om de roltoewijzing te volt ooien.

    ![Selecteer de gebruiker aan wie u de rol wilt toewijzen](./media/groups-pim-eligible/set-assignment-settings.png)

## <a name="using-powershell"></a>PowerShell gebruiken

### <a name="download-the-azure-ad-preview-powershell-module"></a>De Azure AD preview Power shell-module downloaden

Als u de Azure AD #PowerShell-module wilt installeren, gebruikt u de volgende cmdlets:

```powershell
Install-Module -Name AzureADPreview
Import-Module -Name AzureADPreview
```

Als u wilt controleren of de module gereed is voor gebruik, gebruikt u de volgende cmdlet:

```powershell
Get-Module -Name AzureADPreview
```

### <a name="assign-a-group-as-an-eligible-member-of-a-role"></a>Een groep toewijzen als een in aanmerking komend lid van een rol

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = "2019-04-26T20:49:11.770Z"
$schedule.endDateTime = "2019-07-25T20:49:11.770Z"
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId aadRoles -Schedule $schedule -ResourceId "[YOUR TENANT ID]" -RoleDefinitionId "9f8c1837-f885-4dfd-9a75-990f9222b21d" -SubjectId "[YOUR GROUP ID]" -AssignmentState "Eligible" -Type "AdminAdd"
```

## <a name="using-microsoft-graph-api"></a>Microsoft Graph-API gebruiken

```http
POST
https://graph.microsoft.com/beta/privilegedAccess/aadroles/roleAssignmentRequests
{
 "roleDefinitionId": {roleDefinitionId},
 "resourceId": {tenantId},
 "subjectId": {GroupId},
 "assignmentState": "Eligible",
 "type": "AdminAdd",
 "reason": "reason string",
 "schedule": {
   "startDateTime": {DateTime},
   "endDateTime": {DateTime},
   "type": "Once"
 }
}
```

## <a name="next-steps"></a>Volgende stappen

- [Cloudgroepen gebruiken om roltoewijzingen te beheren](groups-concept.md)
- [Problemen met rollen die zijn toegewezen aan cloudgroepen oplossen](groups-faq-troubleshooting.md)
- [Instellingen voor Azure AD-beheerdersrol configureren in Privileged Identity Management](../privileged-identity-management/pim-how-to-change-default-settings.md)
- [Azure-resource rollen toewijzen in Privileged Identity Management](../privileged-identity-management/pim-resource-roles-assign-roles.md)
