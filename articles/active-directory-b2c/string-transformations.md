---
title: Voor beelden van teken reeksen voor claim transformatie voor aangepaste beleids regels
titleSuffix: Azure AD B2C
description: String-voor beelden van claim transformatie voor het IEF-schema (Identity experience Framework) van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85574b7d33af6d9abfe25f5af4d811255f08ce4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452234"
---
# <a name="string-claims-transformations"></a>Teken reeks claim transformaties

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel vindt u voor beelden van het gebruik van de teken reeks claim transformaties van het Framework schema voor identiteits ervaring in Azure Active Directory B2C (Azure AD B2C). Zie [ClaimsTransformations](claimstransformations.md)voor meer informatie.

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Vergelijk twee claims en verwerp een uitzonde ring als deze niet gelijk zijn aan de opgegeven vergelijking inputClaim1, inputClaim2 en stringComparison.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputClaim1 | tekenreeks | Het eerste claim type, dat moet worden vergeleken. |
| Input claim | inputClaim2 | tekenreeks | Tweede claim type, dat moet worden vergeleken. |
| Parameter | stringComparison | tekenreeks | teken reeks vergelijking, een van de waarden: Ordinal, OrdinalIgnoreCase. |

De **AssertStringClaimsAreEqual** -claim transformatie wordt altijd uitgevoerd op basis van een [validatie technische profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md)of een [DisplayConrtol](display-controls.md). De `UserMessageIfClaimsTransformationStringsAreNotEqual` meta gegevens van een zelf-bebevestigde technische profiel bepalen het fout bericht dat aan de gebruiker wordt gepresenteerd. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#claims-transformations-error-messages).


![AssertStringClaimsAreEqual-uitvoering](./media/string-transformations/assert-execution.png)

U kunt deze claim transformatie gebruiken om ervoor te zorgen dat twee ClaimTypes dezelfde waarde hebben. Als dat niet het geval is, wordt er een fout bericht gegenereerd. In het volgende voor beeld wordt gecontroleerd of het **strongAuthenticationEmailAddress** claim type gelijk is aan **e-mail** claim type. Anders wordt er een fout bericht gegenereerd.

```xml
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Met het technische profiel voor niet **-interactieve** validatie wordt de **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** -claim transformatie aangeroepen.
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Het zelfondertekende technische profiel aanroept het technische profiel voor validatie **aanmelding-niet-interactief** .

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Invoer parameters:
  - **stringComparison**: ordinalIgnoreCase
- Resultaat: er is een fout opgetreden

## <a name="changecase"></a>ChangeCase

Wijzigt het hoofdletter gebruik van de gegeven claim in onderste of hoofd letters, afhankelijk van de operator.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputClaim1 | tekenreeks | Het claim type dat moet worden gewijzigd. |
| Parameter | toCase | tekenreeks | Een van de volgende waarden: `LOWER` of `UPPER` . |
| Output claim | Output claim | tekenreeks | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen. |

Gebruik deze claim transformatie om een wille keurig teken reeks claim type te wijzigen in lager of hoofd letters.

```xml
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **e-mail**: SomeOne@contoso.com
- Invoer parameters:
    - **toCase**: lager
- Uitvoer claims:
  - **e-mail**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Hiermee wordt een teken reeks claim gemaakt op basis van de opgegeven invoer parameter in de trans formatie.

| Item | TransformationClaimType | Gegevenstype | Notities |
|----- | ----------------------- | --------- | ----- |
| Parameter | waarde | tekenreeks | De teken reeks die moet worden ingesteld. Deze invoer parameter ondersteunt [teken reeks claim transformatie expressies](string-transformations.md#string-claim-transformations-expressions). |
| Output claim | createdClaim | tekenreeks | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen, met de waarde die is opgegeven in de invoer parameter. |

Gebruik deze claim transformatie om een teken reeks claim waarde in te stellen.

```xml
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer parameter:
    - **waarde**: contoso Service voorwaarden...
- Uitvoer claims:
    - **createdClaim**: het TOS claim type bevat de ' contoso-Service voorwaarden... ' Value.

## <a name="copyclaimifpredicatematch"></a>CopyClaimIfPredicateMatch

Kopieer de waarde van een claim naar een andere als de waarde van de invoer claim overeenkomt met het predicaat uitvoer claim. 

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | tekenreeks | Het claim type dat moet worden gekopieerd. |
| Output claim | Output claim | tekenreeks | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen. De waarde van de invoer claim wordt gecontroleerd aan de hand van dit claim predicaat. |

In het volgende voor beeld wordt de claim waarde signInName naar phoneNumber claim gekopieerd, alleen als de signInName een telefoon nummer is. Zie voor het volledige voor beeld [telefoon nummer of e-mail bericht aanmelden](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/Phone_Email_Base.xml) Starter Pack-beleid.

```xml
<ClaimsTransformation Id="SetPhoneNumberIfPredicateMatch" TransformationMethod="CopyClaimIfPredicateMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example-1"></a>Voorbeeld 1

- Invoer claims:
    - **input claim**: bob@contoso.com
- Uitvoer claims:
    - **output claim**: de uitvoer claim wordt niet gewijzigd ten opzichte van de oorspronkelijke waarde.

### <a name="example-2"></a>Voorbeeld 2

- Invoer claims:
    - **input claim**: + 11234567890
- Uitvoer claims:
    - **output claim**: + 11234567890

## <a name="compareclaims"></a>CompareClaims

Bepaal of een claim van een teken reeks gelijk is aan een andere. Het resultaat is een nieuw Boolean-claim type met de waarde `true` of `false` .

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputClaim1 | tekenreeks | Eerste claim type, dat moet worden vergeleken. |
| Input claim | inputClaim2 | tekenreeks | Tweede claim type, dat moet worden vergeleken. |
| Parameter | operator | tekenreeks | Mogelijke waarden: `EQUAL` of `NOT EQUAL` . |
| Parameter | ignoreCase | booleaans | Hiermee geeft u op of deze vergelijking het hoofdletter gebruik moet negeren van de teken reeksen die worden vergeleken. |
| Output claim | Output claim | booleaans | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen. |

Gebruik deze claim transformatie om te controleren of een claim gelijk is aan een andere claim. Bijvoorbeeld, met de volgende claim transformatie wordt gecontroleerd of de waarde van de **e-mail** claim gelijk is aan de **Verified.email** -claim.

```xml
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Invoer parameters:
    - **operator**: niet gelijk aan
    - **ignoreCase**: True
- Uitvoer claims:
    - **output claim**: True

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Hiermee wordt bepaald of een claim waarde gelijk is aan de waarde van de invoer parameter.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputClaim1 | tekenreeks | Het claim type, dat moet worden vergeleken. |
| Parameter | operator | tekenreeks | Mogelijke waarden: `EQUAL` of `NOT EQUAL` . |
| Parameter | compareTo | tekenreeks | teken reeks vergelijking, een van de waarden: Ordinal, OrdinalIgnoreCase. |
| Parameter | ignoreCase | booleaans | Hiermee geeft u op of deze vergelijking het hoofdletter gebruik moet negeren van de teken reeksen die worden vergeleken. |
| Output claim | Output claim | booleaans | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen. |

U kunt deze claim transformatie gebruiken om te controleren of een claim gelijk is aan een waarde die u hebt opgegeven. De volgende claim transformatie controleert bijvoorbeeld of de waarde van de claim **termsOfUseConsentVersion** gelijk is aan `v1` .

```xml
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld
- Invoer claims:
    - **inputClaim1**: v1
- Invoer parameters:
    - **CompareTo**: v1
    - **operator**: gelijk
    - **ignoreCase**: True
- Uitvoer claims:
    - **output claim**: True

## <a name="createrandomstring"></a>CreateRandomString

Hiermee maakt u een wille keurige teken reeks met de generator wille keurige getallen. Als de generator voor wille keurige getallen van het type is `integer` , kunt u desgewenst een Seed-para meter en een maximum aantal opgeven. Met een optionele para meter voor teken reeks notatie kan de uitvoer worden opgemaakt met behulp van deze, en een optionele base64-para meter geeft aan of de uitvoer Base64 Encoded randomGeneratorType [GUID, integer] output claim (teken reeks) is.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Parameter | randomGeneratorType | tekenreeks | Hiermee geeft u de wille keurige waarde moet worden gegenereerd, `GUID` (globale unieke id) of `INTEGER` (een getal). |
| Parameter | stringFormat | tekenreeks | Beschrijving Format teer de wille keurige waarde. |
| Parameter | base64 | booleaans | Beschrijving Zet de wille keurige waarde om in base64. Als de teken reeks indeling wordt toegepast, wordt de waarde na de teken reeks indeling gecodeerd in base64. |
| Parameter | maximumNumber | int | Beschrijving `INTEGER` Alleen voor randomGeneratorType. Geef het maximum aantal op. |
| Parameter | meerder  | int | Beschrijving `INTEGER` Alleen voor randomGeneratorType. Geef het Seed op voor de wille keurige waarde. Opmerking: hetzelfde zaad levert dezelfde reeks wille keurige getallen op. |
| Output claim | Output claim | tekenreeks | De ClaimTypes die wordt geproduceerd nadat deze claim transformatie is aangeroepen. De wille keurige waarde. |

In het volgende voor beeld wordt een algemene unieke ID gegenereerd. Deze claim transformatie wordt gebruikt om de wille keurige UPN (User Principle Name) te maken.

```xml
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Voorbeeld

- Invoer parameters:
    - **randomGeneratorType**: GUID
- Uitvoer claims:
    - **output claim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

In het volgende voor beeld wordt een wille keurige waarde voor geheel getal gegenereerd tussen 0 en 1000. De waarde wordt opgemaakt als OTP_ {wille keurige waarde}.

```xml
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer parameters:
    - **randomGeneratorType**: geheel getal
    - **maximumNumber**: 1000
    - **stringFormat**: OTP_{0}
    - **Base64**: onwaar
- Uitvoer claims:
    - **output claim**: OTP_853


## <a name="formatlocalizedstring"></a>FormatLocalizedString

Meerdere claims opmaken op basis van de vertaalde gelokaliseerde indelings teken reeks. Deze trans formatie maakt gebruik van de C#- `String.Format` methode.


| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| InputClaims |  |tekenreeks | De verzameling invoer claims die fungeren als teken reeks indeling {0} , {1} , {2} para meters. |
| Parameter | stringFormatId | tekenreeks |  De `StringId` van een [gelokaliseerde teken reeks](localization.md).   |
| Output claim | Output claim | tekenreeks | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen. |

> [!NOTE]
> De Maxi maal toegestane grootte van de teken reeks is 4000.

De FormatLocalizedString-claim transformatie gebruiken:

1. Definieer een [lokalisatie teken reeks](localization.md)en koppel deze aan een [zelfbevestigend technisch profiel](self-asserted-technical-profile.md).
1. De `ElementType` van het `LocalizedString` element moet worden ingesteld op `FormatLocalizedStringTransformationClaimType` .
1. Het `StringId` is een unieke id die u definieert en deze later in uw claim transformatie kunt gebruiken `stringFormatId` .
1. Geef in de claim transformatie de lijst met claims op die moeten worden ingesteld met de gelokaliseerde teken reeks. Stel vervolgens de `stringFormatId` in op de `StringId` van het gelokaliseerde teken reeks element. 
1. In een niet [-bevestigd technisch profiel](self-asserted-technical-profile.md)of een invoer-of uitvoer claim transformatie voor [weer gave](display-controls.md) kunt u een verwijzing naar uw claim transformatie maken.


In het volgende voor beeld wordt een fout bericht gegenereerd wanneer een account zich al in de map bevindt. In het voor beeld worden gelokaliseerde teken reeksen gedefinieerd voor Engels (standaard) en Spaans.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
      </LocalizedStrings>
    </LocalizedResources>
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">Este correo electrónico "{0}" ya es una cuenta de esta organización. Haga clic en Siguiente para iniciar sesión con esa cuenta.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

Met de claim transformatie wordt een antwoord bericht gemaakt op basis van de gelokaliseerde teken reeks. Het bericht bevat het e-mail adres van de gebruiker dat is inge sloten in de gelokaliseerde Sting- *ResponseMessge_EmailExists*.

```xml
<ClaimsTransformation Id="SetResponseMessageForEmailAlreadyExists" TransformationMethod="FormatLocalizedString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormatId" DataType="string" Value="ResponseMessge_EmailExists" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: sarah@contoso.com
- Invoer parameters:
    - **stringFormat**: ResponseMessge_EmailExists
- Uitvoer claims:
  - **output claim**: het e-mail adres sarah@contoso.com is al een account in deze organisatie. Klik op volgende om u aan te melden met dat account.


## <a name="formatstringclaim"></a>FormatStringClaim

Een claim opmaken volgens de gegeven teken reeks voor opmaak. Deze trans formatie maakt gebruik van de C#- `String.Format` methode.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim |tekenreeks |Het claim type dat fungeert als para meter voor de teken reeks indeling {0} . |
| Parameter | stringFormat | tekenreeks | De teken reeks notatie, inclusief de {0}  para meter. Deze invoer parameter ondersteunt [teken reeks claim transformatie expressies](string-transformations.md#string-claim-transformations-expressions).  |
| Output claim | Output claim | tekenreeks | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen. |

> [!NOTE]
> De Maxi maal toegestane grootte van de teken reeks is 4000.

Gebruik deze claim transformatie om een wille keurige teken reeks met één para meter op te maken {0} . In het volgende voor beeld wordt een **userPrincipalName** gemaakt. Alle technische profielen van de sociale ID-provider, zoals `Facebook-OAUTH` het aanroepen van de **CreateUserPrincipalName** om een **userPrincipalName** te genereren.

```xml
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: 5164db16-3eee-4629-bfda-dcc3326790e9
- Invoer parameters:
    - **stringFormat**: cpim_ {0} @ {RelyingPartyTenantId}
- Uitvoer claims:
  - **output claim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Indeling van twee claims volgens de gegeven teken reeks voor opmaak. Deze trans formatie maakt gebruik van de C#- `String.Format` methode.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim |tekenreeks | Het claim type dat fungeert als para meter voor de teken reeks indeling {0} . |
| Input claim | Input claim | tekenreeks | Het claim type dat fungeert als para meter voor de teken reeks indeling {1} . |
| Parameter | stringFormat | tekenreeks | De teken reeks notatie, met inbegrip van de {0} {1} para meters en. Deze invoer parameter ondersteunt [teken reeks claim transformatie expressies](string-transformations.md#string-claim-transformations-expressions).   |
| Output claim | Output claim | tekenreeks | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen. |

> [!NOTE]
> De Maxi maal toegestane grootte van de teken reeks is 4000.

Gebruik deze claim transformatie om een wille keurige teken reeks te Format teren met twee para meters {0} en {1} . In het volgende voor beeld wordt een **DisplayName** gemaakt met de opgegeven indeling:

```xml
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **inputClaim1**: Joe
    - **inputClaim2**: Fernando
- Invoer parameters:
    - **stringFormat**: {0}{1}
- Uitvoer claims:
    - **output claim**: Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Hiermee worden gelokaliseerde teken reeksen naar claims gekopieerd.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Output claim | De naam van de gelokaliseerde teken reeks | tekenreeks | Lijst met claim typen die worden geproduceerd nadat deze claim transformatie is aangeroepen. |

De GetLocalizedStringsTransformation-claim transformatie gebruiken:

1. Definieer een [lokalisatie teken reeks](localization.md) en koppel deze aan een [zelfbevestigend technisch profiel](self-asserted-technical-profile.md).
1. De `ElementType` van het `LocalizedString` element moet worden ingesteld op `GetLocalizedStringsTransformationClaimType` .
1. Het `StringId` is een unieke id die u definieert en deze later in uw claim transformatie kunt gebruiken.
1. Geef in de claim transformatie de lijst met claims op die moeten worden ingesteld met de gelokaliseerde teken reeks. De `ClaimTypeReferenceId` is een verwijzing naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleid. De `TransformationClaimType` is de naam van de gelokaliseerde teken reeks, zoals gedefinieerd in de `StringId` van het `LocalizedString` element.
1. In een niet [-bevestigd technisch profiel](self-asserted-technical-profile.md)of een invoer-of uitvoer claim transformatie voor [weer gave](display-controls.md) kunt u een verwijzing naar uw claim transformatie maken.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

In het volgende voor beeld wordt gezocht naar het onderwerp van de e-mail, de hoofd tekst, uw code bericht en de hand tekening van het e-mail bericht, van gelokaliseerde teken reeksen. Deze claims worden later gebruikt door de aangepaste sjabloon voor e-mail verificatie.

Gelokaliseerde teken reeksen definiëren voor Engels (standaard) en Spaans.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

De claim transformatie stelt de waarde van het *claim type in* op de waarde van de `StringId` *email_subject*.

```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Uitvoer claims:
  - **onderwerp**: e-mail verificatie code van Contoso-account
  - **bericht**: Bedankt voor het verifiëren van je account.
  - **codeIntro**: uw code is
  - **hand tekening**: met vriendelijke groet


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Een item opzoeken op basis van een verzameling claim **beperkingen** .

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | mapFromClaim | tekenreeks | De claim die de tekst bevat die moet worden opgezocht in de **restrictionValueClaim** claims met de **beperkings** verzameling.  |
| Output claim | restrictionValueClaim | tekenreeks | De claim die de **beperkings** verzameling bevat. Nadat de claim transformatie is aangeroepen, bevat de waarde van deze claim de waarde van het geselecteerde item. |

In het volgende voor beeld wordt de beschrijving van het fout bericht opgezocht op basis van de fout code. De **responseMsg** -claim bevat een verzameling fout berichten die aan de eind gebruiker moeten worden gepresenteerd of naar de Relying Party worden verzonden.

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
De claim transformatie zoekt de tekst van het item en retourneert de waarde ervan. Als de beperking is gelokaliseerd met `<LocalizedCollection>` , retourneert de trans formatie van claims de gelokaliseerde waarde.

```xml
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **mapFromClaim**: B2C_V1_90001
- Uitvoer claims:
    - **restrictionValueClaim**: u kunt zich niet aanmelden omdat u een kleine.

## <a name="lookupvalue"></a>LookupValue

Zoek een claim waarde op uit een lijst met waarden op basis van de waarde van een andere claim.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | inputParameterId | tekenreeks | De claim die de opzoek waarde bevat |
| Parameter | |tekenreeks | Verzameling invoer parameters. |
| Parameter | errorOnFailedLookup | booleaans | Controleren of er een fout wordt geretourneerd als er geen overeenkomende zoek actie is. |
| Output claim | inputParameterId | tekenreeks | De ClaimTypes die wordt geproduceerd nadat deze claim transformatie is aangeroepen. De waarde van de overeenkomst `Id` . |

In het volgende voor beeld wordt de domein naam in een van de input parameters-verzamelingen opgezocht. De claim transformatie zoekt de domein naam in de id en retourneert de waarde (een toepassings-ID).

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **inputParameterId**: test.com
- Invoer parameters:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: False
- Uitvoer claims:
    - **output claim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Wanneer `errorOnFailedLookup` de invoer parameter is ingesteld op `true` , wordt de **LookupValue** -claim transformatie altijd uitgevoerd op basis van een [validatie technische profiel](validation-technical-profile.md) dat wordt aangeroepen door een [zelfbevestigend technisch profiel](self-asserted-technical-profile.md)of een [DisplayConrtol](display-controls.md). De `LookupNotFound` meta gegevens van een zelf-bebevestigde technische profiel bepalen het fout bericht dat aan de gebruiker wordt gepresenteerd.

![AssertStringClaimsAreEqual-uitvoering](./media/string-transformations/assert-execution.png)

In het volgende voor beeld wordt de domein naam in een van de input parameters-verzamelingen opgezocht. De claim transformatie zoekt de domein naam in de id en retourneert de waarde (een toepassings-ID) of een fout bericht wordt gegenereerd.

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **inputParameterId**: Live.com
- Invoer parameters:
    - **contoso.com**: 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **Microsoft.com**: 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: True
- Fout:
    - Er is geen overeenkomst gevonden voor de invoer claim waarde in de lijst met invoer parameter-id's en errorOnFailedLookup is waar.


## <a name="nullclaim"></a>NullClaim

De waarde van een bepaalde claim opschonen.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Output claim | claim_to_null | tekenreeks | De waarde van de claim is ingesteld op NULL. |

Gebruik deze claim transformatie om overbodige gegevens uit de eigenschap Bag van claims te verwijderen, zodat de sessie cookie kleiner wordt. In het volgende voor beeld wordt de waarde van het `TermsOfService` claim type verwijderd.

```xml
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoer claims:
    - **output claim**: Welkom bij de app contoso. Als u doorgaat met bladeren en deze website gebruikt, gaat u ermee akkoord dat u voldoet aan de volgende voor waarden...
- Uitvoer claims:
    - **output claim**: null

## <a name="parsedomain"></a>ParseDomain

Hiermee wordt het domein gedeelte van een e-mail adres opgehaald.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | emailAddress | tekenreeks | Het claim type dat het e-mail adres bevat. |
| Output claim | domein | tekenreeks | Het claim type dat is geproduceerd nadat deze claim transformatie is aangeroepen-het domein. |

Gebruik deze claim transformatie voor het parseren van de domein naam na het @-symbool van de gebruiker. De volgende claim transformatie laat zien hoe u de domein naam kunt parseren vanuit een **e-mail** claim.

```xml
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **emailAddress**: joe@outlook.com
- Uitvoer claims:
    - **domein**: Outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Controleert of een teken reeks claim `claimToMatch` en `matchTo` invoer parameter gelijk zijn, en stelt de uitvoer claims in met de waarde die aanwezig is in de `outputClaimIfMatched` invoer parameter en de resulterende uitvoer claim vergelijken, die moet worden ingesteld als `true` of `false` op basis van het resultaat van de vergelijking.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | claimToMatch | tekenreeks | Het claim type, dat moet worden vergeleken. |
| Parameter | matchTo | tekenreeks | De reguliere expressie die moet worden vergeleken. |
| Parameter | outputClaimIfMatched | tekenreeks | De waarde die moet worden ingesteld als teken reeksen gelijk zijn. |
| Parameter | extractGroups | booleaans | Beschrijving Hiermee geeft u op of de regex match moet groepen waarden extra heren. Mogelijke waarden: `true` , of `false` (standaard). | 
| Output claim | Output claim | tekenreeks | Als de reguliere expressie overeenkomt, bevat deze uitvoer claim de waarde van de `outputClaimIfMatched` invoer parameter. Of null, als deze niet overeenkomt. |
| Output claim | regexCompareResultClaim | booleaans | De reguliere expressie komt overeen met het resultaat type van de uitvoer van resultaten, dat moet worden ingesteld als `true` of `false` op basis van het resultaat van de overeenkomst. |
| Output claim| De naam van de claim| tekenreeks | Als de invoer parameter extractGroups is ingesteld op True, wordt een lijst met claim typen die worden geproduceerd nadat deze claim transformatie is aangeroepen. De naam van het claim type moet overeenkomen met de naam van de regex-groep. | 

### <a name="example-1"></a>Voorbeeld 1

Hiermee wordt gecontroleerd of het gegeven telefoon nummer geldig is, op basis van reguliere-expressie patroon voor telefoon nummer.

```xml
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoer claims:
    - **claimToMatch**: "64854114520"
- Invoer parameters:
    - **matchTo**: "^ [0-9] {4,16} $"
    - **outputClaimIfMatched**: "isPhone"
- Uitvoer claims:
    - **output claim**: "isPhone"
    - **regexCompareResultClaim**: True

### <a name="example-2"></a>Voorbeeld 2

Controleert of het gegeven e-mail adres geldig is en retourneert de e-mail alias.

```xml
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Invoer claims:
    - **claimToMatch**: " emily@contoso.com "
- Invoer parameters:
    - **matchTo**: `(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: "isEmail"
    - **extractGroups**: True
- Uitvoer claims:
    - **output claim**: "isEmail"
    - **regexCompareResultClaim**: True
    - **mailalias**: Elsje
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Controleert of een teken reeks claim en `matchTo` invoer parameter gelijk zijn, en stelt de uitvoer claims in met de waarde die aanwezig is in `stringMatchMsg` en de `stringMatchMsgCode` invoer parameters, samen met resultaat uitvoer claim vergelijken, die moet worden ingesteld als `true` of `false` op basis van het resultaat van de vergelijking.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | tekenreeks | Het claim type, dat moet worden vergeleken. |
| Parameter | matchTo | tekenreeks | De teken reeks waarmee moet worden vergeleken `inputClaim` . |
| Parameter | stringComparison | tekenreeks | Mogelijke waarden: `Ordinal` of `OrdinalIgnoreCase` . |
| Parameter | stringMatchMsg | tekenreeks | De eerste waarde die moet worden ingesteld als teken reeksen gelijk zijn. |
| Parameter | stringMatchMsgCode | tekenreeks | De tweede waarde die moet worden ingesteld als teken reeksen gelijk zijn. |
| Output claim | outputClaim1 | tekenreeks | Als teken reeksen gelijk zijn, bevat deze uitvoer claim de waarde van de `stringMatchMsg` invoer parameter. |
| Output claim | outputClaim2 | tekenreeks | Als teken reeksen gelijk zijn, bevat deze uitvoer claim de waarde van de `stringMatchMsgCode` invoer parameter. |
| Output claim | stringCompareResultClaim | booleaans | Het type claim van de uitvoer resultaat dat moet worden ingesteld op `true` of `false` op basis van het resultaat van de vergelijking. |

U kunt deze claim transformatie gebruiken om te controleren of een claim gelijk is aan de waarde die u hebt opgegeven. De volgende claim transformatie controleert bijvoorbeeld of de waarde van de claim **termsOfUseConsentVersion** gelijk is aan `v1` . Zo ja, wijzig de waarde in `v2` .

```xml
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: v1
- Invoer parameters:
    - **matchTo**: v1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg**: B2C_V1_90005
    - **stringMatchMsgCode**: de TOS wordt bijgewerkt naar v2
- Uitvoer claims:
    - **outputClaim1**: B2C_V1_90005
    - **outputClaim2**: de TOS wordt bijgewerkt naar v2
    - **stringCompareResultClaim**: True

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Controleert of een teken reeks claim en `matchTo` invoer parameter gelijk zijn, en stelt de uitvoer claims in met de waarde die aanwezig is in de `outputClaimIfMatched` invoer parameter en de resulterende uitvoer claim vergelijken, die moet worden ingesteld als `true` of `false` op basis van het resultaat van de vergelijking.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | claimToMatch | tekenreeks | Het claim type, dat moet worden vergeleken. |
| Parameter | matchTo | tekenreeks | De teken reeks die moet worden vergeleken met input claim. |
| Parameter | stringComparison | tekenreeks | Mogelijke waarden: `Ordinal` of `OrdinalIgnoreCase` . |
| Parameter | outputClaimIfMatched | tekenreeks | De waarde die moet worden ingesteld als teken reeksen gelijk zijn. |
| Output claim | Output claim | tekenreeks | Als teken reeksen gelijk zijn, bevat deze uitvoer claim de waarde van de `outputClaimIfMatched` invoer parameter. Of null, als de teken reeksen niet overeenkomen. |
| Output claim | stringCompareResultClaim | booleaans | Het type claim van de uitvoer resultaat dat moet worden ingesteld op `true` of `false` op basis van het resultaat van de vergelijking. |

De volgende claim transformatie controleert bijvoorbeeld of de waarde van **ageGroup** claim gelijk is aan `Minor` . Als dat het geval is, retourneert u de waarde in `B2C_V1_90001` .

```xml
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **claimToMatch**: Minor
- Invoer parameters:
    - **matchTo**: Minor
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched**: B2C_V1_90001
- Uitvoer claims:
    - **isMinorResponseCode**: B2C_V1_90001
    - **isMinor**: True


## <a name="stringcontains"></a>StringContains

Bepalen of een opgegeven subtekenreeks in de invoer claim voor komt. Het resultaat is een nieuw Boolean-claim type met de waarde `true` of `false` . `true` Als de waarde-para meter in deze teken reeks voor komt, anders `false` .

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | tekenreeks | Het claim type, dat moet worden doorzocht. |
|Parameter|bevat|tekenreeks|De waarde waarnaar moet worden gezocht.|
|Parameter|ignoreCase|tekenreeks|Hiermee geeft u op of deze vergelijking het hoofdletter gebruik moet negeren van de teken reeks die wordt vergeleken.|
| Output claim | Output claim | tekenreeks | Het claim type dat is geproduceerd nadat deze ClaimsTransformation is aangeroepen. Een Booleaanse indicator als de subtekenreeks in de invoer claim voor komt. |

Gebruik deze claim transformatie om te controleren of een teken reeks claim type een subtekenreeks bevat. In het volgende voor beeld wordt gecontroleerd of het `roles` type teken reeks claim de waarde **beheerder** bevat.

```xml
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: "beheerder, goed keurder, editor"
- Invoer parameters:
    - **bevat**: "beheerder"
    - **ignoreCase**: True
- Uitvoer claims:
    - **output claim**: True

## <a name="stringsubstring"></a>StringSubstring

Haalt delen van een teken reeks claim type op, beginnend bij het teken op de opgegeven positie en retourneert het opgegeven aantal tekens.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | tekenreeks | Het claim type, dat de teken reeks bevat. |
| Parameter | Start index | int | De op nul gebaseerde positie van het begin teken van een subtekenreeks in dit exemplaar. |
| Parameter | lengte | int | Het aantal tekens in de subtekenreeks. |
| Output claim | Output claim | booleaans | Een teken reeks die overeenkomt met de subtekenreeks van length die begint bij start index in dit exemplaar, of leeg als start index gelijk is aan de lengte van deze instantie en de lengte nul is. |

Haal bijvoorbeeld het voor voegsel land/regio telefoon nummer op.


```xml
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: "+ 1644114520"
- Invoer parameters:
    - **Start index**: 0
    - **lengte**: 2
- Uitvoer claims:
    - **output claim**: "+ 1"

## <a name="stringreplace"></a>StringReplace

Zoekt een claim type teken reeks voor een opgegeven waarde en retourneert een nieuwe claim type teken reeks waarin alle exemplaren van een opgegeven teken reeks in de huidige teken reeks worden vervangen door een andere opgegeven teken reeks.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | tekenreeks | Het claim type, dat de teken reeks bevat. |
| Parameter | oldValue | tekenreeks | De teken reeks die moet worden doorzocht. |
| Parameter | newValue | tekenreeks | De teken reeks voor het vervangen van alle exemplaren van `oldValue` |
| Output claim | Output claim | booleaans | Een teken reeks die overeenkomt met de huidige teken reeks, behalve dat alle exemplaren van oldValue worden vervangen door newValue. Als oldValue niet wordt gevonden in het huidige exemplaar, retourneert de methode het huidige exemplaar ongewijzigd. |

U kunt bijvoorbeeld een telefoon nummer normaliseren door de tekens te verwijderen `-`


```xml
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Voorbeeld

- Invoer claims:
    - **input claim**: "+ 164-411-452-054"
- Invoer parameters:
    - **oldValue**: "-"
    - **newValue**: ""
- Uitvoer claims:
    - **output claim**: "+ 164411452054"

## <a name="stringjoin"></a>StringJoin

Voegt de elementen van een opgegeven type teken reeks verzamelings claim toe met behulp van het opgegeven scheidings teken tussen elk element of lid.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | stringCollection | Een verzameling die de teken reeksen bevat die u wilt samen voegen. |
| Parameter | scheidingsteken | tekenreeks | De teken reeks die als schei ding moet worden gebruikt, zoals een komma `,` . |
| Output claim | Output claim | tekenreeks | Een teken reeks die bestaat uit de leden van de `inputClaim` teken reeks verzameling, gescheiden door de `delimiter` invoer parameter. |

In het volgende voor beeld wordt een teken reeks verzameling van gebruikers rollen gebruikt en wordt deze geconverteerd naar een komma als scheidings teken reeks. U kunt deze methode gebruiken om een teken reeks verzameling op te slaan in een Azure AD-gebruikers account. Als u later het account uit de map leest, gebruikt u de `StringSplit` om de teken reeks met het komma scheidings tekens terug te converteren naar de teken reeks verzameling.

```xml
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **input claim**: ["beheerder", "Auteur", "lezer"]
- Invoer parameters:
  - **scheidings teken**: ","
- Uitvoer claims:
  - **output claim**: "beheerder, auteur, lezer"


## <a name="stringsplit"></a>StringSplit

Retourneert een teken reeks matrix die de subtekenreeksen in dit exemplaar bevat die worden gescheiden door elementen van een opgegeven teken reeks.

| Item | TransformationClaimType | Gegevenstype | Notities |
| ---- | ----------------------- | --------- | ----- |
| Input claim | Input claim | tekenreeks | Een type teken reeks claim dat de subtekenreeksen bevat die moeten worden gesplitst. |
| Parameter | scheidingsteken | tekenreeks | De teken reeks die als schei ding moet worden gebruikt, zoals een komma `,` . |
| Output claim | Output claim | stringCollection | Een teken reeks verzameling waarvan de elementen de subtekenreeksen in deze teken reeks bevatten die worden gescheiden door de `delimiter` invoer parameter. |

In het volgende voor beeld wordt een teken reeks met scheidings tekens van gebruikers rollen gebruikt en geconverteerd naar een teken reeks verzameling.

```xml
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Voorbeeld

- Invoer claims:
  - **input claim**: "beheerder, auteur, lezer"
- Invoer parameters:
  - **scheidings teken**: ","
- Uitvoer claims:
  - **output claim**: ["beheerder", "Auteur", "lezer"]

## <a name="string-claim-transformations-expressions"></a>Teken reeks claim transformaties expressies
Claim Transforms-expressies in Azure AD B2C aangepaste beleids regels bieden context informatie over de Tenant-ID en de technische profiel-ID.

  | Expressie | Beschrijving | Voorbeeld |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | De technische profileId naam. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | De Tenant-ID van het Relying Party-beleid. | your-tenant.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | De Tenant-ID van het vertrouwens raamwerk. | your-tenant.onmicrosoft.com |
