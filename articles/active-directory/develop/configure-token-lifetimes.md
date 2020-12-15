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
ms.date: 12/14/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e663cdd3846e804d1dcf96076c07b9a3db84272c
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507741"
---
# <a name="configure-token-lifetime-policies-preview"></a>Levens duur van token beleid configureren (preview-versie)
Er zijn veel scenario's mogelijk in azure AD wanneer u de levens duur van tokens voor apps, service-principals en uw hele organisatie kunt maken en beheren.  

> [!IMPORTANT]
> Na mei 2020 kunnen tenants geen vernieuwings-en sessie token levensduur meer configureren.  Azure Active Directory zal na 30 januari 2021 niet langer de configuratie van bestaande vernieuwings-en sessie tokens in het beleid naleven. U kunt de levens duur van toegangs tokens na de afschaffing nog steeds configureren.  Lees de [levens duur van Configureer bare tokens in micro soft Identity platform](active-directory-configurable-token-lifetimes.md)voor meer informatie.
> Er zijn [mogelijkheden voor verificatie sessie beheer](../conditional-access/howto-conditional-access-session-lifetime.md)geïmplementeerd   in voorwaardelijke toegang tot Azure AD. Met deze nieuwe functie kunt u de levens duur van het vernieuwings token configureren door de aanmeldings frequentie in te stellen.


In deze sectie laten we een paar algemene beleids scenario's door lopen waarmee u nieuwe regels kunt opstellen voor:

* Levens duur van token
* Maximale inactieve tijd van token
* Maximale leeftijd van token

In de voor beelden vindt u informatie over:

* Het standaard beleid van een organisatie beheren
* Een beleid maken voor aanmelden via het web
* Een beleid maken voor een systeem eigen app die een web-API aanroept
* Een geavanceerd beleid beheren

## <a name="prerequisites"></a>Vereisten
In de volgende voor beelden maakt, bijwerkt, koppelt en verwijdert u beleid voor apps, service-principals en uw hele organisatie. Als u geen ervaring hebt met Azure AD, raden we u aan meer te weten te komen over [het verkrijgen van een Azure AD-Tenant](quickstart-create-new-tenant.md) voordat u verdergaat met deze voor beelden.  

Voer de volgende stappen uit om aan de slag te gaan:

1. Down load de nieuwste [open bare preview-versie van Azure AD Power shell-module](https://www.powershellgallery.com/packages/AzureADPreview).
2. Voer de `Connect` opdracht uit om u aan te melden bij uw Azure AD-beheerders account. Voer deze opdracht telkens uit wanneer u een nieuwe sessie start.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Voer de volgende opdracht uit om alle beleids regels weer te geven die in uw organisatie zijn gemaakt. Voer deze opdracht na de meeste bewerkingen uit in de volgende scenario's. Als u de opdracht uitvoert, kunt u ook de * * * * van uw beleid ophalen.

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>Het standaard beleid van een organisatie beheren
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

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Een beleid maken voor een systeem eigen app die een web-API aanroept
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

## <a name="manage-an-advanced-policy"></a>Een geavanceerd beleid beheren
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