---
title: Machtigingsclassificaties configureren met Azure AD
description: Meer informatie over het beheren van gedelegeerde machtigingsclassificaties.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: be58f5cd18d32302d1e92f00afb7d7e0aae09410
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374468"
---
# <a name="configure-permission-classifications"></a>Machtigingsclassificaties configureren

Met machtigingsclassificaties kunt u de impact identificeren die verschillende machtigingen hebben op basis van het beleid en de risico-evaluaties van uw organisatie. U kunt bijvoorbeeld machtigingsclassificaties in toestemmingsbeleid gebruiken om de set machtigingen te identificeren waar gebruikers toestemming voor mogen geven.

## <a name="manage-permission-classifications"></a>Machtigingsclassificaties beheren

Momenteel wordt alleen de machtigingsclassificatie 'Lage impact' ondersteund. Alleen gedelegeerde machtigingen waarvoor geen beheerdersmachtiging is vereist, kunnen worden geclassificeerd als 'Lage impact'.

> [!TIP]
> De minimale machtigingen die nodig zijn voor het basis aanmelden zijn , , en , die allemaal gedelegeerde machtigingen zijn voor `openid` `profile` de `email` `User.Read` `offline_access` Microsoft Graph. Met deze machtigingen kan een app de volledige profielgegevens van de aangemelde gebruiker lezen en deze toegang behouden, zelfs wanneer de gebruiker de app niet meer gebruikt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Volg deze stappen om machtigingen te classificeren met behulp van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) globale [beheerder,](../roles/permissions-reference.md#global-administrator) [toepassingsbeheerder](../roles/permissions-reference.md#application-administrator)of [cloudtoepassingsbeheerder](../roles/permissions-reference.md#cloud-application-administrator)
1. Selecteer **Azure Active Directory**  >  **Bedrijfstoepassingen**  >  **Toestemming en machtigingen Machtigingsclassificaties**  >  .
1. Kies **Machtigingen toevoegen om** een andere machtiging te classificeren als 'Lage impact'.
1. Selecteer de API en selecteer vervolgens de gedelegeerde machtigingen.

In dit voorbeeld hebben we de minimale set machtigingen geclassificeerd die vereist zijn voor een een aanmelding:

:::image type="content" source="media/configure-permission-classifications/permission-classifications.png" alt-text="Machtigingsclassificaties":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt de nieuwste Azure AD PowerShell Preview-module, [AzureADPreview, gebruiken](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)om machtigingen te classificeren. Machtigingsclassificaties worden geconfigureerd op het **ServicePrincipal-object** van de API die de machtigingen publiceert.

#### <a name="list-the-current-permission-classifications-for-an-api"></a>De huidige machtigingsclassificaties voor een API

1. Haal het **ServicePrincipal-object** voor de API op. Hier halen we het ServicePrincipal-object voor de Microsoft Graph-API op:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Lees de classificaties voor gedelegeerde machtigingen voor de API:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="classify-a-permission-as-low-impact"></a>Een machtiging classificeren als 'Lage impact'

1. Haal het **ServicePrincipal-object** voor de API op. Hier halen we het ServicePrincipal-object voor de Microsoft Graph-API op:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Zoek de gedelegeerde machtiging die u wilt classificeren:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Stel de machtigingsclassificatie in met behulp van de naam en id van de machtiging:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="remove-a-delegated-permission-classification"></a>Een gedelegeerde machtigingsclassificatie verwijderen

1. Haal het **ServicePrincipal-object** voor de API op. Hier halen we het ServicePrincipal-object voor de Microsoft Graph-API op:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Zoek de gedelegeerde machtigingsclassificatie die u wilt verwijderen:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Verwijder de machtigingsclassificatie:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

---

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

* [Toestemmingsinstellingen voor gebruikers configureren](configure-user-consent.md)
* [De werkstroom voor beheerders toestemming configureren](configure-admin-consent-workflow.md)
* [Meer informatie over het beheren van toestemming voor toepassingen en het evalueren van toestemmingsaanvragen](manage-consent-requests.md)
* [Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)
* [Machtigingen en toestemming in het Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

Om hulp te krijgen of antwoorden op uw vragen te vinden:
* [Azure AD in Microsoft Q&A](/answers/topics/azure-active-directory.html)