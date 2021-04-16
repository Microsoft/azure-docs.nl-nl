---
title: Gebruikers- en beheerdersmachtigingen beheren - Azure Active Directory | Microsoft Docs
description: Meer informatie over het controleren en beheren van machtigingen voor de toepassing in Azure AD. U kunt bijvoorbeeld alle machtigingen intrekken die aan een toepassing zijn verleend.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: iangithinji
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd137030e4e1f3e88f47ec5ba78b3bde08fe068
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373975"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Actie ondernemen op overprilegeerde of verdachte toepassingen in Azure Active Directory

Meer informatie over het controleren en beheren van toepassingsmachtigingen. Dit artikel bevat verschillende acties die u kunt ondernemen om uw toepassing te beveiligen op basis van het scenario. Deze acties zijn van toepassing op alle toepassingen die zijn toegevoegd aan uw Azure Active Directory (Azure AD)-tenant via toestemming van de gebruiker of beheerder.

Zie toestemmingskader voor Azure Active Directory voor meer informatie over het [verlenen van toestemming aan toepassingen.](../develop/consent-framework.md)

## <a name="prerequisites"></a>Vereisten

Als u de volgende acties wilt uitvoeren, moet u zich aanmelden als globale beheerder, toepassingsbeheerder of cloudtoepassingsbeheerder.

Als u de toegang tot toepassingen wilt beperken, moet u gebruikerstoewijzing vereisen en vervolgens gebruikers of groepen toewijzen aan de toepassing.  Zie Methoden voor het [toewijzen van gebruikers en groepen voor meer informatie.](./assign-user-or-group-access-portal.md)

U kunt de Azure AD-portal openen om contextuele PowerShell-scripts op te halen om de acties uit te voeren.
 
1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder, toepassingsbeheerder of cloudtoepassingsbeheerder.
2. Selecteer **Azure Active Directory**  >  **Bedrijfstoepassingen.**
3. Selecteer de toepassing tot wie u de toegang wilt beperken.
4. Selecteer **Machtigingen**. Selecteer machtigingen controleren **in de opdrachtbalk.**

![Schermopname van het venster Machtigingen controleren.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Toegang tot een toepassing controleren

U wordt aangeraden de toegang tot de toepassing te beperken door de instelling **Gebruikerstoewijzing in te** stellen.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder, toepassingsbeheerder of cloudtoepassingsbeheerder.
2. Selecteer **Azure Active Directory**  >  **Bedrijfstoepassingen.**
3. Selecteer de toepassing tot wie u de toegang wilt beperken.
4. Selecteer **Eigenschappen** en stel **Gebruikersvereiste vereist in** op **Ja.**
5. Selecteer **Gebruiker en groepen** en verwijder vervolgens de ongewenste gebruikers die zijn toegewezen aan de toepassing.
6. Wijs gebruikers of groepen toe aan de toepassing.

U kunt eventueel alle gebruikers verwijderen die zijn toegewezen aan de toepassing met behulp van PowerShell.

## <a name="revoke-all-permissions-for-an-application"></a>Alle machtigingen voor een toepassing intrekken

Met het PowerShell-script worden alle machtigingen ingetrokken die aan deze toepassing zijn verleend.

> [!NOTE]
> Door de huidige verleende machtiging in te trekken, kunnen gebruikers niet voorkomen dat ze opnieuw toestemming geven voor de toepassing. Als u wilt blokkeren dat gebruikers toestemming geven, leest u Configureren [hoe gebruikers toestemming geven voor toepassingen.](configure-user-consent.md)

U kunt de toepassing desgewenst uitschakelen om te zorgen dat gebruikers geen toegang hebben tot de app en om te zorgen dat de toepassing geen toegang heeft tot uw gegevens.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder, toepassingsbeheerder of cloudtoepassingsbeheerder.
2. Selecteer **Azure Active Directory**  >  **Bedrijfstoepassingen.**
3. Selecteer de toepassing tot wie u de toegang wilt beperken.
4. Selecteer **Eigenschappen** en stel vervolgens **Ingeschakeld voor gebruikers om zich aan te melden? in** op **Nee.**

## <a name="investigate-a-suspicious-application"></a>Een verdachte toepassing onderzoeken

U wordt aangeraden de toegang tot de toepassing te beperken door de instelling **Gebruikerstoewijzing in te** stellen. Controleer vervolgens de machtigingen die gebruikers en beheerders aan de toepassing hebben verleend.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder, toepassingsbeheerder of cloudtoepassingsbeheerder.
3. Selecteer **Azure Active Directory**  >  **Bedrijfstoepassingen.**
5. Selecteer de toepassing tot wie u de toegang wilt beperken.
6. Selecteer **Eigenschappen** en stel **Gebruikersvereiste vereist in** op **Ja.**
7. Selecteer **Machtigingen** en controleer de machtigingen van de beheerder en de gebruiker.

Optioneel kunt u met behulp van PowerShell het volgende doen:

- Verwijder alle toegewezen gebruikers om te voorkomen dat ze zich aanmelden bij de toepassing.
- Vernieuw tokens ongeldig voor gebruikers die toegang hebben tot de toepassing.
- Alle machtigingen voor de toepassing intrekken.

U kunt de toepassing ook uitschakelen om de toegang van gebruikers te blokkeren en de toegang van de toepassing tot uw gegevens te stoppen.


## <a name="disable-a-malicious-application"></a>Een schadelijke toepassing uitschakelen 

U wordt aangeraden de toepassing uit te schakelen om de toegang van gebruikers te blokkeren en om te zorgen dat de toepassing geen toegang heeft tot uw gegevens. Als u in plaats daarvan de toepassing verwijdert, kunnen gebruikers opnieuw toestemming geven voor de toepassing en toegang verlenen tot uw gegevens.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder, toepassingsbeheerder of cloudtoepassingsbeheerder.
2. Selecteer **Azure Active Directory**  >  **Bedrijfstoepassingen.**
3. Selecteer de toepassing tot wie u de toegang wilt beperken.
4. Selecteer **Eigenschappen** en kopieer vervolgens de object-id.

### <a name="powershell-commands"></a>PowerShell-opdrachten


Haal de object-id van de service-principal op.

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale beheerder, toepassingsbeheerder of cloudtoepassingsbeheerder.
2. Selecteer **Azure Active Directory**  >  **Bedrijfstoepassingen.**
3. Selecteer de toepassing tot wie u de toegang wilt beperken.
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
De vernieuwingstokens ongeldig maken.
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
- [Toestemming voor toepassingen beheren en toestemmingsaanvraag evalueren](manage-consent-requests.md)
- [Gebruikerstoestemming configureren](configure-user-consent.md)
- [Werkstroom voor beheerders toestemming configureren](configure-admin-consent-workflow.md)
