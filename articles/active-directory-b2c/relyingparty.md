---
title: RelyingParty-Azure Active Directory B2C | Microsoft Docs
description: Geef het RelyingParty-element van een aangepast beleid in Azure Active Directory B2C op.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9c50bd71f4e2e5bbe12518f5a5d1cd486af9723a
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509748"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Het element **RelyingParty** geeft aan dat de gebruikers traject moet worden afgedwongen voor de huidige aanvraag om Azure Active Directory B2C (Azure AD B2C). Ook wordt de lijst met claims opgegeven die de toepassing Relying Party (RP) nodig heeft als onderdeel van het gepubliceerde token. Een RP-toepassing, zoals een web-, mobiele of bureaublad toepassing, roept het RP-beleids bestand aan. Het RP-beleids bestand voert een specifieke taak uit, zoals het aanmelden, het opnieuw instellen van een wacht woord of het bewerken van een profiel. Meerdere toepassingen kunnen hetzelfde RP-beleid gebruiken en één toepassing kan meerdere beleids regels gebruiken. Alle RP-toepassingen ontvangen hetzelfde token met claims en de gebruiker verloopt over hetzelfde traject van de gebruiker.

In het volgende voor beeld ziet u een **RelyingParty** -element in het *B2C_1A_signup_signin* -beleids bestand:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Het optionele **RelyingParty** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | De standaard gebruikers traject voor de RP-toepassing. |
| Eindpunten | 0:1 | Een lijst met eind punten. Zie [User info-eind punt](userinfo-endpoint.md)voor meer informatie. |
| UserJourneyBehaviors | 0:1 | Het bereik van het gedrag van de reis van de gebruiker. |
| TechnicalProfile | 1:1 | Een technisch profiel dat wordt ondersteund door de RP-toepassing. Het technische profiel biedt een contract voor de RP-toepassing om contact op te nemen met Azure AD B2C. |

## <a name="endpoints"></a>Eindpunten

Het element **endpoints** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Eindpunt | 1:1 | Een verwijzing naar een eind punt.|

Het element **endpoint** bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Yes | Een unieke id van het eind punt.|
| UserJourneyReferenceId | Yes | Een id van de gebruikers traject in het beleid. Zie voor meer informatie [gebruikers ritten](userjourneys.md)  | 

In het volgende voor beeld ziet u een Relying Party met het [User info-eind punt](userinfo-endpoint.md):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
  ...
```

## <a name="defaultuserjourney"></a>DefaultUserJourney

Het `DefaultUserJourney` element bevat een verwijzing naar de id van de gebruikers traject die meestal wordt gedefinieerd in het basis-of uitbrei ding beleid. In de volgende voor beelden ziet u de reis registratie of aanmeldings gebruiker opgegeven in het **RelyingParty** -element:

*B2C_1A_signup_signin* beleid:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* of *B2C_1A_TrustFrameworkExtensionPolicy*:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Het element **DefaultUserJourney** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ReferenceId | Yes | Een id van de gebruikers traject in het beleid. Zie voor meer informatie [gebruikers ritten](userjourneys.md) |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Het **UserJourneyBehaviors** -element bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Het bereik van het gedrag van de sessie voor eenmalige aanmelding (SSO) van een gebruikers traject. |
| SessionExpiryType |0:1 | Het verificatie gedrag van de sessie. Mogelijke waarden: `Rolling` of `Absolute` . De `Rolling` waarde (standaard) geeft aan dat de gebruiker aangemeld blijft, zolang de gebruiker voortdurend actief is in de toepassing. De `Absolute` waarde geeft aan dat de gebruiker opnieuw moet worden geverifieerd na de tijds periode die is opgegeven voor de levens duur van de toepassings sessie. |
| SessionExpiryInSeconds | 0:1 | De levens duur van de Azure AD B2C's-sessie cookie die is opgegeven als een geheel getal dat is opgeslagen in de browser van de gebruiker bij geslaagde verificatie. |
| JourneyInsights | 0:1 | De Azure-toepassing Insights-instrumentatie sleutel die moet worden gebruikt. |
| ContentDefinitionParameters | 0:1 | De lijst met sleutel waardeparen die moeten worden toegevoegd aan de laad-URI van de inhouds definitie. |
|ScriptExecution| 0:1| De ondersteunde modi voor [Java script](javascript-and-page-layout.md) -uitvoering. Mogelijke waarden: `Allow` of `Disallow` (standaard).

### <a name="singlesignon"></a>SingleSignOn

Het element **SingleSignOn** bevat in het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Bereik | Yes | Het bereik van het gedrag bij eenmalige aanmelding. Mogelijke waarden: `Suppressed` , `Tenant` , `Application` of `Policy` . De `Suppressed` waarde geeft aan dat het gedrag wordt onderdrukt en dat de gebruiker altijd wordt gevraagd een id-provider te selecteren.  De `Tenant` waarde geeft aan dat het gedrag wordt toegepast op alle beleids regels in de Tenant. Bijvoorbeeld: een gebruiker die door twee beleids ritten voor een Tenant navigeert, wordt niet gevraagd om een id-provider te selecteren. De `Application` waarde geeft aan dat het gedrag wordt toegepast op alle beleids regels voor de toepassing die de aanvraag maakt. Bijvoorbeeld: een gebruiker die door twee beleids ritten voor een toepassing navigeert, wordt niet gevraagd om een id-provider te selecteren. De `Policy` waarde geeft aan dat het gedrag alleen van toepassing is op een beleid. Bijvoorbeeld: een gebruiker die door twee beleids trajecten voor een vertrouwens raamwerk navigeert, wordt gevraagd een id-provider selectie in te scha kelen bij het overschakelen tussen de beleids regels. |
| KeepAliveInDays | Yes | Hiermee wordt bepaald hoe lang de gebruiker aangemeld blijft. Als u de waarde instelt op 0, wordt de KMSI-functionaliteit uitgeschakeld. Zie [me aangemeld blijven](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi)voor meer informatie. |
|EnforceIdTokenHintOnLogout| No|  Forceren dat een eerder uitgegeven ID-token wordt door gegeven aan het afmeldings eindpunt als hint voor de huidige geverifieerde sessie van de eind gebruiker met de client. Mogelijke waarden: `false` (standaard) of `true` . Zie voor meer informatie [Web Sign-in with OpenID Connect Connect](openid-connect.md).  |


## <a name="journeyinsights"></a>JourneyInsights

Het **JourneyInsights** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| TelemetryEngine | Yes | De waarde moet zijn `ApplicationInsights` . |
| InstrumentationKey | Yes | De teken reeks die de instrumentatie sleutel voor het Application Insights-element bevat. |
| DeveloperMode | Yes | Mogelijke waarden: `true` of `false` . Als `true` Application Insights de telemetrie versnellen door de verwerkings pijplijn. Deze instelling is geschikt voor ontwikkeling, maar is beperkt bij hoge volumes. De gedetailleerde activiteiten logboeken zijn alleen ontworpen om te helpen bij het ontwikkelen van aangepast beleid. Gebruik de ontwikkelings modus niet in productie. In Logboeken worden alle claims verzameld die worden verzonden naar en van de id-providers tijdens de ontwikkeling. Bij gebruik in productie neemt de ontwikkelaar de verantwoordelijkheid voor PII (privé Identificeer bare informatie) die is verzameld in het logboek van de app Insights waarvan ze eigenaar zijn. Deze gedetailleerde logboeken worden alleen verzameld wanneer deze waarde is ingesteld op `true` .|
| ClientEnabled | Yes | Mogelijke waarden: `true` of `false` . Indien `true` , verzendt het Application Insights script aan de client zijde voor het bijhouden van de pagina weergave en fouten aan de client zijde. |
| ServerEnabled | Yes | Mogelijke waarden: `true` of `false` . Als `true` de bestaande UserJourneyRecorder-JSON als aangepaste gebeurtenis wordt verzonden naar Application Insights. |
| TelemetryVersion | Yes | De waarde moet zijn `1.0.0` . |

Zie [Logboeken verzamelen](troubleshoot-with-application-insights.md) voor meer informatie

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Door aangepaste beleids regels te gebruiken in Azure AD B2C, kunt u een para meter verzenden in een query reeks. Door de parameter door te geven aan uw HTML-eindpunt, kunt u de pagina-inhoud dynamisch wijzigen. U kunt bijvoorbeeld de achtergrondafbeelding op de registratie- of aanmeldingspagina van Azure AD B2C wijzigen, op basis van een parameter die u doorgeeft vanuit uw web- of mobiele toepassing. Azure AD B2C geeft de query reeks parameters door aan uw Dynamic HTML-bestand, zoals een aspx-bestand.

In het volgende voor beeld wordt een para meter met de naam `campaignId` met een waarde van `hawaii` in de query reeks door gegeven:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Het element **ContentDefinitionParameters** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0: n | Een teken reeks die het sleutel waarde-paar bevat dat is toegevoegd aan de query reeks van een load-URI voor de inhouds definitie. |

Het element **ContentDefinitionParameter** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Name | Yes | De naam van het sleutel waarde-paar. |

Zie [de gebruikers interface configureren met dynamische inhoud met behulp van aangepast beleid](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri) voor meer informatie.

## <a name="technicalprofile"></a>TechnicalProfile

Het element **TechnicalProfile** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Id | Yes | De waarde moet zijn `PolicyProfile` . |

De **TechnicalProfile** bevat de volgende elementen:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | De teken reeks die de naam van het technische profiel bevat. |
| Beschrijving | 0:1 | De teken reeks die de beschrijving van het technische profiel bevat. |
| Protocol | 1:1 | Het protocol dat wordt gebruikt voor de Federatie. |
| Metagegevens | 0:1 | De verzameling sleutel  /waarde-paren die wordt gebruikt door het protocol voor communicatie met het eind punt in de loop van een trans actie om de interactie tussen de Relying Party en andere deel nemers van de community te configureren. |
| OutputClaims | 1:1 | Een lijst met claim typen die worden beschouwd als uitvoer in het technische profiel. Elk van deze elementen bevat een verwijzing naar een **claim** type dat al is gedefinieerd in de sectie **ClaimsSchema** of in een beleid van waaruit dit beleids bestand wordt overgenomen. |
| SubjectNamingInfo | 1:1 | De onderwerpnaam die wordt gebruikt in tokens. |

Het **protocol** element bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Name | Yes | De naam van een geldig protocol dat wordt ondersteund door Azure AD B2C dat wordt gebruikt als onderdeel van het technische profiel. Mogelijke waarden: `OpenIdConnect` of `SAML2` . De `OpenIdConnect` waarde vertegenwoordigt de OpenID Connect Connect 1,0-protocol norm als per OpenID Connect Foundation-specificatie. De `SAML2` vertegenwoordigt het SAML 2,0-protocol standaard als per Oasis-specificatie. |

### <a name="metadata"></a>Metagegevens

Wanneer het protocol is `SAML` , bevat een meta gegevenselement de volgende elementen.

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| IdpInitiatedProfileEnabled | No | Hiermee wordt aangegeven of de door IDP geïnitieerde stroom wordt ondersteund. Mogelijke waarden: `true` of `false` (standaard). | 
| XmlSignatureAlgorithm | No | De methode die Azure AD B2C gebruikt voor het ondertekenen van het SAML-antwoord. Mogelijke waarden: `Sha256` , `Sha384` , `Sha512` of `Sha1` . Zorg ervoor dat u het handtekening algoritme aan beide zijden met dezelfde waarde configureert. Gebruik alleen de algoritme die door uw certificaat wordt ondersteund. Zie [SAML-Uitgever van technische profielen](saml-issuer-technical-profile.md#metadata)voor meer informatie over het configureren van de SAML-bevestiging. |
| DataEncryptionMethod | No | Hiermee wordt de methode aangegeven die Azure AD B2C gebruikt voor het versleutelen van de gegevens met behulp van het Advanced Encryption Standard-algoritme (AES). De meta gegevens bepalen de waarde van het `<EncryptedData>` element in het SAML-antwoord. Mogelijke waarden: `Aes256` (standaard), `Aes192` , `Sha512` of ` Aes128` . |
| KeyEncryptionMethod| No | Hiermee wordt de methode aangegeven die Azure AD B2C gebruikt voor het versleutelen van de kopie van de sleutel die is gebruikt voor het versleutelen van de gegevens. De meta gegevens bepalen de waarde van het  `<EncryptedKey>` element in het SAML-antwoord. Mogelijke waarden: ` Rsa15` (standaard)-RSA-algoritme (open bare sleutel crypto grafie Standard) versie 1,5, ` RsaOaep` -RSA OAEP-versleutelings algoritme (optimale asymmetrische-coderings opvulling). |
| UseDetachedKeys | No |  Mogelijke waarden: `true` , of `false` (standaard). Wanneer de waarde is ingesteld op `true` , Azure AD B2C de indeling van de versleutelde beweringen wijzigen. Als u ontkoppelde sleutels gebruikt, wordt de versleutelde bewering toegevoegd als onderliggend element van de EncrytedAssertion in plaats van de EncryptedData. |
| WantsSignedResponses| No | Hiermee wordt aangegeven of Azure AD B2C de `Response` sectie van het SAML-antwoord ondertekent. Mogelijke waarden: `true` (standaard) of `false` .  |

### <a name="outputclaims"></a>OutputClaims

Het element **OutputClaims** bevat het volgende element:

| Element | Instanties | Beschrijving |
| ------- | ----------- | ----------- |
| Output claim | 0: n | De naam van een verwacht claim type in de lijst met ondersteunde typen voor het beleid waarop de Relying Party zich abonneert. Deze claim dient als uitvoer voor het technische profiel. |

Het **output claim** -element bevat de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Yes | Een verwijzing naar een **claim** type dat al is gedefinieerd in de sectie **ClaimsSchema** in het beleids bestand. |
| Standaard | No | Een standaard waarde die kan worden gebruikt als de claim waarde leeg is. |
| PartnerClaimType | No | Verzendt de claim in een andere naam zoals deze is geconfigureerd in de definitie claim type. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Met het **SubjectNameingInfo** -element bepaalt u de waarde van het onderwerp van de token:
- **JWT-token** -de `sub` claim. Dit is een principal waarvan het token informatie bedient, zoals de gebruiker van een toepassing. Deze waarde is onveranderbaar en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het kan worden gebruikt om veilige autorisatie controles uit te voeren, zoals wanneer het token wordt gebruikt om toegang te krijgen tot een bron. Standaard wordt de subject claim gevuld met de object-ID van de gebruiker in de Directory. Zie [token, sessie en configuratie van eenmalige aanmelding](session-behavior.md)voor meer informatie.
- **SAML-token** : het `<Subject><NameID>` element waarmee het subject-element wordt aangeduid. De NameID-indeling kan worden gewijzigd.

Het element **SubjectNamingInfo** bevat het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| Claim type | Yes | Een verwijzing naar de **PartnerClaimType** van een uitvoer claim. De uitvoer claims moeten worden gedefinieerd in de **OutputClaims** -verzameling van het Relying Party-beleid. |
| Indeling | No | Wordt gebruikt voor SAML-relying party's voor het instellen van de **NameID-indeling** die wordt geretourneerd in de SAML-bevestiging. |

In het volgende voor beeld ziet u hoe u een OpenID Connect Connect-Relying Party definieert. De onderwerpnaam informatie is geconfigureerd als `objectId` :

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
De JWT-token bevat de `sub` claim met het objectId van de gebruiker:

```json
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```

In het volgende voor beeld ziet u hoe u een SAML-Relying Party definieert. De informatie over de onderwerpnaam is geconfigureerd als de `objectId` , en het NameID `format` is opgegeven:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient"/>
  </TechnicalProfile>
</RelyingParty>
```
