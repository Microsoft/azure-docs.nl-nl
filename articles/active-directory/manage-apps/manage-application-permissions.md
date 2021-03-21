---
title: Gebruikers-en beheerders machtigingen beheren-Azure Active Directory | Microsoft Docs
description: Meer informatie over het controleren en beheren van machtigingen voor de toepassing in azure AD. U kunt bijvoorbeeld alle machtigingen intrekken die aan een toepassing zijn verleend.
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd2bc7b9ccc9efe0c6fdf8de02665160667cb8f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256929"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Actie onderneemt over overbevoegd of verdachte toepassingen in Azure Active Directory

Meer informatie over het controleren en beheren van toepassings machtigingen. Dit artikel bevat verschillende acties die u kunt uitvoeren om uw toepassing te beveiligen op basis van het scenario. Deze acties zijn van toepassing op alle toepassingen die zijn toegevoegd aan uw Azure Active Directory-Tenant (Azure AD) via toestemming van de gebruiker of beheerder.

Zie [Azure Active Directory instemming-Framework](../develop/consent-framework.md)voor meer informatie over het inzenden van toepassingen.

## <a name="prerequisites"></a>Vereisten

Als u de volgende acties wilt uitvoeren, moet u zich aanmelden als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.

Als u de toegang tot toepassingen wilt beperken, moet u de gebruikers toewijzing vereisen en vervolgens gebruikers of groepen toewijzen aan de toepassing.  Zie [methoden voor het toewijzen van gebruikers en groepen](./assign-user-or-group-access-portal.md)voor meer informatie.

U kunt toegang krijgen tot de Azure AD-Portal om context afhankelijke Power shell-scripts te krijgen om de acties uit te voeren.
 
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. Selecteer **Machtigingen**. Selecteer in de opdracht balk de optie **Controleer machtigingen**.

![Scherm opname van het venster machtigingen controleren.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Toegang tot een toepassing beheren

U kunt het beste de toegang tot de toepassing beperken door de instelling **gebruikers toewijzing** in te scha kelen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. Selecteer **Eigenschappen** en stel vervolgens de **vereiste gebruiker** in op **Ja**.
5. Selecteer **gebruikers en groepen** en verwijder de ongewenste gebruikers die zijn toegewezen aan de toepassing.
6. Gebruikers of groepen toewijzen aan de toepassing.

U kunt eventueel alle gebruikers verwijderen die aan de toepassing zijn toegewezen met behulp van Power shell.

## <a name="revoke-all-permissions-for-an-application"></a>Alle machtigingen voor een toepassing intrekken

Als u het Power shell-script gebruikt, worden alle machtigingen ingetrokken die aan deze toepassing zijn verleend.

> [!NOTE]
> Als u de huidige verleende machtiging intrekt, worden gebruikers niet meer gestemd op de toepassing. Als u wilt voor komen dat gebruikers worden gestemd, lees dan [configureren hoe gebruikers toestemming geven voor toepassingen](configure-user-consent.md).

U kunt de toepassing eventueel uitschakelen om te voor komen dat gebruikers toegang krijgen tot de app en om ervoor te zorgen dat de toepassing geen toegang kan krijgen tot uw gegevens.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. **Eigenschappen** selecteren en vervolgens ingeschakeld instellen **dat gebruikers zich kunnen aanmelden?** 

## <a name="investigate-a-suspicious-application"></a>Een verdachte toepassing onderzoeken

U kunt het beste de toegang tot de toepassing beperken door de instelling **gebruikers toewijzing** in te scha kelen. Controleer vervolgens de machtigingen die gebruikers en beheerders hebben verleend aan de toepassing.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
3. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
5. Selecteer de toepassing waarvoor u de toegang wilt beperken.
6. Selecteer **Eigenschappen** en stel vervolgens de **vereiste gebruiker** in op **Ja**.
7. Selecteer **machtigingen** en controleer de machtigingen beheerder en gebruiker gemachtigd.

U kunt ook met behulp van Power shell het volgende doen:

- Verwijder alle toegewezen gebruikers om zich aan te melden bij de toepassing.
- Vernieuwings tokens ongeldig maken voor gebruikers die toegang hebben tot de toepassing.
- Alle machtigingen voor de toepassing intrekken.

U kunt de toepassing ook uitschakelen om de toegang van gebruikers te blok keren en de toegang tot uw gegevens van de toepassing te stoppen.


## <a name="disable-a-malicious-application"></a>Een schadelijke toepassing uitschakelen 

We raden u aan de toepassing uit te scha kelen om de toegang van gebruikers te blok keren en om te voor komen dat de toepassing toegang heeft tot uw gegevens. Als u in plaats daarvan de toepassing verwijdert, kunnen gebruikers de toepassing opnieuw toestemming geven en toegang verlenen tot uw gegevens.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. Selecteer **Eigenschappen** en kopieer vervolgens de object-id.

### <a name="powershell-commands"></a>PowerShell-opdrachten


De object-ID van de Service-Principal ophalen.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als een globale beheerder, een toepassings beheerder of een Cloud toepassings beheerder.
2. Selecteer **Azure Active Directory**  >  **bedrijfs toepassingen**.
3. Selecteer de toepassing waarvoor u de toegang wilt beperken.
4. Selecteer **Eigenschappen** en kopieer vervolgens de object-id.

```powershell
$sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
$sp.ObjectId
```
Verwijder alle gebruikers die zijn toegewezen aan de toepassing.
 ```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
 ```

Machtigingen intrekken die aan de toepassing worden verleend.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```
De vernieuwings tokens ongeldig maken.
```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```
## <a name="next-steps"></a>Volgende stappen
- [Toestemming voor toepassingen beheren en aanvraag voor toestemming evalueren](manage-consent-requests.md)
- [Gebruikerstoestemming configureren](configure-user-consent.md)
- [Werk stroom voor het beheer van toestemming configureren](configure-admin-consent-workflow.md)
