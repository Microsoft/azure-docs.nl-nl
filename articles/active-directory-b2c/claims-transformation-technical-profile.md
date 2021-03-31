---
title: Een technische profiel voor een claim transformatie definiëren
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel voor claim transformatie in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6553b9ec120ca0e1e479b400495b61bc68c88cf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85201205"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel voor het transformeren van claims definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met een technisch profiel voor het transformeren van claims kunt u uitvoer claim transformaties aanroepen om claims waarden te manipuleren, claims te valideren of standaard waarden in te stellen voor een set uitvoer claims.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary` . Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C: `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

In het volgende voor beeld wordt een technische profiel voor het transformeren van claims weer gegeven:

```xml
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element is verplicht. U moet ten minste één uitvoer claim opgeven die door het technische profiel wordt geretourneerd. In het volgende voor beeld ziet u hoe u standaard waarden instelt in de uitvoer claims:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>Trans formaties uitvoer claims

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt om claims te wijzigen of nieuwe te genereren. Met het volgende technische profiel wordt de **RemoveAlternativeSecurityIdByIdentityProvider** -claim transformatie aangeroepen. Deze claim transformatie verwijdert een sociale identificatie uit de verzameling van **AlternativeSecurityIds**. De uitvoer claims van dit technische profiel zijn **identityProvider2**, die zijn ingesteld op `facebook.com` , en **AlternativeSecurityIds**, die de lijst met sociale identiteiten bevat die aan deze gebruiker zijn gekoppeld nadat de Facebook.com-identiteit is verwijderd.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Met het technische profiel voor claim transformatie kunt u een claim transformatie uitvoeren vanuit de Orchestration-stap van elke gebruiker. In het volgende voor beeld roept de Orchestration-stap een van de technische profielen ontkoppelen, zoals **Unlink-Facebook-OAUTH**. Met dit technische profiel wordt het technische profiel voor de claim transformatie **RemoveAlternativeSecurityIdByIdentityProvider**, waarmee een nieuwe **AlternativeSecurityIds2** -claim wordt gegenereerd die de lijst met sociale identiteiten van gebruikers bevat, terwijl de Facebook-identiteit uit de verzamelingen wordt verwijderd.

```xml
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true` , of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true` . |

## <a name="use-a-validation-technical-profile"></a>Een technische validatie profiel gebruiken

Een trans formatie van een technische profiel voor claims kan worden gebruikt om gegevens te valideren. In het volgende voor beeld vraagt het [zelf bevestigde technische profiel](self-asserted-technical-profile.md) met de naam **LocalAccountSignUpWithLogonEmail** de gebruiker twee keer het e-mail adres in te voeren. vervolgens wordt het [validatie technische profiel](validation-technical-profile.md) met de naam **Validate e-mail** aangeroepen om de e-mail berichten te valideren. Met het technische profiel **valideren-e-mail** wordt het **AssertEmailAreEqual** van de claims-trans formatie aangeroepen om de twee claims **-e-mail** en **emailRepeat** te vergelijken en een uitzonde ring te genereren als deze niet gelijk zijn aan de opgegeven vergelijking.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Met het technische profiel voor de claim transformatie wordt de **AssertEmailAreEqual** -claim transformatie aangeroepen, waarin wordt bevestigd dat de e-mail berichten van de gebruiker hetzelfde zijn.

```xml
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

Een zelf-bevestigd technisch profiel kan het technische profiel voor validatie aanroepen en het fout bericht weer geven zoals opgegeven in de meta gegevens van de **UserMessageIfClaimsTransformationStringsAreNotEqual** .

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
