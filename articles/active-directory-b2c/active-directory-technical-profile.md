---
title: Een technische Azure AD-profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een Azure Active Directory technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 49d8e83c158cd14357a74b9dde4af1daba26dc36
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109110"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een Azure Active Directory technisch profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor het Azure Active Directory gebruikers beheer. In dit artikel worden de specifieke specificaties beschreven van een technisch profiel voor interactie met een claim provider die ondersteuning biedt voor dit gestandaardiseerde protocol.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary` . Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

De volgende technische profielen van het [standaard beleid voor Starter Packs](custom-policy-get-started.md#custom-policy-starter-pack) van Azure AD bevatten het **Aad-algemene** technische profiel. De technische profielen van Azure AD geven niet het protocol op omdat het protocol is geconfigureerd in het **Aad-algemeen** Technical profiel:
 
- **Aad-UserReadUsingAlternativeSecurityId** en **Aad-UserReadUsingAlternativeSecurityId-fout** : Zoek een sociaal account in de Directory.
- **Aad-UserWriteUsingAlternativeSecurityId** : een nieuw sociaal account maken.
- **Aad-UserReadUsingEmailAddress** : Zoek een lokaal account in de Directory.
- **Aad-UserWriteUsingLogonEmail** : een nieuw lokaal account maken.
- **Aad-UserWritePasswordUsingObjectId** : een wacht woord van een lokaal account bijwerken.
- **Aad-UserWriteProfileUsingObjectId** : een gebruikers profiel van een lokaal of sociaal account bijwerken.
- **Aad-UserReadUsingObjectId** : een gebruikers profiel van een lokaal of sociaal account lezen.
- **Aad-UserWritePhoneNumberUsingObjectId** : het MFA-telefoon nummer van een lokaal of sociaal account schrijven

In het volgende voor beeld wordt het **Aad-algemene** technische profiel weer gegeven:

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="inputclaims"></a>InputClaims

Het InputClaims-element bevat een claim waarmee een account in de Directory wordt gezocht of een nieuwe wordt gemaakt. Er moet precies één input claim-element in de claim verzameling voor invoer zijn voor alle technische profielen van Azure AD. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in Azure Active Directory.

Als u een bestaand gebruikers account wilt lezen, bijwerken of verwijderen, is de invoer claim een sleutel waarmee het account in azure AD-adres lijst op unieke wijze wordt geïdentificeerd. Bijvoorbeeld **objectId**, **userPrincipalName**, **signInNames. emailAddress**, **signInNames. username** of **alternativeSecurityId**. 

Als u een nieuw gebruikers account wilt maken, is de invoer claim een sleutel waarmee een lokaal of federatief account op unieke wijze wordt geïdentificeerd. Bijvoorbeeld lokale account: **signInNames. emailAddress** of **signInNames. username**. Voor een federatief account: **alternativeSecurityId**.

Het [InputClaimsTransformations](technicalprofiles.md#inputclaimstransformations) -element kan een verzameling trans formatie-elementen voor invoer claims bevatten die worden gebruikt voor het wijzigen van de invoer claim of het genereren van een nieuwe.

## <a name="outputclaims"></a>OutputClaims

Het **OutputClaims** -element bevat een lijst met claims die worden geretourneerd door het technische profiel van Azure AD. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in Azure Active Directory. U kunt ook claims toevoegen die niet worden geretourneerd door de Azure Active Directory, zolang u het kenmerk hebt ingesteld `DefaultValue` .

Het [OutputClaimsTransformations](technicalprofiles.md#outputclaimstransformations) -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

Het **Aad-UserWriteUsingLogonEmail-** technische profiel maakt bijvoorbeeld een lokaal account en retourneert de volgende claims:

- **objectId**, een id van het nieuwe account
- **newuser**, die aangeeft of de gebruiker nieuw is
- **authenticationSource**, waarbij verificatie wordt ingesteld op `localAccountAuthentication`
- **userPrincipalName**: de User Principal name van het nieuwe account
- **signInNames. emailAddress**, de aanmeldings naam van het account, vergelijkbaar met de invoer claim van de **e-mail**

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

Het **PersistedClaims** -element bevat alle waarden die door Azure AD moeten worden bewaard met mogelijke toewijzings informatie tussen een claim type dat al is gedefinieerd in de sectie [ClaimsSchema](claimsschema.md) in het beleid en de naam van het Azure AD-kenmerk.

Het **Aad-UserWriteUsingLogonEmail-** technische profiel, waarmee een nieuw lokaal account wordt gemaakt, houdt rekening met de volgende claims:

```xml
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

De naam van de claim is de naam van het kenmerk Azure AD, tenzij het kenmerk **PartnerClaimType** is opgegeven, dat de naam van het Azure AD-kenmerk bevat.

## <a name="requirements-of-an-operation"></a>Vereisten van een bewerking

- Er moet precies één **input claim** -element aanwezig zijn in de claim verzameling voor alle technische profielen van Azure AD.
- Het [artikel kenmerken van gebruikers profielen](user-profile-attributes.md) beschrijft de ondersteunde Azure AD B2C gebruikers profiel kenmerken die u kunt gebruiken in de invoer claims, uitvoer claims en permanente claims. 
- Als de bewerking is `Write` of `DeleteClaims` , moet deze ook worden weer gegeven in een **PersistedClaims** -element.
- De waarde van de **userPrincipalName** -claim moet de indeling van hebben `user@tenant.onmicrosoft.com` .
- De **DisplayName** -claim is vereist en kan geen lege teken reeks zijn.

## <a name="azure-ad-technical-provider-operations"></a>Bewerkingen voor de technische provider van Azure AD

### <a name="read"></a>Lezen

Met de **Lees** bewerking worden gegevens over één gebruikers account gelezen. Met het volgende technische profiel worden gegevens over een gebruikers account gelezen met de objectId van de gebruiker:

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Schrijven

Met de **Schrijf** bewerking wordt één gebruikers account gemaakt of bijgewerkt. Het volgende technische profiel maakt een nieuw sociaal account:

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

Met de bewerking **DeleteClaims** wordt de informatie uit een verstrekte lijst met claims gewist. Met het volgende technische profiel worden claims verwijderd:

```xml
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Met de bewerking **DeleteClaimsPrincipal** wordt één gebruikers account uit de map verwijderd. Met het volgende technische profiel wordt een gebruikers account uit de Directory verwijderd met behulp van de user principal name:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Met het volgende technische profiel wordt een sociaal gebruikers account verwijderd met behulp van **alternativeSecurityId**:

```xml
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bewerking | Yes | De bewerking die moet worden uitgevoerd. Mogelijke waarden: `Read` , `Write` , `DeleteClaims` of `DeleteClaimsPrincipal` . |
| RaiseErrorIfClaimsPrincipalDoesNotExist | No | Een fout veroorzaken als het gebruikers object niet in de map bestaat. Mogelijke waarden: `true` of `false` . |
| RaiseErrorIfClaimsPrincipalAlreadyExists | No | Er wordt een fout gegenereerd als het gebruikers object al bestaat. Mogelijke waarden: `true` of `false` .|
| ApplicationObjectId | No | De object-id van de toepassing voor extensie kenmerken. Waarde: ObjectId van een toepassing. Zie voor meer informatie [aangepaste kenmerken gebruiken in een aangepast profiel beleid bewerken](custom-policy-custom-attributes.md). |
| ClientId | No | De client-id voor toegang tot de Tenant als derde partij. Zie voor meer informatie [aangepaste kenmerken gebruiken in een aangepast profiel beleid bewerken](custom-policy-custom-attributes.md) |
| IncludeClaimResolvingInClaimsHandling  | No | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true` , of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true` . |

### <a name="ui-elements"></a>UI-elementen
 
De volgende instellingen kunnen worden gebruikt voor het configureren van het fout bericht dat wordt weer gegeven bij een fout. De meta gegevens moeten worden geconfigureerd in het [zelfondertekende](self-asserted-technical-profile.md) technische profiel. De fout berichten kunnen worden [gelokaliseerd](localization.md).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalAlreadyExists | No | Als er een fout is opgetreden (zie beschrijving van het RaiseErrorIfClaimsPrincipalAlreadyExists-kenmerk), geeft u het bericht op dat moet worden weer gegeven voor de gebruiker als het gebruikers object al bestaat. |
| UserMessageIfClaimsPrincipalDoesNotExist | No | Als er een fout is opgetreden (Zie de beschrijving van het kenmerk RaiseErrorIfClaimsPrincipalDoesNotExist), geeft u het bericht op dat moet worden weer gegeven voor de gebruiker als het gebruikers object niet bestaat. |


## <a name="next-steps"></a>Volgende stappen

Zie het volgende artikel voor een voor beeld van het gebruik van het technische profiel van Azure AD:

- [Claims toevoegen en gebruikers invoer aanpassen met aangepaste beleids regels in Azure Active Directory B2C](configure-user-input.md)














