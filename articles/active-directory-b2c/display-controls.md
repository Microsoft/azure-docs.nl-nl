---
title: Verwijzing naar besturings element weer geven
titleSuffix: Azure AD B2C
description: Verwijzing voor de besturings elementen Azure AD B2C weer geven. Gebruik weer gave-besturings elementen voor het aanpassen van de gebruikers trajecten die zijn gedefinieerd in uw aangepaste beleids regels.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 441a77823c77305e567e9e1436715bc51ca48c11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97387051"
---
# <a name="display-controls"></a>Besturings elementen weer geven

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een **Weergave besturings element** is een gebruikers interface-element met speciale functionaliteit en interactie met de Azure Active Directory B2C (Azure AD B2C) back-end-service. Hiermee kan de gebruiker acties uitvoeren op de pagina die een [validatie technische profiel](validation-technical-profile.md) aan de back-end aanroept. Besturings elementen voor weer gave worden op de pagina weer gegeven en er wordt verwezen door een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md).

In de volgende afbeelding ziet u een zelf-bevestigde aanmeldings pagina met twee besturings elementen voor weer gave waarmee een primair en secundair e-mail adres wordt gevalideerd.

![Voor beeld van weer gave-besturings element](media/display-controls/display-control-email.png)

## <a name="prerequisites"></a>Vereisten

 In de sectie [meta gegevens](self-asserted-technical-profile.md#metadata) van een [zelf-bevestigde technische profiel](self-asserted-technical-profile.md)moet de [ContentDefinition](contentdefinitions.md) waarnaar wordt verwezen, zijn `DataUri` ingesteld op pagina-contract versie 2.0.0 of hoger. Bijvoorbeeld:

```xml
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>Besturings elementen voor weer gave definiëren

Het element **DisplayControl** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Een id die wordt gebruikt voor het weergave besturings element. Hiernaar kan worden [verwezen](#referencing-display-controls). |
| UserInterfaceControlType | Ja | Het type van het weergave besturings element. Momenteel wordt de [VerificationControl](display-control-verification.md) ondersteund |

Het element **DisplayControl** bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **InputClaims** worden gebruikt om vooraf de waarde in te vullen van de claims die moeten worden verzameld van de gebruiker. Zie het element [InputClaims](technicalprofiles.md#input-claims) voor meer informatie. |
| DisplayClaims | 0:1 | **DisplayClaims** worden gebruikt om claims weer te geven die van de gebruiker moeten worden verzameld. Zie het element [DisplayClaim](technicalprofiles.md#displayclaim) voor meer informatie.|
| OutputClaims | 0:1 | **OutputClaims** worden gebruikt om claims weer te geven die tijdelijk moeten worden opgeslagen voor dit **besturings element**. Zie het element [OutputClaims](technicalprofiles.md#output-claims) voor meer informatie.|
| Acties | 0:1 | **Acties** worden gebruikt om de technische profielen voor validatie weer te geven die moeten worden aangeroepen voor gebruikers acties die worden uitgevoerd op de front-end. |

### <a name="input-claims"></a>Invoer claims

In een besturings element voor weer gave kunt u **InputClaims** -elementen gebruiken om de waarde van claims die van de gebruiker op de pagina moeten worden verzameld vooraf in te vullen. Alle **InputClaimsTransformations** kunnen worden gedefinieerd in het door uzelf beweringen technische profiel dat verwijst naar dit weergave besturings element.

In het volgende voor beeld wordt het e-mail adres dat is gecontroleerd met het adres dat al aanwezig is, vooraf ingevuld.

```xml
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>Claims weer geven

Elk type weergave besturings element vereist een andere set weer gave claims, [uitvoer claims](#output-claims)en [acties](#display-control-actions) die moeten worden uitgevoerd.

Net als de **weer gave-claims** die in een niet [-bevestigd technisch profiel](self-asserted-technical-profile.md#display-claims)zijn gedefinieerd, vertegenwoordigen de weer gave claims de claims die moeten worden verzameld van de gebruiker in het besturings element voor weer gave. Voor het element **claim** type waarnaar wordt verwezen, moet het **UserInputType** -element worden opgegeven voor een gebruikers invoer die wordt ondersteund door Azure AD B2C, zoals `TextBox` of `DropdownSingleSelect` . Als een aanvraag waarde voor weer geven vereist is voor een **actie**, stelt u het **vereiste** kenmerk in op `true` om te zorgen dat de gebruiker een waarde voor die specifieke weergave claim opgeeft.

Bepaalde weergave claims zijn vereist voor bepaalde typen weergave besturings elementen. **VerificationCode** is bijvoorbeeld vereist voor het besturings element weer gave van het type **VerificationControl**. Gebruik het kenmerk **ControlClaimType** om op te geven welke DisplayClaim is aangewezen voor die vereiste claim. Bijvoorbeeld:

```xml
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>Uitvoer claims

De **uitvoer claims** van een besturings element voor weer gave worden niet naar de volgende Orchestration-stap verzonden. Ze worden tijdelijk alleen opgeslagen voor de huidige sessie voor weergave beheer. Deze tijdelijke claims kunnen worden gedeeld tussen de verschillende acties van hetzelfde weergave besturings element.

Als u de uitvoer claims wilt samen stellen aan de volgende indelings stap, gebruikt u de **OutputClaims** van het daad werkelijke zelf-beweringen technische profiel dat verwijst naar dit weergave besturings element.

### <a name="display-control-actions"></a>Besturings acties weer geven

De **acties** van een weergave besturings element zijn procedures die zich voordoen in de Azure AD B2C back-end wanneer een gebruiker een bepaalde actie uitvoert aan de client zijde (de browser). Bijvoorbeeld de validaties die moeten worden uitgevoerd wanneer de gebruiker een knop op de pagina selecteert.

Een actie definieert een lijst met **technische profielen voor validatie**. Ze worden gebruikt voor het valideren van enkele of alle weer gave claims van het besturings element voor weer gave. Het validatie-technische profiel valideert de gebruikers invoer en retourneert mogelijk een fout naar de gebruiker. U kunt **ContinueOnError**, **ContinueOnSuccess** en **voor waarden** in de actie voor het weer geven van besturings elementen gebruiken die vergelijkbaar zijn met de manier waarop ze worden gebruikt bij [validatie van technische profielen](validation-technical-profile.md) in een zelf bevestigd technisch profiel.

#### <a name="actions"></a>Acties

Het element **Actions** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Actie | 1: n | Lijst met acties die moeten worden uitgevoerd. |

#### <a name="action"></a>Bewerking

Het element **Action** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Ja | Het type bewerking. Mogelijke waarden: `SendCode` of `VerifyCode` . De `SendCode` waarde verzendt een code naar de gebruiker. Deze actie kan twee technische profielen voor validatie bevatten: één om een code te genereren en één om deze te verzenden. De `VerifyCode` waarde controleert de code die de gebruiker in het tekstvak invoer heeft getypt. |

Het element **Action** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| ValidationClaimsExchange | 1:1 | De id's van de technische profielen die worden gebruikt voor het valideren van enkele of alle weergave claims van het referentie-technische profiel. Alle invoer claims van het technische profiel waarnaar wordt verwezen, moeten worden weer gegeven in de weer gave claims van het technische profiel waarnaar wordt verwezen. |

#### <a name="validationclaimsexchange"></a>ValidationClaimsExchange

Het element **ValidationClaimsExchange** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Een technisch profiel dat moet worden gebruikt voor het valideren van een aantal of alle weer gave claims van het referentie-technische profiel. |

Het **ValidationTechnicalProfile** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel is al gedefinieerd in het beleid of het bovenliggende beleid. |
|ContinueOnError|Nee| Geeft aan of validatie van de volgende validatie technische profielen moet worden voortgezet als het technische profiel voor validatie een fout veroorzaakt. Mogelijke waarden: `true` of `false` (de standaard instelling voor de verwerking van verdere validatie profielen wordt gestopt en er wordt een fout geretourneerd). |
|ContinueOnSuccess | Nee | Geeft aan of validatie van volgende validatie profielen moet worden voortgezet als het technische profiel voor de validatie is geslaagd. Mogelijke waarden: `true` of `false` . De standaard waarde is `true` , wat inhoudt dat de verwerking van verdere validatie profielen zal worden voortgezet. |

Het element **ValidationTechnicalProfile** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Voor waarden | 0:1 | Een lijst met voor waarden waaraan moet worden voldaan om het technische profiel voor validatie te kunnen uitvoeren. |

Het element **voor waarde** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| `Type` | Ja | Het type controle of query dat moet worden uitgevoerd voor de voor waarde. Mogelijke waarden: `ClaimsExist` of `ClaimEquals` . `ClaimsExist` Hiermee geeft u op dat de acties moeten worden uitgevoerd als de opgegeven claims bestaan in de huidige claimset van de gebruiker. `ClaimEquals` Hiermee geeft u op dat de acties moeten worden uitgevoerd als de opgegeven claim bestaat en de waarde ervan gelijk is aan de opgegeven waarde. |
| `ExecuteActionsIf` | Ja | Hiermee wordt aangegeven of de acties in de voor waarde moeten worden uitgevoerd als de test True of False is. |

Het element **voor waarde** bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Waarde | 1: n | De gegevens die worden gebruikt door de controle. Als het type van deze controle is `ClaimsExist` , geeft dit veld een ClaimTypeReferenceId op die moet worden opgevraagd. Als het type controle is `ClaimEquals` , specificeert dit veld een ClaimTypeReferenceId om op te vragen. Geef de waarde op die moet worden ingecheckt in een ander element value.|
| Bewerking | 1:1 | De actie die moet worden uitgevoerd als de voor waarde wordt gecontroleerd binnen een Orchestration-stap. De waarde van de **actie** wordt ingesteld op `SkipThisValidationTechnicalProfile` , waarmee wordt aangegeven dat het bijbehorende technische profiel voor validatie niet moet worden uitgevoerd. |

In het volgende voor beeld wordt het e-mail adres verzonden en geverifieerd met behulp van het [technische profiel van Azure AD SSPR](aad-sspr-technical-profile.md).

```xml
<DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
  <InputClaims></InputClaims>
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
  </DisplayClaims>
  <OutputClaims></OutputClaims>
  <Actions>
    <Action Id="SendCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendCode" />
      </ValidationClaimsExchange>
    </Action>
    <Action Id="VerifyCode">
      <ValidationClaimsExchange>
        <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-VerifyCode" />
      </ValidationClaimsExchange>
    </Action>
  </Actions>
</DisplayControl>
```

In het volgende voor beeld wordt een code verzonden via e-mail of SMS op basis van de selectie van de **mfaType** claim met de voor waarden van de gebruiker.

```xml
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>Verwijzen naar weergave besturings elementen

Er wordt verwezen naar weer gave-besturings elementen in de [weer gave claims](self-asserted-technical-profile.md#display-claims) van het [zelf-beweringen technische profiel](self-asserted-technical-profile.md).

Bijvoorbeeld:

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```

## <a name="next-steps"></a>Volgende stappen

Zie voor voor beelden van het gebruik van weergave besturings element: 

- [Aangepaste e-mail verificatie met Mailjet](custom-email-mailjet.md)
- [Aangepaste e-mail verificatie met SendGrid](custom-email-sendgrid.md)
