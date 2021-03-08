---
title: ClaimsProviders-Azure Active Directory B2C | Microsoft Docs
description: Geef het ClaimsProvider-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 846394266b981c14788148be465912b14bc1fb3e
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447908"
---
# <a name="claimsproviders"></a>ClaimsProviders

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een claim biedt een interface voor communicatie met verschillende soorten partijen via de [technische profielen](technicalprofiles.md). Elke claim provider moet een of meer technische profielen hebben die de eind punten bepalen en de protocollen die nodig zijn om te communiceren met de claim provider. Een claim provider kan meerdere technische profielen hebben. Er kunnen bijvoorbeeld meerdere technische profielen worden gedefinieerd, omdat de claim provider meerdere protocollen, verschillende eind punten met verschillende mogelijkheden ondersteunt of verschillende claims op verschillende verzekerings niveaus uitgeeft. Het kan acceptabel zijn om gevoelige claims te vrijgeven in één gebruikers traject, maar niet in een andere.

Een gebruikers traject combineert technische profielen aan de hand van Orchestration-stappen om uw bedrijfs logica te definiëren. 

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <Domain>Domain name</Domain>
    <DisplayName>Display name</DisplayName>
    <TechnicalProfiles>
      </TechnicalProfile>
        ...
      </TechnicalProfile>
        ...
    </TechnicalProfiles>
  </ClaimsProvider>
  ...
</ClaimsProviders>
```

Het element **ClaimsProviders** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| ClaimsProvider | 1: n | Een erkende claim provider die kan worden gebruikt in verschillende trajecten van de gebruiker. |

## <a name="claimsprovider"></a>ClaimsProvider

Het **ClaimsProvider** -element bevat de volgende onderliggende elementen:

| Element | Instanties | Description |
| ------- | ---------- | ----------- |
| Domain | 0:1 | Een teken reeks die de domein naam voor de claim provider bevat. Als uw claim provider bijvoorbeeld het technische profiel voor Facebook bevat, is de domein naam Facebook.com. Deze domein naam wordt gebruikt voor alle technische profielen die zijn gedefinieerd in de claim provider, tenzij deze wordt overschreven door het technische profiel. Er kan ook worden verwezen naar de domein naam in een **domain_hint**. Zie voor meer informatie de sectie **Aanmelden omleiden naar een sociale provider** van het [rechtstreeks aanmelden instellen met behulp van Azure Active Directory B2C](direct-signin.md). |
| DisplayName | 1:1 | Een teken reeks die de naam van de claim provider bevat. |
| [TechnicalProfiles](technicalprofiles.md) | 0:1 | Een set technische profielen die worden ondersteund door de claim provider |

**ClaimsProvider** organiseert hoe uw technische profielen verband houden met de claim provider. In het volgende voor beeld ziet u de Azure Active Directory claim provider met de Azure Active Directory technische profielen:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-Common">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      ...
    </TechnicalProfile>
      ...
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      ...
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePhoneNumberUsingObjectId">
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

In het volgende voor beeld wordt de Facebook-claim provider weer gegeven met het **Facebook-OAUTH-** technische profiel.

```xml
<ClaimsProvider>
  <Domain>facebook.com</Domain>
  <DisplayName>Facebook</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Facebook-OAUTH">
      <DisplayName>Facebook</DisplayName>
      <Protocol Name="OAuth2" />
        ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
