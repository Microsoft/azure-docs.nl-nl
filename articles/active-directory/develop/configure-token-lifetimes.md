---
title: Levensduur instellen voor tokens
titleSuffix: Microsoft identity platform
description: Meer informatie over het instellen van levensduur voor tokens die zijn uitgegeven door het Microsoft Identity Platform. Meer informatie over het beheren van het standaardbeleid van een organisatie, het maken van een beleid voor web-aanmelding, het maken van een beleid voor een systeemeigen app die een web-API aanroept en een geavanceerd beleid beheren.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363883"
---
# <a name="configure-token-lifetime-policies-preview"></a>Levensduurbeleid voor token configureren (preview)
U kunt de levensduur opgeven van een toegangs-, SAML- of id-token dat is uitgegeven door het Microsoft Identity Platform. U kunt de levensduur van een token instellen voor alle apps in uw organisatie, voor een multitenanttoepassing (voor meerdere organisaties) of voor een specifieke service-principal in uw organisatie. Lees configureerbare [levensduur van token voor meer informatie.](active-directory-configurable-token-lifetimes.md)

In deze sectie doorloop we een algemeen beleidsscenario dat u kan helpen bij het opleggen van nieuwe regels voor de levensduur van het token. In het voorbeeld leert u hoe u een beleid maakt dat vereist dat gebruikers vaker worden geverifieerd in uw web-app.

## <a name="get-started"></a>Aan de slag

Download de nieuwste openbare preview-versie van [azure AD PowerShell-module om aan](https://www.powershellgallery.com/packages/AzureADPreview)de slag te gaan.

Voer vervolgens de opdracht `Connect` uit om u aan te melden bij uw Azure AD-beheerdersaccount. Voer deze opdracht uit telkens wanneer u een nieuwe sessie start.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Een beleid maken voor web-aanmelding

In dit voorbeeld maakt u een beleid dat vereist dat gebruikers vaker worden geverifieerd in uw web-app. Met dit beleid stelt u de levensduur van de toegangs-/id-tokens in op de service-principal van uw web-app.

1. Maak een beleid voor de levensduur van een token.

    Met dit beleid wordt voor web-aanmelding de levensduur van het toegangs-/id-token op twee uur.

    Voer de cmdlet [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit om het beleid te maken:

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    Voer de cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uit om uw nieuwe beleid te zien en om de **ObjectId** van het beleid op te halen:

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. Wijs het beleid toe aan uw service-principal. U moet ook de **ObjectId van uw** service-principal op te halen.

    Gebruik de cmdlet [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) om alle service-principals of één service-principal van uw organisatie te bekijken.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    Wanneer u de service-principal hebt, moet u de cmdlet [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) uitvoeren:

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>Bestaande beleidsregels in een tenant weergeven

Als u alle beleidsregels wilt zien die in uw organisatie zijn gemaakt, moet u de cmdlet [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) uitvoeren.  Alle resultaten met gedefinieerde eigenschapswaarden die afwijken van de hierboven vermelde standaardwaarden, vallen binnen het bereik van de buiten gebruik stellen.

```powershell
Get-AzureADPolicy -All $true
```

Als u wilt zien welke apps en service-principals zijn gekoppeld aan een specifiek beleid dat u hebt geïdentificeerd, moet u de volgende cmdlet [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) uitvoeren door **1a37dom8-5da7-4cc8-87c7-efbc0326cf20** te vervangen door een van uw beleids-ID's. Vervolgens kunt u beslissen of u de aanmeldingsfrequentie voor voorwaardelijke toegang wilt configureren of de standaardinstellingen van Azure AD wilt blijven gebruiken.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

Als uw tenant beleidsregels heeft die aangepaste waarden definiëren voor de configuratie-eigenschappen van het vernieuwings- en sessie-token, raadt Microsoft u aan dat beleid bij te werken naar waarden die de standaardinstellingen weerspiegelen die hierboven worden beschreven. Als er geen wijzigingen worden aangebracht, worden de standaardwaarden automatisch door Azure AD in ere genomen.

### <a name="troubleshooting"></a>Problemen oplossen
Sommige gebruikers hebben een fout `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` gerapporteerd na het uitvoeren van de `Get-AzureADPolicy` cmdlet. Als tijdelijke oplossing kunt u het volgende uitvoeren om de AzureAD-module te verwijderen/opnieuw te installeren en vervolgens de AzureADPreview-module te installeren:

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [de beheermogelijkheden voor verificatiesessies](../conditional-access/howto-conditional-access-session-lifetime.md) in Voorwaardelijke toegang van Azure AD.
