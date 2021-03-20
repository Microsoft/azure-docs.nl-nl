---
title: Technische profielen voor Azure AD SSPR in aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Aangepaste beleids verwijzing voor technische profielen van Azure AD SSPR in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e6fcf956639d827a8654c5ee80e7cab8cadf930
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85383594"
---
# <a name="define-an-azure-ad-sspr-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Een technisch Azure AD SSPR-profiel definiëren in een Azure AD B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het controleren van een e-mail adres voor Self-service voor wachtwoord herstel (SSPR). Gebruik het technische profiel van Azure AD SSPR om een code te genereren en te verzenden naar een e-mail adres en controleer vervolgens de code. Het technische profiel van Azure AD SSPR kan ook een fout bericht retour neren. Het validatie-technische profiel valideert de door de gebruiker verschafte gegevens voordat de gebruikers traject doorgaat. Met het technische profiel voor validatie wordt een fout bericht weer gegeven op een zelf-bevestigingen pagina.

Dit technische profiel:

- Biedt geen interface om met de gebruiker te communiceren. In plaats daarvan wordt de gebruikers interface aangeroepen vanuit een [zelfbevestigend](self-asserted-technical-profile.md) technisch profiel of een [weer gave-besturings element](display-controls.md) als [technische profiel voor validatie](validation-technical-profile.md).
- Maakt gebruik van de Azure AD SSPR-service om een code te genereren en te verzenden naar een e-mail adres en vervolgens de code te controleren.  
- Hiermee wordt een e-mail adres gevalideerd via een verificatie code.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary` . Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C:

```
Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

In het volgende voor beeld wordt het technische profiel van Azure AD SSPR weer gegeven:

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-email"></a>E-mail verzenden

De eerste modus van dit technische profiel is om een code te genereren en deze te verzenden. De volgende opties kunnen worden geconfigureerd voor deze modus.

### <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die moeten worden verzonden naar Azure AD SSPR. U kunt de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in het technische profiel van SSPR.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| emailAddress | Ja | De id voor de gebruiker die eigenaar is van het e-mail adres. De `PartnerClaimType` eigenschap van de invoer claim moet worden ingesteld op `emailAddress` . |


Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar de Azure AD SSPR-service worden verzonden.

### <a name="output-claims"></a>Uitvoer claims

De provider van het Azure AD SSPR-protocol retourneert geen **OutputClaims**, dus het is niet nodig om uitvoer claims op te geven. U kunt echter claims toevoegen die niet worden geretourneerd door de provider van het Azure AD SSPR-protocol zolang u het kenmerk hebt ingesteld `DefaultValue` .

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | Moet **SendCode** zijn.  |

#### <a name="ui-elements"></a>UI-elementen

De volgende meta gegevens kunnen worden gebruikt voor het configureren van de fout berichten die worden weer gegeven bij het verzenden van een SMS-fout. De meta gegevens moeten worden geconfigureerd in het [zelfondertekende](self-asserted-technical-profile.md) technische profiel. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#azure-ad-sspr).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfInternalError | Nee | Fout bericht van gebruiker als er een interne fout is opgetreden op de server. |
| UserMessageIfThrottled| Nee | Gebruikers fout bericht als een aanvraag is beperkt.|


### <a name="example-send-an-email"></a>Voor beeld: een e-mail verzenden

In het volgende voor beeld ziet u een technisch profiel van Azure AD SSPR dat wordt gebruikt voor het verzenden van code via e-mail.

```XML
<TechnicalProfile Id="AadSspr-SendCode">
  <DisplayName>Send Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">SendCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>Code verifiëren

De tweede modus van dit technische profiel is om een code te controleren. De volgende opties kunnen worden geconfigureerd voor deze modus.

### <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die moeten worden verzonden naar Azure AD SSPR. U kunt de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in het technische profiel van SSPR.

| ClaimReferenceId | Vereist | Beschrijving |
| --------- | -------- | ----------- | ----------- |
| emailAddress| Ja | Hetzelfde e-mail adres dat eerder is gebruikt om een code te verzenden. Het wordt ook gebruikt om een e-mail verificatie sessie te vinden. De `PartnerClaimType` eigenschap van de invoer claim moet worden ingesteld op `emailAddress` .|
| verificationCode  | Ja | De verificatie code van de gebruiker die moet worden geverifieerd. De `PartnerClaimType` eigenschap van de invoer claim moet worden ingesteld op `verificationCode` . |

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat de Azure AD SSPR-service wordt aangeroepen.

### <a name="output-claims"></a>Uitvoer claims

De provider van het Azure AD SSPR-protocol retourneert geen **OutputClaims**, dus het is niet nodig om uitvoer claims op te geven. U kunt echter claims toevoegen die niet worden geretourneerd door de provider van het Azure AD SSPR-protocol zolang u het kenmerk hebt ingesteld `DefaultValue` .

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Ja | Moet **VerifyCode** zijn |

#### <a name="ui-elements"></a>UI-elementen

De volgende meta gegevens kunnen worden gebruikt voor het configureren van de fout berichten die worden weer gegeven wanneer de code verificatie mislukt. De meta gegevens moeten worden geconfigureerd in het [zelfondertekende](self-asserted-technical-profile.md) technische profiel. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#azure-ad-sspr).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
|UserMessageIfChallengeExpired | Het bericht dat wordt weer gegeven aan de gebruiker als de sessie met de code verificatie is verlopen. De code is verlopen of de code is nooit voor een bepaalde id gegenereerd.|
|UserMessageIfInternalError | Fout bericht van gebruiker als er een interne fout is opgetreden op de server.|
|UserMessageIfThrottled | Gebruikers fout bericht als een aanvraag is beperkt.|
|UserMessageIfVerificationFailedNoRetry | Het bericht dat wordt weer gegeven aan de gebruiker als deze een ongeldige code heeft verstrekt en de gebruiker de juiste code niet mag opgeven.|
|UserMessageIfVerificationFailedRetryAllowed | Het bericht dat wordt weer gegeven aan de gebruiker als deze een ongeldige code heeft verstrekt en de gebruiker de juiste code kan opgeven.|

### <a name="example-verify-a-code"></a>Voor beeld: een code verifiëren

In het volgende voor beeld ziet u een technisch profiel van Azure AD SSPR dat wordt gebruikt om de code te controleren.

```XML
<TechnicalProfile Id="AadSspr-VerifyCode">
  <DisplayName>Verify Code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AadSsprProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="Operation">VerifyCode</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="verificationCode" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress"/>
  </InputClaims>
</TechnicalProfile>
```