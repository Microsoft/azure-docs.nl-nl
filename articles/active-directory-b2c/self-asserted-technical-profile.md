---
title: Een zelf-bevestigd technisch profiel in een aangepast beleid definiëren
titleSuffix: Azure AD B2C
description: Definieer een zelf-bevestigd technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 14195ad4638c724cf0c8dd46945a0da79ec0e4ec
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509697"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een zelf-bevestigd technisch profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Alle interacties in Azure Active Directory B2C (Azure AD B2C) waar de gebruiker wordt verwacht om invoer te bieden, zijn zelfondertekende technische profielen. Bijvoorbeeld een aanmeldings pagina, een aanmeldings pagina of een pagina voor het opnieuw instellen van een wacht woord.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary` . Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C, voor zelfbevestigend: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

In het volgende voor beeld ziet u een zelf-bevestigd technisch profiel voor het aanmelden via e-mail:

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>Invoer claims

In een zelf-bevestigd technisch profiel kunt u de **InputClaims** -en **InputClaimsTransformations** -elementen gebruiken om de waarde van de claims die worden weer gegeven op de niet-bevestigde pagina (claims weer geven), vooraf in te vullen. In het beleid voor het bewerken van profielen leest de gebruikers traject eerst het gebruikers profiel uit de Azure AD B2C Directory-service. vervolgens stelt het zelfondertekende technische profiel de invoer claims in met de gebruikers gegevens die zijn opgeslagen in het gebruikers profiel. Deze claims worden verzameld uit het gebruikers profiel en vervolgens weer gegeven aan de gebruiker die de bestaande gegevens kan bewerken.

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>Claims weer geven

De functie voor het weer geven van claims is momenteel beschikbaar als **Preview-versie**.

Het **DisplayClaims** -element bevat een lijst met claims die op het scherm moeten worden weer gegeven voor het verzamelen van gegevens van de gebruiker. Als u de waarden van weer gave claims vooraf wilt invullen, gebruikt u de invoer claims die eerder zijn beschreven. Het element kan ook een standaard waarde bevatten.

De volg orde van de claims in **DisplayClaims** geeft aan in welke volg orde Azure AD B2C de claims op het scherm worden weer gegeven. Als u wilt afdwingen dat de gebruiker een waarde voor een specifieke claim opgeeft, stelt u het **vereiste** kenmerk van het **DisplayClaim** -element in op `true` .

Het element **claim** type in de **DisplayClaims** -verzameling moet het **UserInputType** -element instellen op elk type gebruikers invoer dat door Azure AD B2C wordt ondersteund. Bijvoorbeeld `TextBox` of `DropdownSingleSelect`.

### <a name="add-a-reference-to-a-displaycontrol"></a>Een verwijzing naar een DisplayControl toevoegen

In de claim verzameling weer geven kunt [u een verwijzing naar een weer gave](display-controls.md) die u hebt gemaakt, toevoegen. Een weergave besturings element is een gebruikers interface-element dat speciale functionaliteit heeft en samenwerkt met de Azure AD B2C back-end-service. Hiermee kan de gebruiker acties uitvoeren op de pagina die een validatie technische profiel aan de back-end aanroept. U kunt bijvoorbeeld een e-mail adres, telefoon nummer of loyaliteits nummer van de klant controleren.

In het volgende voor beeld `TechnicalProfile` ziet u hoe claims worden weer gegeven met besturings elementen voor weer gave.

* De eerste weergave claim geeft een verwijzing naar het `emailVerificationControl` besturings element weer geven, waarmee het e-mail adres wordt verzameld en geverifieerd.
* De vijfde weergave claim verwijst naar het `phoneVerificationControl` Weergave besturings element waarmee een telefoon nummer wordt verzameld en geverifieerd.
* De andere weer gave claims worden ClaimTypes van de gebruiker verzameld.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

Zoals vermeld, kan een weergave claim met een verwijzing naar een weergave besturings element een eigen validatie uitvoeren, bijvoorbeeld het e-mail adres controleren. Daarnaast ondersteunt de zelfbevestigende pagina het gebruik van een validatie technische profiel voor het valideren van de volledige pagina, inclusief eventuele gebruikers invoer (claim typen of besturings elementen weer geven), voordat u verdergaat met de volgende Orchestration-stap.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>Gebruik zorgvuldig combi neren van claims en uitvoer claims

Als u een of meer **DisplayClaim** -elementen opgeeft in een zelf-bevestigd technisch profiel, moet u een DisplayClaim gebruiken voor *elke* claim die u op het scherm wilt weer geven en van de gebruiker wilt verzamelen. Er worden geen uitvoer claims weer gegeven met een niet-bevestigd technisch profiel dat ten minste één weergave claim bevat.

Bekijk het volgende voor beeld waarin een `age` claim wordt gedefinieerd als een **uitvoer** claim in een basis beleid. Voordat u de weer gave claims aan het zelfondertekende technische profiel toevoegt, `age` wordt de claim op het scherm weer gegeven voor het verzamelen van gegevens van de gebruiker:

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

Als een blad beleid dat die basis overneemt, wordt opgegeven `officeNumber` als een **weergave** claim:

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

De `age` claim in het basis beleid wordt niet meer op het scherm weer gegeven voor de gebruiker. Dit is het effectief ' verborgen '. Als u de claim wilt weer geven `age` en de leeftijds waarde van de gebruiker wilt verzamelen, moet u een `age` **DisplayClaim** toevoegen.

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die moeten worden geretourneerd naar de volgende Orchestration-stap. Het kenmerk **DefaultValue** wordt alleen gebruikt als de claim nooit is ingesteld. Als deze in een vorige Orchestration-stap is ingesteld, wordt de standaard waarde niet van kracht, zelfs niet als de gebruiker de waarde leeg laat. Als u het gebruik van een standaard waarde wilt forceren, stelt u het kenmerk **AlwaysUseDefaultValue** in op `true` .

Uit veiligheids overwegingen is een wachtwoord claim waarde ( `UserInputType` ingesteld op `Password` ) alleen beschikbaar voor de validatie technische profielen van het zelfondertekende technische profiel. U kunt de wachtwoord claim niet gebruiken in de volgende Orchestration-stappen. 

> [!NOTE]
> In eerdere versies van het Framework voor identiteits ervaring (IEF) werden uitvoer claims gebruikt voor het verzamelen van gegevens van de gebruiker. Als u gegevens wilt verzamelen van de gebruiker, gebruikt u in plaats daarvan een **DisplayClaims** -verzameling.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

### <a name="when-you-should-use-output-claims"></a>Wanneer u uitvoer claims moet gebruiken

In een niet-bevestigd technisch profiel retourneert de claim verzameling output de claims naar de volgende Orchestration-stap.

Uitvoer claims gebruiken wanneer:

- **Claims worden uitgevoerd door trans formatie van uitvoer claims**.
- Het **instellen van een standaard waarde in een uitvoer claim** zonder het verzamelen van gegevens van de gebruiker of het retour neren van de gegevens uit het technische profiel voor validatie. Het `LocalAccountSignUpWithLogonEmail` zelfondertekende technische profiel stelt de **uitgevoerde-SelfAsserted-invoer** claim in op `true` .
- **Een technisch profiel voor validatie retourneert de uitvoer claims** -uw technische profiel kan een validatie technische profiel aanroepen dat enkele claims retourneert. U kunt de claims inbellen en terugsturen naar de volgende indelings stappen in de gebruikers reis. Als u zich bijvoorbeeld aanmeldt met een lokaal account, roept het zelfondertekende technische profiel met de naam het technische profiel voor validatie op met de naam `SelfAsserted-LocalAccountSignin-Email` `login-NonInteractive` . Dit technische profiel valideert de gebruikers referenties en retourneert ook het gebruikers profiel. Zoals ' userPrincipalName ', ' displayName ', ' naam ' en ' Achternaam '.
- **Een besturings element voor weer gave retourneert de uitvoer claims** -uw technische profiel bevat mogelijk een verwijzing naar een [besturings element voor weer gave](display-controls.md). Het besturings element weer gave retourneert enkele claims, zoals het geverifieerde e-mail adres. U kunt de claims inbellen en terugsturen naar de volgende indelings stappen in de gebruikers reis. De functie voor het weer geven van besturings elementen is momenteel beschikbaar als **Preview-versie**.

In het volgende voor beeld wordt het gebruik van een zelf-bevestigd technisch profiel gedemonstreerd dat zowel claims als uitvoer claims gebruikt.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="output-claims-sign-up-or-sign-in-page"></a>Registratie van uitvoer claims of aanmeldings pagina

Houd bij een gecombineerde registratie en aanmeldings pagina rekening met het volgende wanneer u een [DataUri](contentdefinitions.md#datauri) element van een inhouds definitie gebruikt. Hiermee geeft u een `unifiedssp` of `unifiedssd` pagina Type op:

- Alleen de claims voor gebruikers naam en wacht woord worden weer gegeven.
- De eerste twee uitvoer claims moeten de gebruikers naam en het wacht woord zijn (in deze volg orde). 
- Andere claims worden niet weer gegeven. voor deze claims moet u het `defaultValue` technische profiel voor validatie van claim formulieren instellen of aanroepen. 

## <a name="persist-claims"></a>Claims persistent maken

Het element PersistedClaims wordt niet gebruikt. Het zelfondertekende technische profiel bevat niet de gegevens die naar Azure AD B2C worden bewaard. In plaats daarvan wordt een aanroep uitgevoerd naar een technische profiel voor validatie dat verantwoordelijk is voor het persistent maken van de gegevens. Het aanmeldings beleid maakt bijvoorbeeld gebruik van het `LocalAccountSignUpWithLogonEmail` zelfondertekende technische profiel om het nieuwe gebruikers profiel te verzamelen. `LocalAccountSignUpWithLogonEmail`Met het technische profiel wordt het technische profiel voor validatie aangeroepen om het account te maken in azure AD B2C.

## <a name="validation-technical-profiles"></a>Validatie van technische profielen

Een validatie technische profiel wordt gebruikt voor het valideren van een aantal of alle uitvoer claims van het referentie-technische profiel. De invoer claims van het technische profiel voor validatie moeten worden weer gegeven in de uitvoer claims van het zelfondertekende technische profiel. Het validatie-technische profiel valideert de gebruikers invoer en kan een fout naar de gebruiker retour neren.

Het technische profiel voor validatie kan elk technisch profiel in het beleid zijn, zoals [Azure Active Directory](active-directory-technical-profile.md) of een [rest API](restful-technical-profile.md) technische profielen. In het vorige voor beeld wordt `LocalAccountSignUpWithLogonEmail` met het technische profiel gecontroleerd of de signinName niet in de directory bestaat. Als dat niet het geval is, maakt het technische profiel voor validatie een lokaal account en retourneert het objectId, authenticationSource, newUser. Het `SelfAsserted-LocalAccountSignin-Email` technische profiel roept het `login-NonInteractive` validatie technische profiel aan om de gebruikers referenties te valideren.

U kunt ook een REST API technisch profiel aanroepen met uw bedrijfs logica, invoer claims overschrijven of gebruikers gegevens verrijken door verder te integreren met zakelijke line-of-business-toepassingen. Zie voor meer informatie [validatie technische profiel](validation-technical-profile.md)

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| setting. operatingMode <sup>1</sup>| No | Voor een aanmeldings pagina bepaalt deze eigenschap het gedrag van het veld gebruikers naam, zoals invoer validatie en fout berichten. Verwachte waarden: `Username` of `Email` .  |
| AllowGenerationOfClaimsWithNullValues| No| Er kan een claim met een null-waarde worden gegenereerd. Zo wordt in een case-gebruiker geen selectie vakje geselecteerd.|
| ContentDefinitionReferenceId | Yes | De id van de [inhouds definitie](contentdefinitions.md) die aan dit technische profiel is gekoppeld. |
| EnforceEmailVerification | No | Voor de registratie of het profiel bewerken, wordt een e-mail verificatie afgedwongen. Mogelijke waarden: `true` (standaard) of `false` . |
| setting. retryLimit | No | Hiermee bepaalt u het aantal keren dat een gebruiker de gegevens kan opgeven die worden gecontroleerd op basis van het technische profiel voor validatie. Bijvoorbeeld: een gebruiker probeert zich aan te melden met een account dat al bestaat en het probeert tot de limiet is bereikt.
| SignUpTarget <sup>1</sup>| No | De Exchange-doel-id voor de aanmelding. Wanneer de gebruiker op de knop registratie klikt, wordt Azure AD B2C de opgegeven Exchange-id uitgevoerd. |
| setting. showCancelButton | No | Hiermee wordt de knop Annuleren weer gegeven. Mogelijke waarden: `true` (standaard) of `false` |
| setting. showContinueButton | No | Hiermee wordt de knop door gaan weer gegeven. Mogelijke waarden: `true` (standaard) of `false` |
| setting. showSignupLink <sup>2</sup>| No | Hiermee wordt de knop registratie weer gegeven. Mogelijke waarden: `true` (standaard) of `false` |
| setting. forgotPasswordLinkLocation <sup>2</sup>| No| Hiermee wordt de verg eten wachtwoord koppeling weer gegeven. Mogelijke waarden: `AfterInput` (standaard) de koppeling wordt weer gegeven aan de onderkant van de pagina of `None` de koppeling met verg eten wacht woord wordt verwijderd.|
| setting. enableRememberMe <sup>2</sup>| No| Hiermee wordt het selectie vakje [aangemeld blijven](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) weer gegeven. Mogelijke waarden: `true` , of `false` (standaard). |
| setting. inputVerificationDelayTimeInMilliseconds <sup>3</sup>| No| Verbetert de gebruikers ervaring door te wachten tot de gebruiker stopt met typen en vervolgens de waarde te valideren. Standaard waarde 2000 milliseconden. |
| IncludeClaimResolvingInClaimsHandling  | No | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true` , of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true` . |

Opmerkingen:
1. Beschikbaar voor inhouds definitie [DataUri](contentdefinitions.md#datauri) type van `unifiedssp` of `unifiedssd` .
1. Beschikbaar voor inhouds definitie [DataUri](contentdefinitions.md#datauri) type van `unifiedssp` of `unifiedssd` . [Versie](page-layout.md) 1.1.0 en hoger van de pagina-indeling.
1. Beschikbaar voor de versie 1.2.0 en hoger van de [pagina-indeling](page-layout.md) .

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het element **CryptographicKeys** wordt niet gebruikt.
