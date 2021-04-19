---
title: Technische profielen voor voorwaardelijke toegang in aangepast beleid
titleSuffix: Azure AD B2C
description: Aangepaste beleidsverwijzing voor technische profielen voor voorwaardelijke toegang in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ebdc1c9c92f6e3debf08cb640e46424c4ad9d5ad
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107721885"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel voor voorwaardelijke toegang definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) Voorwaardelijke toegang is het hulpprogramma dat wordt gebruikt door Azure AD B2C om signalen samen te brengen, beslissingen te nemen en organisatiebeleid af te dwingen. Het automatiseren van een risicoanalyse met beleidsvoorwaarden betekent dat riskante aanmeldingen tegelijk worden geïdentificeerd en vervolled of geblokkeerd.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

Het **kenmerk Naam** van het element **Protocol** moet worden ingesteld op `Proprietary` . Het **handlerkenmerk** moet de volledig gekwalificeerde naam bevatten van de protocol-handlerassemblage die wordt gebruikt door Azure AD B2C:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

In het volgende voorbeeld ziet u een technisch profiel voor voorwaardelijke toegang:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Evaluatie van voorwaardelijke toegang

Bij elke aanmelding evalueert Azure AD B2C alle beleidsregels en zorgt u ervoor dat aan alle vereisten wordt voldaan voordat de gebruiker toegang wordt verleent. Toegang blokkeren overschrijven alle andere configuratie-instellingen. In **de** evaluatiemodus van het technische profiel voor voorwaardelijke toegang worden de signalen geëvalueerd die tijdens Azure AD B2C aanmelding met een lokaal account zijn verzameld. Het resultaat van het technische profiel voor voorwaardelijke toegang is een set claims die het resultaat zijn van de evaluatie van voorwaardelijke toegang. Het Azure AD B2C gebruikt deze claims in een volgende orchestration-stap om actie te ondernemen, zoals de gebruiker blokkeren of de gebruiker vragen om meervoudige verificatie. De volgende opties kunnen worden geconfigureerd voor deze modus.

### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| OperationType | Yes | Moet Evaluatie **zijn.**  |

### <a name="input-claims"></a>Invoerclaims

Het **element InputClaims bevat** een lijst met claims die naar voorwaardelijke toegang moeten worden verzenden. U kunt de naam van uw claim ook aan de naam die is gedefinieerd in het technische profiel voor voorwaardelijke toegang.

| ClaimReferenceId | Vereist | Gegevenstype | Beschrijving |
| --------- | -------- | ----------- |----------- |
| UserId | Yes | tekenreeks | De id van de gebruiker die zich meldt. |
| AuthenticationMethodsUsed | Yes |stringCollection | De lijst met methoden die de gebruiker heeft gebruikt om zich aan te melden. Mogelijke waarden: `Password` , en `OneTimePasscode` . |
| IsFederated | Ja |booleaans | Geeft aan of een gebruiker zich heeft aangemeld met een federatief account. De waarde moet `false` zijn. |
| IsMfaRegistered | Ja |booleaans | Geeft aan of de gebruiker al een telefoonnummer heeft ingeschreven voor meervoudige verificatie. |


Het **element InputClaimsTransformations** kan een verzameling **InputClaimsTransformation-elementen** bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren voordat ze naar de service voor voorwaardelijke toegang worden verzonden.

### <a name="output-claims"></a>Uitvoerclaims

Het **element OutputClaims** bevat een lijst met claims die worden gegenereerd door conditionalAccessProtocolProvider. U kunt ook de naam van uw claim aan de onderstaande naam toe te wijsen.

| ClaimReferenceId | Vereist | Gegevenstype | Beschrijving |
| --------- | -------- | ----------- |----------- |
| Uitdagingen | Yes |stringCollection | Lijst met acties voor het herstellen van de geïdentificeerde bedreiging. Mogelijke waarden: `block` |
| MultiConditionalAccessStatus | Yes | stringCollection |  |

Het **element OutputClaimsTransformations** kan een verzameling **OutputClaimsTransformation-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe te genereren.

### <a name="example-evaluation"></a>Voorbeeld: Evaluatie

In het volgende voorbeeld ziet u een technisch profiel voor voorwaardelijke toegang dat wordt gebruikt om de aanmeldingsbedreiging te evalueren.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Herstel

De **herstelmodus van** het technische profiel voor voorwaardelijke toegang informeert Azure AD B2C dat de geïdentificeerde aanmeldingsbedreiging is verteerd. De volgende opties kunnen worden geconfigureerd voor de herstelmodus.

### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| OperationType | Yes | Moet **Herstel zijn.**  |

### <a name="input-claims"></a>Invoerclaims

Het **element InputClaims bevat** een lijst met claims die naar voorwaardelijke toegang moeten worden verzenden. U kunt de naam van uw claim ook aan de naam die is gedefinieerd in het technische profiel voor voorwaardelijke toegang.

| ClaimReferenceId | Vereist | Gegevenstype | Beschrijving |
| --------- | -------- | ----------- |----------- |
| Uitdagingen Tevreden | Yes | stringCollection| De lijst met opgeloste uitdagingen voor het herstellen van de geïdentificeerde bedreiging als retour uit de evaluatiemodus claimt uitdagingen.|


Het **element InputClaimsTransformations** kan een verzameling **InputClaimsTransformation-elementen** bevatten die worden gebruikt om de invoerclaims te wijzigen of nieuwe claims te genereren voordat de service voor voorwaardelijke toegang wordt aanroept.

### <a name="output-claims"></a>Uitvoerclaims

De protocolprovider voor voorwaardelijke toegang retourneert geen **OutputClaims,** dus u hoeft geen uitvoerclaims op te geven. U kunt echter claims opnemen die niet worden geretourneerd door de protocolprovider voor voorwaardelijke toegang, zolang u het kenmerk in `DefaultValue` stelt.

Het **element OutputClaimsTransformations** kan een verzameling **OutputClaimsTransformation-elementen** bevatten die worden gebruikt om de uitvoerclaims te wijzigen of nieuwe te genereren.

### <a name="example-remediation"></a>Voorbeeld: Herstel

In het volgende voorbeeld ziet u een technisch profiel voor voorwaardelijke toegang dat wordt gebruikt voor het herstellen van de aanmeldingsbedreiging.

```xml
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [het toevoegen van voorwaardelijke toegang aan gebruikersstromen in Azure Active Directory B2C](conditional-access-user-flow.md).
