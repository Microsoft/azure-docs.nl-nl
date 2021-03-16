---
title: TrustFrameworkPolicy-Azure Active Directory B2C | Microsoft Docs
description: Geef het TrustFrameworkPolicy-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9bf1cc197a7d6977ccb6ef69e157d9f8a76a58d5
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470720"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een aangepast beleid wordt weer gegeven als een of meer XML-indelings bestanden die naar elkaar in een hiërarchische keten verwijzen. Met de XML-elementen worden elementen van het beleid gedefinieerd, zoals het claim schema, claim transformaties, inhouds definities, claim providers, technische profielen, gebruikers traject en indelings stappen. Elk beleids bestand wordt gedefinieerd in het **TrustFrameworkPolicy** -element van het hoogste niveau van een beleids bestand.

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="yourtenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


Het **TrustFrameworkPolicy** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Ja | De schema versie die moet worden gebruikt om het beleid uit te voeren. De waarde moet `0.3.0.0` |
| TenantObjectId | Nee | De unieke object-id van de Azure Active Directory B2C (Azure AD B2C)-Tenant. |
| TenantId | Ja | De unieke id van de Tenant waartoe dit beleid behoort. |
| PolicyId | Ja | De unieke id voor het beleid. Deze id moet worden voorafgegaan door *B2C_1A_* |
| PublicPolicyUri | Ja | De URI voor het beleid, dat een combi natie is van de Tenant-ID en de beleids-ID. |
| Als Deployment mode | Nee | Mogelijke waarden: `Production` , of `Development` . `Production` is de standaardwaarde. Gebruik deze eigenschap om fouten in uw beleid op te sporen. Zie [Logboeken verzamelen](troubleshoot-with-application-insights.md)voor meer informatie. |
| UserJourneyRecorderEndpoint | Nee | Het eind punt dat wordt gebruikt voor logboek registratie. De waarde moet worden ingesteld op `urn:journeyrecorder:applicationinsights` als het kenmerk bestaat. Zie [Logboeken verzamelen](troubleshoot-with-application-insights.md)voor meer informatie. |


In het volgende voor beeld ziet u hoe u het **TrustFrameworkPolicy** -element opgeeft:

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Het **TrustFrameworkPolicy** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| BasePolicy| 0:1| De id van een basis beleid. |
| [BuildingBlocks](buildingblocks.md) | 0:1 | De bouw stenen van uw beleid. |
| [ClaimsProviders](claimsproviders.md) | 0:1 | Een verzameling claim providers. |
| [UserJourneys](userjourneys.md) | 0:1 | Een verzameling van gebruikers ritten. |
| [RelyingParty](relyingparty.md) | 0:1 | Een definitie van een Relying Party beleid. |

Als u een beleid van een ander beleid wilt overnemen, moet u een **BasePolicy** -element declareren onder het element **TrustFrameworkPolicy** van het beleids bestand. Het element **BasePolicy** is een verwijzing naar het basis beleid van waaruit dit beleid is afgeleid.

Het **BasePolicy** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | --------|
| TenantId | 1:1 | De id van uw Azure AD B2C-Tenant. |
| PolicyId | 1:1 | De id van het bovenliggende beleid. |


In het volgende voor beeld ziet u hoe u een basis beleid opgeeft. Dit **B2C_1A_TrustFrameworkExtensions** beleid wordt afgeleid van het **B2C_1A_TrustFrameworkBase** -beleid.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

