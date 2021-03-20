---
title: Boolean-voor beelden van claim transformatie voor aangepast beleid
titleSuffix: Azure AD B2C
description: Boole-voor beelden van claim transformatie voor het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/06/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7c292f939339add06168c55236f8666651e4aace
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85201273"
---
# <a name="boolean-claims-transformations"></a>Booleaanse claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden van het gebruik van de Boole-claim transformaties van het Framework-schema voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="andclaims"></a>AndClaims

Voert een en-bewerking uit van twee Booleaanse inputClaims en stelt de output claim in met het resultaat van de bewerking.

| Item  | TransformationClaimType  | Gegevenstype  | Notities |
|-------| ------------------------ | ---------- | ----- |
| Input claim | inputClaim1 | booleaans | Het eerste claim type dat moet worden geëvalueerd. |
| Input claim | inputClaim2  | booleaans | Het tweede claim type dat moet worden geëvalueerd. |
|Output claim | Output claim | booleaans | De ClaimTypes die wordt geproduceerd nadat deze claim transformatie is aangeroepen (True of false). |

De volgende claims-trans formatie demonstreert hoe en hoe en met de ClaimTypes: en `isEmailNotExist` `isSocialAccount` . De uitvoer claim `presentEmailSelfAsserted` wordt ingesteld op `true` als de waarde van beide invoer claims `true` . In een indelings stap kunt u een voor waarde gebruiken voor het vooraf instellen van een zelfbevestigende pagina, alleen als een e-mail adres voor een sociaal account leeg is.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-andclaims"></a>Voor beeld van AndClaims

- Invoer claims:
    - **inputClaim1**: True
    - **inputClaim2**: False
- Uitvoer claims:
    - **output claim**: False


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Controleert of Boole-waarden van twee claims gelijk zijn en genereert een uitzonde ring als dat niet het geval is.

| Item | TransformationClaimType  | Gegevenstype  | Notities |
| ---- | ------------------------ | ---------- | ----- |
| Input claim | Input claim | booleaans | Het claim type dat moet worden bevestigd. |
| Parameter |valueToCompareTo | booleaans | De waarde die moet worden vergeleken (waar of onwaar). |

De **AssertBooleanClaimIsEqualToValue** -claim transformatie wordt altijd uitgevoerd op basis van een [validatie technische profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md). De meta gegevens van het zelfondertekende technische profiel **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** bepalen het fout bericht dat de gebruiker aan het technische profiel presenteert. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual-uitvoering](./media/boolean-transformations/assert-execution.png)

De volgende claims transformatie laat zien hoe u de waarde van een Booleaanse claim type met een `true` waarde kunt controleren. Als de waarde van het `accountEnabled` claim type is ingesteld op False, wordt een fout bericht gegenereerd.

```xml
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Het `login-NonInteractive` validatie technische profiel roept de `AssertAccountEnabledIsTrue` trans formatie van claims aan.

```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Het zelfondertekende technische profiel aanroept het technische profiel voor validatie **aanmelding-niet-interactief** .

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example-of-assertbooleanclaimisequaltovalue"></a>Voor beeld van AssertBooleanClaimIsEqualToValue

- Invoer claims:
    - **input claim**: False
    - **valueToCompareTo**: True
- Resultaat: er is een fout opgetreden

## <a name="comparebooleanclaimtovalue"></a>CompareBooleanClaimToValue

Controleert of de Booleaanse waarde van een claim gelijk is aan `true` of `false` en retourneert het resultaat van de compressie.

| Item | TransformationClaimType  | Gegevenstype  | Notities |
| ---- | ------------------------ | ---------- | ----- |
| Input claim | Input claim | booleaans | Het claim type dat moet worden bevestigd. |
| Parameter |valueToCompareTo | booleaans | De waarde die moet worden vergeleken (waar of onwaar). |
| Output claim | compareResult | booleaans | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

De volgende claims transformatie laat zien hoe u de waarde van een Booleaanse claim type met een `true` waarde kunt controleren. Als de waarde van het `IsAgeOver21Years` claim type is gelijk aan `true` , wordt de claim transformatie geretourneerd `true` `false` .

```xml
<ClaimsTransformation Id="AssertAccountEnabled" TransformationMethod="CompareBooleanClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="IsAgeOver21Years" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim  ClaimTypeReferenceId="accountEnabled" TransformationClaimType="compareResult"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-comparebooleanclaimtovalue"></a>Voor beeld van CompareBooleanClaimToValue

- Invoer claims:
    - **input claim**: False
- Invoer parameters:
    - **valueToCompareTo**: True
- Uitvoer claims:
    - **compareResult**: False

## <a name="notclaims"></a>NotClaims

Hiermee wordt een niet-bewerking uitgevoerd van de Booleaanse input claim en wordt de output claim ingesteld met het resultaat van de bewerking.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | booleaans | De claim die moet worden gebruikt. |
| Output claim | Output claim | booleaans | De ClaimTypes die worden geproduceerd nadat deze ClaimsTransformation is aangeroepen (True of false). |

Gebruik deze claim transformatie om logische ontkenning op een claim uit te voeren.

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-notclaims"></a>Voor beeld van NotClaims

- Invoer claims:
    - **input claim**: False
- Uitvoer claims:
    - **output claim**: True

## <a name="orclaims"></a>OrClaims

Hiermee worden een of twee Booleaanse inputClaims berekend en wordt de output claim ingesteld met het resultaat van de bewerking.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputClaim1 | booleaans | Het eerste claim type dat moet worden geëvalueerd. |
| Input claim | inputClaim2 | booleaans | Het tweede claim type dat moet worden geëvalueerd. |
| Output claim | Output claim | booleaans | De ClaimTypes die wordt geproduceerd nadat deze ClaimsTransformation is aangeroepen (True of false). |

De volgende claims transformatie laat zien hoe `Or` twee Booleaanse ClaimTypes. In de Orchestration-stap kunt u een voor waarde gebruiken voor het vooraf instellen van een zelfbevestigende pagina, als de waarde van een van de claims is `true` .

```xml
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-of-orclaims"></a>Voor beeld van OrClaims

- Invoer claims:
    - **inputClaim1**: True
    - **inputClaim2**: False
- Uitvoer claims:
    - **output claim**: True
