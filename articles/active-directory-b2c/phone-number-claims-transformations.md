---
title: Claim transformaties voor het telefoon nummer in aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Verwijzing naar het aangepaste beleid voor claim transformaties voor telefoon nummer in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e175a81efc1ab0950c1fda314efb206ff97a2b7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85385379"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Geef claim transformaties voor het telefoon nummer op in Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u Naslag informatie en voor beelden voor het gebruik van de claim transformaties van het Framework voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie over claim transformaties in het algemeen.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertphonenumberclaimtostring"></a>ConvertPhoneNumberClaimToString

Hiermee wordt een `phoneNumber` gegevens type geconverteerd naar een `string` gegevens type.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | phoneNumber | phoneNumber |  Het claim type dat moet worden geconverteerd naar een teken reeks. |
| Output claim | phoneNumberString | tekenreeks | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen. |

In dit voor beeld wordt de claim cellPhoneNumber met het waardetype ' `phoneNumber` geconverteerd naar een cellPhone-claim met het waardetype `string` .

```xml
<ClaimsTransformation Id="PhoneNumberToString" TransformationMethod="ConvertPhoneNumberClaimToString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="cellPhoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="cellPhone" TransformationClaimType="phoneNumberString" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **phonenumber**: + 11234567890 (phonenumber)
- Uitvoer claims:
  - **phoneNumberString**: + 11234567890 (teken reeks)


## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

Deze claim transformatie valideert de notatie van het telefoon nummer. Als het een geldige indeling heeft, wijzigt u deze in een standaard indeling die wordt gebruikt door Azure AD B2C. Als het opgegeven telefoon nummer geen geldige indeling heeft, wordt een fout bericht weer gegeven.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | phoneNumberString | tekenreeks |  De teken reeks claim voor het telefoon nummer. Het telefoon nummer moet in de internationale indeling zijn, compleet met een toonaangevend "+" en land/regio-code. Als `country` er een invoer claim wordt opgegeven, is het telefoon nummer in de lokale indeling (zonder de land-/regiocode). |
| Input claim | country | tekenreeks | Beschrijving De teken reeks claim voor de land-/regiocode van het telefoon nummer in de ISO3166-indeling (de twee letters ISO-3166 land/regio code). |
| Output claim | Output claim | phoneNumber | Het resultaat van deze claim transformatie. |

De **ConvertStringToPhoneNumberClaim** -claim transformatie wordt altijd uitgevoerd op basis van een [validatie technische profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md) of een [Weergave besturings element](display-controls.md). De meta gegevens van het zelfondertekende technische profiel **UserMessageIfClaimsTransformationInvalidPhoneNumber** bepalen het fout bericht dat aan de gebruiker wordt gepresenteerd.

![Diagram van het uitvoerings traject voor het fout bericht](./media/phone-authentication/assert-execution.png)

U kunt deze claim transformatie gebruiken om ervoor te zorgen dat de gegeven teken reeks claim een geldig telefoon nummer is. Als dat niet het geval is, wordt er een fout bericht gegenereerd. In het volgende voor beeld wordt gecontroleerd of het **phoneString** claim type inderdaad een geldig telefoon nummer is en retourneert het telefoon nummer in de standaard indeling Azure AD B2C. Anders wordt er een fout bericht gegenereerd.

```xml
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

Het zelfondertekende technische profiel dat het validatie technische profiel aanroept dat deze claim transformatie bevat, kan het fout bericht definiëren.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Voorbeeld 1

- Invoer claims:
  - **phoneNumberString**: 033 456-7890
  - **land**: DK
- Uitvoer claims:
  - **output claim**: + 450334567890

### <a name="example-2"></a>Voorbeeld 2

- Invoer claims:
  - **phoneNumberString**: + 1 (123) 456-7890
- Uitvoer claims:
  - **output claim**: + 11234567890


## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

Hiermee worden de land-/regiocode en het nationale nummer uit de invoer claim geëxtraheerd en wordt eventueel een uitzonde ring gegenereerd als het opgegeven telefoon nummer ongeldig is.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | phoneNumber | tekenreeks | De teken reeks claim van het telefoon nummer. Het telefoon nummer moet in de internationale indeling zijn, compleet met een toonaangevend "+" en land/regio-code. |
| Parameter | throwExceptionOnFailure | booleaans | Beschrijving Een para meter die aangeeft of er een uitzonde ring wordt gegenereerd wanneer het telefoon nummer ongeldig is. De standaard waarde is False. |
| Parameter | countryCodeType | tekenreeks | Beschrijving Een para meter die het type land-/regiocode in de uitvoer claim aangeeft. Beschik bare waarden zijn **CallingCode** (de internationale oproep code voor een land/regio, bijvoorbeeld + 1) of **iso3166** (de ISO-3166-land code van twee letters). |
| Output claim | nationalNumber | tekenreeks | De teken reeks claim voor het nationale nummer van het telefoon nummer. |
| Output claim | countryCode | tekenreeks | De teken reeks claim voor de land-/regiocode van het telefoon nummer. |


Als de **GetNationalNumberAndCountryCodeFromPhoneNumberString** -claim transformatie wordt uitgevoerd op basis van een [validatie technische profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelfondertekend technisch profiel](self-asserted-technical-profile.md) of een [Weergave besturings actie](display-controls.md#display-control-actions), bepaalt de meta gegevens van het door de **UserMessageIfPhoneNumberParseFailure** zelf gedefinieerde technische profiel de fout melding die wordt weer gegeven aan de gebruiker.

![Diagram van het uitvoerings traject voor het fout bericht](./media/phone-authentication/assert-execution.png)

U kunt deze claim transformatie gebruiken om een volledig telefoon nummer te splitsen in het land-/regionummer en het nationale nummer. Als het ingevoerde telefoon nummer niet geldig is, kunt u een fout bericht genereren.

In het volgende voor beeld wordt geprobeerd het telefoon nummer te splitsen in nationaal nummer en land/regio code. Als het telefoon nummer geldig is, wordt het telefoon nummer overschreven door het nationale nummer. Als het telefoon nummer ongeldig is, wordt er geen uitzonde ring gegenereerd en heeft het telefoon nummer nog steeds de oorspronkelijke waarde.

```xml
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

Het zelfondertekende technische profiel dat het validatie technische profiel aanroept dat deze claim transformatie bevat, kan het fout bericht definiëren.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>Voorbeeld 1

- Invoer claims:
  - **phonenumber**: + 49 (123) 456-7890
- Invoer parameters:
  - **throwExceptionOnFailure**: False
  - **countryCodeType**: iso3166
- Uitvoer claims:
  - **nationalNumber**: 1234567890
  - **countryCode**: de

### <a name="example-2"></a>Voorbeeld 2

- Invoer claims:
  - **phonenumber**: + 49 (123) 456-7890
- Invoer parameters
  - **throwExceptionOnFailure**: False
  - **countryCodeType**: CallingCode
- Uitvoer claims:
  - **nationalNumber**: 1234567890
  - **countryCode**: + 49
