---
title: De levens duur voor tokens instellen
titleSuffix: Microsoft identity platform
description: Meer informatie over het instellen van de levens duur voor tokens die zijn uitgegeven door het micro soft Identity-platform. Meer informatie over het beheren van het standaard beleid van een organisatie, het maken van een beleid voor het aanmelden van een web, het maken van een beleid voor een systeem eigen app die een web-API aanroept en een geavanceerd beleid beheert.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 3ec94543a53e3e5b7709801de8f4cf1dde3fc3d9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99428112"
---
# <a name="configure-token-lifetime-policies-preview"></a>Levens duur van token beleid configureren (preview-versie)
U kunt de levens duur opgeven van een token voor toegang, SAML of ID dat is uitgegeven door het micro soft Identity-platform. U kunt de levensduur van een token instellen voor alle apps in uw organisatie, voor een multitenanttoepassing (voor meerdere organisaties) of voor een specifieke service-principal in uw organisatie. Lees de [levens duur van Configureer bare tokens](active-directory-configurable-token-lifetimes.md)voor meer informatie.

In deze sectie wordt een gemeen schappelijke beleids scenario door lopen waarmee u nieuwe regels voor de levens duur van tokens kunt opleggen. In het voor beeld leert u hoe u een beleid kunt maken dat vereist dat gebruikers vaker worden geverifieerd in uw web-app.

## <a name="get-started"></a>Aan de slag
Voer de volgende stappen uit om aan de slag te gaan:

1. Down load de nieuwste [open bare preview-versie van Azure AD Power shell-module](https://www.powershellgallery.com/packages/AzureADPreview).
1. Voer de `Connect` opdracht uit om u aan te melden bij uw Azure AD-beheerders account. Voer deze opdracht telkens uit wanneer u een nieuwe sessie start.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. Als u alle beleids regels wilt zien die zijn gemaakt in uw organisatie, voert u de cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit.  Alle resultaten met gedefinieerde eigenschaps waarden die verschillen van de standaard instellingen die hierboven worden vermeld, vallen binnen het bereik van de buiten gebruiks telling.

    ```powershell
    Get-AzureADPolicy -All $true
    ```

1. Als u wilt zien welke apps en service-principals zijn gekoppeld aan een specifiek beleid dat u hebt geïdentificeerd, voert u de volgende [Get-AzureADPolicyAppliedObject-](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) cmdlet uit door **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** te vervangen door een van de beleids-id's. Vervolgens kunt u bepalen of u de aanmeldings frequentie voor voorwaardelijke toegang wilt configureren of dat u de standaard instellingen van Azure AD wilt blijven gebruiken.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

Als uw Tenant beleid heeft voor het definiëren van aangepaste waarden voor de configuratie-eigenschappen van het vernieuwings-en sessie token, raadt micro soft u aan die beleids regels bij te werken naar waarden die overeenkomen met de hierboven beschreven standaard waarden. Als er geen wijzigingen worden aangebracht, wordt de standaard waarde door Azure AD automatisch nageleefd.

## <a name="create-a-policy-for-web-sign-in"></a>Een beleid maken voor aanmelden via het web

In dit voor beeld maakt u een beleid waarmee gebruikers vaker moeten worden geverifieerd in uw web-app. Met dit beleid wordt de levens duur van de toegangs-ID-tokens en de maximale leeftijd van een multi-factor-sessie token ingesteld op de service-principal van uw web-app.

1. Maak een beleid voor levens duur van tokens.

    Dit beleid, voor aanmelding bij het web, stelt de levens duur van het toegangs-en ID-token en de maximale leeftijd van het single-factor sessie token in op twee uur.

    1. Voer de cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit om het beleid te maken:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Voer de cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit om het nieuwe beleid weer te geven en de beleids- **ObjectId** op te halen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Wijs het beleid toe aan uw service-principal. U moet ook de **ObjectId** voor uw Service-Principal ophalen.

    1. Gebruik de cmdlet [Get-azureadserviceprincipal namelijk niet](/powershell/module/azuread/get-azureadserviceprincipal) om de service-principals van uw organisatie of een enkele service-principal te bekijken.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Wanneer u de Service-Principal hebt, voert u de cmdlet [add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) uit:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>Beleid voor levens duur van tokens maken voor vernieuwings-en sessie tokens
> [!IMPORTANT]
> Vanaf 30 januari 2021 kunt u de levens duur van het vernieuwings-en sessie token niet configureren. Azure Active Directory voldoet niet meer aan de configuratie van vernieuwen en sessie token in bestaande beleids regels.  Nieuwe tokens die zijn uitgegeven nadat bestaande tokens zijn verlopen, worden nu ingesteld op de [standaard configuratie](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement). U kunt nog steeds de levens duur van de toegangs-, SAML-en ID-token configureren na de configuratie van het vernieuwings-en sessie token.
>
> De levens duur van het bestaande token wordt niet gewijzigd. Nadat deze zijn verlopen, wordt er een nieuw token uitgegeven op basis van de standaard waarde.
>
> Als u moet door gaan met het definiëren van de tijds periode voordat een gebruiker wordt gevraagd zich opnieuw aan te melden, configureert u de aanmeldings frequentie in voorwaardelijke toegang. Lees voor meer informatie over voorwaardelijke toegang [verificatie sessie beheer configureren met voorwaardelijke toegang](../conditional-access/howto-conditional-access-session-lifetime.md).

### <a name="manage-an-organizations-default-policy"></a>Het standaard beleid van een organisatie beheren
In dit voor beeld maakt u een beleid waarmee uw gebruikers zich minder vaak kunnen aanmelden in uw hele organisatie. Als u dit wilt doen, maakt u een beleid voor de levens duur van tokens voor het vernieuwen van tokens die in uw organisatie worden toegepast. Het beleid wordt toegepast op elke toepassing in uw organisatie en op elke service-principal waarvoor nog geen beleid is ingesteld.

1. Maak een beleid voor levens duur van tokens.

    1. Stel het token voor het vernieuwen van de enkelvoudige factor in op ' until-ingetrokken '. Het token verloopt niet totdat de toegang is ingetrokken. Maak de volgende beleids definitie:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Voer de cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit om het beleid te maken:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Als u witruimte wilt verwijderen, voert u de cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Voer de volgende opdracht uit om het nieuwe beleid te bekijken en de **ObjectId** van het beleid op te halen:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Werk het beleid bij.

    U kunt besluiten dat het eerste beleid dat u in dit voor beeld hebt ingesteld, niet zo strikt is als uw service vereist. Voer de volgende opdracht uit om in te stellen dat uw token voor het vernieuwen van één factor binnen twee dagen verloopt:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Een beleid maken voor een systeem eigen app die een web-API aanroept
In dit voor beeld maakt u een beleid dat vereist dat gebruikers minder vaak verifiëren. Het beleid verlengt ook de hoeveelheid tijd die een gebruiker inactief mag zijn voordat de gebruiker opnieuw moet worden geverifieerd. Het beleid wordt toegepast op de Web-API. Wanneer de systeem eigen app de Web-API als bron aanvraagt, wordt dit beleid toegepast.

1. Maak een beleid voor levens duur van tokens.

    1. Voer de cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit om een strikt beleid voor een web-API te maken:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Voer de volgende opdracht uit om het nieuwe beleid weer te geven:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Wijs het beleid toe aan uw web-API. U moet ook de **ObjectId** van uw toepassing ophalen. Gebruik de cmdlet [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) om de **ObjectId** van uw app te vinden, of gebruik de [Azure Portal](https://portal.azure.com/).

    Haal de **ObjectId** van uw app op en wijs het beleid toe:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>Een geavanceerd beleid beheren
In dit voor beeld maakt u een paar beleids regels om te leren hoe het prioriteits systeem werkt. U leert ook hoe u meerdere beleids regels beheert die op verschillende objecten worden toegepast.

1. Maak een beleid voor levens duur van tokens.

    1. Voer de cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit om een standaard beleid voor organisaties te maken waarmee de levens duur van de token voor eenmalige vernieuwing wordt ingesteld op 30 dagen.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Voer de cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit om het nieuwe beleid weer te geven:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Wijs het beleid toe aan een service-principal.

    Nu hebt u een beleid dat van toepassing is op de hele organisatie. U kunt dit beleid voor 30 dagen voor een specifieke Service-Principal bewaren, maar u kunt het standaard beleid van de organisatie wijzigen in de bovengrens van ' tot-ingetrokken '.

    1. Als u de service-principals van uw organisatie wilt weer geven, gebruikt u de cmdlet [Get-azureadserviceprincipal namelijk niet](/powershell/module/azuread/get-azureadserviceprincipal) .

    1. Wanneer u de Service-Principal hebt, voert u de cmdlet [add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) uit:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Stel de `IsOrganizationDefault` vlag in op False:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Maak een nieuw standaard beleid voor organisaties:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    U hebt nu het oorspronkelijke beleid gekoppeld aan uw Service-Principal en het nieuwe beleid is ingesteld als standaard beleid voor uw organisatie. Het is belang rijk om te onthouden dat beleids regels die worden toegepast op service-principals prioriteit hebben boven het standaard beleid van de organisatie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [beheer mogelijkheden voor verificatie sessies](../conditional-access/howto-conditional-access-session-lifetime.md) in voorwaardelijke toegang tot Azure AD.
