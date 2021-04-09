---
title: REST API claim uitwisselingen-Azure Active Directory B2C
description: REST API claims-uitwisselingen toevoegen aan aangepaste beleids regels in Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84053df34ffda0d4686ad80a9e5f3af00ac53d72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94949488"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Walkthrough: REST API claims-uitwisselingen toevoegen aan aangepaste beleids regels in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met Azure Active Directory B2C (Azure AD B2C) kunnen ontwikkel aars van identiteiten een interactie met een REST-API in een gebruikers traject integreren. Aan het einde van dit overzicht kunt u een Azure AD B2C gebruikers traject maken dat samenwerkt met de [rest-Services](custom-policy-rest-api-intro.md).

In dit scenario verrijken we de token gegevens van de gebruiker door te integreren met een zakelijke line-of-Business-werk stroom. Tijdens het aanmelden of aanmelden met een lokaal of federatief account Azure AD B2C roept een REST API op om de uitgebreide profiel gegevens van de gebruiker van een externe gegevens bron op te halen. In dit voor beeld stuurt Azure AD B2C de unieke id van de gebruiker, de objectId. De REST API retourneert vervolgens het account saldo van de gebruiker (een wille keurig getal). Gebruik dit voor beeld als uitgangs punt om te integreren met uw eigen CRM-systeem, marketing database of een zakelijke werk stroom.

U kunt ook de interactie als een technische profiel voor validatie ontwerpen. Dit is geschikt wanneer het REST API gegevens valideert op het scherm en claims retourneert. Zie voor meer informatie [Walkthrough: integreer rest API claims in uw Azure AD B2C gebruikers traject om gebruikers invoer te valideren](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Vereisten

- Voer de stappen in aan de [slag met aangepast beleid](custom-policy-get-started.md). U moet beschikken over een werkend aangepast beleid voor het aanmelden en aanmelden met lokale accounts.
- Meer informatie over het [integreren van rest API claim uitwisselingen in uw aangepaste beleid voor Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Een REST API-eind punt voorbereiden

Voor dit scenario moet u een REST API hebben dat valideert of de Azure AD B2C objectId van een gebruiker is geregistreerd in uw back-end-systeem. Als de REST API is geregistreerd, wordt het gebruikers account saldo geretourneerd. Als dat niet het geval is, registreert de REST API het nieuwe account in de Directory en wordt het begin saldo geretourneerd `50.00` .

De volgende JSON-code illustreert de gegevens Azure AD B2C naar uw REST API-eind punt worden verzonden. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Zodra uw REST API de gegevens valideert, moet deze een HTTP 200 (OK) retour neren met de volgende JSON-gegevens:

```json
{
    "balance": "760.50"
}
```

De installatie van het REST API-eind punt valt buiten het bereik van dit artikel. Er is een [Azure functions](../azure-functions/functions-reference.md) -voor beeld gemaakt. U hebt toegang tot de volledige Azure-functie code op [github](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Claims definiëren

Een claim biedt tijdelijke opslag van gegevens tijdens het uitvoeren van een Azure AD B2C beleid. U kunt claims declareren binnen de sectie [claim schema](claimsschema.md) . 

1. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claims toe aan het **ClaimsSchema** -element.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Het technische profiel van de REST API toevoegen 

Een onderliggend [technisch profiel](restful-technical-profile.md) biedt ondersteuning voor uw eigen rest-service. Azure AD B2C verzendt gegevens naar de REST-service in een `InputClaims` verzameling en ontvangt gegevens terug in een `OutputClaims` verzameling. Zoek het element **ClaimsProviders** in uw <em>**`TrustFrameworkExtensions.xml`**</em> bestand en voeg als volgt een nieuwe claim provider toe:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

In dit voor beeld `userLanguage` wordt de naar de rest-service verzonden `lang` binnen de JSON-nettolading. De waarde van de `userLanguage` claim bevat de huidige gebruikers taal-id. Zie [claim resolver](claim-resolver-overview.md)voor meer informatie.

### <a name="configure-the-restful-api-technical-profile"></a>Het technische profiel van de REST API configureren 

Nadat u uw REST API hebt geïmplementeerd, stelt u de meta gegevens van het `REST-ValidateProfile` technische profiel in op uw eigen rest API, met inbegrip van:

- **ServiceUrl**. Stel de URL van het REST API-eind punt in.
- **SendClaimsIn**. Opgeven hoe de invoer claims worden verzonden naar de claim provider voor de REST.
- **AuthenticationType**. Stel het type verificatie in dat wordt uitgevoerd door de claim provider voor de REST. 
- **AllowInsecureAuthInProduction**. In een productie omgeving moet u deze meta gegevens instellen op `true`
    
Raadpleeg de [meta gegevens van het rest technische profiel](restful-technical-profile.md#metadata) voor meer configuraties.

De opmerkingen hierboven `AuthenticationType` en `AllowInsecureAuthInProduction` Geef de wijzigingen op die u moet aanbrengen wanneer u overstapt naar een productie omgeving. Zie [Secure rest API](secure-rest-api.md)(Engelstalig) voor meer informatie over het beveiligen van uw rest-api's voor productie.

## <a name="add-an-orchestration-step"></a>Een Orchestration-stap toevoegen

Met [gebruikersbelevingen](userjourneys.md) worden expliciete paden opgegeven waarmee een op claims gebaseerde toepassing de gewenste claims voor een gebruiker kan verkrijgen. Een gebruikers traject wordt weer gegeven als een indelings reeks die moet worden gevolgd door een geslaagde trans actie. U kunt Orchestration-stappen toevoegen of aftrekken. In dit geval voegt u een nieuwe Orchestration-stap toe die wordt gebruikt om de gegevens die aan de toepassing worden verstrekt, te verbeteren nadat de gebruiker zich heeft aangemeld of zich aanmeldt via de REST API aanroep.

1. Open het basis bestand van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> .
1. Zoek het- `<UserJourneys>` element. Kopieer het hele element en verwijder het vervolgens.
1. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Plak het `<UserJourneys>` in het extensie bestand na het sluiten van het `<ClaimsProviders>` element.
1. Zoek de `<UserJourney Id="SignUpOrSignIn">` en voeg de volgende Orchestration-stap toe vóór de laatste.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refactoreer de laatste Orchestration-stap door de `Order` to te wijzigen in `8` . De laatste twee indelings stappen moeten er als volgt uitzien:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Herhaal de laatste twee stappen voor de **ProfileEdit** -en **PasswordReset** -gebruikers ritten.


## <a name="include-a-claim-in-the-token"></a>Een claim in het token toevoegen 

`balance`Voeg een uitvoer claim toe aan het bestand om de claim terug te sturen naar de Relying Party-toepassing <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Als u een uitvoer claim toevoegt, wordt de claim verzonden naar het token na een succes volle gebruikers reis. Wijzig het technische profiel element in de sectie Relying Party om toe te voegen `balance` als een uitvoer claim.
 
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
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Herhaal deze stap voor de **ProfileEdit.xml** en **PasswordReset.xml** gebruikers reizen.

Sla de bestanden op die u hebt gewijzigd: *TrustFrameworkBase.xml*, en *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* en *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant bevat door in het bovenste menu op het filter **Map en abonnement** te klikken en de map te kiezen waarin de Azure AD-tenant zich bevindt.
1. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
1. Selecteer een **Framework voor identiteits ervaring**.
1. Selecteer **aangepast beleid uploaden** en upload vervolgens de beleids bestanden die u hebt gewijzigd: *TrustFrameworkBase.xml* en *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* en *PasswordReset.xml*. 
1. Selecteer het registratie-of aanmeldings beleid dat u hebt geüpload en klik op de knop **nu uitvoeren** .
1. U moet zich kunnen aanmelden met een e-mail adres of een Facebook-account.
1. Het token dat teruggestuurd naar uw toepassing bevat de `balance` claim.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het beveiligen van uw Api's:

- [Walkthrough: Integreer REST API claim uitwisselingen in uw Azure AD B2C gebruikers traject als een indelings stap](custom-policy-rest-api-claims-exchange.md)
- [De REST-API beveiligen](secure-rest-api.md)
- [Referentie: technisch profiel (REST)](restful-technical-profile.md)