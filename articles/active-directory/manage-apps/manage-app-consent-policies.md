---
title: Toestemming beleid voor apps in azure AD beheren
description: Meer informatie over het beheren van ingebouwde en aangepaste beleids regels voor het toestemming van apps om te bepalen wanneer toestemming kan worden verleend.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 62a8b48d6b33a92b62bc4c3634794190585615b7
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222017"
---
# <a name="manage-app-consent-policies"></a>Beleid voor app-toestemming beheren

Met Azure AD Power shell kunt u toestemming beleid voor apps weer geven en beheren.

Een beleid voor het door geven van apps bestaat uit een of meer ' voor waarden sets ' en nul of meer ' uitsluitingen '. Voor een gebeurtenis die in een app-toestemming beleid wordt overwogen, moet deze overeenkomen met *ten minste* één ' voor waardenset ' en mag deze niet overeenkomen met *de* ingestelde voor waarde ' uitsluiten '.

Elke voor waarde die is ingesteld, bestaat uit verschillende voor waarden. Voor een gebeurtenis die overeenkomt met een ingestelde voor waarde, moet aan *alle* voor waarden in de voor waarde worden voldaan.

Beleid voor toestemming van de app waarbij de ID begint met ' micro soft-' zijn ingebouwde beleids regels. Sommige van deze ingebouwde beleids regels worden gebruikt in bestaande ingebouwde Directory rollen. Met het beleid voor toestemming van de app worden bijvoorbeeld de `microsoft-application-admin` voor waarden beschreven waaronder de rollen beheerder en Cloud toepassings beheerder de bevoegdheid hebben om toestemming te geven voor de beheerder voor de hele Tenant. Ingebouwde beleids regels kunnen worden gebruikt in aangepaste Directory-rollen en om instellingen voor gebruikers toestemming te configureren, maar kunnen niet worden bewerkt of verwijderd.

## <a name="pre-requisites"></a>Vereisten

1. Zorg ervoor dat u de [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) -module gebruikt. Deze stap is belang rijk als u de [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) -module en de [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) -module hebt geïnstalleerd.

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Verbinding maken met Azure AD Power shell.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Een lijst met bestaande beleids regels voor toestemming

Het is een goed idee om eerst vertrouwd te raken met het bestaande app-toestemming beleid in uw organisatie:

1. Alle beleids regels voor toestemming van apps weer geven:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Bekijk de voor waarden sets van een beleid onder ' includes ':

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. De voor waarden van de voor waarde ' excludes ' weer geven:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Een aangepast app-toestemming beleid maken

Volg deze stappen voor het maken van een aangepast app-toestemming beleid:

1. Maak een nieuw beleid voor een lege app-toestemming.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Voeg ' include '-waarden sets toe.

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Herhaal deze stap om extra ' include '-voor waarden toe te voegen.

1. Voeg desgewenst voor waarden sets toe.

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   Herhaal deze stap om extra ' exclude '-voor waarden toe te voegen.

Zodra het beleid voor de toestemming van de app is gemaakt, kunt u toestemming van de [gebruiker toestaan](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) voor dit beleid.

## <a name="delete-a-custom-app-consent-policy"></a>Een aangepast app-toestemming beleid verwijderen

1. Hieronder ziet u hoe u een aangepast beleid voor app-toestemming kunt verwijderen. **Deze actie kan niet ongedaan worden gemaakt.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Het verwijderen van een app-toestemming beleid kan niet worden hersteld. Als u per ongeluk een aangepast beleid voor app-toestemming verwijdert, moet u het beleid opnieuw maken.

---

### <a name="supported-conditions"></a>Ondersteunde voor waarden

De volgende tabel bevat de lijst met ondersteunde voor waarden voor het beleid voor de toestemming van apps.

| Voorwaarde | Description|
|:---------------|:----------|
| PermissionClassification | De [machtigings classificatie](configure-permission-classifications.md) voor de machtiging die wordt verleend, of ' all ', zodat deze overeenkomt met een machtigings classificatie (inclusief machtigingen die niet zijn geclassificeerd). De standaard waarde is "all". |
| PermissionType | Het machtigings type van de machtiging die wordt verleend. Gebruik ' Application ' voor toepassings machtigingen (bijvoorbeeld app-rollen) of ' gedelegeerde ' voor gedelegeerde machtigingen. <br><br>**Opmerking**: de waarde ' delegatedUserConsentable ' geeft gedelegeerde machtigingen aan die niet door de API-uitgever zijn geconfigureerd om toestemming te geven aan de beheerder: deze waarde kan worden gebruikt in het ingebouwde beleid voor machtigings toekenning, maar kan niet worden gebruikt in een aangepast beleid voor machtigings verlening. Vereist. |
| ResourceApplication | De **AppId** van de bron toepassing (bijvoorbeeld de API) waarvoor een machtiging wordt verleend, of een wille keurige, die overeenkomt met een resource toepassing of API. De standaard waarde is any. |
| Machtigingen | De lijst met machtigings-Id's voor de specifieke machtigingen die moeten overeenkomen met, of een lijst met de enkele waarde ' all ' zodat deze overeenkomt met elke machtiging. De standaard instelling is de enige waarde all. <ul><li>Gemachtigde machtiging-Id's vindt u in de eigenschap **OAuth2Permissions** van het ServicePrincipal-object van de API.</li><li>Id's van toepassings machtigingen vindt u in de eigenschap **AppRoles** van het ServicePrincipal-object van de API.</li></ol> |
| ClientApplicationIds | Een lijst met **AppId** -waarden voor de client toepassingen die moeten worden vergeleken met, of een lijst met de enkele waarde ' all ' die overeenkomt met elke client toepassing. De standaard instelling is de enige waarde all. |
| ClientApplicationTenantIds | Een lijst met Azure Active Directory Tenant-Id's waarin de client toepassing is geregistreerd, of een lijst met de enkele waarde ' all ' die overeenkomt met de client-apps die zijn geregistreerd in een Tenant. De standaard instelling is de enige waarde all. |
| ClientApplicationPublisherIds | Een lijst met Microsoft Partner Network-Id's (MPN) voor [geverifieerde uitgevers](../develop/publisher-verification-overview.md) van de client toepassing of een lijst met de enkele waarde ' all ' die overeenkomt met client-apps van elke uitgever. De standaard instelling is de enige waarde all. |
| ClientApplicationsFromVerifiedPublisherOnly | Stel dit in `$true` op alleen client toepassingen met een [geverifieerde uitgevers](../develop/publisher-verification-overview.md)te vergelijken. Stel deze in op `$false` een wille keurige client-app, zelfs als deze geen geverifieerde uitgever heeft. De standaardinstelling is `$false`. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

* [Instellingen voor gebruikers toestemming configureren](configure-user-consent.md)
* [De beheerder toestemming werk stroom configureren](configure-admin-consent-workflow.md)
* [Meer informatie over het beheren van toestemming voor toepassingen en het evalueren van toestemming aanvragen](manage-consent-requests.md)
* [Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)
* [Machtigingen en toestemming in het micro soft Identity-platform](../develop/v2-permissions-and-consent.md)

Om hulp te krijgen of antwoorden op uw vragen te vinden:
* [Azure AD op micro soft Q&A](https://docs.microsoft.com/answers/products/)