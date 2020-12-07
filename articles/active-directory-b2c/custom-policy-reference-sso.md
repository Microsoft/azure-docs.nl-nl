---
title: Beheer van sessie voor eenmalige aanmelding met aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Meer informatie over het beheren van SSO-sessies met aangepaste beleids regels in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e47eda29b0ad1c47edad08195b2ffd0fe3835af
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750472"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Sessie beheer voor eenmalige aanmelding in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Sessie beheer voor eenmalige aanmelding (SSO)](session-overview.md) maakt gebruik van dezelfde semantiek als andere technische profielen in aangepast beleid. Wanneer een Orchestration-stap wordt uitgevoerd, wordt het technische profiel dat is gekoppeld aan de stap, opgevraagd voor een `UseTechnicalProfileForSessionManagement` verwijzing. Als dat het geval is, wordt de SSO-sessie provider waarnaar wordt verwezen, gecontroleerd om te zien of de gebruiker een deel nemer voor de sessie is. Als dit het geval is, wordt de SSO-sessie provider gebruikt om de sessie opnieuw in te vullen. Op dezelfde manier wordt de provider gebruikt voor het opslaan van gegevens in de sessie als er een SSO-sessie provider is opgegeven, wanneer de uitvoering van een Orchestration-stap is voltooid.

Azure AD B2C heeft een aantal SSO-sessie providers gedefinieerd die kunnen worden gebruikt:

|Sessie provider  |Bereik  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  Geen       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Azure AD B2C interne sessie beheerder.      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Tussen Azure AD B2C en OAuth1, OAuth2 of OpenID Connect Connect ID-provider.        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Tussen een OAuth2 of OpenID connect verbinding maken Relying Party toepassing en Azure AD B2C.        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Tussen Azure AD B2C en SAML-ID-provider. En tussen een SAML-service provider (Relying Party toepassing) en Azure AD B2C.  |        




SSO-beheer klassen worden opgegeven met behulp van het- `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />` element van een technisch profiel.

## <a name="input-claims"></a>Invoer claims

Het `InputClaims` element is leeg of afwezig.

## <a name="persisted-claims"></a>Permanente claims

Claims die moeten worden geretourneerd naar de toepassing of worden gebruikt door de voor waarden in de volgende stappen, moeten worden opgeslagen in de sessie of worden uitgebreid met een lees bewerking vanuit het profiel van de gebruiker in de Directory. Door permanente claims te gebruiken zorgt u ervoor dat uw verificatie trajecten niet mislukken voor ontbrekende claims. Gebruik het `<PersistedClaims>` element van het technische profiel om claims toe te voegen in de sessie. Wanneer de provider wordt gebruikt om de sessie opnieuw in te vullen, worden de permanente claims toegevoegd aan de claim verzameling.

## <a name="output-claims"></a>Uitvoer claims

De `<OutputClaims>` wordt gebruikt voor het ophalen van claims uit de sessie.

## <a name="session-providers"></a>Sessie providers

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Zoals de naam bepaalt, heeft deze provider niets. Deze provider kan worden gebruikt om SSO-gedrag voor een specifiek technisch profiel te onderdrukken. Het volgende `SM-Noop` technische profiel is opgenomen in het [aangepaste beleids Starter Pack](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Deze provider kan worden gebruikt voor het opslaan van claims in een sessie. Er wordt doorgaans verwezen naar deze provider in een technisch profiel dat wordt gebruikt voor het beheren van lokale en federatieve accounts. Het volgende `SM-AAD` technische profiel is opgenomen in het [aangepaste beleids Starter Pack](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


Het volgende `SM-MFA` technische profiel is opgenomen in het [aangepaste beleids Starter Pack](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa` . Dit technische profiel beheert de multi-factor Authentication-sessie.

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Deze provider wordt gebruikt om het scherm ' ID-provider kiezen ' te onderdrukken en af te melden bij een federatieve id-provider. Er wordt meestal verwezen naar een technisch profiel dat is geconfigureerd voor een federatieve id-provider, zoals Facebook of Azure Active Directory. Het volgende `SM-SocialLogin` technische profiel is opgenomen in het [aangepaste beleids Starter Pack](custom-policy-get-started.md#custom-policy-starter-pack).

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Nee | Momenteel niet gebruikt, kan worden genegeerd. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Deze provider wordt gebruikt voor het beheren van de Azure AD B2C sessies tussen een OAuth2-of OpenID connect-verbinding Relying Party en Azure AD B2C.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Deze provider wordt gebruikt voor het beheren van de Azure AD B2C SAML-sessies tussen een Relying Party toepassing of een federatieve SAML-ID-provider. Wanneer u de SSO-provider gebruikt voor het opslaan van een SAML ID-provider sessie, `RegisterServiceProviders` moet u deze instellen op `false` . Het volgende `SM-Saml-idp` technische profiel wordt gebruikt door het [technische profiel van de SAML-identiteits provider](saml-identity-provider-technical-profile.md).

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Wanneer u de provider gebruikt voor het opslaan van de B2C SAML-sessie, `RegisterServiceProviders` moet de worden ingesteld op `true` . Voor het afmelden van de SAML-sessie is de `SessionIndex` en `NameID` voltooid.

Het volgende `SM-Saml-issuer` technische profiel wordt gebruikt door een [SAML-Uitgever technisch profiel](saml-issuer-technical-profile.md)

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving|
| --- | --- | --- |
| IncludeSessionIndex | Nee | Momenteel niet gebruikt, kan worden genegeerd.|
| RegisterServiceProviders | Nee | Geeft aan dat de provider alle SAML-service providers moet registreren waarvoor een bevestiging is verleend. Mogelijke waarden: `true` (standaard) of `false` .|


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure AD B2C sessie](session-overview.md).
- Meer informatie over het [configureren van het gedrag van sessies in aangepaste beleids regels](session-behavior-custom-policy.md).
