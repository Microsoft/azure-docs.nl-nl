---
title: De standaard waarden van de token levensduur wijzigen voor aangepaste Azure AD-apps
description: De levens duur van tokens bijwerken voor uw toepassing die u ontwikkelt op Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: d39f378171443f028ef6b549b120b22f2a3405c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582938"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>De standaard waarden voor de levens duur van tokens wijzigen voor een aangepaste toepassing

In dit artikel wordt beschreven hoe u Azure AD Power shell gebruikt om een toegangs token levensduur beleid in te stellen. Met Azure AD Premium kunnen app-ontwikkel aars en Tenant beheerders de levens duur configureren van tokens die zijn uitgegeven voor niet-vertrouwelijke clients. Het token levensduur beleid wordt ingesteld op basis van de Tenant of de bronnen waartoe toegang wordt verkregen.

> [!IMPORTANT]
> Na mei 2020 kunnen tenants geen vernieuwings-en sessie token levensduur meer configureren.  Azure Active Directory zal na 30 januari 2021 niet langer de configuratie van bestaande vernieuwings-en sessie tokens in het beleid naleven. U kunt de levens duur van toegangs tokens na de afschaffing nog steeds configureren. Lees de [levens duur van Configureer bare tokens in azure AD](./active-directory-configurable-token-lifetimes.md)voor meer informatie.
> Er zijn [mogelijkheden voor verificatie sessie beheer](../conditional-access/howto-conditional-access-session-lifetime.md)geïmplementeerd   in voorwaardelijke toegang tot Azure AD. Met deze nieuwe functie kunt u de levens duur van het vernieuwings token configureren door de aanmeldings frequentie in te stellen.  

Down load de [Azure AD Power shell-module](https://www.powershellgallery.com/packages/AzureADPreview)om een beleid voor levens duur van toegangs tokens in te stellen.
Voer de opdracht **Connect-AzureAD-confirm** uit.

Hier volgt een voor beeld van een beleid dat gebruikers in uw web-app vaker moeten verifiëren. Met dit beleid wordt de levens duur ingesteld van de toegang tot de service-principal van uw web-app. Maak het beleid en wijs dit toe aan uw service-principal. U moet ook de ObjectId voor uw Service-Principal ophalen.

```powershell
$policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"

$sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Configureer bare token levensduur in azure AD](./active-directory-configurable-token-lifetimes.md) voor meer informatie over het configureren van de levens duur van tokens die zijn uitgegeven door Azure AD, inclusief het instellen van de levens duur van tokens voor alle apps in uw organisatie, voor een multi tenant-app of voor een specifieke Service-Principal in uw organisatie. 
* [Azure AD-token referentie](./id-tokens.md)
