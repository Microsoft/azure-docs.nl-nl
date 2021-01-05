---
title: OTP-verificatie (one-time password) inschakelen
titleSuffix: Azure AD B2C
description: Meer informatie over het instellen van een OTP-scenario (one-time password) met behulp van Azure AD B2C aangepast beleid.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 12b9639342e2e35b9229aa15bb9cfb4695427606
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881188"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Een eenmalige wachtwoord technische profiel definiëren in een Azure AD B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het beheren van het genereren en verifiëren van een eenmalig wacht woord. Gebruik een technisch profiel om een code te genereren en controleer de code later.

Het eenmalige wacht woord technisch profiel kan ook een fout bericht retour neren tijdens de verificatie van de code. Ontwerp de integratie met het eenmalige wacht woord met behulp van een **validatie technische profiel**. Een profiel voor validatie technische verificatie roept het technische profiel voor eenmalige wacht woorden aan om een code te controleren. Het validatie-technische profiel valideert de door de gebruiker verschafte gegevens voordat de gebruikers traject doorgaat. Met het technische profiel voor validatie wordt er een fout bericht weer gegeven op een zelf-bevestigingen pagina.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary` . Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C:

```xml
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

In het volgende voor beeld ziet u een technisch profiel met een eenmalig wacht woord:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>Code genereren

De eerste modus van dit technische profiel is het genereren van een code. Hieronder vindt u de opties die kunnen worden geconfigureerd voor deze modus. Gegenereerde codes en pogingen worden bijgehouden in de sessie. 

### <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die vereist zijn voor verzen ding naar de provider voor eenmalig wachtwoord protocol. U kunt de naam van uw claim ook toewijzen aan de hieronder gedefinieerde naam.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| progid's | Ja | De id waarmee de gebruiker wordt geïdentificeerd die de code later moet verifiëren. Dit wordt vaak gebruikt als de id van de bestemming waarnaar de code wordt geleverd, bijvoorbeeld e-mail adres of telefoon nummer. |

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar de eenmalige wachtwoord protocol provider worden verzonden.

### <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die zijn gegenereerd door de provider voor eenmalig wacht woord protocol. U kunt de naam van uw claim ook toewijzen aan de hieronder gedefinieerde naam.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| otpGenerated | Ja | De gegenereerde code waarvan de sessie wordt beheerd door Azure AD B2C. |

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="metadata"></a>Metagegevens

De volgende instellingen kunnen worden gebruikt voor het configureren van de modus voor code generatie:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | Nee | Tijd in seconden totdat de code verloopt. Minimum: `60` ; Maximum: `1200` ; Standaard: `600` . Telkens wanneer er een code wordt gegeven (zelfde code met `ReuseSameCode` of een nieuwe code), wordt het verloop van de code verlengd. Deze keer wordt ook gebruikt om de time-out voor opnieuw proberen in te stellen (zodra het maximum aantal pogingen is bereikt, wordt de gebruiker vergrendeld om nieuwe codes te verkrijgen tot deze tijd is verstreken). |
| CodeLength | Nee | Lengte van de code. De standaardwaarde is `6`. |
| CharacterSet | Nee | De tekenset voor de code, opgemaakt voor gebruik in een reguliere expressie. Bijvoorbeeld `a-z0-9A-Z`. De standaardwaarde is `0-9`. De tekenset moet mini maal tien verschillende tekens bevatten in de opgegeven set. |
| NumRetryAttempts | Nee | Het aantal verificatie pogingen voordat de code als ongeldig wordt beschouwd. De standaardwaarde is `5`. |
| NumCodeGenerationAttempts | Nee | Het aantal pogingen voor het genereren van maximum code per id. De standaard waarde is 10 als niet is opgegeven. |
| Bewerking | Ja | De bewerking die moet worden uitgevoerd. Mogelijke waarde: `GenerateCode` . |
| ReuseSameCode | Nee | Of dezelfde code moet worden opgegeven in plaats van een nieuwe code te genereren wanneer de gegeven code niet is verlopen en nog geldig is. De standaardwaarde is `false`.  |



### <a name="example"></a>Voorbeeld

Het volgende voor beeld `TechnicalProfile` wordt gebruikt voor het genereren van een code:

```xml
<TechnicalProfile Id="GenerateCode">
  <DisplayName>Generate Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">GenerateCode</Item>
    <Item Key="CodeExpirationInSeconds">600</Item>
    <Item Key="CodeLength">6</Item>
    <Item Key="CharacterSet">0-9</Item>
    <Item Key="NumRetryAttempts">5</Item>
    <Item Key="NumCodeGenerationAttempts">15</Item>
    <Item Key="ReuseSameCode">false</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Code verifiëren

De tweede modus van dit technische profiel is om een code te controleren. Hieronder vindt u de opties die kunnen worden geconfigureerd voor deze modus.

### <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die vereist zijn voor verzen ding naar de provider voor eenmalig wachtwoord protocol. U kunt de naam van uw claim ook toewijzen aan de hieronder gedefinieerde naam.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| progid's | Ja | De id voor het identificeren van de gebruiker die eerder een code heeft gegenereerd. Dit wordt vaak gebruikt als de id van de bestemming waarnaar de code wordt geleverd, bijvoorbeeld e-mail adres of telefoon nummer. |
| otpToVerify | Ja | De verificatie code van de gebruiker. |

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar de eenmalige wachtwoord protocol provider worden verzonden.

### <a name="output-claims"></a>Uitvoer claims

Er zijn geen uitvoer claims beschikbaar tijdens de code verificatie van deze protocol provider.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="metadata"></a>Metagegevens

De volgende instellingen kunnen worden gebruikt voor de code verificatie modus:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | De bewerking die moet worden uitgevoerd. Mogelijke waarde: `VerifyCode` . |


### <a name="ui-elements"></a>UI-elementen

De volgende meta gegevens kunnen worden gebruikt voor het configureren van de fout berichten die worden weer gegeven wanneer de code verificatie mislukt. De meta gegevens moeten worden geconfigureerd in het [zelfondertekende](self-asserted-technical-profile.md) technische profiel. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#one-time-password-error-messages).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | Nee | Het bericht dat wordt weer gegeven aan de gebruiker als de sessie met de code verificatie is verlopen. Het is de code verlopen of de code is nooit voor een bepaalde id gegenereerd. |
| UserMessageIfMaxRetryAttempted | Nee | Het bericht dat aan de gebruiker wordt weer gegeven als het maximum aantal toegestane verificatie pogingen is overschreden. |
| UserMessageIfMaxNumberOfCodeGenerated | Nee | Het bericht dat wordt weer gegeven aan de gebruiker als het genereren van de code het Maxi maal toegestane aantal pogingen heeft overschreden. |
| UserMessageIfInvalidCode | Nee | Het bericht dat wordt weer gegeven aan de gebruiker als deze een ongeldige code heeft gegeven. |
| UserMessageIfVerificationFailedRetryAllowed | Nee | Het bericht dat wordt weer gegeven aan de gebruiker als deze een ongeldige code heeft verstrekt en de gebruiker de juiste code kan opgeven.  |
|UserMessageIfSessionConflict|Nee| Het bericht dat wordt weer gegeven aan de gebruiker als de code niet kan worden geverifieerd.|

### <a name="example"></a>Voorbeeld

Het volgende voor beeld `TechnicalProfile` wordt gebruikt voor het controleren van een code:

```xml
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor een voor beeld van het gebruik van een technisch profiel met een eenmalig wacht woord met aangepaste e-mail verificatie:

- Aangepaste e-mail verificatie in Azure Active Directory B2C ([Mailjet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md))

