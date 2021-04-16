---
title: App-toestemmingsbeleid beheren in Azure AD
description: Meer informatie over het beheren van ingebouwd en aangepast app-toestemmingsbeleid om te bepalen wanneer toestemming kan worden verleend.
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
ms.openlocfilehash: 44299fadd17d1acfa292dd88bd57c8be4a44be36
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375692"
---
# <a name="manage-app-consent-policies"></a>Beleid voor app-toestemming beheren

Met Azure AD PowerShell kunt u app-toestemmingsbeleid weergeven en beheren.

Een app-toestemmingsbeleid bestaat uit een of meer 'includes'-voorwaardensets en nul of meer 'excludes'-voorwaardensets. Een gebeurtenis moet in een app-toestemmingsbeleid  in aanmerking komen voor ten minste één  'includes'-voorwaardeset en mag niet overeenkomen met een voorwaarde die is ingesteld voor 'uitsluiten'.

Elke voorwaardenset bestaat uit verschillende voorwaarden. Een gebeurtenis moet overeenkomen met een voorwaardeset als *aan* alle voorwaarden in de voorwaardenset wordt voldaan.

App-toestemmingsbeleid waarbij de id begint met 'microsoft-' zijn ingebouwde beleidsregels. Sommige van deze ingebouwde beleidsregels worden gebruikt in bestaande ingebouwde adreslijstrollen. In het app-toestemmingsbeleid worden bijvoorbeeld de voorwaarden beschreven waaronder de rollen `microsoft-application-admin` Toepassingsbeheerder en Cloudtoepassingsbeheerder tenantbrede beheerdersmachtigingen mogen verlenen. Ingebouwd beleid kan worden gebruikt in aangepaste adreslijstrollen en om toestemmingsinstellingen voor gebruikers te configureren, maar kan niet worden bewerkt of verwijderd.

## <a name="pre-requisites"></a>Vereisten

1. Zorg ervoor dat u de [AzureADPreview-module](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) gebruikt. Deze stap is belangrijk als u zowel de [AzureAD-module](/powershell/module/azuread/) als de [AzureADPreview-module hebt](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) geïnstalleerd.

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Maak verbinding met Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>Bestaand app-toestemmingsbeleid op een lijst zetten

Het is een goed idee om eerst vertrouwd te raken met het bestaande app-toestemmingsbeleid in uw organisatie:

1. Alle app-toestemmingsbeleidsregels opsnissen:

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. Bekijk de voorwaardensets 'bevat' van een beleid:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. Bekijk de voorwaardensets 'excludes':

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>Een aangepast app-toestemmingsbeleid maken

Volg deze stappen om een aangepast app-toestemmingsbeleid te maken:

1. Maak een nieuw leeg app-toestemmingsbeleid.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. Voeg 'includes'-voorwaardensets toe.

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   Herhaal deze stap om aanvullende voorwaardesets voor opnemen toe te voegen.

1. Voeg eventueel voorwaardesets 'excludes' toe.

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

   Herhaal deze stap om aanvullende voorwaardesets voor uitsluiten toe te voegen.

Zodra het app-toestemmingsbeleid is gemaakt, kunt u toestemming van de gebruiker [toestaan,](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) afhankelijk van dit beleid.

## <a name="delete-a-custom-app-consent-policy"></a>Een aangepast app-toestemmingsbeleid verwijderen

1. Hieronder ziet u hoe u een aangepast app-toestemmingsbeleid kunt verwijderen. **Deze actie kan niet ongedaan worden gemaakt.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> Het beleid voor verwijderde app-toestemming kan niet worden hersteld. Als u per ongeluk een aangepast app-toestemmingsbeleid verwijdert, moet u het beleid opnieuw maken.

---

### <a name="supported-conditions"></a>Ondersteunde voorwaarden

De volgende tabel bevat de lijst met ondersteunde voorwaarden voor app-toestemmingsbeleid.

| Voorwaarde | Description|
|:---------------|:----------|
| PermissionClassification | De [machtigingsclassificatie](configure-permission-classifications.md) voor de machtiging die wordt verleend, of 'alle' die moet overeenkomen met een machtigingsclassificatie (inclusief machtigingen die niet zijn geclassificeerd). De standaardwaarde is 'all'. |
| PermissionType | Het machtigingstype van de machtiging die wordt verleend. Gebruik 'toepassing' voor toepassingsmachtigingen (bijvoorbeeld app-rollen) of 'gedelegeerd' voor gedelegeerde machtigingen. <br><br>**Opmerking:** de waarde 'delegatedUserConsentable' geeft gedelegeerde machtigingen aan die niet door de API-uitgever zijn geconfigureerd om beheerders toestemming te vereisen. Deze waarde kan worden gebruikt in ingebouwd beleid voor machtigingen verlenen, maar kan niet worden gebruikt in aangepast beleid voor machtigingen verlenen. Vereist. |
| ResourceApplication | De **AppId** van de resourcetoepassing (bijvoorbeeld de API) waarvoor een machtiging wordt verleend of 'elke' die moet overeenkomen met een resourcetoepassing of API. De standaardwaarde is 'any'. |
| Machtigingen | De lijst met machtigings-ID's voor de specifieke machtigingen die moeten overeenkomen met of een lijst met de enkele waarde 'all' die moet overeenkomen met een machtiging. De standaardwaarde is de enkele waarde 'all'. <ul><li>Gedelegeerde machtigings-ID's vindt u in de **eigenschap OAuth2Permissions** van het ServicePrincipal-object van de API.</li><li>Toepassingsmachtigings-ID's vindt u in de **eigenschap AppRoles** van het ServicePrincipal-object van de API.</li></ol> |
| ClientApplicationIds | Een lijst met **AppId-waarden** waarmee de clienttoepassingen moeten overeenkomen, of een lijst met de enkele waarde 'all' om overeen te komen met elke clienttoepassing. De standaardwaarde is de enkele waarde 'all'. |
| ClientApplicationTenantIds | Een lijst met Azure Active Directory tenant-ID's waarin de clienttoepassing is geregistreerd, of een lijst met de enkele waarde 'all' die moet overeenkomen met client-apps die zijn geregistreerd in een tenant. De standaardwaarde is de enkele waarde 'all'. |
| ClientApplicationPublisherIds | Een lijst met Microsoft Partner Network-ID's (MPN) voor geverifieerde uitgevers van de [clienttoepassing,](../develop/publisher-verification-overview.md) of een lijst met de enige waarde 'alle' die moet overeenkomen met client-apps van elke uitgever. De standaardwaarde is de enkele waarde 'all'. |
| ClientApplicationsFromVerifiedPublisherOnly | Stel in `$true` op om alleen overeen te komen op clienttoepassingen met een [geverifieerde uitgevers](../develop/publisher-verification-overview.md). Stel in `$false` op overeenkomst op elke client-app, zelfs als deze geen geverifieerde uitgever heeft. De standaardinstelling is `$false`. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

* [Instellingen voor gebruikers toestemming configureren](configure-user-consent.md)
* [De werkstroom voor beheerders toestemming configureren](configure-admin-consent-workflow.md)
* [Meer informatie over het beheren van toestemming voor toepassingen en het evalueren van toestemmingsaanvragen](manage-consent-requests.md)
* [Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)
* [Machtigingen en toestemming in het Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

Om hulp te krijgen of antwoorden op uw vragen te vinden:
* [Azure AD in Microsoft Q&A](/answers/products/)