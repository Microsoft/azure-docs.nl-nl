---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Geef het TechnicalProfiles-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7bd04790c7ac124afe3e9b503803f27118ae959
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861869"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Een technisch profiel biedt een Framework met een ingebouwd mechanisme om te communiceren met verschillende soorten partijen met behulp van een aangepast beleid in Azure Active Directory B2C (Azure AD B2C). Technische profielen worden gebruikt om te communiceren met uw Azure AD B2C-Tenant, een gebruiker te maken of een gebruikers profiel te lezen. Een technisch profiel kan worden bevestigd om interactie met de gebruiker mogelijk te maken. Verzamel bijvoorbeeld de referenties van de gebruiker om u aan te melden en vervolgens de pagina voor de registratie pagina of het wacht woord opnieuw instellen weer te geven.

## <a name="type-of-technical-profiles"></a>Type technische profielen

Met een technisch profiel kunnen deze soorten scenario's worden ingeschakeld:

- [Application Insights](application-insights-technical-profile.md) -gebeurtenis gegevens worden verzonden naar [Application Insights](../azure-monitor/app/app-insights-overview.md).
- [Azure Active Directory](active-directory-technical-profile.md) : biedt ondersteuning voor het Azure Active Directory B2C gebruikers beheer.
- [Azure ad multi-factor Authentication](multi-factor-auth-technical-profile.md) : biedt ondersteuning voor het controleren van een telefoon nummer met behulp van azure AD multi-factor Authentication (MFA). 
- [Claims transformeren](claims-transformation-technical-profile.md) : claim trans formaties voor uitvoer aanroepen om claims waarden te manipuleren, claims te valideren of standaard waarden in te stellen voor een set uitvoer claims.
- [Id-token Hint](id-token-hint.md) : valideert `id_token_hint` JWT-token handtekening, de naam van de verlener en de token doelgroep en extraheert de claim van het inkomende token.
- [JWT-token Uitgever](jwt-issuer-technical-profile.md) : er wordt een JWT-token verzonden dat terugkeert naar de Relying Party-toepassing.
- [OAuth1](oauth1-technical-profile.md) -Federatie met de ID-provider van een OAuth 1,0-protocol.
- [OAuth2](oauth2-technical-profile.md) -Federatie met de ID-provider van een OAuth 2,0-protocol.
- [Eenmalig wacht woord](one-time-password-technical-profile.md) : biedt ondersteuning voor het beheren van het genereren en verifiëren van een eenmalig wacht woord.
- [OpenID Connect Connect](openid-connect-technical-profile.md) -Federation met elke OpenID Connect Connect protocol-ID-provider.
- [Telefoon factor](phone-factor-technical-profile.md) : ondersteuning voor het registreren en verifiëren van telefoon nummers.
- Betrouw bare [provider](restful-technical-profile.md) : oproep naar rest API services, zoals het valideren van gebruikers invoer, verrijkende gebruikers gegevens of het integreren van line-of-business-toepassingen.
- [SAML-ID-provider](saml-identity-provider-technical-profile.md) -Federatie met elke id-provider van het SAML-protocol.
- [SAML-token Uitgever](saml-issuer-technical-profile.md) : er wordt een SAML-token verzonden dat terugkeert naar de Relying Party-toepassing.
- [Zelfbevestigend](self-asserted-technical-profile.md) : interactie met de gebruiker. Verzamel bijvoorbeeld de referenties van de gebruiker om u aan te melden, de registratie pagina weer te geven of het wacht woord opnieuw in te stellen.
- [Sessie beheer](custom-policy-reference-sso.md) : verschillende soorten sessies verwerken.

## <a name="technical-profile-flow"></a>Technische profiel stroom

Alle typen technische profielen delen hetzelfde concept. U verzendt invoer claims, voert claims-trans formatie uit en communiceert met de geconfigureerde partij, zoals een id-provider, REST API of Azure AD-adreslijst Services. Nadat het proces is voltooid, retourneert het technische profiel de uitvoer claims en kan er uitvoer claims trans formatie worden uitgevoerd. In het volgende diagram ziet u hoe de trans formaties en toewijzingen waarnaar wordt verwezen in het technische profiel worden verwerkt. De uitvoer claims van het technische profiel worden direct opgeslagen in de claim Bag, onafhankelijk van de partij waarbij het technische profiel communiceert, nadat elke claim transformatie is uitgevoerd.

![Diagram dat de technische profiel stroom illustreert](./media/technical-profiles/technical-profile-flow.png)

1. **Eenmalige aanmelding (SSO) sessie beheer** : Hiermee wordt de sessie status van het technische profiel hersteld met behulp van [SSO-sessie beheer](custom-policy-reference-sso.md).
1. **Invoer claims transformeren** : voordat het technische profiel wordt gestart, voert Azure AD B2C invoer [claims trans formatie](claimstransformations.md)uit.
1. **Invoer claims** : claims worden opgehaald uit de claims-Bag die worden gebruikt voor het technische profiel.
1. **Uitvoering van technisch profiel** -het technische profiel waarmee de claims worden uitgewisseld met de geconfigureerde partij. Bijvoorbeeld:
    - Leid de gebruiker om naar de ID-provider om de aanmelding te volt ooien. Nadat het aanmelden is geslaagd, keert de gebruiker terug en wordt de uitvoering van het technische profiel voortgezet.
    - Aanroepen van een REST API tijdens het verzenden van para meters als InputClaims en het ophalen van informatie als OutputClaims.
    - Het gebruikers account maken of bijwerken.
    - Hiermee wordt het MFA-tekst bericht verzonden en gecontroleerd.
1. **Technische profielen voor validatie** : een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md) kan [validatie van technische profielen](validation-technical-profile.md) aanroepen om de door de gebruiker gedocumenteerde gegevens te valideren.
1. **Uitvoer claims** : claims worden teruggestuurd naar de Bag van claims. U kunt deze claims gebruiken in de volgende indelings stap of trans formaties uitvoer claims.
1. **Uitvoer van claim transformaties** -nadat het technische profiel is voltooid, Azure AD B2C uitvoer [claims trans formatie](claimstransformations.md)uitvoert. 
1. **Beheer van sessie voor eenmalige aanmelding (SSO)** : persistente gegevens van het technische profiel blijven in de sessie met behulp van [SSO-sessie beheer](custom-policy-reference-sso.md).

Een **TechnicalProfiles** -element bevat een set technische profielen die worden ondersteund door de claim provider. Elke claim provider moet een of meer technische profielen hebben die de eind punten bepalen en de protocollen die nodig zijn om te communiceren met de claim provider. Een claim provider kan meerdere technische profielen hebben.

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
| Id | Ja | Een unieke id van het technische profiel. Naar het technische profiel kan worden verwezen via deze id vanuit andere elementen in het beleids bestand. Bijvoorbeeld **OrchestrationSteps** en **ValidationTechnicalProfile**. |

De **TechnicalProfile** bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Domain | 0:1 | De domein naam voor het technische profiel. Als uw technische profiel bijvoorbeeld de Facebook-ID-provider opgeeft, is de domein naam Facebook.com. |
| DisplayName | 1:1 | De weergave naam van het technische profiel. |
| Beschrijving | 0:1 | De beschrijving van het technische profiel. |
| Protocol | 1:1 | Het protocol dat wordt gebruikt voor communicatie met de andere partij. |
| Metagegevens | 0:1 | Een verzameling sleutel/waarde-paren die worden gebruikt door het protocol voor communicatie met het eind punt in de loop van een trans actie. |
| InputTokenFormat | 0:1 | De indeling van het invoer token. Mogelijke waarden: `JSON` , `JWT` , `SAML11` of `SAML2` . De `JWT` waarde vertegenwoordigt een JSON Web token volgens IETF-specificatie. De `SAML11` waarde vertegenwoordigt een SAML 1,1-beveiligings token conform de Oasis-specificatie.  De `SAML2` waarde vertegenwoordigt een SAML 2,0-beveiligings token conform de Oasis-specificatie. |
| OutputTokenFormat | 0:1 | De indeling van het uitvoer token. Mogelijke waarden: `JSON` , `JWT` , `SAML11` of `SAML2` . |
| CryptographicKeys | 0:1 | Een lijst met cryptografische sleutels die worden gebruikt in het technische profiel. |
| InputClaimsTransformations | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim transformaties die moeten worden uitgevoerd voordat claims naar de claim provider of de Relying Party worden verzonden. |
| InputClaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claim typen die in het technische profiel worden opgenomen als invoer. |
| PersistedClaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claim typen die door de claim provider worden bewaard en die betrekking hebben op het technische profiel. |
| DisplayClaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claim typen die worden gepresenteerd door de claim provider, die betrekking heeft op het [zelfondertekende technische profiel](self-asserted-technical-profile.md). De functie DisplayClaims is momenteel beschikbaar als **Preview-versie**. |
| OutputClaims | 0:1 | Een lijst met de eerder gedefinieerde verwijzingen naar claim typen die worden uitgevoerd als uitvoer in het technische profiel. |
| OutputClaimsTransformations | 0:1 | Een lijst met eerder gedefinieerde verwijzingen naar claim transformaties die moeten worden uitgevoerd nadat de claims zijn ontvangen van de claim provider. |
| ValidationTechnicalProfiles | 0: n | Een lijst met verwijzingen naar andere technische profielen die het technische profiel gebruikt voor validatie doeleinden. Zie voor meer informatie [validatie technische profiel](validation-technical-profile.md)|
| SubjectNamingInfo | 0:1 | Hiermee bepaalt u de productie van de onderwerpnaam in tokens waarin de onderwerpnaam afzonderlijk van claims is opgegeven. Bijvoorbeeld OAuth of SAML.  |
| IncludeInSso | 0:1 |  Of het gebruik van dit technische profiel het gedrag van eenmalige aanmelding (SSO) moet Toep assen voor de sessie, of in plaats daarvan expliciete interactie vereist. Dit element is alleen geldig in SelfAsserted-profielen die worden gebruikt binnen een validatie technische profiel. Mogelijke waarden: `true` (standaard) of `false` . |
| IncludeClaimsFromTechnicalProfile | 0:1 | Een id van een technisch profiel waarvan u wilt dat alle invoer-en uitvoer claims aan dit technische profiel worden toegevoegd. Het technische profiel waarnaar wordt verwezen, moet in hetzelfde beleids bestand worden gedefinieerd. |
| IncludeTechnicalProfile |0:1 | Een id van een technisch profiel waarvan u wilt dat alle gegevens worden toegevoegd aan dit technische profiel. |
| UseTechnicalProfileForSessionManagement | 0:1 | Een ander technisch profiel dat moet worden gebruikt voor sessie beheer. |
|EnabledForUserJourneys| 0:1 |Hiermee wordt bepaald of het technische profiel wordt uitgevoerd in een reis van de gebruiker.  |

## <a name="protocol"></a>Protocol

Het **protocol** geeft het protocol aan dat moet worden gebruikt voor communicatie met de andere partij. Het **protocol** element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Naam | Ja | De naam van een geldig protocol dat wordt ondersteund door Azure AD B2C dat wordt gebruikt als onderdeel van het technische profiel. Mogelijke waarden: `OAuth1` , `OAuth2` , `SAML2` , `OpenIdConnect` , `Proprietary` of `None` . |
| Handler | Nee | Wanneer de protocol naam is ingesteld op `Proprietary` , geeft u de volledig gekwalificeerde naam op van de assembly die wordt gebruikt door Azure AD B2C om de protocolhandler te bepalen. |

## <a name="metadata"></a>Metagegevens

Het **META** gegevenselement bevat de relevante configuratie opties voor een specifiek protocol. De lijst met ondersteunde meta gegevens wordt gedocumenteerd in de bijbehorende [technische profiel](#type-of-technical-profiles) specificatie. Een **META** gegevenselement bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Item | 0: n | De meta gegevens die betrekking hebben op het technische profiel. Elk type technisch profiel heeft een andere set meta gegevens items. Zie de sectie met technische profiel typen voor meer informatie. |

### <a name="item"></a>Item

Het element **item** van het **META** gegevenselement bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Sleutel | Ja | De meta gegevens sleutel. Zie elk [type technische profiel](#type-of-technical-profiles)voor de lijst met meta gegevens items. |

In het volgende voor beeld ziet u het gebruik van meta gegevens die relevant zijn voor het [technische profiel van OAuth2](oauth2-technical-profile.md#metadata).

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

In het volgende voor beeld ziet u het gebruik van meta gegevens die relevant zijn voor [rest API technisch profiel](restful-technical-profile.md#metadata).

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

Met Azure AD B2C worden geheimen en certificaten opgeslagen in de vorm van [beleids sleutels](policy-keys-overview.md) om een vertrouwens relatie tot stand te brengen met de services waarmee deze worden geïntegreerd. Tijdens het uitvoeren van het technische profiel Azure AD B2C worden de cryptografische sleutels opgehaald uit Azure AD B2C-beleids sleutels en vervolgens de sleutels maken vertrouwen, versleutelen of ondertekenen van een token. Deze vertrouwens relaties bestaan uit:

- Federatie met [OAuth1](oauth1-technical-profile.md#cryptographic-keys)-, [OAuth2](oauth2-technical-profile.md#cryptographic-keys)-en [SAML](saml-identity-provider-technical-profile.md#cryptographic-keys) -id-providers
- De verbinding met [rest API services](secure-rest-api.md) beveiligen
- Ondertekenen en versleutelen van de [JWT](jwt-issuer-technical-profile.md#cryptographic-keys) -en [SAML](saml-issuer-technical-profile.md#cryptographic-keys) -tokens

Het element **CryptographicKeys** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Sleutel | 1: n | Een cryptografische sleutel die wordt gebruikt in dit technische profiel. |

### <a name="key"></a>Sleutel

Het **sleutel** element bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Nee | Een unieke id van een bepaalde sleutel paar waarnaar wordt verwezen vanuit andere elementen in het beleids bestand. |
| StorageReferenceId | Ja | Een id van een opslag sleutel container waarnaar wordt verwezen vanuit andere elementen in het beleids bestand. |

## <a name="input-claims-transformations"></a>Invoer van claim transformaties

Het **InputClaimsTransformations** -element kan een verzameling trans formatie-elementen van invoer claims bevatten die worden gebruikt voor het wijzigen van invoer claims of het genereren van een nieuwe. 

De uitvoer claims van een vorige claim transformatie in de verzameling claims transformatie kunnen claims van een volgende invoer claims-trans formatie zijn, zodat u een reeks claim transformatie kunt maken, afhankelijk van elkaar.

Het element **InputClaimsTransformations** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1: n | De id van een claim transformatie die moet worden uitgevoerd voordat claims naar de claim provider of de Relying Party worden verzonden. Een claim transformatie kan worden gebruikt om bestaande ClaimsSchema claims te wijzigen of nieuwe te genereren. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Het element **InputClaimsTransformation** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een claim transformatie die al is gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

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

De **InputClaims** haalt claims op uit de claims-Bag en wordt gebruikt voor het technische profiel. Een [zelfbevestigend technisch profiel](self-asserted-technical-profile.md) gebruikt bijvoorbeeld de invoer claims om de uitvoer claims die de gebruiker levert, vooraf in te vullen. Een REST API technisch profiel gebruikt de invoer claims voor het verzenden van invoer parameters naar het REST API eind punt. Azure Active Directory gebruikt een invoer claim als een unieke id voor het lezen, bijwerken of verwijderen van een account.

Het element **InputClaims** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Input claim | 1: n | Een verwachte invoer claim type. |

### <a name="inputclaim"></a>Input claim

Het **input claim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Standaard | Nee | Een standaard waarde die moet worden gebruikt om een claim te maken als de claim aangegeven door ClaimTypeReferenceId niet bestaat, zodat de resulterende claim kan worden gebruikt als een input claim van het technische profiel. |
| PartnerClaimType | Nee | De id van het claim type van de externe partner waaraan het opgegeven claim type van het beleid is toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven claim type van het beleid toegewezen aan het partner claim type met dezelfde naam. Gebruik deze eigenschap wanneer de naam van het claim type afwijkt van de andere partij. De eerste claim naam is bijvoorbeeld ' noemer ', terwijl de partner een claim met de naam ' first_name ' gebruikt. |

## <a name="display-claims"></a>Claims weer geven

Het **DisplayClaims** -element bevat een lijst met claims die zijn gedefinieerd door een [zelfondertekend technisch profiel](self-asserted-technical-profile.md) dat moet worden weer gegeven op het scherm voor het verzamelen van gegevens van de gebruiker. In de verzameling claims weer geven kunt u een verwijzing naar een [claim type](claimsschema.md)of een [DisplayControl](display-controls.md) die u hebt gemaakt, toevoegen. 

- Een claim type is een verwijzing naar een claim die op het scherm moet worden weer gegeven. 
  - Als u wilt afdwingen dat de gebruiker een waarde voor een specifieke claim opgeeft, stelt u het **vereiste** kenmerk van het **DisplayClaim** -element in op `true` .
  - Als u de waarden van weer gave claims vooraf wilt invullen, gebruikt u de invoer claims die eerder zijn beschreven. Het element kan ook een standaard waarde bevatten.
  - Het element **claim** type in de **DisplayClaims** -verzameling moet het **UserInputType** -element instellen op elk type gebruikers invoer dat door Azure AD B2C wordt ondersteund. Bijvoorbeeld `TextBox` of `DropdownSingleSelect`.
- Een weergave besturings element is een gebruikers interface-element dat speciale functionaliteit heeft en samenwerkt met de Azure AD B2C back-end-service. Hiermee kan de gebruiker acties uitvoeren op de pagina die een validatie technische profiel aan de back-end aanroept. U kunt bijvoorbeeld een e-mail adres, telefoon nummer of loyaliteits nummer van de klant controleren.

De volg orde van de elementen in **DisplayClaims** geeft aan in welke volg orde Azure AD B2C de claims op het scherm worden weer gegeven. 

Het element **DisplayClaims** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| DisplayClaim | 1: n | Een verwachte invoer claim type. |

### <a name="displayclaim"></a>DisplayClaim

Het **DisplayClaim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Nee | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| DisplayControlReferenceId | Nee | De id van een [Weergave besturings element](display-controls.md) dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Vereist | Nee | Hiermee wordt aangegeven of de weergave claim vereist is. |

In het volgende voor beeld ziet u het gebruik van de besturings elementen weer geven claims en weer gave met in een zelf-bevestigd technisch profiel.

![Een zelf-bevestigd technisch profiel met weer gave claims](./media/technical-profiles/display-claims.png)

In het volgende technische profiel:

- De eerste weergave claim geeft een verwijzing naar het `emailVerificationControl` besturings element weer geven, waarmee het e-mail adres wordt verzameld en geverifieerd.
- De vijfde weergave claim verwijst naar het `phoneVerificationControl` Weergave besturings element waarmee een telefoon nummer wordt verzameld en geverifieerd.
- De andere weer gave claims worden ClaimTypes van de gebruiker verzameld.

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

### <a name="persisted-claims"></a>Permanente claims

Het **PersistedClaims** -element bevat alle waarden die door het [technische profiel van Azure AD](active-directory-technical-profile.md) moeten worden bewaard met mogelijke toewijzings gegevens tussen een claim type dat al is gedefinieerd in de sectie [ClaimsSchema](claimsschema.md) van het beleid en de kenmerk naam van Azure AD.

De naam van de claim is de naam van het [kenmerk Azure AD](user-profile-attributes.md) , tenzij het kenmerk **PartnerClaimType** is opgegeven, dat de naam van het Azure AD-kenmerk bevat.

Het **PersistedClaims** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| PersistedClaim | 1: n | Het claim type moet persistent worden gemaakt. |

### <a name="persistedclaim"></a>PersistedClaim

Het **PersistedClaim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Standaard | Nee | Een standaard waarde die moet worden gebruikt om een claim te maken als de claim aangegeven door ClaimTypeReferenceId niet bestaat, zodat de resulterende claim kan worden gebruikt als een input claim van het technische profiel. |
| PartnerClaimType | Nee | De id van het claim type van de externe partner waaraan het opgegeven claim type van het beleid is toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven claim type van het beleid toegewezen aan het partner claim type met dezelfde naam. Gebruik deze eigenschap wanneer de naam van het claim type afwijkt van de andere partij. De eerste claim naam is bijvoorbeeld ' noemer ', terwijl de partner een claim met de naam ' first_name ' gebruikt. |

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

De **OutputClaims** zijn de verzameling claims die worden teruggestuurd naar de claims Bag nadat het technische profiel is voltooid. U kunt deze claims gebruiken in de volgende indelings stap of trans formaties uitvoer claims. Het element **OutputClaims** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Output claim | 1: n | Een verwacht type uitvoer claim. |

### <a name="outputclaim"></a>Output claim

Het **output claim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | De id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand of het bovenliggende beleids bestand. |
| Standaard | Nee | Een standaard waarde die moet worden gebruikt om een claim te maken als de claim aangegeven door ClaimTypeReferenceId niet bestaat, zodat de resulterende claim kan worden gebruikt als een input claim van het technische profiel. |
|AlwaysUseDefaultValue |Nee |Het gebruik van de standaard waarde afdwingen.  |
| PartnerClaimType | Nee | De id van het claim type van de externe partner waaraan het opgegeven claim type van het beleid is toegewezen. Als het kenmerk PartnerClaimType niet is opgegeven, wordt het opgegeven claim type van het beleid toegewezen aan het partner claim type met dezelfde naam. Gebruik deze eigenschap wanneer de naam van het claim type afwijkt van de andere partij. De eerste claim naam is bijvoorbeeld ' noemer ', terwijl de partner een claim met de naam ' first_name ' gebruikt. |

## <a name="output-claims-transformations"></a>Trans formaties uitvoer claims

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe. Na de uitvoering worden de uitvoer claims weer in de claims-Bag geplaatst. U kunt deze claims in de volgende Orchestration-stap gebruiken.

De uitvoer claims van een vorige claim transformatie in de verzameling claims transformatie kunnen claims van een volgende invoer claims-trans formatie zijn, zodat u een reeks claim transformatie kunt maken, afhankelijk van elkaar.

Het element **OutputClaimsTransformations** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1: n | De id's van claim transformaties die moeten worden uitgevoerd voordat claims naar de claim provider of de Relying Party worden verzonden. Een claim transformatie kan worden gebruikt om bestaande ClaimsSchema claims te wijzigen of nieuwe te genereren. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Het element **OutputClaimsTransformation** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een claim transformatie die al is gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

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

Een validatie technische profiel wordt gebruikt voor het valideren van een aantal of alle uitvoer claims van de verwijzing naar een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md#validation-technical-profiles). Een technische validatie profiel is een normaal technisch profiel van elk protocol, zoals [Azure Active Directory](active-directory-technical-profile.md) of een [rest API](restful-technical-profile.md). Het technische profiel voor validatie retourneert uitvoer claims of retourneert fout code. Het fout bericht wordt weer gegeven aan de gebruiker op het scherm, zodat de gebruiker het opnieuw kan proberen.

In het volgende diagram ziet u hoe Azure AD B2C een technische profiel validatie gebruikt om de gebruikers referenties te valideren

![Diagram validatie technische profiel stroom](./media/technical-profiles/validation-technical-profile.png) 

Het element **ValidationTechnicalProfiles** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | De id's van de technische profielen die worden gebruikt, valideren enkele of alle uitvoer claims van het technische profiel waarnaar wordt verwezen. Alle invoer claims van het technische profiel waarnaar wordt verwezen, moeten worden weer gegeven in de uitvoer claims van het referentie-technische profiel. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Het element **ValidationTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel is al gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

De **SubjectNamingInfo** definieert de naam van het onderwerp dat wordt gebruikt in tokens in een [Relying Party beleid](relyingparty.md#subjectnaminginfo). De **SubjectNamingInfo** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Claim type | Ja | Een id van een claim type dat al is gedefinieerd in de sectie ClaimsSchema in het beleids bestand. |

## <a name="include-technical-profile"></a>Technisch profiel toevoegen

Een technisch profiel kan een ander technisch profiel bevatten om instellingen te wijzigen of nieuwe functionaliteit toe te voegen. Het element **IncludeTechnicalProfile** is een verwijzing naar het algemene technische profiel van waaruit een technisch profiel is afgeleid. Gebruik insluiting als u meerdere technische profielen hebt die de kern elementen delen om de redundantie en complexiteit van uw beleids elementen te verminderen. Gebruik een gemeen schappelijk technisch profiel met de gemeen schappelijke set configuratie, samen met specifieke technische profielen voor de taak die het algemene technische profiel bevatten. Stel dat u een [rest API technisch profiel](restful-technical-profile.md) hebt met één eind punt waar u een andere set claims voor verschillende scenario's moet verzenden. Maak een gemeen schappelijk technisch profiel met de gedeelde functionaliteit, zoals de REST API eind punt-URI, meta gegevens, het verificatie type en de cryptografische sleutels. Maak vervolgens specifieke technische profielen voor de taak die het algemene technische profiel bevatten, voeg de invoer claims, uitvoer claims toe of overschrijf de REST API eindpunt-URI die relevant is voor dat technische profiel.

Het element **IncludeTechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel is al gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |


In het volgende voor beeld ziet u het gebruik van de insluiting:

- *Rest-api-common* -een algemeen technisch profiel met de basis configuratie.
- *Rest-ValidateProfile* : bevat het technische profiel *rest-API-commom* en geeft de invoer-en uitvoer claims aan.
- *Rest-UpdateProfile* : bevat het technische profiel *rest-API-commom* , geeft de invoer claims op en overschrijft de `ServiceUrl` meta gegevens.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-API-Commom">
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
      <IncludeTechnicalProfile ReferenceId="REST-API-Commom" />
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
      <IncludeTechnicalProfile ReferenceId="REST-API-Commom" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

### <a name="multi-level-inclusion"></a>Insluiting op meerdere niveaus 

Een technisch profiel kan één technisch profiel bevatten. Er is geen limiet voor het aantal opname niveaus. Het **Aad-UserReadUsingAlternativeSecurityId-error-** technisch profiel bevat bijvoorbeeld de **Aad-UserReadUsingAlternativeSecurityId**. Met dit technische profiel wordt het `RaiseErrorIfClaimsPrincipalDoesNotExist` meta gegevens item ingesteld op en wordt er `true` een fout gegenereerd als er geen sociaal account in de directory bestaat. **Aad-UserReadUsingAlternativeSecurityId-fout** onderdrukt dit gedrag en schakelt dat fout bericht uit.

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

Zowel **Aad-UserReadUsingAlternativeSecurityId-** mis en  **Aad-UserReadUsingAlternativeSecurityId** geven niet het vereiste **protocol** element op omdat het is opgegeven in het **Aad-algemene** technische profiel.

```xml
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```

## <a name="use-technical-profile-for-session-management"></a>Technisch profiel gebruiken voor sessie beheer

De verwijzing naar het **UseTechnicalProfileForSessionManagement** -element naar het [technische profiel voor eenmalige aanmelding](custom-policy-reference-sso.md). Het element **UseTechnicalProfileForSessionManagement** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Een id van een technisch profiel is al gedefinieerd in het beleids bestand of het bovenliggende beleids bestand. |

## <a name="enabled-for-user-journeys"></a>Ingeschakeld voor gebruikers reizen

De [ClaimsProviderSelections](userjourneys.md#claimsproviderselection) in een gebruikers traject definieert de lijst met opties voor de selectie van claim providers en de bijbehorende volg orde. Met het **EnabledForUserJourneys** -element dat u filtert, welke claim provider beschikbaar is voor de gebruiker. Het **EnabledForUserJourneys** -element bevat een van de volgende waarden:

- Voer **altijd** het technische profiel uit.
- **Nooit**, sla het technische profiel over.
- **OnClaimsExistence** kan alleen worden uitgevoerd wanneer een bepaalde claim is opgegeven in het technische profiel.
- **OnItemExistenceInStringCollectionClaim**, alleen uitvoeren wanneer een item in een teken reeks verzamelings claim voor komt.
- **OnItemAbsenceInStringCollectionClaim** kan alleen worden uitgevoerd wanneer een item niet bestaat in een claim voor een teken reeks verzameling.

Voor het gebruik van **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** of **OnItemAbsenceInStringCollectionClaim**, moet u de volgende meta gegevens opgeven: **ClaimTypeOnWhichToEnable** geeft het type van de claim op dat moet worden geëvalueerd, **ClaimValueOnWhichToEnable** geeft de waarde aan die moet worden vergeleken.

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
