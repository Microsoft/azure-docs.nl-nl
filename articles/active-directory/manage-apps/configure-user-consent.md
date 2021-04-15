---
title: Configureren hoe eindgebruikers toestemming geven voor toepassingen met behulp van Azure AD
description: Leer hoe u beheert hoe en wanneer gebruikers toestemming kunnen geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie.
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
ms.openlocfilehash: 95a651f6201c9f60500c9191821edb7eb76b8535
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374434"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configureren hoe eindgebruikers toestemming geven voor toepassingen

U kunt uw toepassingen integreren met het Microsoft Identity Platform zodat gebruikers zich kunnen aanmelden met hun werk- of schoolaccount en toegang kunnen krijgen tot de gegevens van uw organisatie om rijke gegevensgestuurde ervaringen te bieden.

Voordat een toepassing toegang kan krijgen tot de gegevens van uw organisatie, moet een gebruiker de toepassing daarvoor machtigingen verlenen. Verschillende machtigingen staan verschillende toegangsniveaus toe. Standaard mogen alle gebruikers toestemming geven voor toepassingen voor machtigingen waarvoor geen toestemming van de beheerder is vereist. Een gebruiker kan bijvoorbeeld standaard toestemming geven om een app toegang te geven tot zijn postvak, maar kan geen toestemming geven om een app ongefetterd toegang te geven tot lezen en schrijven naar alle bestanden in uw organisatie.

Door gebruikers apps toegang te geven tot gegevens, kunnen gebruikers eenvoudig nuttige toepassingen verkrijgen en productief zijn. In sommige gevallen kan deze configuratie echter een risico vormen als deze niet zorgvuldig wordt bewaakt en gecontroleerd.

> [!IMPORTANT]
> Om het risico te beperken dat kwaadwillende toepassingen proberen gebruikers toegang te geven tot de gegevens van uw organisatie, wordt u aangeraden om alleen toestemming van de gebruiker toe te staan voor toepassingen die zijn gepubliceerd door een geverifieerde [uitgever.](../develop/publisher-verification-overview.md)

## <a name="user-consent-settings"></a>Instellingen voor gebruikers toestemming

App-toestemmingsbeleid beschrijft voorwaarden waaraan moet worden voldaan voordat toestemming kan worden verleend aan een app. Deze beleidsregels kunnen voorwaarden bevatten voor de app die toegang aanvraagt, evenals de machtigingen die de app aanvraagt.

Door te kiezen welk app-toestemmingsbeleid van toepassing is op alle gebruikers, kunt u limieten instellen voor wanneer eindgebruikers toestemming mogen verlenen aan apps en wanneer ze beheerdersbeoordeling en -goedkeuring moeten aanvragen:

* **Gebruikers toestemming uitschakelen:** gebruikers kunnen geen machtigingen verlenen aan toepassingen. Gebruikers kunnen zich blijven aanmelden bij apps waarvoor ze eerder toestemming hebben gegeven of waarvoor beheerders namens hen toestemming hebben gegeven, maar ze mogen niet zelf toestemming geven voor nieuwe machtigingen of nieuwe apps. Alleen gebruikers aan wie een directoryrol is verleend die de machtiging voor het verlenen van toestemming bevat, kunnen toestemming geven voor nieuwe apps.

* Gebruikers kunnen toestemming geven voor apps van geverifieerde uitgevers of uw **organisatie,** maar alleen voor machtigingen die u selecteert: alle gebruikers kunnen alleen toestemming geven voor apps die zijn gepubliceerd door een geverifieerde uitgever en apps die zijn geregistreerd in uw tenant. [](../develop/publisher-verification-overview.md) Gebruikers kunnen alleen toestemming geven voor de machtigingen die u hebt geclassificeerd als 'lage impact'. U moet [machtigingen classificeren](configure-permission-classifications.md) om te selecteren voor welke machtigingen gebruikers toestemming mogen geven.

* **Gebruikers kunnen toestemming geven voor alle apps:** met deze optie kunnen alle gebruikers toestemming geven voor elke machtiging waarvoor geen beheerders toestemming is vereist, voor elke toepassing.

* **Aangepast app-toestemmingsbeleid:** voor nog meer opties over de voorwaarden voor het verlenen van toestemming van gebruikers kunt u aangepast [app-toestemmingsbeleid](manage-app-consent-policies.md#create-a-custom-app-consent-policy)maken en deze configureren om toestemming van de gebruiker aan te vragen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Instellingen voor toestemming van de gebruiker configureren via de Azure Portal:

1. Meld u als globale [Azure Portal](https://portal.azure.com) aan bij [de Azure Portal.](../roles/permissions-reference.md#global-administrator)
1. Selecteer **Azure Active Directory**  >  **Bedrijfstoepassingen Toestemming** en  >  **machtigingen Instellingen** voor toestemming van  >  **gebruikers.**
1. Selecteer **onder Gebruikers toestemming voor toepassingen** welke toestemmingsinstelling u wilt configureren voor alle gebruikers.
1. Selecteer **Opslaan om** uw instellingen op te slaan.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Instellingen voor toestemming van de gebruiker":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt de nieuwste Azure AD PowerShell Preview-module, [AzureADPreview,](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview)gebruiken om te kiezen welk app-toestemmingsbeleid de toestemming van gebruikers voor toepassingen bepaalt.

#### <a name="disable-user-consent"></a>Toestemming van de gebruiker uitschakelen

Als u toestemming van de gebruiker wilt uitschakelen, stelt u het toestemmingsbeleid in dat de toestemming van de gebruiker leeg is:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Toestaan dat toestemming van de gebruiker is onderworpen aan een app-toestemmingsbeleid

Als u toestemming van de gebruiker wilt toestaan, kiest u welk app-toestemmingsbeleid de autorisatie van gebruikers moet bepalen om toestemming te verlenen aan apps:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Vervang `{consent-policy-id}` door de id van het beleid dat u wilt toepassen. U kunt een aangepast [app-toestemmingsbeleid kiezen](manage-app-consent-policies.md#create-a-custom-app-consent-policy) dat u hebt gemaakt of u kunt kiezen uit de volgende ingebouwde beleidsregels:

| Id | Description |
|:---|:------------|
| microsoft-user-default-low | **Toestemming van de gebruiker voor apps van geverifieerde uitgevers toestaan voor geselecteerde machtigingen**<br /> Sta alleen beperkte gebruikers toestemming toe voor apps van geverifieerde uitgevers en apps die zijn geregistreerd in uw tenant en alleen voor machtigingen die u classificeert als 'Lage impact'. (Vergeet niet om machtigingen [te classificeren om](configure-permission-classifications.md) te selecteren voor welke machtigingen gebruikers toestemming mogen geven.) |
| microsoft-user-default-legacy | **Toestemming van de gebruiker voor apps toestaan**<br /> Met deze optie kunnen alle gebruikers toestemming geven voor elke machtiging waarvoor geen beheerders toestemming is vereist, voor elke toepassing |
  
Als u bijvoorbeeld toestemming van de gebruiker wilt inschakelen voor het ingebouwde `microsoft-user-default-low` beleid:

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [](configure-admin-consent-workflow.md) Schakel de werkstroom voor beheerdersgoedkeuring in, zodat gebruikers de controle en goedkeuring kunnen aanvragen van een toepassing waar de gebruiker geen toestemming voor mag geven, bijvoorbeeld wanneer toestemming van de gebruiker is uitgeschakeld of wanneer een toepassing machtigingen aanvraagt die de gebruiker niet mag verlenen.

## <a name="risk-based-step-up-consent"></a>Op risico gebaseerde stapsgewijse toestemming

Op risico gebaseerde stapsgewijse toestemming vermindert de blootstelling van gebruikers aan schadelijke apps die [ongeoorloofde toestemmingsaanvragen doen.](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) Als Microsoft een riskante aanvraag voor toestemming van eindgebruikers detecteert, is voor de aanvraag in plaats daarvan een 'stap-up' vereist voor beheerders toestemming. Deze mogelijkheid is standaard ingeschakeld, maar dit leidt alleen tot een gedragswijziging wanneer toestemming van eindgebruikers is ingeschakeld.

Wanneer een aanvraag voor riskante toestemming wordt gedetecteerd, wordt in de toestemmingsprompt een bericht weergegeven waarin wordt aangegeven dat er goedkeuring van de beheerder nodig is. Als de [werkstroom voor de toestemmingsaanvraag](configure-admin-consent-workflow.md) van de beheerder is ingeschakeld, kan de gebruiker de aanvraag rechtstreeks vanaf de toestemmingsprompt naar een beheerder verzenden voor verdere controle. Als deze niet is ingeschakeld, wordt het volgende bericht weergegeven:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; heeft machtigingen nodig voor toegang tot resources in uw organisatie die alleen een beheerder kan verlenen. Vraag een beheerder om toestemming te verlenen voor deze app voordat u deze kunt gebruiken.

In dit geval wordt een controlegebeurtenis ook vastgelegd met de categorie 'ApplicationManagement', het activiteitstype 'Toestemming voor toepassing' en de Statusreden van 'Riskante toepassing gedetecteerd'.

> [!IMPORTANT]
> Beheerders moeten [alle toestemmingsaanvragen zorgvuldig evalueren](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) voordat ze een aanvraag goedkeuren, met name wanneer Microsoft risico's heeft gedetecteerd.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Op risico's gebaseerde stapsgewijse toestemming uitschakelen of opnieuw inschakelen met behulp van PowerShell

U kunt de Azure AD PowerShell Preview-module, [AzureADPreview,](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)gebruiken om de vereiste stapsgewijs toestemming van de beheerder uit te schakelen in gevallen waarin Microsoft risico's detecteert of om deze opnieuw in te schakelen als deze eerder is uitgeschakeld.

1. Zorg ervoor dat u de [AzureADPreview-module](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) gebruikt. Deze stap is belangrijk als u zowel de [AzureAD-module](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0) als de [AzureADPreview-module hebt](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) geïnstalleerd.

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Inzicht in de waarde van instellingen:

    | Instelling       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Booleaans |  Vlag die aangeeft of toestemming van de gebruiker wordt geblokkeerd wanneer een riskante aanvraag wordt gedetecteerd. |

1. Werk de waarde van instellingen bij voor de gewenste configuratie:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

* [Toestemmingsinstellingen voor gebruikers configureren](configure-user-consent.md)
* [Beleid voor app-toestemming beheren](manage-app-consent-policies.md)
* [De werkstroom voor beheerders toestemming configureren](configure-admin-consent-workflow.md)
* [Meer informatie over het beheren van toestemming voor toepassingen en het evalueren van toestemmingsaanvragen](manage-consent-requests.md)
* [Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)
* [Machtigingen en toestemming in het Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

Om hulp te krijgen of antwoorden op uw vragen te vinden:
* [Azure AD op Microsoft Q&A.](/answers/topics/azure-active-directory.html)