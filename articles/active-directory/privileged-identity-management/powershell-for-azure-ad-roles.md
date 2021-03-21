---
title: Power shell voor Azure AD-rollen in PIM-Azure AD | Microsoft Docs
description: Azure AD-rollen beheren met Power shell-cmdlets in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2774c63d34de3de951aa8076b56d203b976158ec
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548747"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Power shell voor Azure AD-rollen in Privileged Identity Management

Dit artikel bevat instructies voor het gebruik van Azure Active Directory (Azure AD) Power shell-cmdlets voor het beheren van Azure AD-rollen in Privileged Identity Management (PIM). Ook wordt uitgelegd hoe u kunt instellen met de Azure AD Power shell-module.

> [!Note]
> Onze officiële Power shell wordt alleen ondersteund als u werkt met de nieuwe versie van Azure AD Privileged Identity Management. Ga naar Privileged Identity Management en controleer of u de volgende banner hebt op de Blade snel starten.
> [![Controleer de versie van privileged Identity Management die u hebt](media/pim-how-to-add-role-to-user/pim-new-version.png "Selecteer Azure AD-> Privileged Identity Management")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) Als u deze banner niet hebt, moet u een ogen blik geduld. deze bijgewerkte ervaring wordt momenteel in de komende weken geïmplementeerd.
> De Privileged Identity Management Power shell-cmdlets worden ondersteund via de Azure AD-preview-module. Als u een andere module gebruikt en er een fout bericht wordt weer gegeven in de module, moet u deze nieuwe module gaan gebruiken. Als er productie systemen zijn gebouwd op basis van een andere module, neemt u contact op met [pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) .

## <a name="installation-and-setup"></a>Installatie en setup

1. De Azure AD-preview-module installeren

    ```powershell
    Install-module AzureADPreview
    ```

1. Zorg ervoor dat u over de vereiste rolmachtigingen beschikt voordat u doorgaat. Als u probeert beheer taken uit te voeren, zoals het toewijzen van een roltoewijzing of het bijwerken van de rolinstellingen, moet u ervoor zorgen dat u de rol beheerder van globale beheerder of bevoorrechte rol hebt. Als u alleen probeert uw eigen toewijzing te activeren, zijn er geen machtigingen vereist die niet langer zijn dan de standaard gebruikers machtigingen.

1. Verbinding maken met Azure AD.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. Zoek de Tenant-id voor uw Azure AD-organisatie door naar de map met **Azure Active Directory**  >    >  **-** eigenschappen te gaan. Gebruik in de sectie cmdlets deze ID wanneer u de resourceId wilt opgeven.

    ![De organisatie-ID in de eigenschappen voor de Azure AD-organisatie zoeken](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> De volgende secties bieden eenvoudige voor beelden waarmee u aan de slag kunt. Meer gedetailleerde documentatie over de volgende cmdlets vindt u op [https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#privileged_role_management) . U moet echter "kunt" in de para meter providerID vervangen door "aadRoles". U moet ook de Tenant-ID voor uw Azure AD-organisatie gebruiken als de para meter resourceId.

## <a name="retrieving-role-definitions"></a>Roldefinities ophalen

Gebruik de volgende cmdlet om alle ingebouwde en aangepaste Azure AD-rollen in uw Azure AD-organisatie op te halen. Deze belang rijke stap geeft u de toewijzing tussen de rolnaam en de Roledefinitionid hebben. De Roledefinitionid hebben wordt in deze cmdlets gebruikt om te verwijzen naar een specifieke rol.

De Roledefinitionid hebben is specifiek voor uw Azure AD-organisatie en wijkt af van de Roledefinitionid hebben die worden geretourneerd door de functie beheer-API.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

Resultaat:

![Alle rollen voor de Azure AD-organisatie ophalen](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>Roltoewijzingen ophalen

Gebruik de volgende cmdlet om alle roltoewijzingen in uw Azure AD-organisatie op te halen.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

Gebruik de volgende cmdlet om alle roltoewijzingen voor een bepaalde gebruiker op te halen. Deze lijst staat ook bekend als ' mijn rollen ' in de Azure AD-Portal. Het enige verschil is hier dat u een filter voor de onderwerp-ID hebt toegevoegd. De onderwerp-ID in deze context is de gebruikers-ID of de groeps-ID.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

Gebruik de volgende cmdlet om alle roltoewijzingen voor een bepaalde functie op te halen. De Roledefinitionid hebben hier is de ID die wordt geretourneerd door de vorige cmdlet.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

De cmdlets resulteren in een lijst van de hieronder weer gegeven roltoewijzings objecten. De onderwerp-ID is de gebruikers-ID van de gebruiker aan wie de rol is toegewezen. De toewijzings status kan actief of in aanmerking komen. Als de gebruiker actief is en er een ID in het veld LinkedEligibleRoleAssignmentId staat, betekent dit dat de rol momenteel is geactiveerd.

Resultaat:

![Alle roltoewijzingen voor de Azure AD-organisatie ophalen](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>Een rol toewijzen

Gebruik de volgende cmdlet om een in aanmerking komende toewijzing te maken.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

De planning, waarmee de begin-en eind tijd van de toewijzing wordt gedefinieerd, is een object dat kan worden gemaakt zoals in het volgende voor beeld:

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> Als de waarde van endDateTime is ingesteld op NULL, wordt een permanente toewijzing aangeduid.

## <a name="activate-a-role-assignment"></a>Een roltoewijzing activeren

Gebruik de volgende cmdlet om een in aanmerking komende toewijzing te activeren.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

Deze cmdlet is bijna identiek aan de cmdlet voor het maken van een roltoewijzing. Het belangrijkste verschil tussen de cmdlets is dat voor de para meter – type activering ' userAdd ' is in plaats van ' adminAdd '. Het andere verschil is dat de para meter – AssignmentState is "actief" in plaats van "in aanmerking". "

> [!Note]
> Er zijn twee beperkende scenario's voor het activeren van rollen via Power shell.
> 1. Als u het ticket systeem/ticket nummer in de instelling van uw rol nodig hebt, is het niet mogelijk om die als een para meter op te geven. Daarom is het niet mogelijk om de rol te activeren buiten het Azure Portal. Deze functie wordt in de komende maanden geïmplementeerd naar Power shell.
> 1. Als u multi-factor Authentication vereist voor de activering van rollen, is er op dit moment geen manier om de gebruiker te vragen wanneer ze hun rol activeren. In plaats daarvan moeten gebruikers de MFA-Challenge activeren wanneer ze verbinding maken met Azure AD door [dit blog bericht](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) van een van onze technici te volgen. Als u een App ontwikkelt voor PIM, is het een mogelijke implementatie om gebruikers te vragen en ze opnieuw te verbinden met de module nadat ze een "MfaRule"-fout hebben ontvangen.

## <a name="retrieving-and-updating-role-settings"></a>Rolinstellingen ophalen en bijwerken

Gebruik de volgende cmdlet om alle rolinstellingen in uw Azure AD-organisatie op te halen.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

Er zijn vier hoofd objecten in de instelling. Slechts drie van deze objecten worden momenteel door PIM gebruikt. De UserMemberSettings zijn activerings instellingen, AdminEligibleSettings zijn toewijzings instellingen voor in aanmerking komende toewijzingen en de AdminmemberSettings zijn toewijzings instellingen voor actieve toewijzingen.

[![Functie-instellingen ophalen en bijwerken.](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

Als u de functie-instelling wilt bijwerken, moet u het bestaande instellings object voor een bepaalde rol ophalen en wijzigingen aanbrengen:

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

U kunt de instelling vervolgens vooraf Toep assen op een van de objecten voor een bepaalde rol, zoals hieronder wordt weer gegeven. De ID is hier de instellings-ID van de rol die kan worden opgehaald uit het resultaat van de cmdlet lijst rolinstellingen.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>Volgende stappen

- [Een aangepaste Azure AD-rol toewijzen](azure-ad-custom-roles-assign.md)
- [Een aangepaste gebruikersrol toewijzing van Azure AD verwijderen of bijwerken](azure-ad-custom-roles-update-remove.md)
- [Een aangepaste functie toewijzing voor Azure AD configureren](azure-ad-custom-roles-configure.md)
- [Roldefinities in azure AD](../roles/permissions-reference.md)
