---
title: Claim uitwisseling REST API als validatie
titleSuffix: Azure AD B2C
description: Een overzicht van het maken van een Azure AD B2C gebruikers traject dat communiceert met de REST-services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 761bc4db7760ef5e84e3fc3c8a5deea5d4508f51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94951924"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>Walkthrough: Integreer REST API claims in uw Azure AD B2C gebruikers traject om gebruikers invoer te valideren

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Met het Identity experience Framework (IEF) dat is onderpinnen Azure Active Directory B2C (Azure AD B2C) kunnen ontwikkel aars van identiteiten een interactie met een REST-API in een gebruikers traject integreren.  Aan het einde van dit overzicht kunt u een Azure AD B2C gebruikers traject maken dat communiceert met de [rest-Services](custom-policy-rest-api-intro.md) om gebruikers invoer te valideren.

In dit scenario voegen we de mogelijkheid toe om een loyaliteits nummer in te voeren op de registratie pagina van Azure AD B2C. We valideren of deze combi natie van e-mail en loyaliteits nummer is toegewezen aan een promotie code door deze gegevens naar een REST API te verzenden. Als de REST API een promotie code voor deze gebruiker vindt, wordt deze teruggestuurd naar Azure AD B2C. Ten slotte wordt de promotie code ingevoegd in de token claims die de toepassing moet gebruiken.

U kunt de interactie ook als een Orchestration-stap ontwerpen. Dit is geschikt wanneer de REST API geen gegevens op het scherm valideert en altijd claims retourneert. Zie voor meer informatie [Walkthrough: integreer rest API claim uitwisselingen in uw Azure AD B2C gebruikers traject als een](custom-policy-rest-api-claims-exchange.md)indelings stap.

## <a name="prerequisites"></a>Vereisten

- Voer de stappen in aan de [slag met aangepast beleid](custom-policy-get-started.md). U moet beschikken over een werkend aangepast beleid voor het aanmelden en aanmelden met lokale accounts.
- Meer informatie over het [integreren van rest API claim uitwisselingen in uw aangepaste beleid voor Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Een REST API-eind punt voorbereiden

Voor dit scenario moet u een REST API hebben dat valideert of een e-mail adres in uw back-end-systeem is geregistreerd met een loyaliteits-ID. Als deze is geregistreerd, moet de REST API een code voor de registratie promotie retour neren die de klant kan gebruiken voor het kopen van goederen in uw toepassing. Anders moet de REST API een HTTP 409-fout bericht retour neren: de loyale ID {getrouwheids-ID} is niet gekoppeld aan het e-mail adres {email}.

De volgende JSON-code illustreert de gegevens Azure AD B2C naar uw REST API-eind punt worden verzonden. 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

Zodra uw REST API de gegevens valideert, moet deze een HTTP 200 (OK) retour neren met de volgende JSON-gegevens:

```json
{
    "promoCode": "24534"
}
```

Als de validatie is mislukt, moet de REST API een HTTP 409 (conflict) retour neren met het `userMessage` JSON-element. De IEF verwacht de `userMessage` claim dat de rest API retourneert. Deze claim wordt weer gegeven als een teken reeks voor de gebruiker als de validatie mislukt.

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Het technische profiel van de REST API toevoegen 

Een onderliggend [technisch profiel](restful-technical-profile.md) biedt ondersteuning voor uw eigen rest-service. Azure AD B2C verzendt gegevens naar de REST-service in een `InputClaims` verzameling en ontvangt gegevens terug in een `OutputClaims` verzameling. Zoek het element **ClaimsProviders** en voeg als volgt een nieuwe claim provider toe:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
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

## <a name="validate-the-user-input"></a>De gebruikers invoer valideren

Als u het loyaliteits nummer van de gebruiker tijdens het aanmelden wilt ophalen, moet u de gebruiker in staat stellen deze gegevens in te voeren op het scherm. Voeg de **loyaltyId** -uitvoer claim toe aan de registratie pagina door deze toe te voegen aan het element van de bestaande technische profiel registratie sectie `OutputClaims` . Geef de volledige lijst met uitvoer claims op om de volg orde te bepalen waarin de claims worden weer gegeven op het scherm.  

Voeg de validatie technische profiel verwijzing toe aan het technische profiel voor aanmelden, waarmee de wordt aangeroepen `REST-ValidateProfile` . Het nieuwe technische profiel voor validatie wordt toegevoegd aan de bovenkant van de `<ValidationTechnicalProfiles>` verzameling die in het basis beleid is gedefinieerd. Dit gedrag houdt in dat u na een geslaagde validatie de Azure AD B2C verplaatst om het account in de Directory te maken.   

1. Zoek het element **ClaimsProviders** . Voeg als volgt een nieuwe claim provider toe:

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>Een claim in het token toevoegen 

Voeg een uitvoer claim toe aan het bestand om de claim code terug te sturen naar de Relying Party-toepassing <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Met de uitvoer claim kan de claim worden toegevoegd aan het token na een geslaagde gebruikers reis, waarna deze wordt verzonden naar de toepassing. Wijzig het technische profiel element in het gedeelte Relying Party om de als uitvoer claim toe te voegen `promoCode` .
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Het aangepaste beleid testen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant bevat door in het bovenste menu op het filter **Map en abonnement** te klikken en de map te kiezen waarin de Azure AD-tenant zich bevindt.
1. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
1. Selecteer een **Framework voor identiteits ervaring**.
1. Selecteer **aangepast beleid uploaden** en upload de beleids bestanden die u hebt gewijzigd: *TrustFrameworkExtensions.xml* en *SignUpOrSignin.xml*. 
1. Selecteer het registratie-of aanmeldings beleid dat u hebt geüpload en klik op de knop **nu uitvoeren** .
1. U moet zich kunnen aanmelden met een e-mail adres.
1. Klik op de koppeling **nu registreren** .
1. Typ 1234 in de **loyaliteits-id** en klik op **door gaan**. Op dit moment moet u een validatie fout bericht ontvangen.
1. Wijzig naar een andere waarde en klik op **door gaan**.
1. Het token dat teruggestuurd naar uw toepassing bevat de `promoCode` claim.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het beveiligen van uw Api's:

- [Walkthrough: Integreer REST API claim uitwisselingen in uw Azure AD B2C gebruikers traject als een indelings stap](custom-policy-rest-api-claims-exchange.md)
- [De REST-API beveiligen](secure-rest-api.md)
- [Referentie: technisch profiel (REST)](restful-technical-profile.md)