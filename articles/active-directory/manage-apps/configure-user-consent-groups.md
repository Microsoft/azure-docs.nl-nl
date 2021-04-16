---
title: Toestemming van groepseigenaar configureren voor apps die toegang hebben tot groepsgegevens met behulp van Azure AD
description: Meer informatie over beheren of groeps- en teameigenaren toestemming kunnen geven voor toepassingen die toegang hebben tot de gegevens van de groep of het team.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: iangithinji
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: be28148aacf270f2f3cfabad4cbd5f03afa05d3b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374417"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Toestemming van groepseigenaar configureren voor apps die toegang hebben tot groepsgegevens

Groeps- en teameigenaren kunnen toepassingen, zoals toepassingen die zijn gepubliceerd door externe leveranciers, autoreren voor toegang tot de gegevens van uw organisatie die aan een groep zijn gekoppeld. Een teameigenaar in Microsoft Teams kan bijvoorbeeld toestaan dat een app alle Teams-berichten in het team kan lezen of het basisprofiel van de leden van een groep kan bekijken. Zie [Resourcespecifieke toestemming in Microsoft Teams](/microsoftteams/resource-specific-consent) voor meer informatie.

## <a name="manage-group-owner-consent-to-apps"></a>Toestemming van groepseigenaar voor apps beheren

U kunt configureren welke gebruikers toestemming mogen geven voor apps die toegang hebben tot de gegevens van hun groepen of teams, of u kunt dit uitschakelen voor alle gebruikers.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Volg deze stappen om toestemming van groepseigenaar te beheren voor apps die toegang hebben tot groepsgegevens:

1. Meld u als globale [Azure Portal](https://portal.azure.com) aan bij [de Azure Portal.](../roles/permissions-reference.md#global-administrator)
2. Selecteer **Azure Active Directory**  >  **Bedrijfstoepassingen Toestemming** en  >  **machtigingen Instellingen** voor toestemming van de  >  **gebruiker.**
3. Selecteer **onder Toestemming van groepseigenaar voor apps die toegang hebben** tot gegevens de optie die u wilt inschakelen.
4. Selecteer **Opslaan om** uw instellingen op te slaan.

In dit voorbeeld mogen alle groepseigenaren toestemming geven voor apps die toegang hebben tot de gegevens van hun groepen:

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="Toestemmingsinstellingen voor groepseigenaar":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt de Azure AD PowerShell Preview-module, [AzureADPreview,](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)gebruiken om de mogelijkheid van groepseigenaren in of uit te schakelen om toestemming te geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie voor de groepen waar ze eigenaar van zijn.

1. Zorg ervoor dat u de [AzureADPreview-module](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) gebruikt. Deze stap is belangrijk als u zowel de [AzureAD-module](/powershell/module/azuread/) als de [AzureADPreview-module hebt](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) geïnstalleerd.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Maak verbinding met Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Haal de huidige waarde op voor de **directory-instellingen voor toestemmingsbeleid** in uw tenant. Hiervoor moet worden gecontroleerd of de directory-instellingen voor deze functie zijn gemaakt, en zo niet, met behulp van de waarden uit de bijbehorende directory-instellingensjabloon.

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

1. Inzicht in de instellingswaarden. Er zijn twee instellingenwaarden waarmee wordt bepaald welke gebruikers een app toegang kunnen geven tot de gegevens van hun groep:

    | Instelling       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Booleaans | Vlag die aangeeft of groepseigenaren groepspecifieke machtigingen mogen verlenen. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | Als _EnableGroupSpecificConsent_ is ingesteld op 'True' en deze waarde is ingesteld op de object-id van een groep, worden leden van de geïdentificeerde groep gemachtigd om groepsspecifieke machtigingen te verlenen aan de groepen waar ze eigenaar van zijn. |

1. Werk instellingenwaarden bij voor de gewenste configuratie:

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

* [Toestemmingsinstellingen voor gebruikers configureren](configure-user-consent.md)
* [De werkstroom voor beheerders toestemming configureren](configure-admin-consent-workflow.md)
* [Meer informatie over het beheren van toestemming voor toepassingen en het evalueren van toestemmingsaanvragen](manage-consent-requests.md)
* [Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)
* [Machtigingen en toestemming in het Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

Om hulp te krijgen of antwoorden op uw vragen te vinden:
* [Azure AD in Microsoft Q&A ](/answers/topics/azure-active-directory.html)