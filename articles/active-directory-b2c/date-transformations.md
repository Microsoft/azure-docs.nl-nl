---
title: Datum van voor beelden van claim transformatie voor aangepast beleid
description: Datum voor beelden van claim transformatie voor het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eaf58b964517162ee7f7eb925e1e64830eedc087
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85202548"
---
# <a name="date-claims-transformations"></a>Datum claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden van het gebruik van de datum claim transformaties van het Framework voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="assertdatetimeisgreaterthan"></a>AssertDateTimeIsGreaterThan

Controleert of een datum-en tijd claim (teken reeks gegevens type) later is dan een tweede datum en tijd claim (teken reeks gegevens type) en genereert een uitzonde ring.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | leftOperand | tekenreeks | Type van de eerste claim, dat later moet zijn dan de tweede claim. |
| Input claim | rightOperand | tekenreeks | Tweede claim type, dat eerder moet zijn dan de eerste claim. |
| Parameter | AssertIfEqualTo | booleaans | Hiermee wordt aangegeven of deze bevestiging moet worden door gegeven als de linkeroperand gelijk is aan de rechteroperand. |
| Parameter | AssertIfRightOperandIsNotPresent | booleaans | Hiermee geeft u op of deze bevestiging moet worden door gegeven als de juiste operand ontbreekt. |
| Parameter | TreatAsEqualIfWithinMillseconds | int | Hiermee geeft u het aantal milliseconden op dat is toegestaan tussen de twee datum tijden om de tijden te bepalen die gelijk zijn (bijvoorbeeld om rekening te houden met Clock scheefheid). |

De **AssertDateTimeIsGreaterThan** -claim transformatie wordt altijd uitgevoerd op basis van een [validatie technische profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md). De meta gegevens van het zelfondertekende technische profiel **DateTimeGreaterThan** bepalen het fout bericht dat de gebruiker aan het technische profiel presenteert. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#claims-transformations-error-messages).

![AssertStringClaimsAreEqual-uitvoering](./media/date-transformations/assert-execution.png)

In het volgende voor beeld wordt de `currentDateTime` claim met de `approvedDateTime` claim vergeleken. Er wordt een fout gegenereerd als deze `currentDateTime` later is dan `approvedDateTime` . De trans formatie behandelt waarden als gelijk als ze binnen vijf minuten (30000 milliseconden) verschillen.

```xml
<ClaimsTransformation Id="AssertApprovedDateTimeLaterThanCurrentDateTime" TransformationMethod="AssertDateTimeIsGreaterThan">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="approvedDateTime" TransformationClaimType="leftOperand" />
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="rightOperand" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
    <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
    <InputParameter Id="TreatAsEqualIfWithinMillseconds" DataType="int" Value="300000" />
  </InputParameters>
</ClaimsTransformation>
```

Het `login-NonInteractive` validatie technische profiel roept de `AssertApprovedDateTimeLaterThanCurrentDateTime` trans formatie van claims aan.
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertApprovedDateTimeLaterThanCurrentDateTime" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Het zelfondertekende technische profiel aanroept het technische profiel voor validatie **aanmelding-niet-interactief** .

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="DateTimeGreaterThan">Custom error message if the provided left operand is greater than the right operand.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **leftOperand**: 2020-03-2016t15:00:00.0000000 z
    - **rightOperand**: 2020-03-01T14:00:00.0000000 z
- Resultaat: er is een fout opgetreden

## <a name="convertdatetodatetimeclaim"></a>ConvertDateToDateTimeClaim

Hiermee wordt een **datum** claim type geconverteerd naar een datum **/tijd** -claim type. De claim transformatie converteert de tijd notatie en voegt 12:00:00 AM toe aan de datum.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | date | Het claim type dat moet worden geconverteerd. |
| Output claim | Output claim | dateTime | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

In het volgende voor beeld wordt de conversie van de claim `dateOfBirth` (datum gegevens type) naar een andere claim `dateOfBirthWithTime` (gegevens type datetime) gedemonstreerd.

```xml
  <ClaimsTransformation Id="ConvertToDateTime" TransformationMethod="ConvertDateToDateTimeClaim">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="dateOfBirth" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="dateOfBirthWithTime" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: 2020-15-03
- Uitvoer claims:
    - **output claim**: 2020-15-03T00:00:00.0000000 z

## <a name="convertdatetimetodateclaim"></a>ConvertDateTimeToDateClaim

Converteert een datum **/tijd** -claim type naar een **date** claim type. De claim transformatie verwijdert de tijd notatie van de datum.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | dateTime | Het claim type dat moet worden geconverteerd. |
| Output claim | Output claim | date | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

In het volgende voor beeld wordt de conversie van de claim `systemDateTime` (datetime-gegevens type) naar een andere claim `systemDate` (gegevens type datum) gedemonstreerd.

```xml
<ClaimsTransformation Id="ConvertToDate" TransformationMethod="ConvertDateTimeToDateClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDate" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **input claim**: 2020-15-03T11:34:22.0000000 z
- Uitvoer claims:
  - **output claim**: 2020-15-03

## <a name="getcurrentdatetime"></a>GetCurrentDateTime

De huidige UTC-datum en-tijd ophalen en de waarde toevoegen aan een claim type.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Output claim | currentDateTime | dateTime | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

```xml
<ClaimsTransformation Id="GetSystemDateTime" TransformationMethod="GetCurrentDateTime">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="systemDateTime" TransformationClaimType="currentDateTime" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

* Uitvoer claims:
    * **currentDateTime**: 2020-15-03T11:40:35.0000000 z

## <a name="datetimecomparison"></a>DateTimeComparison

Bepalen of een datum/tijd later, eerder of gelijk aan een andere dateTime is. Het resultaat is een nieuwe Boolean claim type Boole met de waarde `true` of `false` .

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | firstDateTime | dateTime | De eerste datum/tijd om te vergelijken of deze eerder of later is dan de tweede datum/tijd. Null-waarde genereert een uitzonde ring. |
| Input claim | secondDateTime | dateTime | De tweede datum/tijd om te vergelijken of deze eerder of later is dan de eerste datum/tijd. Null-waarde wordt beschouwd als de huidige datetTime. |
| Parameter | operator | tekenreeks | Een van de volgende waarden: zelfde, later dan of eerder dan. |
| Parameter | timeSpanInSeconds | int | De time span toevoegen aan de eerste datum/tijd. |
| Output claim | result | booleaans | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. |

Gebruik deze claim transformatie om te bepalen of twee ClaimTypes gelijk zijn aan, later of eerder dan elkaar. U kunt bijvoorbeeld de laatste keer dat een gebruiker uw service voorwaarden heeft geaccepteerd (TOS) opslaan. Na 3 maanden kunt u de gebruiker vragen om opnieuw toegang te krijgen tot de TOS.
Als u de claim transformatie wilt uitvoeren, moet u eerst de huidige datum/tijd en de laatste keer dat de gebruiker de TOS accepteert, ophalen.

```xml
<ClaimsTransformation Id="CompareLastTOSAcceptedWithCurrentDateTime" TransformationMethod="DateTimeComparison">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="currentDateTime" TransformationClaimType="firstDateTime" />
    <InputClaim ClaimTypeReferenceId="extension_LastTOSAccepted" TransformationClaimType="secondDateTime" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="later than" />
    <InputParameter Id="timeSpanInSeconds" DataType="int" Value="7776000" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="result" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **firstDateTime**: 2020-01-01T00:00:00.100000 z
    - **secondDateTime**: 2020-04-01T00:00:00.100000 z
- Invoer parameters:
    - **operator**: later dan
    - **timeSpanInSeconds**: 7776000 (90 dagen)
- Uitvoer claims:
    - **resultaat**: True
