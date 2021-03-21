---
title: Configureren hoe eind gebruikers toestemming geven voor toepassingen met Azure AD
description: Meer informatie over hoe u kunt beheren hoe en wanneer gebruikers toestemming kunnen geven voor toepassingen die toegang hebben tot de gegevens van uw organisatie.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 68bb846ebb0199691161bc501441df908eb8ad87
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101643606"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Configureren hoe eindgebruikers toestemming geven voor toepassingen

U kunt uw toepassingen integreren met het micro soft Identity-platform zodat gebruikers zich kunnen aanmelden met hun werk-of school account en toegang hebben tot de gegevens van uw organisatie om uitgebreide gegevensgestuurde ervaringen te leveren.

Voordat een toepassing toegang kan krijgen tot de gegevens van uw organisatie, moet een gebruiker de toepassings machtigingen hiervoor verlenen. Met verschillende machtigingen kunnen verschillende toegangs niveaus worden toegestaan. Standaard kunnen alle gebruikers toestemming geven voor machtigingen waarvoor geen toestemming van de beheerder nodig is. Een gebruiker kan bijvoorbeeld standaard toestemming geven om toegang te krijgen tot het postvak van een app, maar kan niet toestemming geven om de toegang tot alle bestanden in uw organisatie te lezen en te schrijven voor een app onbelemmerde.

Door gebruikers toe te staan apps toegang te verlenen tot gegevens, kunnen gebruikers eenvoudig nuttige toepassingen verkrijgen en productief zijn. In sommige gevallen kan deze configuratie echter een risico vormen als deze niet zorgvuldig wordt bewaakt en beheerd.

> [!IMPORTANT]
> Om het risico te verkleinen dat kwaadwillende toepassingen gebruikers toegang geven tot de gegevens van uw organisatie, wordt u aangeraden alleen toestemming van de gebruiker toe te staan voor toepassingen die zijn gepubliceerd door een [gecontroleerde uitgever](../develop/publisher-verification-overview.md).

## <a name="user-consent-settings"></a>Instellingen voor gebruikers toestemming

Het app-toestemming beleid beschrijft voor waarden waaraan moet worden voldaan voordat een app kan worden ingestemd. Dit beleid kan voor waarden bevatten op de app die toegang vraagt, evenals de machtigingen die de app aanvraagt.

Als u kiest welke app-machtigings beleid van toepassing is voor alle gebruikers, kunt u limieten instellen voor wanneer eind gebruikers toestemming mogen geven tot apps, en wanneer ze moeten worden gevraagd om de beheerder en goed keuring van beheerders te vragen:

* **Gebruikers toestemming uitschakelen** : gebruikers kunnen geen machtigingen toekennen aan toepassingen. Gebruikers kunnen zich blijven aanmelden bij apps die voorheen hebben gecommuniceerd naar of die zijn gemachtigd door beheerders in hun naam, maar ze mogen niet toestemming geven voor nieuwe machtigingen of aan nieuwe apps. Alleen gebruikers die een directory-rol met toestemming voor het verlenen van toestemming hebben verleend, kunnen toestemming geven voor nieuwe apps.

* **Gebruikers kunnen toestemming geven voor apps van geverifieerde uitgevers of uw organisatie, maar alleen voor machtigingen die u selecteert** . alle gebruikers kunnen alleen toestemming geven voor apps die zijn gepubliceerd door een [geverifieerde Uitgever](../develop/publisher-verification-overview.md) en apps die zijn geregistreerd in uw Tenant. Gebruikers kunnen alleen toestemming geven voor de machtigingen die u hebt geclassificeerd als ' lage impact '. U moet de [machtigingen classificeren](configure-permission-classifications.md) om te selecteren welke machtigingen gebruikers toestemming mogen geven.

* **Gebruikers kunnen voor alle apps toestemming** geven: met deze optie kunnen alle gebruikers toestemming geven voor machtigingen waarvoor geen beheerders toestemming is vereist voor elke toepassing.

* **Aangepast beleid** voor de toestemming van de app-voor nog meer opties voor de voor waarden die gelden voor de toestemming van de gebruiker, kunt u een [aangepast beleid voor de toestemming van apps maken](manage-app-consent-policies.md#create-a-custom-app-consent-policy)en configureren dat deze voor toestemming van de gebruiker worden toegepast.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Instellingen voor gebruikers toestemming configureren via de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) als [globale beheerder](../roles/permissions-reference.md#global-administrator).
1. Selecteer **Azure Active Directory**  >  toestemming van **bedrijfs toepassingen**  >  **en machtigingen**  >  **instellingen voor gebruikers toestemming**.
1. Selecteer onder **toestemming van de gebruiker voor toepassingen** de instelling van de toestemming die u wilt configureren voor alle gebruikers.
1. Selecteer **Opslaan** om uw instellingen op te slaan.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="Instellingen voor gebruikers toestemming":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt de nieuwste Azure AD Power shell preview-module, [AzureADPreview](/powershell/azure/active-directory/install-adv2?preserve-view=true&view=azureadps-2.0-preview), gebruiken om te kiezen welk app-toestemming beleid gebruikers instemming voor toepassingen bepaalt.

#### <a name="disable-user-consent"></a>Toestemming van de gebruiker uitschakelen

Als u toestemming van de gebruiker wilt uitschakelen, stelt u het beleid voor toestemming voor de toestemming van de gebruiker in op leeg:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>Toestemming van de gebruiker toestaan voor een app-toestemming beleid

Als u toestemming van de gebruiker wilt toestaan, kiest u welk app-machtigings beleid gebruikers autorisatie moet bepalen om toestemming te verlenen aan apps:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

Vervang door `{consent-policy-id}` de id van het beleid dat u wilt Toep assen. U kunt kiezen voor een [aangepast app-toestemming beleid](manage-app-consent-policies.md#create-a-custom-app-consent-policy) dat u hebt gemaakt, of u kunt kiezen uit de volgende ingebouwde beleids regels:

| Id | Beschrijving |
|:---|:------------|
| micro soft-gebruiker-standaard-laag | **Gebruikers toestemming geven voor apps van geverifieerde uitgevers, voor geselecteerde machtigingen**<br /> Sta beperkte toestemming van de gebruiker alleen toe voor apps van geverifieerde uitgevers en apps die zijn geregistreerd in uw Tenant, en alleen voor machtigingen die u als ' lage impact ' classificeert. (Vergeet niet om [machtigingen te classificeren](configure-permission-classifications.md) om te selecteren naar welke machtigingen gebruikers toestemming mogen geven.) |
| micro soft-gebruiker-standaard-verouderd | **Toestemming van de gebruiker voor apps toestaan**<br /> Met deze optie kunnen alle gebruikers toestemming geven voor machtigingen waarvoor geen beheerders toestemming is vereist voor elke toepassing |
  
Als u bijvoorbeeld toestemming van de gebruiker wilt verlenen aan het ingebouwde beleid `microsoft-user-default-low` :

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> [Schakel de beheerder toestemming werk stroom](configure-admin-consent-workflow.md) in om gebruikers toe te staan om de controle en goed keuring van een beheerder aan te vragen van een toepassing waarbij de gebruiker niet toestemming mag geven, bijvoorbeeld wanneer toestemming van de gebruiker is uitgeschakeld of wanneer een toepassing machtigingen aanvraagt die de gebruiker niet mag verlenen.

## <a name="risk-based-step-up-consent"></a>Op risico gebaseerde Step-up toestemming

Met behulp van op risico gebaseerde stapsgewijze toestemming kan de gebruikers bloot stellen aan schadelijke apps die [illegale toestemming aanvragen](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)doen. Als micro soft een aanvraag voor een Risk ante toestemming van de eind gebruiker detecteert, heeft de aanvraag een ' stapsgewijs ' door gegeven aan de beheerder. Deze mogelijkheid is standaard ingeschakeld, maar dit resulteert alleen in een wijziging in het gedrag wanneer toestemming van de eind gebruiker is ingeschakeld.

Wanneer een Risk ante toestemming aanvraag wordt gedetecteerd, wordt in de toestemming prompt een bericht weer gegeven waarin wordt aangegeven dat de goed keuring van de beheerder nodig is. Als de [aanvraag werk stroom beheerder toestemming](configure-admin-consent-workflow.md) is ingeschakeld, kan de gebruiker de aanvraag naar een beheerder verzenden voor verdere controle, rechtstreeks vanuit de toestemming prompt. Als de functie niet is ingeschakeld, wordt het volgende bericht weer gegeven:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; heeft toestemming nodig om toegang te krijgen tot bronnen in uw organisatie die alleen door een beheerder kunnen worden verleend. Vraag een beheerder om toestemming te verlenen voor deze app voordat u deze kunt gebruiken.

In dit geval wordt er ook een controle gebeurtenis vastgelegd met de categorie ' ApplicationManagement ', het activiteitstype ' instemming met de toepassing ' en de status van ' Risk ante toepassing gedetecteerd '.

> [!IMPORTANT]
> Beheerders moeten [alle toestemming aanvragen zorgvuldig evalueren](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) voordat ze een aanvraag goed keuren, met name wanneer micro soft risico heeft gedetecteerd.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Op risico gebaseerde Step-up van de stap uitschakelen of opnieuw inschakelen met behulp van Power shell

U kunt de Azure AD Power shell preview-module, [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview), gebruiken om de stap-tot-toestemming van de beheerder uit te scha kelen in gevallen waarin micro soft risico detecteert of het opnieuw inschakelt als dit eerder was uitgeschakeld.

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. Meer informatie over de instellingen waarde:

    | Instelling       | Type         | Beschrijving  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Booleaans |  Markering die aangeeft of de gebruikers toestemming wordt geblokkeerd wanneer een Risk ante aanvraag wordt gedetecteerd. |

1. Waarde voor de update-instellingen voor de gewenste configuratie:

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

* [Instellingen voor gebruikers toestemming configureren](configure-user-consent.md)
* [Beleid voor app-toestemming beheren](manage-app-consent-policies.md)
* [De beheerder toestemming werk stroom configureren](configure-admin-consent-workflow.md)
* [Meer informatie over het beheren van toestemming voor toepassingen en het evalueren van toestemming aanvragen](manage-consent-requests.md)
* [Een toepassing beheerderstoestemming verlenen voor de hele tenant](grant-admin-consent.md)
* [Machtigingen en toestemming in het micro soft Identity-platform](../develop/v2-permissions-and-consent.md)

Om hulp te krijgen of antwoorden op uw vragen te vinden:
* [Azure AD op micro soft Q&A.](/answers/topics/azure-active-directory.html)