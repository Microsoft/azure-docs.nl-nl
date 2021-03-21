---
title: Machtigings classificaties configureren met Azure AD
description: Meer informatie over het beheren van gedelegeerde machtigings classificaties.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: phsignor
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 12a4ffb311e01ebb78b1ae392d1243c5d67eff6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644561"
---
# <a name="configure-permission-classifications"></a>Machtigingsclassificaties configureren

Met machtigings classificaties kunt u de invloed identificeren die verschillende machtigingen hebben volgens het beleid van uw organisatie en risico-evaluaties. U kunt bijvoorbeeld machtigings classificaties in toestemming beleid gebruiken om de set machtigingen te identificeren die gebruikers mogen toestemming geven.

## <a name="manage-permission-classifications"></a>Machtigings classificaties beheren

Op dit moment wordt alleen de machtiging classificatie ' lage impact ' ondersteund. Alleen gedelegeerde machtigingen waarvoor geen beheerders toestemming is vereist, kunnen worden geclassificeerd als ' lage impact '.

> [!TIP]
> De minimale machtigingen die nodig zijn voor eenvoudige aanmelding `openid` ,, `profile` , `email` `User.Read` en `offline_access` , zijn alle gedelegeerde machtigingen voor de Microsoft Graph. Met deze machtigingen kan een app de volledige profiel details van de aangemelde gebruiker lezen en kan deze toegang behouden, zelfs wanneer de gebruiker de app niet meer gebruikt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Volg deze stappen om machtigingen te classificeren met behulp van de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als [globale beheerder](../roles/permissions-reference.md#global-administrator), [toepassings beheerder](../roles/permissions-reference.md#application-administrator)of beheerder van de [Cloud toepassing](../roles/permissions-reference.md#cloud-application-administrator)
1. Selecteer **Azure Active Directory**  >  toestemming voor **bedrijfs toepassingen**  >  **en**  >  **classificaties** voor machtigingen.
1. Kies **machtigingen toevoegen** om een andere machtiging als ' lage impact ' te classificeren.
1. Selecteer de API en selecteer vervolgens de gedelegeerde machtiging (en).

In dit voor beeld hebben we de minimale set vereiste machtiging voor eenmalige aanmelding geclassificeerd:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Machtigings classificaties":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt de nieuwste Azure AD Power shell preview-module, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), gebruiken om machtigingen te classificeren. Machtigings classificaties worden geconfigureerd op het **ServicePrincipal** -object van de API die de machtigingen publiceert.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>De huidige machtigings classificaties voor een API weer geven

1. Het **ServicePrincipal** -object voor de API ophalen. Hier ophalen we het ServicePrincipal-object voor de Microsoft Graph-API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Lees de gedelegeerde machtigings classificaties voor de API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Een machtiging classificeren als ' lage impact '

1. Het **ServicePrincipal** -object voor de API ophalen. Hier ophalen we het ServicePrincipal-object voor de Microsoft Graph-API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Zoek de gedelegeerde machtiging die u wilt classificeren:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Stel de machtigings classificatie in met behulp van de naam en ID van de machtiging:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Een gedelegeerde machtigings classificatie verwijderen

1. Het **ServicePrincipal** -object voor de API ophalen. Hier ophalen we het ServicePrincipal-object voor de Microsoft Graph-API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Zoek de gedelegeerde machtigings classificatie die u wilt verwijderen:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. De machtigings classificatie verwijderen:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

* [Instellingen voor gebruikers toestemming configureren](configure-user-consent.md)
* [De beheerder toestemming werk stroom configureren](configure-admin-consent-workflow.md)
* [Meer informatie over het beheren van toestemming voor toepassingen en het evalueren van toestemming aanvragen](manage-consent-requests.md)
* [Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)
* [Machtigingen en toestemming in het micro soft Identity-platform](../develop/v2-permissions-and-consent.md)

Om hulp te krijgen of antwoorden op uw vragen te vinden:
* [Azure AD op micro soft Q&A](/answers/topics/azure-active-directory.html)