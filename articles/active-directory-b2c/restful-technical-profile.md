---
title: Een onderliggend technisch profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een onderliggend technisch profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eb6d82019cccd1da327461cb0a0635aea4f3647f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174968"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een onderliggend technisch profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor de integratie van uw eigen REST-service. Azure AD B2C verzendt gegevens naar de REST-service in een claim verzameling met invoer en ontvangt gegevens terug in een verzameling uitvoer claims. Zie voor meer informatie [integratie rest API claim uitwisselingen in uw aangepaste beleid Azure AD B2C](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `Proprietary` . Het kenmerk **handler** moet de volledig gekwalificeerde naam van de assembly van de protocolhandler bevatten die wordt gebruikt door Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` .

In het volgende voor beeld wordt een onderliggend technisch profiel weer gegeven:

```xml
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Invoer claims

Het **InputClaims** -element bevat een lijst met claims die moeten worden verzonden naar de rest API. U kunt de naam van uw claim ook toewijzen aan de naam die is gedefinieerd in de REST API. In het volgende voor beeld ziet u de toewijzing tussen uw beleid en de REST API. De naam van **de claim wordt** verzonden naar de rest API als **FirstName**, **terwijl de achternaam wordt** verzonden als **LastName**. De **e-mail** claim is ingesteld als is.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Het **InputClaimsTransformations** -element kan een verzameling **InputClaimsTransformation** -elementen bevatten die worden gebruikt om de invoer claims te wijzigen of nieuwe te genereren voordat deze naar de rest API worden verzonden.

## <a name="send-a-json-payload"></a>Een JSON-nettolading verzenden

Met het technische profiel van REST API kunt u een complexe JSON-nettolading verzenden naar een eind punt.

Een complexe JSON-nettolading verzenden:

1. Bouw uw JSON-nettolading met de [GenerateJson](json-transformations.md) -claim transformatie.
1. In het REST API technische profiel:
    1. Een invoer claim transformatie toevoegen met een verwijzing naar de `GenerateJson` claim transformatie.
    1. Stel de `SendClaimsIn` optie meta gegevens in op `body`
    1. Stel de `ClaimUsedForRequestPayload` optie meta gegevens in op de naam van de claim met de JSON-nettolading.
    1. Voeg in de invoer claim een verwijzing toe naar de invoer claim met de JSON-nettolading.

In het volgende voor beeld wordt `TechnicalProfile` een e-mail verificatie verzonden met behulp van een e-mail service van derden (in dit geval SendGrid).

```xml
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
    <Item Key="DefaultUserMessageIfRequestFailed">Cannot process your request right now, please try again later.</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die zijn geretourneerd door de rest API. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de REST API. U kunt ook claims toevoegen die niet worden geretourneerd door de REST API ID-provider, zolang u het kenmerk hebt ingesteld `DefaultValue` .

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

In het volgende voor beeld ziet u de claim die wordt geretourneerd door de REST API:

- De **MembershipId** -claim die is toegewezen aan de naam van de **loyaltyNumber** -claim.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de ID-provider:

- De **loyaltyNumberIsNew** -claim waarvoor een standaard waarde is ingesteld op `true` .

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ServiceUrl | Ja | De URL van het REST API-eind punt. |
| AuthenticationType | Ja | Het type verificatie dat wordt uitgevoerd door de claim provider voor de REST. Mogelijke waarden: `None` , `Basic` , `Bearer` ,  `ClientCertificate` of `ApiKeyHeader` . <br /><ul><li>De `None` waarde geeft aan dat de rest API anoniem is. </li><li>De `Basic` waarde geeft aan dat de rest API is beveiligd met HTTP Basic-verificatie. Alleen geverifieerde gebruikers, met inbegrip van Azure AD B2C, hebben toegang tot uw API. </li><li>De `ClientCertificate` waarde (aanbevolen) geeft aan dat de rest API de toegang beperkt met behulp van verificatie op basis van client certificaten. Alleen services die de juiste certificaten hebben, bijvoorbeeld Azure AD B2C, hebben toegang tot uw API. </li><li>De `Bearer` waarde geeft aan dat de rest API de toegang beperkt met behulp van client OAuth2 Bearer-token. </li><li>De `ApiKeyHeader` waarde geeft aan dat de rest API is beveiligd met de http-header van de API-sleutel, zoals *x-functions Key*. </li></ul> |
| AllowInsecureAuthInProduction| Nee| Hiermee wordt aangegeven of de `AuthenticationType` kan worden ingesteld op `none` in productie omgeving ( `DeploymentMode` van de [TrustFrameworkPolicy](trustframeworkpolicy.md) is ingesteld op `Production` of niet is opgegeven). Mogelijke waarden: True of False (standaard). |
| SendClaimsIn | Nee | Hiermee wordt aangegeven hoe de invoer claims worden verzonden naar de claim provider voor de REST. Mogelijke waarden: `Body` (standaard), `Form` , `Header` `Url` of `QueryString` . De `Body` waarde is de invoer claim die in de hoofd tekst van de aanvraag wordt verzonden in de JSON-indeling. De `Form` waarde is de invoer claim die in de hoofd tekst van de aanvraag wordt verzonden in een ampersand ' & ' gescheiden sleutel waarde-indeling. De `Header` waarde is de invoer claim die in de aanvraag header wordt verzonden. De `Url` waarde is de invoer claim die wordt verzonden in de URL, bijvoorbeeld https://{claim1}. example. com/{claim2}/{claim3}? { claim4} = {claim5}. De `QueryString` waarde is de invoer claim die wordt verzonden in de query teken reeks van de aanvraag. De HTTP-woorden die door elk van beide worden aangeroepen, zijn als volgt:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: Ophalen</li><li>`Url`: Ophalen</li><li>`QueryString`: Ophalen</li></ul> |
| ClaimsFormat | Nee | Momenteel niet gebruikt, kan worden genegeerd. |
| ClaimUsedForRequestPayload| Nee | Naam van een teken reeks claim die de payload bevat die naar de REST API moet worden verzonden. |
| DebugMode | Nee | Voert het technische profiel in de foutopsporingsmodus. Mogelijke waarden: `true` , of `false` (standaard). In de foutopsporingsmodus kan de REST API meer informatie retour neren. Zie de sectie [fout bericht over het retour neren](#returning-validation-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true` , of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true` . |
| ResolveJsonPathsInJsonTokens  | Nee | Hiermee wordt aangegeven of het technische profiel JSON-paden oplost. Mogelijke waarden: `true` , of `false` (standaard). Gebruik deze meta gegevens om gegevens van een genest JSON-element te lezen. Stel in een [output claim](technicalprofiles.md#output-claims)de in `PartnerClaimType` op het JSON-pad-element dat u wilt uitvoeren. Bijvoorbeeld: `firstName.localized` , of `data.0.to.0.email` .|
| UseClaimAsBearerToken| Nee| De naam van de claim die het Bearer-token bevat.|

## <a name="error-handling"></a>Foutafhandeling

De volgende meta gegevens kunnen worden gebruikt voor het configureren van de fout berichten die worden weer gegeven bij REST API fout. De fout berichten kunnen worden [gelokaliseerd](localization-string-ids.md#restful-service-error-messages).

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| DefaultUserMessageIfRequestFailed | Nee | Een standaard aangepast fout bericht voor alle uitzonde ringen van REST API.|
| UserMessageIfCircuitOpen | Nee | Fout bericht wanneer de REST API niet bereikbaar is. Als u niets opgeeft, wordt de DefaultUserMessageIfRequestFailed geretourneerd. |
| UserMessageIfDnsResolutionFailed | Nee | Fout bericht voor de uitzonde ring van de DNS-omzetting. Als u niets opgeeft, wordt de DefaultUserMessageIfRequestFailed geretourneerd. | 
| UserMessageIfRequestTimeout | Nee | Fout bericht wanneer er een time-out optreedt voor de verbinding. Als u niets opgeeft, wordt de DefaultUserMessageIfRequestFailed geretourneerd. | 

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Als het type verificatie is ingesteld op `None` , wordt het **CryptographicKeys** -element niet gebruikt.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Als het type verificatie is ingesteld op `Basic` , bevat het **CryptographicKeys** -element de volgende kenmerken:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ja | De gebruikers naam die wordt gebruikt voor verificatie. |
| BasicAuthenticationPassword | Ja | Het wacht woord dat wordt gebruikt voor verificatie. |

In het volgende voor beeld ziet u een technisch profiel met basis verificatie:

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Als het type verificatie is ingesteld op `ClientCertificate` , bevat het **CryptographicKeys** -element het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| ClientCertificate | Ja | Het x509-certificaat (RSA key set) dat moet worden gebruikt voor verificatie. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Als het type verificatie is ingesteld op `Bearer` , bevat het **CryptographicKeys** -element het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Nee | Het OAuth 2,0 Bearer-token. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

Als het type verificatie is ingesteld op `ApiKeyHeader` , bevat het **CryptographicKeys** -element het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| De naam van de HTTP-header, zoals `x-functions-key` of `x-api-key` . | Ja | De sleutel die wordt gebruikt om te verifiëren. |

> [!NOTE]
> Op dit moment ondersteunt Azure AD B2C slechts één HTTP-header voor verificatie. Als voor uw REST-aanroep meerdere headers zijn vereist, zoals een client-ID en client geheim, moet u de aanvraag op een of andere manier proxy.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ApiKeyHeader</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-validation-error-message"></a>Validatie fout bericht retour neren

Uw REST API moet mogelijk een fout bericht retour neren, zoals ' de gebruiker is niet gevonden in het CRM-systeem '. Als er een fout optreedt, moet de REST API een HTTP 4xx-fout bericht retour neren, zoals 400 (ongeldige aanvraag) of 409 (conflict) status code. De antwoord tekst bevat een fout bericht dat is ingedeeld in JSON:

```json
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| versie | Ja | Uw REST API-versie. Bijvoorbeeld: 1.0.1 |
| status | Ja | Moet 409 zijn |
| code | Nee | Een fout code van de REST-eindpunt provider, die wordt weer gegeven wanneer `DebugMode` is ingeschakeld. |
| requestId | Nee | Een aanvraag-id van de REST-eindpunt provider, die wordt weer gegeven wanneer `DebugMode` is ingeschakeld. |
| userMessage | Ja | Een fout bericht dat wordt weer gegeven aan de gebruiker. |
| developerMessage | Nee | De uitgebreide beschrijving van het probleem en hoe het kan worden opgelost, dat wordt weer gegeven wanneer `DebugMode` is ingeschakeld. |
| moreInfo | Nee | Een URI die verwijst naar extra informatie, die wordt weer gegeven wanneer `DebugMode` is ingeschakeld. |


Het volgende voor beeld toont een C#-klasse die een fout bericht retourneert:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor voor beelden van het gebruik van een reactief technisch profiel:

- [REST API claim uitwisselingen integreren in uw aangepaste beleid voor Azure AD B2C](custom-policy-rest-api-intro.md)
- [Walkthrough: REST API claims-uitwisselingen integreren in uw Azure AD B2C gebruikers door voeren als validatie van gebruikers invoer](custom-policy-rest-api-claims-validation.md)
- [Walkthrough: REST API claims-uitwisselingen toevoegen aan aangepaste beleids regels in Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Uw REST API Services beveiligen](secure-rest-api.md)
