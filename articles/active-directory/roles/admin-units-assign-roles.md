---
title: Rollen toewijzen en een lijst met beheereenheidbereik - Azure Active Directory | Microsoft Docs
description: Gebruik beheereenheden om het bereik van roltoewijzingen in de Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: e24bf5df84015ada6b62c35fdd29571c66e06ebd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505255"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>Specifieke rollen toewijzen aan een beheereenheid

In Azure Active Directory (Azure AD) kunt u voor gedetailleerder beheer gebruikers toewijzen aan een Azure AD-rol met een bereik dat is beperkt tot een of meer beheereenheden.

Zie Aan de slag om het gebruik van PowerShell en Microsoft Graph voor beheer van [beheereenheden voor te bereiden.](admin-units-manage.md#get-started)

## <a name="available-roles"></a>Beschikbare rollen

Rol  |  Beschrijving
----- |  -----------
Verificatiebeheerder  |  Heeft alleen toegang tot het weergeven, instellen en opnieuw instellen van verificatiemethodegegevens voor gebruikers die geen beheerder zijn in de toegewezen beheereenheid.
Groepsbeheerder  |  Kan alle aspecten van groepen en groepen, zoals naamgeving en verloopbeleid, alleen beheren in de toegewezen beheereenheid.
Helpdeskbeheerder  |  Kan wachtwoorden voor niet-beheerders en helpdeskbeheerders alleen opnieuw instellen in de toegewezen beheereenheid.
Licentiebeheerder  |  Licentietoewijzingen kunnen alleen binnen de beheereenheid worden toegewezen, verwijderd en bijgewerkt.
Wachtwoordbeheerder  |  Kan wachtwoorden voor niet-beheerders en wachtwoordbeheerders alleen binnen de toegewezen beheereenheid opnieuw instellen.
Gebruikersbeheerder  |  Kan alle aspecten van gebruikers en groepen beheren, waaronder het opnieuw instellen van wachtwoorden voor beperkte beheerders binnen de toegewezen beheereenheid.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>Beveiligingsprincipa die kunnen worden toegewezen aan een scoped rol

De volgende beveiligingsprincipa kunnen worden toegewezen aan een rol met een beheereenheidbereik:

* Gebruikers
* Rol toewijsbare cloudgroepen (preview)
* Service Principal Name (SPN)

## <a name="assign-a-scoped-role"></a>Een scoped rol toewijzen

U kunt een scoped rol toewijzen met behulp van Azure Portal, PowerShell of Microsoft Graph.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

1. Ga in Azure Portal naar **Azure AD.**

1. Selecteer **Beheereenheden en** selecteer vervolgens de beheereenheid aan wie u een gebruikersrolbereik wilt toewijzen. 

1. Selecteer in het linkerdeelvenster **Rollen en beheerders om** alle beschikbare rollen weer te geven.

   ![Schermopname van het deelvenster Rol en beheerders voor het selecteren van een beheereenheid waarvan u het rolbereik wilt toewijzen.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. Selecteer de rol die moet worden toegewezen en selecteer vervolgens **Toewijzingen toevoegen.** 

1. Selecteer in **het deelvenster Toewijzingen** toevoegen een of meer gebruikers die aan de rol moeten worden toegewezen.

   ![Selecteer de rol die u wilt bereik en selecteer vervolgens Toewijzingen toevoegen](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Zie Azure AD-rollen toewijzen in PIM als u een rol wilt toewijzen aan een beheereenheid met behulp van Azure AD Privileged Identity Management [(PIM).](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)

### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

U kunt de gemarkeerde sectie wijzigen zoals vereist voor de specifieke omgeving.

### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

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

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>Een lijst weergeven van de beheerders met een bereik in een beheereenheid

U kunt een lijst met beheerders met een bereik weergeven met behulp van Azure Portal, PowerShell of Microsoft Graph.

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

U kunt alle roltoewijzingen weergeven die zijn gemaakt met een beheereenheidbereik in de sectie [Beheereenheden van Azure AD.](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit) 

1. Ga in Azure Portal naar **Azure AD.**

1. Selecteer in het linkerdeelvenster **Beheereenheden** en selecteer vervolgens de beheereenheid voor de lijst met roltoewijzingen die u wilt weergeven. 

1. Selecteer **Rollen en beheerders** en open vervolgens een rol om de toewijzingen in de beheereenheid weer te geven.

### <a name="use-powershell"></a>PowerShell gebruiken

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

U kunt de gemarkeerde sectie wijzigen zoals vereist voor uw specifieke omgeving.

### <a name="use-microsoft-graph"></a>Gebruik Microsoft Graph

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
- [Problemen oplossen met rollen die zijn toegewezen aan cloudgroepen](groups-faq-troubleshooting.md)
