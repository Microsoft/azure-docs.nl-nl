---
title: Een OpenID Connect Connect Technical-profiel in een aangepast beleid definiëren
titleSuffix: Azure AD B2C
description: Definieer een OpenID Connect Connect Technical-profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8273d4bbb0b58a256521cf11cacf6d1fed67e10d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345113"
---
# <a name="define-an-openid-connect-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een OpenID Connect Connect Technical-profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor de [OpenID Connect Connect](https://openid.net/2015/04/17/openid-connect-certification-program/) protocol-ID-provider. OpenID Connect Connect 1,0 definieert een Identity Layer boven op OAuth 2,0 en vertegenwoordigt de status van de kunst in moderne verificatie protocollen. Met een OpenID Connect Connect Technical-profiel kunt u communiceren met een OpenID connect-verbinding op basis van een id-provider, zoals Azure AD. Met federeren met een id-provider kunnen gebruikers zich aanmelden met hun bestaande sociale of bedrijfs identiteiten.

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `OpenIdConnect` . Het protocol voor het technische profiel **MSA-OIDC** is bijvoorbeeld `OpenIdConnect` :

```xml
<TechnicalProfile Id="MSA-OIDC">
  <DisplayName>Microsoft Account</DisplayName>
  <Protocol Name="OpenIdConnect" />
  ...
```

## <a name="input-claims"></a>Invoer claims

De **InputClaims** -en **InputClaimsTransformations** -elementen zijn niet vereist. Maar u wilt mogelijk extra para meters naar uw ID-provider verzenden. In het volgende voor beeld wordt de **domain_hint** query teken reeks parameter met de waarde van `contoso.com` aan de autorisatie aanvraag toegevoegd.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die zijn geretourneerd door de OpenID Connect Connect-ID-provider. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de ID-provider. U kunt ook claims toevoegen die niet worden geretourneerd door de ID-provider, zolang u het kenmerk hebt ingesteld `DefaultValue` .

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

In het volgende voor beeld worden de claims weer gegeven die zijn geretourneerd door de micro soft-account-ID-provider:

- De **subclaim die** is toegewezen aan de **issuerUserId** -claim.
- De **naam** claim die is toegewezen aan de **DisplayName** -claim.
- Het **e-mail adres** zonder naam toewijzing.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de ID-provider:

- De claim **Identity provider** die de naam van de ID-provider bevat.
- De **authenticationSource** claim met de standaard waarde **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
</OutputClaims>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_id | Ja | De toepassings-id van de ID-provider. |
| IdTokenAudience | Nee | De doel groep van de id_token. Indien opgegeven, wordt door Azure AD B2C gecontroleerd of de `aud` claim in een token dat door de identiteits provider is geretourneerd, gelijk is aan de waarde die is opgegeven in de IdTokenAudience-meta gegevens.  |
| METAGEGEVENSARCHIEFMETHODE | Ja | Een URL die verwijst naar een configuratie document van een OpenID Connect Connect-ID-provider, dat ook wel bekend staat als OpenID Connect goed bekend configuratie-eind punt. De URL kan de `{tenant}` expressie bevatten, die wordt vervangen door de naam van de Tenant.  |
| authorization_endpoint | Nee | Een URL die verwijst naar een OpenID Connect Connect ID-provider configuratie autorisatie-eind punt. De waarde van authorization_endpoint meta gegevens heeft voor rang boven de `authorization_endpoint` opgegeven in het OpenID Connect-bekende configuratie-eind punt. De URL kan de `{tenant}` expressie bevatten, die wordt vervangen door de naam van de Tenant. |
| uitgever | Nee | De unieke id van een OpenID Connect Connect-ID-provider. De waarde van de meta gegevens van de verlener heeft voor rang op de `issuer` opgegeven in het OpenID Connect-configuratie-eind punt.  Indien opgegeven, Azure AD B2C controleert of de `iss` claim in een token dat door de identiteits provider is geretourneerd, gelijk is aan de waarde die is opgegeven in de meta gegevens van de verlener. |
| ProviderName | Nee | De naam van de ID-provider.  |
| response_types | Nee | Het antwoord type volgens de OpenID Connect Connect Core 1,0-specificatie. Mogelijke waarden: `id_token` , `code` of `token` . |
| response_mode | Nee | De methode die de ID-provider gebruikt om het resultaat terug te sturen naar Azure AD B2C. Mogelijke waarden: `query` , `form_post` (standaard) of `fragment` . |
| scope | Nee | Het bereik van de aanvraag die is gedefinieerd volgens de OpenID Connect Connect Core 1,0-specificatie. Zoals `openid` , `profile` , en `email` . |
| HttpBinding | Nee | De verwachte HTTP-binding met het toegangs token en claims token-eind punten. Mogelijke waarden: `GET` of `POST` .  |
| ValidTokenIssuerPrefixes | Nee | Een sleutel die kan worden gebruikt om u aan te melden bij elk van de tenants wanneer u een multi tenant-ID-provider gebruikt, zoals Azure Active Directory. |
| UsePolicyInRedirectUri | Nee | Hiermee wordt aangegeven of een beleid moet worden gebruikt bij het samen stellen van de omleidings-URI. Wanneer u uw toepassing in de ID-provider configureert, moet u de omleidings-URI opgeven. De omleidings-URI verwijst naar Azure AD B2C, `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` .  Als u opgeeft `false` , moet u een omleidings-URI toevoegen voor elk beleid dat u gebruikt. Bijvoorbeeld: `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/{policy-name}/oauth2/authresp`. |
| MarkAsFailureOnStatusCode5xx | Nee | Hiermee wordt aangegeven of een aanvraag naar een externe service als een fout moet worden gemarkeerd als de HTTP-status code zich in het 5xx bereik bevindt. De standaardwaarde is `false`. |
| DiscoverMetadataByTokenIssuer | Nee | Geeft aan of de OIDC-meta gegevens moeten worden gedetecteerd met behulp van de verlener in het JWT-token. |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true` , of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true` . |
|token_endpoint_auth_method| Nee| Hiermee geeft u op hoe Azure AD B2C de Authentication-Header naar het eind punt van het token verzendt. Mogelijke waarden: `client_secret_post` (standaard) en `client_secret_basic` (open bare preview). Zie [OpenID Connect Connect client Authentication sectie](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)(Engelstalig) voor meer informatie. |


```xml
<Metadata>
  <Item Key="ProviderName">https://login.live.com</Item>
  <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
  <Item Key="response_types">code</Item>
  <Item Key="response_mode">form_post</Item>
  <Item Key="scope">openid profile email</Item>
  <Item Key="HttpBinding">POST</Item>
  <Item Key="UsePolicyInRedirectUri">false</Item>
  <Item Key="client_id">Your Microsoft application client ID</Item>
</Metadata>
```

### <a name="ui-elements"></a>UI-elementen
 
De volgende instellingen kunnen worden gebruikt voor het configureren van het fout bericht dat wordt weer gegeven bij een fout. De meta gegevens moeten worden geconfigureerd in het technische profiel OpenID Connect Connect. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#sign-up-or-sign-in-error-messages).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| UserMessageIfClaimsPrincipalDoesNotExist | Nee | Het bericht dat wordt weer gegeven aan de gebruiker als een account met de gegeven gebruikers naam niet in de map is gevonden. |
| UserMessageIfInvalidPassword | Nee | Het bericht dat wordt weer gegeven aan de gebruiker als het wacht woord onjuist is. |
| UserMessageIfOldPasswordUsed| Nee |  Het bericht dat wordt weer gegeven aan de gebruiker als er een oud wacht woord wordt gebruikt.|

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het element **CryptographicKeys** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_secret | Ja | Het client geheim van de identiteits provider toepassing. De cryptografische sleutel is alleen vereist als de **response_types** meta gegevens zijn ingesteld op `code` . In dit geval maakt Azure AD B2C een andere aanroep voor het uitwisselen van de autorisatie code voor een toegangs token. Als de meta gegevens zijn ingesteld op, `id_token` kunt u de cryptografische sleutel weglaten.  |

## <a name="redirect-uri"></a>Omleidings-URI

Wanneer u de omleidings-URI van uw ID-provider configureert, voert u in `https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp` . Zorg ervoor dat u vervangt door `{your-tenant-name}` de naam van uw Tenant. De omleidings-URI moet in alle kleine letters zijn.

Voorbeelden:

- [Micro soft-account (MSA) toevoegen als een id-provider met behulp van aangepast beleid](identity-provider-microsoft-account-custom.md)
- [Aanmelden met Azure AD-accounts](identity-provider-azure-ad-single-tenant-custom.md)
- [Gebruikers toestaan zich aan te melden bij een multi tenant Azure AD-ID-provider met behulp van aangepast beleid](identity-provider-azure-ad-multi-tenant-custom.md)
