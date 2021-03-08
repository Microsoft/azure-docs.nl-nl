---
title: Lokalisatie-Azure Active Directory B2C
description: Geef het lokalisatie-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 3a5afcd8c0ef0c31353cd2369ead332675c9877f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453118"
---
# <a name="localization-element"></a>Lokalisatie-element

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het **lokalisatie** -element biedt ondersteuning voor meerdere land instellingen of talen in het beleid voor de gebruikers ritten. Met de lokalisatie ondersteuning in beleids regels kunt u:

- Stel de expliciete lijst met ondersteunde talen in een beleid in en kies een standaard taal.
- Taalspecifieke teken reeksen en verzamelingen opgeven.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

Het **lokalisatie** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Ingeschakeld | Nee | Mogelijke waarden: `true` of `false` . |

Het **lokalisatie** -element bevat de volgende XML-elementen

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1: n | Lijst met ondersteunde talen. |
| LocalizedResources | 0: n | Lijst met gelokaliseerde resources. |

## <a name="supportedlanguages"></a>SupportedLanguages

Het **SupportedLanguages** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| DefaultLanguage | Ja | De taal die moet worden gebruikt als standaard voor gelokaliseerde resources. |
| MergeBehavior | Nee | Een opsommings waarde van waarden die samen worden samengevoegd met een claim type dat aanwezig is in een bovenliggend beleid met dezelfde id. Gebruik dit kenmerk wanneer u een claim overschrijft die is opgegeven in basis beleid. Mogelijke waarden: `Append` , `Prepend` of `ReplaceAll` . De `Append` waarde geeft aan dat het verzamelen van gegevens moet worden toegevoegd aan het einde van de verzameling die in het bovenliggende beleid is opgegeven. De `Prepend` waarde geeft aan dat het verzamelen van gegevens moet worden toegevoegd vóór de verzameling die in het bovenliggende beleid is opgegeven. De `ReplaceAll` waarde geeft aan dat het verzamelen van gegevens die in het bovenliggende beleid zijn gedefinieerd, moet worden genegeerd, in plaats van de gegevens die in het huidige beleid zijn gedefinieerd. |

### <a name="supportedlanguages"></a>SupportedLanguages

Het **SupportedLanguages** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1: n | Geeft inhoud weer die voldoet aan een taal code per RFC 5646-Tags voor het identificeren van talen. |

## <a name="localizedresources"></a>LocalizedResources

Het **LocalizedResources** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt om gelokaliseerde bronnen uniek te identificeren. |

Het **LocalizedResources** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0: n | Definieert volledige verzamelingen in verschillende cult uren. Een verzameling kan een verschillend aantal items en verschillende teken reeksen voor verschillende cult uren hebben. Voor beelden van verzamelingen zijn de opsommingen die worden weer gegeven in claim typen. Een lijst met landen/regio's wordt bijvoorbeeld weer gegeven aan de gebruiker in een vervolg keuzelijst. |
| LocalizedStrings | 0: n | Hiermee worden alle teken reeksen gedefinieerd, met uitzonde ring van de teken reeksen die in verzamelingen worden weer gegeven, in verschillende cult uren. |

### <a name="localizedcollections"></a>LocalizedCollections

Het **LocalizedCollections** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1: n | Lijst met ondersteunde talen. |

#### <a name="localizedcollection"></a>LocalizedCollection

Het **LocalizedCollection** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Type | Ja | Verwijst naar een claim type-element of een gebruikers interface-element in het beleids bestand. |
| ElementId | Ja | Een teken reeks die een verwijzing bevat naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema, dat wordt gebruikt als **element** type is ingesteld op a. |
| TargetCollection | Ja | De doel verzameling. |

Het **LocalizedCollection** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Item | 0: n | Hiermee definieert u een beschik bare optie voor de gebruiker om te selecteren voor een claim in de gebruikers interface, zoals een waarde in een vervolg keuzelijst. |

Het element **item** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Tekst | Ja | De gebruiks vriendelijke weergave teken reeks die moet worden weer gegeven aan de gebruiker in de gebruikers interface voor deze optie. |
| Waarde | Ja | De waarde van de teken reeks claim die is gekoppeld aan het selecteren van deze optie. |
| SelectByDefault | Nee | Hiermee wordt aangegeven of deze optie standaard moet worden geselecteerd in de gebruikers interface. Mogelijke waarden: True of false. |

In het volgende voor beeld ziet u het gebruik van het element **LocalizedCollections** . Het bevat twee **LocalizedCollection** -elementen, een voor Engels en een andere voor Spaans. Stel de **beperkings** verzameling van de claim `Gender` in met een lijst met items voor Engels en Spaans.

```xml
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### <a name="localizedstrings"></a>LocalizedStrings

Het **LocalizedStrings** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| LocalizedString | 1: n | Een gelokaliseerde teken reeks. |

Het **LocalizedString** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Type | Ja | Mogelijke waarden: [ClaimsProvider](#claimsprovider), [claim](#claimtype)type, [errorMessage](#errormessage), [GetLocalizedStringsTransformationClaimType](#getlocalizedstringstransformationclaimtype), [FormatLocalizedStringTransformationClaimType](#formatlocalizedstringtransformationclaimtype), [predicaat](#predicate), [InputValidation](#inputvalidation)of [UxElement](#uxelement).   | 
| ElementId | Ja | Als **element type** is ingesteld op `ClaimType` , `Predicate` , of `InputValidation` , bevat dit element een verwijzing naar een claim type dat al is gedefinieerd in de sectie ClaimsSchema. |
| StringId | Ja | Als **element type** is ingesteld op `ClaimType` , bevat dit element een verwijzing naar een kenmerk van een claim type. Mogelijke waarden: `DisplayName` , `AdminHelpText` of `PatternHelpText` . De `DisplayName` waarde wordt gebruikt om de weergave naam van de claim in te stellen. De `AdminHelpText` waarde wordt gebruikt om de Help-tekst naam van de claim gebruiker in te stellen. De `PatternHelpText` waarde wordt gebruikt om de Help-tekst van het claim patroon in te stellen. Als element **type** is ingesteld op `UxElement` , bevat dit element een verwijzing naar een kenmerk van een element van een gebruikers interface. Als element **type** is ingesteld op `ErrorMessage` , wordt met dit element de id van een fout bericht opgegeven. Zie [lokalisatie teken reeks-id's](localization-string-ids.md) voor een volledige lijst met `UxElement` id's.|

## <a name="elementtype"></a>Type

De verwijzing naar het element type naar een claim object, een claim transformatie of een gebruikers interface-element in het beleid dat moet worden gelokaliseerd.

| Element dat moet worden gelokaliseerd | Type | ElementId |StringId |
| --------- | -------- | ----------- |----------- |
| Naam van ID-provider |`ClaimsProvider`| | De ID van het ClaimsExchange-element|
| Kenmerken van claim type|`ClaimType`|Naam van het claim type| Het kenmerk van de claim die moet worden gelokaliseerd. Mogelijke waarden: `AdminHelpText` ,, en `DisplayName` `PatternHelpText` `UserHelpText` .|
|Foutbericht|`ErrorMessage`||De ID van het fout bericht |
|Hiermee worden gelokaliseerde teken reeksen naar claims gekopieerd|`GetLocalizedStringsTra nsformationClaimType`||De naam van de uitvoer claim|
|Gebruikers bericht van predikaat|`Predicate`|De naam van het predikaat| Het kenmerk van het predikaat dat moet worden gelokaliseerd. Mogelijke waarden: `HelpText` .|
|Gebruikers bericht van de predicaat groep|`InputValidation`|De ID van het PredicateValidation-element.|De ID van het PredicateGroup-element. De predikaat-groep moet een onderliggend element zijn van het validatie-predicaat dat is gedefinieerd in de ElementId.|
|Elementen van de gebruikersinterface |`UxElement` | | De ID van het element van de gebruikers interface dat moet worden gelokaliseerd.|
|[Besturings element weer geven](display-controls.md) |`DisplayControl` |De ID van het weergave besturings element. | De ID van het element van de gebruikers interface dat moet worden gelokaliseerd.|

## <a name="examples"></a>Voorbeelden

### <a name="claimsprovider"></a>ClaimsProvider

De ClaimsProvider-waarde wordt gebruikt om een van de weergave naam van claim providers te lokaliseren. 

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
  </ClaimsExchanges>
</OrchestrationStep>

```

In het volgende voor beeld ziet u hoe de weergave naam van claim providers wordt gelokaliseerd.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### <a name="claimtype"></a>Claim type

De waarde voor claim type wordt gebruikt om een van de claim kenmerken te lokaliseren. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

In het volgende voor beeld ziet u hoe de kenmerken DisplayName, UserHelpText en PatternHelpText van het type e-mail claim worden gelokaliseerd.

```xml
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### <a name="errormessage"></a>ErrorMessage

De waarde ErrorMessage wordt gebruikt om een van de systeem fout berichten te lokaliseren. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

In het volgende voor beeld ziet u hoe het fout bericht UserMessageIfClaimsPrincipalAlreadyExists wordt gelokaliseerd.


```xml
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### <a name="formatlocalizedstringtransformationclaimtype"></a>FormatLocalizedStringTransformationClaimType

De FormatLocalizedStringTransformationClaimType-waarde wordt gebruikt om claims in een gelokaliseerde teken reeks op te maken. Zie [FormatLocalizedString claims Transformation](string-transformations.md#formatlocalizedstring) (Engelstalig) voor meer informatie.


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

In het volgende voor beeld ziet u hoe u de teken reeks indeling van de FormatLocalizedStringTransformationClaimType-claim transformatie kunt lokaliseren.

```xml
<LocalizedString ElementType="FormatLocalizedStringTransformationClaimType" StringId="ResponseMessge_EmailExists">The email '{0}' is already an account in this organization. Click Next to sign in with that account.</LocalizedString>
```

### <a name="getlocalizedstringstransformationclaimtype"></a>GetLocalizedStringsTransformationClaimType

De GetLocalizedStringsTransformationClaimType-waarde wordt gebruikt om gelokaliseerde teken reeksen naar claims te kopiëren. Zie [GetLocalizedStringsTransformation claims Transformation](string-transformations.md#getlocalizedstringstransformation) (Engelstalig) voor meer informatie.


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

In het volgende voor beeld ziet u hoe uitvoer claims van de GetLocalizedStringsTransformation-claim transformatie worden gelokaliseerd.

```xml
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
<LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
```

### <a name="predicate"></a>Predicaat

De predicaat waarde wordt gebruikt om een van de [predicaat](predicates.md) -fout berichten te lokaliseren. 

```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

In het volgende voor beeld ziet u hoe predikaten Help-tekst worden gelokaliseerd.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### <a name="inputvalidation"></a>InputValidation

De InputValidation-waarde wordt gebruikt om een van de fout berichten van de [PredicateValidation](predicates.md) -groep te lokaliseren. 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

In het volgende voor beeld ziet u hoe u een Help-tekst voor een predicaat validatie groep kunt lokaliseren.

```xml
<LocalizedString ElementType="InputValidation" ElementId="CustomPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### <a name="uxelement"></a>UxElement

De UxElement-waarde wordt gebruikt om een van de elementen van de gebruikers interface te lokaliseren. In het volgende voor beeld ziet u hoe de knoppen door gaan en annuleren worden gelokaliseerd.

```xml
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

### <a name="displaycontrol"></a>Besturings

De waarde voor weer gave wordt gebruikt om een van de elementen van de gebruikers interface van het [Weergave besturings element](display-controls.md) te lokaliseren. Wanneer deze functie is ingeschakeld, gebruikt het besturings element voor het weer geven van localizedStrings de ***prioriteit** _ voor een aantal van de _ *UxElement** StringIDs, zoals **ver_but_send**, **ver_but_edit**, **ver_but_resend** en **ver_but_verify**. In het volgende voor beeld ziet u hoe u de knoppen verzenden en verifiëren kunt lokaliseren. 

```xml
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
<LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
```

In de sectie meta gegevens van een niet-bevestigd technisch profiel moet DataUri zijn ingesteld op [versie](page-layout.md) 2.1.0 of hoger van de ContentDefinition. Bijvoorbeeld:

```xml
<ContentDefinition Id="api.selfasserted">
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.1.0</DataUri>
  ...
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor lokalisatie van voor beelden:

- [Taal aanpassing met aangepast beleid in Azure Active Directory B2C](language-customization.md)
- [Taal aanpassing met gebruikers stromen in Azure Active Directory B2C](language-customization.md)
