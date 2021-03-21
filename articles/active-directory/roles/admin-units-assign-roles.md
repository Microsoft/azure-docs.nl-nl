---
title: Rollen toewijzen en weer geven met het bereik van de beheer eenheid-Azure Active Directory | Microsoft Docs
description: Beheer eenheden gebruiken om het bereik van roltoewijzingen in Azure Active Directory te beperken.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fc0c4bf9f71a8fe7e8cf49b83d32ac594dbe062
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103011380"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Specifieke rollen toewijzen aan een beheereenheid

In Azure Active Directory (Azure AD) kunt u, voor uitgebreidere administratieve controle, gebruikers toewijzen aan een Azure AD-rol met een bereik dat beperkt is tot een of meer beheer eenheden.

Zie aan de [slag](admin-units-manage.md#get-started)voor het voorbereiden van het gebruik van Power shell en Microsoft Graph voor beheer van beheer eenheden.

## <a name="available-roles"></a>Beschik bare rollen

Rol  |  Beschrijving
----- |  -----------
Verificatiebeheerder  |  Heeft toegang voor het weer geven, instellen en opnieuw instellen van verificatie methode-informatie voor een niet-beheerders gebruiker in de toegewezen beheer eenheid.
Groepsbeheerder  |  Kan alle aspecten van groepen en groeps instellingen, zoals naamgeving en verloop beleid, in de toegewezen administratieve eenheid beheren.
Helpdeskbeheerder  |  Kan wacht woorden voor niet-beheerders en helpdesk beheerders opnieuw instellen in de toegewezen beheer eenheid.
Licentiebeheerder  |  Kan alleen licentie toewijzingen binnen de beheer eenheid toewijzen, verwijderen en bijwerken.
Wachtwoordbeheerder  |  Kan wacht woorden voor niet-beheerders en wachtwoord beheerders binnen de toegewezen beheer eenheid opnieuw instellen.
Gebruikersbeheerder  |  Kan alle aspecten van gebruikers en groepen beheren, met inbegrip van het opnieuw instellen van wacht woorden voor beperkte beheerders binnen de toegewezen beheer eenheid.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Beveiligings-principals die kunnen worden toegewezen aan een scoped rol

De volgende beveiligings-principals kunnen worden toegewezen aan een rol met een beheer eenheid bereik:

* Gebruikers
* Door de functie toewijs bare Cloud groepen (preview-versie)
* Service Principal Name (SPN)

## <a name="assign-a-scoped-role"></a>Een scoped rol toewijzen

U kunt een scoped-rol toewijzen met behulp van de Azure Portal, Power shell of Microsoft Graph.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in de Azure Portal naar **Azure AD**.

1. Selecteer **beheer eenheden** en selecteer vervolgens de beheer eenheid waaraan u een gebruikersrol bereik wilt toewijzen. 

1. Selecteer in het linkerdeel venster de optie **rollen en beheerders** om alle beschik bare rollen weer te geven.

   ![Scherm opname van het deel venster ' rol en beheerder ' voor het selecteren van een administratieve eenheid waarvan u het rolinstantie wilt toewijzen.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Selecteer de rol die u wilt toewijzen en selecteer vervolgens **toewijzingen toevoegen**. 

1. Selecteer in het deel venster **toewijzingen toevoegen** een of meer gebruikers die moeten worden toegewezen aan de rol.

   ![Selecteer de rol die u wilt bereik en selecteer vervolgens toewijzingen toevoegen](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Zie [Azure AD-rollen toewijzen in PIM](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)als u een rol wilt toewijzen aan een beheer eenheid met behulp van Azure AD PRIVILEGED Identity Management (PIM).

### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Account Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.AzureAD.Model.RoleMemberInfo
$roleMember.ObjectId = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -ObjectId $adminUnitObj.ObjectId -RoleObjectId $role.ObjectId -RoleMemberInfo $roleMember
```

U kunt de gemarkeerde sectie wijzigen als vereist voor de specifieke omgeving.

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Aanvraag

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
Hoofdtekst

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Een lijst met beheerders binnen een beheer eenheid weer geven

U kunt een lijst met scoped beheerders weer geven met behulp van de Azure Portal, Power shell of Microsoft Graph.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt alle roltoewijzingen weer geven die zijn gemaakt met een bereik administratieve eenheid in het [gedeelte Administrative units van Azure AD](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit). 

1. Ga in de Azure Portal naar **Azure AD**.

1. Selecteer **beheer eenheden** in het linkerdeel venster en selecteer vervolgens de beheer eenheid voor de lijst met roltoewijzingen die u wilt weer geven. 

1. Selecteer **rollen en beheerders** en open vervolgens een rol om de toewijzingen in de beheer eenheid weer te geven.

### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -ObjectId $adminUnitObj.ObjectId | fl *
```

U kunt de gemarkeerde sectie wijzigen als vereist voor uw specifieke omgeving.

### <a name="use-microsoft-graph"></a>Microsoft Graph gebruiken

Aanvraag

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

Hoofdtekst

```http
{}
```

## <a name="next-steps"></a>Volgende stappen

- [Cloudgroepen gebruiken om roltoewijzingen te beheren](groups-concept.md)
- [Problemen met rollen die zijn toegewezen aan Cloud groepen oplossen](groups-faq-troubleshooting.md)
