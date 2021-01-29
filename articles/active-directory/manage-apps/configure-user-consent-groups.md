---
title: Toestemming van groeps eigenaar configureren voor apps die toegang hebben tot groeps gegevens met behulp van Azure AD
description: Meer informatie over het beheren of groeps-en team eigenaren kunnen toestemming geven voor toepassingen die toegang hebben tot de gegevens van de groep of het team.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: b318ed0d5cd97ca7c020097dff25516f0783c70e
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055294"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Toestemming van groeps eigenaar configureren voor apps die toegang hebben tot groeps gegevens

Groeps-en team eigenaren kunnen toepassingen, zoals toepassingen die door derden worden gepubliceerd, machtigen om toegang te krijgen tot de gegevens van uw organisatie die aan een groep zijn gekoppeld. Een team eigenaar in micro soft teams kan bijvoorbeeld toestaan dat een app alle teams berichten in het team leest of het basis profiel van de leden van een groep vermeldt. Zie [resource-specifieke toestemming in micro soft teams](/microsoftteams/resource-specific-consent) voor meer informatie.

## <a name="manage-group-owner-consent-to-apps"></a>Toestemming van groeps eigenaar beheren voor apps

U kunt configureren welke gebruikers toestemming mogen geven om toegang te krijgen tot de gegevens van hun groepen of teams, of u kunt dit uitschakelen voor alle gebruikers.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Volg deze stappen om de groeps eigenaar toestemming te geven voor het openen van groeps gegevens door apps:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als [globale beheerder](../roles/permissions-reference.md#global-administrator--company-administrator).
2. Selecteer **Azure Active Directory**  >  toestemming van **bedrijfs toepassingen**  >  **en machtigingen**  >  **instellingen voor gebruikers toestemming**.
3. Selecteer onder **toestemming van groeps eigenaar voor apps die toegang krijgen tot gegevens** de optie die u wilt inschakelen.
4. Selecteer **Opslaan** om uw instellingen op te slaan.

In dit voor beeld mogen alle groeps eigenaren toestemming geven voor apps die toegang hebben tot de gegevens van hun groepen:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Instellingen voor toestemming van groeps eigenaar":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt de Azure AD Power shell preview-module, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), gebruiken om de mogelijkheid van de groeps eigenaren in of uit te scha kelen, zodat toepassingen toegang krijgen tot de gegevens van uw organisatie voor de groepen waarvan ze eigenaar zijn.

1. Zorg ervoor dat u de [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) -module gebruikt. Deze stap is belang rijk als u de [AzureAD](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) -module en de [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) -module hebt geïnstalleerd.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Verbinding maken met Azure AD Power shell.

   ```powershell
   Connect-AzureAD
   ```

1. Haal de huidige waarde op voor de instellingen van de map met instellingen voor het **toestemming beleid** in uw Tenant. Hiervoor moet worden gecontroleerd of de Directory-instellingen voor deze functie zijn gemaakt en als dat niet het geval is, gebruikt u de waarden uit de bijbehorende sjabloon voor Directory-instellingen.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. Meer informatie over de instellings waarden. Er zijn twee instellingen waarden die bepalen welke gebruikers een app kunnen gebruiken om toegang te krijgen tot de gegevens van de groep:

    | Instelling       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Booleaans | Vlag waarmee wordt aangegeven of groeps eigenaren specifieke machtigingen mogen verlenen. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Als _EnableGroupSpecificConsent_ is ingesteld op ' True ' en deze waarde is ingesteld op de object-id van een groep, worden leden van de geïdentificeerde groep gemachtigd om groeps-specifieke machtigingen te verlenen aan de groepen waarvan ze eigenaar zijn. |

1. Update-instellingen waarden voor de gewenste configuratie:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. Sla uw wijzigingen op.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
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
* [Azure AD op micro soft Q&A ](https://docs.microsoft.com/answers/topics/azure-active-directory.html)