---
title: Technische profielen
titleSuffix: Azure AD B2C
description: Geef het TechnicalProfiles-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bcff1ffd574db910c3206d82e4da0e9428db788f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631792"
---
# <a name="technical-profiles"></a>Technische profielen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een *technisch profiel* biedt een Framework met een ingebouwd mechanisme om te communiceren met verschillende soorten partijen. Technische profielen worden gebruikt om te communiceren met uw Azure Active Directory B2C-Tenant (Azure AD B2C) om een gebruiker te maken of een gebruikers profiel te lezen. Een technisch profiel kan worden bevestigd om interactie met de gebruiker mogelijk te maken. Een technisch profiel kan bijvoorbeeld de referenties van de gebruiker verzamelen om zich aan te melden en vervolgens de pagina voor de registratie pagina of het wacht woord opnieuw instellen weer geven.

## <a name="types-of-technical-profiles"></a>Typen technische profielen

Met een technisch profiel kunnen deze soorten scenario's worden ingeschakeld:

- [Application Insights](analytics-with-application-insights.md): Hiermee worden gebeurtenis gegevens naar [Application Insights](../azure-monitor/app/app-insights-overview.md)verzonden.
- [Azure AD](active-directory-technical-profile.md): biedt ondersteuning voor het Azure AD B2C gebruikers beheer.
- [Azure AD multi-factor Authentication](multi-factor-auth-technical-profile.md): biedt ondersteuning voor het controleren van een telefoon nummer met behulp van Azure AD multi-factor Authentication.
- [Claim transformatie](claims-transformation-technical-profile.md): roept uitvoer claim transformaties aan om claims waarden te manipuleren, claims te valideren of standaard waarden in te stellen voor een set uitvoer claims.
- [Id-token Hint](id-token-hint.md): valideert de `id_token_hint` JWT-token handtekening, de naam van de verlener en de token doelgroep, en extraheert de claim van het inkomende token.
- [JWT-token Uitgever](jwt-issuer-technical-profile.md): verstuurt een JWT-token dat terugkeert naar de Relying Party-toepassing.
- [OAuth1](oauth1-technical-profile.md): Federatie met de ID-provider van een OAuth 1,0-protocol.
- [OAuth2](oauth2-technical-profile.md): Federatie met de ID-provider van een OAuth 2,0-protocol.
- [Eenmalig wacht woord](one-time-password-technical-profile.md): biedt ondersteuning voor het beheren van het genereren en verifiëren van een eenmalig wacht woord.
- [OpenID Connect Connect](openid-connect-technical-profile.md): Federatie met elke OpenID Connect Connect protocol-ID-provider.
- [Telefoon factor](phone-factor-technical-profile.md): ondersteunt inschrijving en verificatie van telefoon nummers.
- Betrouwde [provider](restful-technical-profile.md): roept rest API services aan, zoals het valideren van gebruikers invoer, het verrijken van gebruikers gegevens of het integreren van de integratie met line-of-business-toepassingen.
- [SAML-ID-provider](identity-provider-generic-saml.md): Federatie met een id-provider voor het SAML-protocol.
- [SAML-token Uitgever](saml-service-provider.md): er wordt een SAML-token uitgegeven dat terugkeert naar de Relying Party-toepassing.
- [Zelfbevestigend](self-asserted-technical-profile.md): communiceert met de gebruiker. Bijvoorbeeld, verzamelt de gebruikers referenties om zich aan te melden, de registratie pagina weer te geven of het wacht woord opnieuw in te stellen.
- [Sessie beheer](custom-policy-reference-sso.md): Hiermee worden verschillende soorten sessies afgehandeld.

## <a name="technical-profile-flow"></a>Technische profiel stroom

Alle typen technische profielen delen hetzelfde concept. Ze worden gestart door de invoer claims te lezen en claim transformaties uit te voeren. Vervolgens communiceren ze met de geconfigureerde partij, zoals een id-provider, REST API of Azure AD Directory Services. Nadat het proces is voltooid, retourneert het technische profiel de uitvoer claims en kunnen uitvoer claim transformaties worden uitgevoerd. In het volgende diagram ziet u hoe de trans formaties en toewijzingen waarnaar wordt verwezen in het technische profiel worden verwerkt. Nadat de claim transformatie is uitgevoerd, worden de uitvoer claims direct opgeslagen in de claim Bag, onafhankelijk van de partij waarmee het technische profiel samenwerkt.

![Diagram dat de technische profiel stroom illustreert.](./media/technical-profiles/technical-profile-flow.png)

1. **Beheer van sessie voor eenmalige aanmelding (SSO)**: Hiermee wordt de sessie status van het technische profiel hersteld met behulp van [SSO-sessie beheer](custom-policy-reference-sso.md).
1. **Invoer claims trans formatie**: voordat het technische profiel wordt gestart, voert Azure AD B2C invoer [claims-trans formatie](claimstransformations.md)uit.
1. **Invoer claims**: claims worden opgehaald uit de claim Bag die worden gebruikt voor het technische profiel.
1. **Uitvoering van het technische profiel**: met het technische profiel worden de claims uitgewisseld met de geconfigureerde partij. Bijvoorbeeld:
    - Hiermee wordt de gebruiker omgeleid naar de ID-provider om de aanmelding te volt ooien. Nadat het aanmelden is geslaagd, keert de gebruiker terug en wordt de uitvoering van het technische profiel voortgezet.
    - Hiermee wordt een REST API aangeroepen tijdens het verzenden van para meters als InputClaims en wordt weer gegeven als OutputClaims.
    - Hiermee wordt het gebruikers account gemaakt of bijgewerkt.
    - Hiermee wordt het SMS-bericht met multi-factor Authentication verzonden en gecontroleerd.
1. **Technische profielen voor validatie**: een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md) kan [validatie technische profielen](validation-technical-profile.md) aanroepen om de door de gebruiker gedocumenteerde gegevens te valideren.
1. **Uitvoer claims**: claims worden teruggestuurd naar de claim verzameling. U kunt deze claims gebruiken in de volgende indelings stap of trans formaties voor uitvoer claims.
1. **Uitvoer van claim transformaties**: wanneer het technische profiel is voltooid, worden in azure AD B2C uitvoer [claim transformaties](claimstransformations.md)uitgevoerd.
1. **SSO-sessie beheer**: de gegevens van het technische profiel blijven behouden in de sessie met behulp van [SSO-sessie beheer](custom-policy-reference-sso.md).

Een **TechnicalProfiles** -element bevat een set technische profielen die worden ondersteund door de claim provider. Elke claim provider moet ten minste één technisch profiel hebben. Het technische profiel bepaalt de eind punten en de protocollen die nodig zijn om te communiceren met de claim provider. Een claim provider kan meerdere technische profielen hebben.

```xml
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        ...
      </Metadata>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Het element **TechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
|---------|---------|---------|
| Id | Yes | Een unieke id van het technische profiel. Naar het technische profiel kan worden verwezen met behulp van deze id vanuit andere elementen in het beleids bestand. Voor beelden zijn **OrchestrationSteps** en **ValidationTechnicalProfile**. |

Het **TechnicalProfile** -element bevat de volgende elementen:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Domain | 0:1 | De domein naam voor het technische profiel. Als uw technische profiel bijvoorbeeld de Facebook-ID-provider opgeeft, is de domein naam Facebook.com. |
| DisplayName | 1:1 | De weergave naam van het technische profiel. |
| Description | 0:1 | De beschrijving van het technische profiel. |
| Protocol | 1:1 | Het protocol dat wordt gebruikt voor communicatie met de andere partij. |
| Metagegevens | 0:1 | Een set sleutels en waarden waarmee het gedrag van het technische profiel wordt bepaald. |
| InputTokenFormat | 0:1 | De indeling van het invoer token. Mogelijke waarden zijn `JSON` , `JWT` , `SAML11` , of `SAML2` . De `JWT` waarde vertegenwoordigt een JSON Web Token per IETF-specificatie. De `SAML11` waarde vertegenwoordigt een SAML 1,1-beveiligings token volgens de Oasis-specificatie. De `SAML2` waarde vertegenwoordigt een SAML 2,0-beveiligings token volgens de Oasis-specificatie. |
| OutputTokenFormat | 0:1 | De indeling van het uitvoer token. Mogelijke waarden zijn `JSON` , `JWT` , `SAML11` , of `SAML2` . |
| CryptographicKeys | 0:1 | Een lijst met cryptografische sleutels die worden gebruikt in het technische profiel. |
| InputClaimsTransformations | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim transformaties die moeten worden uitgevoerd voordat claims naar de claim provider of de Relying Party worden verzonden. |
| InputClaims | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim typen die in het technische profiel worden opgenomen als invoer. |
| PersistedClaims | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim typen die door het technische profiel blijvend worden bewaard. |
| DisplayClaims | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim typen die door het [zelfondertekende technische profiel](self-asserted-technical-profile.md)worden gepresenteerd. De functie DisplayClaims is momenteel beschikbaar als preview-versie. |
| OutputClaims | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim typen die worden uitgevoerd als uitvoer in het technische profiel. |
| OutputClaimsTransformations | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim transformaties die moeten worden uitgevoerd nadat de claims zijn ontvangen van de claim provider. |
| ValidationTechnicalProfiles | 0: n | Een lijst met verwijzingen naar andere technische profielen die het technische profiel gebruikt voor validatie doeleinden. Zie validatie van het [technische profiel](validation-technical-profile.md)voor meer informatie.|
| SubjectNamingInfo | 0:1 | Hiermee bepaalt u de productie van de onderwerpnaam in tokens waarin de onderwerpnaam afzonderlijk van claims is opgegeven. Voor beelden zijn OAuth of SAML. |
| IncludeInSso | 0:1 | Of het gebruik van dit technische profiel SSO-gedrag moet Toep assen voor de sessie of in plaats daarvan expliciete interactie vereist. Dit element is alleen geldig in SelfAsserted-profielen die worden gebruikt binnen een validatie technische profiel. Mogelijke waarden zijn `true` (standaard) of `false` . |
| IncludeClaimsFromTechnicalProfile | 0:1 | Een id van een technisch profiel waarvan u wilt dat alle invoer-en uitvoer claims aan dit technische profiel worden toegevoegd. Het technische profiel waarnaar wordt verwezen, moet in hetzelfde beleids bestand worden gedefinieerd. |
| IncludeTechnicalProfile |0:1 | Een id van een technisch profiel waarvan u wilt dat alle gegevens worden toegevoegd aan dit technische profiel. |
| UseTechnicalProfileForSessionManagement | 0:1 | Een ander technisch profiel dat moet worden gebruikt voor sessie beheer. |
|EnabledForUserJourneys| 0:1 |Hiermee wordt bepaald of het technische profiel wordt uitgevoerd in een reis van de gebruiker. |

## <a name="protocol"></a>Protocol

Het **protocol** element geeft het protocol op dat moet worden gebruikt voor communicatie met de andere partij. Het **protocol** element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Name | Yes | De naam van een geldig protocol dat wordt ondersteund door Azure AD B2C die wordt gebruikt als onderdeel van het technische profiel. Mogelijke waarden zijn,,,, `OAuth1` `OAuth2` `SAML2` `OpenIdConnect` `Proprietary` of `None` . |
| Handler | No | Als de protocol naam is ingesteld op `Proprietary` , geeft u de naam op van de assembly die wordt gebruikt door Azure AD B2C om de protocol-handler te bepalen. |

## <a name="metadata"></a>Metagegevens

Het **META** gegevenselement bevat de relevante configuratie opties voor een specifiek protocol. De lijst met ondersteunde meta gegevens wordt gedocumenteerd in de bijbehorende [technische profiel](#types-of-technical-profiles) specificatie. Een **META** gegevenselement bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Item | 0: n | De meta gegevens die betrekking hebben op het technische profiel. Elk type technisch profiel heeft een andere set meta gegevens items. Zie de sectie met technische profiel typen voor meer informatie. |

### <a name="item"></a>Item

Het element **item** van het **META** gegevenselement bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Sleutel | Yes | De meta gegevens sleutel. Zie elk [type technische profiel](#types-of-technical-profiles) voor de lijst met meta gegevens items. |

In het volgende voor beeld ziet u het gebruik van meta gegevens die relevant zijn voor het [technische profiel OAuth2](oauth2-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  ...
  <Metadata>
    <Item Key="ProviderName">facebook</Item>
    <Item Key="authorization_endpoint">https://www.facebook.com/dialog/oauth</Item>
    <Item Key="AccessTokenEndpoint">https://graph.facebook.com/oauth/access_token</Item>
    <Item Key="HttpBinding">GET</Item>
    <Item Key="UsePolicyInRedirectUri">0</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

In het volgende voor beeld ziet u het gebruik van meta gegevens die relevant zijn voor het [rest API technische profiel](restful-technical-profile.md#metadata).

```xml
<TechnicalProfile Id="REST-Validate-Email">
  ...
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    ...
  </Metadata>
  ...
</TechnicalProfile>
```

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Als u een vertrouwens relatie tot stand wilt brengen met de services die worden geïntegreerd met, Azure AD B2C worden geheimen en certificaten opgeslagen in de vorm van [beleids sleutels](policy-keys-overview.md). Tijdens de uitvoering van het technische profiel Azure AD B2C de cryptografische sleutels uit Azure AD B2C-beleids sleutels worden opgehaald. Azure AD B2C gebruikt vervolgens de sleutels voor het instellen van een vertrouwens relatie of het versleutelen of ondertekenen van een token. Deze vertrouwens relaties bestaan uit:

- Federatie met [OAuth1](oauth1-technical-profile.md#cryptographic-keys)-, [OAuth2](oauth2-technical-profile.md#cryptographic-keys)-en [SAML](identity-provider-generic-saml.md) -id-providers.
- De verbinding met [rest API services](secure-rest-api.md)beveiligen.
- Ondertekenen en versleutelen van de [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) -en [SAML](saml-service-provider.md) -tokens.

Het element **CryptographicKeys** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Sleutel | 1: n | Een cryptografische sleutel die wordt gebruikt in dit technische profiel. |

### <a name="key"></a>Sleutel

Het **sleutel** element bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | No | Een unieke id van een bepaalde sleutel paar waarnaar wordt verwezen vanuit andere elementen in het beleids bestand. |
| StorageReferenceId | Yes | Een id van een opslag sleutel container waarnaar wordt verwezen vanuit andere elementen in het beleids bestand. |

## <a name="input-claims-transformations"></a>Invoer van claim transformaties

Het **InputClaimsTransformations** -element kan een verzameling trans formatie-elementen van invoer claims bevatten die worden gebruikt om invoer claims te wijzigen of nieuwe te genereren.

De uitvoer claims van een vorige claim transformatie in de verzameling voor het transformeren van claims kunnen invoer claims van een volgende invoer claims-trans formatie zijn. Op deze manier kunt u een reeks claim transformaties hebben die van elkaar afhankelijk zijn.

Het element **InputClaimsTransformations** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | De id van een claim transformatie die moet worden uitgevoerd voordat claims naar de claim provider of de Relying Party worden verzonden. Een claim transformatie kan worden gebruikt om bestaande ClaimsSchema claims te wijzigen of nieuwe te genereren. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Het element **InputClaimsTransformation** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Een id van een claim transformatie die al is gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

De volgende technische profielen verwijzen naar de **CreateOtherMailsFromEmail** -claim transformatie. De claim transformatie voegt de waarde van de `email` claim toe aan de `otherMails` verzameling voordat de gegevens worden opgeslagen in de map.

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  ...
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  ...
</TechnicalProfile>
```

## <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element haalt claims op uit de claim Bag die worden gebruikt voor het technische profiel. Een [zelfbevestigend technisch profiel](self-asserted-technical-profile.md) gebruikt bijvoorbeeld de invoer claims om de uitvoer claims die de gebruiker levert, vooraf in te vullen. Een REST API technisch profiel gebruikt de invoer claims voor het verzenden van invoer parameters naar het REST API eind punt. Azure AD gebruikt een invoer claim als een unieke id voor het lezen, bijwerken of verwijderen van een account.

Het element **InputClaims** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Input claim | 1: n | Een verwachte invoer claim type. |

### <a name="inputclaim"></a>Input claim

Het **input claim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | De id van een claim type. De claim is al gedefinieerd in de sectie claim schema in het beleids bestand of het bovenliggende beleids bestand. |
| Standaard | No | Een standaard waarde die moet worden gebruikt om een claim te maken als de claim die wordt aangegeven door ClaimTypeReferenceId niet bestaat, zodat de resulterende claim kan worden gebruikt als een input claim-element van het technische profiel. |
| PartnerClaimType | No | De id van het claim type van de externe partner waaraan het opgegeven claim type van het beleid is toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven type beleids claim toegewezen aan het partner claim type met dezelfde naam. Gebruik deze eigenschap wanneer de naam van het claim type afwijkt van de andere partij. Een voor beeld is als de eerste claim naam is *opgegeven*, terwijl de partner gebruikmaakt van een claim met de naam *first_name*. |

## <a name="display-claims"></a>Claims weer geven

Het **DisplayClaims** -element bevat een lijst met claims die op het scherm moeten worden weer gegeven voor het verzamelen van gegevens van de gebruiker. In de verzameling claims weer geven kunt u een verwijzing naar een [claim type](claimsschema.md) of een [Weergave besturings element](display-controls.md) toevoegen die u hebt gemaakt.

- Een claim type is een verwijzing naar een claim die op het scherm moet worden weer gegeven.
  - Als u wilt afdwingen dat de gebruiker een waarde voor een specifieke claim opgeeft, stelt u het **vereiste** kenmerk van het **DisplayClaim** -element in op `true` .
  - Als u de waarden van weer gave claims vooraf wilt invullen, gebruikt u de invoer claims die eerder zijn beschreven. Het element kan ook een standaard waarde bevatten.
  - Het element **claim** type in de **DisplayClaims** -verzameling moet het **UserInputType** -element instellen op elk type gebruikers invoer dat door Azure AD B2C wordt ondersteund. Voor beelden zijn `TextBox` of `DropdownSingleSelect` .
- Een weergave besturings element is een gebruikers interface-element dat speciale functionaliteit heeft en samenwerkt met de Azure AD B2C back-end-service. Hiermee kan de gebruiker acties uitvoeren op de pagina die een validatie technische profiel aan de back-end aanroept. Een voor beeld is het verifiëren van een e-mail adres, telefoon nummer of loyaliteits nummer van de klant.

De volg orde van de elementen in **DisplayClaims** geeft aan in welke volg orde Azure AD B2C de claims op het scherm worden weer gegeven.

Het element **DisplayClaims** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | Een verwachte invoer claim type. |

### <a name="displayclaim"></a>DisplayClaim

Het **DisplayClaim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | No | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| DisplayControlReferenceId | No | De id van een [Weergave besturings element](display-controls.md) dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Vereist | No | Hiermee wordt aangegeven of de weergave claim vereist is. |

In het volgende voor beeld ziet u het gebruik van de besturings elementen weer geven claims en weer gave in een zelf-bevestigd technisch profiel.

![Scherm opname van een zelf-bevestigd technisch profiel met weer gave claims.](./media/technical-profiles/display-claims.png)

In het volgende technische profiel:

- De eerste weergave claim geeft een verwijzing naar het `emailVerificationControl` besturings element weer geven, waarmee het e-mail adres wordt verzameld en geverifieerd.
- De vijfde weergave claim verwijst naar het `phoneVerificationControl` Weergave besturings element waarmee een telefoon nummer wordt verzameld en geverifieerd.
- De andere weer gave claims zijn claim type-elementen die moeten worden verzameld van de gebruiker.

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

## <a name="persisted-claims"></a>Permanente claims

Het **PersistedClaims** -element bevat alle waarden die moeten worden bewaard met een [technische Azure AD-profiel](active-directory-technical-profile.md) met mogelijke toewijzings informatie tussen een claim type dat al is gedefinieerd in de sectie [ClaimsSchema](claimsschema.md) in het beleid en de kenmerk naam van Azure AD.

De naam van de claim is de naam van het [kenmerk Azure AD](user-profile-attributes.md) , tenzij het kenmerk **PartnerClaimType** is opgegeven, dat de naam van het Azure AD-kenmerk bevat.

Het element **PersistedClaims** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Het claim type moet persistent worden gemaakt. |

### <a name="persistedclaim"></a>PersistedClaim

Het **PersistedClaim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Standaard | No | Een standaard waarde die moet worden gebruikt om een claim te maken als de claim niet bestaat. |
| PartnerClaimType | No | De id van het claim type van de externe partner waaraan het opgegeven claim type van het beleid is toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven type beleids claim toegewezen aan het partner claim type met dezelfde naam. Gebruik deze eigenschap wanneer de naam van het claim type afwijkt van de andere partij. Een voor beeld is als de eerste claim naam is *opgegeven*, terwijl de partner gebruikmaakt van een claim met de naam *first_name*. |

In het volgende voor beeld wordt het **UserWriteUsingLogonEmail-** technische profiel van Aad of het [Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts), waarmee een nieuw lokaal account wordt gemaakt, de volgende claims behouden:

```xml
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
  <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
  <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
  <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
  <PersistedClaim ClaimTypeReferenceId="givenName" />
  <PersistedClaim ClaimTypeReferenceId="surname" />
</PersistedClaims>
```

## <a name="output-claims"></a>Uitvoer claims

Het element **OutputClaims** is een verzameling claims die terug naar de claim Bag worden geretourneerd nadat het technische profiel is voltooid. U kunt deze claims gebruiken in de volgende indelings stap of trans formaties voor uitvoer claims. Het element **OutputClaims** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| Output claim | 1: n | Een verwacht type uitvoer claim. |

### <a name="outputclaim"></a>Output claim

Het **output claim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Standaard | No | Een standaard waarde die moet worden gebruikt om een claim te maken als de claim niet bestaat. |
|AlwaysUseDefaultValue |No |Hiermee wordt het gebruik van de standaard waarde afgedwongen. |
| PartnerClaimType | No | De id van het claim type van de externe partner waaraan het opgegeven claim type van het beleid is toegewezen. Als het partner claim type kenmerk niet is opgegeven, wordt het opgegeven type beleids claim toegewezen aan het partner claim type met dezelfde naam. Gebruik deze eigenschap wanneer de naam van het claim type afwijkt van de andere partij. Een voor beeld is als de eerste claim naam is *opgegeven*, terwijl de partner gebruikmaakt van een claim met de naam *first_name*. |

## <a name="output-claims-transformations"></a>Trans formaties uitvoer claims

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten. De uitvoer claim transformaties worden gebruikt om de uitvoer claims te wijzigen of nieuwe te genereren. Na de uitvoering worden de uitvoer claims weer in de claims-Bag geplaatst. U kunt deze claims in de volgende Orchestration-stap gebruiken.

De uitvoer claims van een vorige claim transformatie in de verzameling voor het transformeren van claims kunnen invoer claims van een volgende invoer claims-trans formatie zijn. Op deze manier kunt u een reeks claim transformaties hebben die van elkaar afhankelijk zijn.

Het element **OutputClaimsTransformations** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | De id's van claim transformaties die moeten worden uitgevoerd voordat claims naar de claim provider of de Relying Party worden verzonden. Een claim transformatie kan worden gebruikt om bestaande ClaimsSchema claims te wijzigen of nieuwe te genereren. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Het element **OutputClaimsTransformation** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Een id van een claim transformatie die al is gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

Het volgende technische profiel verwijst naar de AssertAccountEnabledIsTrue-claim transformatie om te evalueren of het account is ingeschakeld of niet na het lezen `accountEnabled` van de claim uit de Directory.

```xml
<TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
  ...
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="accountEnabled" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
  ...
</TechnicalProfile>
```

## <a name="validation-technical-profiles"></a>Validatie van technische profielen

Een validatie technische profiel wordt gebruikt voor het valideren van uitvoer claims in een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md#validation-technical-profiles). Een technische validatie profiel is een normaal technisch profiel van elk protocol, zoals [Azure AD](active-directory-technical-profile.md) of een [rest API](restful-technical-profile.md). Het technische profiel voor validatie retourneert uitvoer claims of retourneert fout code. Het fout bericht wordt weer gegeven aan de gebruiker op het scherm, zodat de gebruiker het opnieuw kan proberen.

In het volgende diagram ziet u hoe Azure AD B2C een validatie technische profiel gebruikt om de gebruikers referenties te valideren.

![Diagram waarin een validatie technische profiel stroom wordt weer gegeven.](./media/technical-profiles/validation-technical-profile.png)

Het element **ValidationTechnicalProfiles** bevat het volgende element:

| Element | Instanties | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | De id's van de technische profielen die worden gebruikt, valideren enkele of alle uitvoer claims van het technische profiel waarnaar wordt verwezen. Alle invoer claims van het technische profiel waarnaar wordt verwezen, moeten worden weer gegeven in de uitvoer claims van het referentie-technische profiel. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Het element **ValidationTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Een id van een technisch profiel is al gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

Het **SubjectNamingInfo** -element definieert de onderwerpnaam die wordt gebruikt in tokens in een [Relying Party beleid](relyingparty.md#subjectnaminginfo). Het element **SubjectNamingInfo** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Claim type | Yes | Een id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand. |

## <a name="include-technical-profile"></a>Technisch profiel toevoegen

Een technisch profiel kan een ander technisch profiel bevatten om instellingen te wijzigen of nieuwe functionaliteit toe te voegen. Het element **IncludeTechnicalProfile** is een verwijzing naar het algemene technische profiel van waaruit een technisch profiel is afgeleid. Gebruik insluiting als u meerdere technische profielen hebt die de kern elementen delen om de redundantie en complexiteit van uw beleids elementen te verminderen. Gebruik een gemeen schappelijk technisch profiel met de gemeen schappelijke set configuratie, samen met specifieke technische profielen voor de taak die het algemene technische profiel bevatten.

Stel dat u een [technisch profiel voor rest API](restful-technical-profile.md) hebt met één eind punt waar u verschillende sets claims voor verschillende scenario's moet verzenden. Maak een gemeen schappelijk technisch profiel met de gedeelde functionaliteit, zoals de REST API eind punt-URI, meta gegevens, het verificatie type en de cryptografische sleutels. Maak specifieke technische profielen voor de taak die het algemene technische profiel bevatten. Voeg vervolgens de invoer-en uitvoer claims toe of overschrijf de REST API eindpunt-URI die relevant is voor het technische profiel.

Het element **IncludeTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Een id van een technisch profiel is al gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

In het volgende voor beeld ziet u het gebruik van de insluiting:

- **Rest-api-common**: een gemeen schappelijk technisch profiel met de basis configuratie.
- **Rest-ValidateProfile**: bevat het technische profiel **rest-api-common** en geeft de invoer-en uitvoer claims aan.
- **Rest-UpdateProfile**: bevat het technische profiel **rest-api-common** , geeft de invoer claims op en overschrijft de `ServiceUrl` meta gegevens.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Common">
      <DisplayName>Base REST API configuration</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
      </CryptographicKeys>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Validate the account and return promo code</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="REST-UpdateProfile">
       <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/update</Item>
      </Metadata>
      <DisplayName>Update the user profile</DisplayName>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="REST-API-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multilevel-inclusion"></a>Opnames op meerdere niveaus

Een technisch profiel kan één technisch profiel bevatten. Er is geen limiet voor het aantal opname niveaus. Het **' Aad-UserReadUsingAlternativeSecurityId-error-** technisch profiel bevat bijvoorbeeld **Aad-UserReadUsingAlternativeSecurityId**. Met dit technische profiel wordt het `RaiseErrorIfClaimsPrincipalDoesNotExist` meta gegevens item ingesteld op en wordt er `true` een fout gegenereerd als er geen sociaal account in de directory bestaat. **Aad-UserReadUsingAlternativeSecurityId-fout** onderdrukt dit gedrag en schakelt dat fout bericht uit.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**Aad-UserReadUsingAlternativeSecurityId** bevat het `AAD-Common` technische profiel.

```xml
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Zowel **Aad-UserReadUsingAlternativeSecurityId-** mis en **Aad-UserReadUsingAlternativeSecurityId** geven niet het vereiste **protocol** element op omdat het is opgegeven in het **Aad-algemene** technische profiel.

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>Technisch profiel gebruiken voor sessie beheer

Het **UseTechnicalProfileForSessionManagement** -element verwijst naar het [technische profiel voor SSO-sessies](custom-policy-reference-sso.md). Het element **UseTechnicalProfileForSessionManagement** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Een id van een technisch profiel is al gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

## <a name="enabled-for-user-journeys"></a>Ingeschakeld voor gebruikers reizen

De [ClaimsProviderSelections](userjourneys.md#claims-provider-selection) in een gebruikers traject definieert de lijst met opties voor de selectie van claim providers en de bijbehorende volg orde. Met het element **EnabledForUserJourneys** filtert u welke claim provider beschikbaar is voor de gebruiker. Het **EnabledForUserJourneys** -element bevat een van de volgende waarden:

- **Altijd**: voert het technische profiel uit.
- **Nooit**: Hiermee slaat u het technische profiel over.
- **OnClaimsExistence**: wordt alleen uitgevoerd als er een bepaalde claim in het technische profiel is opgegeven.
- **OnItemExistenceInStringCollectionClaim**: wordt alleen uitgevoerd wanneer een item in een teken reeks verzamelings claim voor komt.
- **OnItemAbsenceInStringCollectionClaim**: wordt alleen uitgevoerd wanneer een item niet bestaat in een claim voor een teken reeks verzameling.

Voor het gebruik van **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** of **OnItemAbsenceInStringCollectionClaim** moet u de volgende meta gegevens opgeven:

- **ClaimTypeOnWhichToEnable**: Hiermee geeft u het claim type op dat moet worden geëvalueerd.
- **ClaimValueOnWhichToEnable**: Hiermee geeft u de waarde op die moet worden vergeleken.

Het volgende technische profiel wordt alleen uitgevoerd als de teken reeks verzameling **identityProviders** de waarde bevat `facebook.com` :

```xml
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
