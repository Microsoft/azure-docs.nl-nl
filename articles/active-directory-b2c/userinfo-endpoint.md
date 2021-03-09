---
title: User info-eind punt | Microsoft Docs
description: Definieer een user info-eind punt in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c060a029b1cdbdd890ced96cab732966cb652de0
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500577"
---
# <a name="userinfo-endpoint"></a>Eindpunt voor gebruikersinfo

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Het user info-eind punt maakt deel uit van de OIDC-specificatie ( [OpenID Connect Connect Standard](https://openid.net/specs/openid-connect-core-1_0.html#UserInfo) ) en is ontworpen om claims te retour neren over de geverifieerde gebruiker. Het user info-eind punt wordt gedefinieerd in het Relying Party-beleid met behulp van het element [endpoint](relyingparty.md#endpoints) .  

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="userinfo-endpoint-overview"></a>Informatie over het user info-eind punt

De gebruikers gegevens UserJourney geeft het volgende aan:

- **Autorisatie**: het user info-eind punt wordt beveiligd met een Bearer-token. Een uitgegeven toegangs token wordt weer gegeven in de autorisatie-header voor het user info-eind punt. Met het beleid wordt het technische profiel opgegeven dat het inkomende token valideert en worden claims opgehaald, zoals de objectId van de gebruiker. De objectId van de gebruiker wordt gebruikt om de claims op te halen die moeten worden geretourneerd in het antwoord van de user info endpoint-reis. 
- **Orchestration-stap**: 
  - Er wordt een indelings stap gebruikt voor het verzamelen van informatie over de gebruiker. Op basis van de claims binnen het binnenkomende toegangs token roept de gebruikers traject een [Azure Active Directory technisch profiel](active-directory-technical-profile.md) op om gegevens over de gebruiker op te halen, bijvoorbeeld door de gebruiker te lezen door de objectId. 
  - **Optionele** indelings stappen: u kunt meer Orchestration-stappen toevoegen, zoals een rest API technisch profiel om meer informatie over de gebruiker op te halen. 
  - **User info-verlener** : Hiermee geeft u de lijst met claims op die het user info-eind punt retourneert.

## <a name="create-a-userinfo-endpoint"></a>Een user info-eind punt maken

### <a name="1-add-the-token-issuer-technical-profile"></a>1. het technische profiel voor de token uitgever toevoegen

1. Open het *TrustFrameworkExtensions.xml* -bestand.
1. Als deze nog niet bestaat, voegt u een ClaimsProvider-element en de onderliggende elementen toe als het eerste element onder het element BuildingBlocks.
1. Voeg de volgende claim provider toe:

    ```xml
    <!-- 
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Token Issuer</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="UserInfoIssuer">
            <DisplayName>JSON Issuer</DisplayName>
            <Protocol Name="None" />
            <OutputTokenFormat>JSON</OutputTokenFormat>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <!-- The Below claims are what will be returned on the UserInfo Endpoint if in the Claims Bag-->
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId"/>
              <InputClaim ClaimTypeReferenceId="givenName"/>
              <InputClaim ClaimTypeReferenceId="surname"/>
              <InputClaim ClaimTypeReferenceId="displayName"/>
              <InputClaim ClaimTypeReferenceId="signInNames.emailAddress"/>
            </InputClaims>
          </TechnicalProfile>
          <TechnicalProfile Id="UserInfoAuthorization">
            <DisplayName>UserInfo authorization</DisplayName>
            <Protocol Name="None" />
            <InputTokenFormat>JWT</InputTokenFormat>
            <Metadata>
              <!-- Update the Issuer and Audience below -->
              <!-- Audience is optional, Issuer is required-->
              <Item Key="issuer">https://yourtenant.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/</Item>
              <Item Key="audience">[ "22222222-2222-2222-2222-222222222222", "33333333-3333-3333-3333-333333333333" ]</Item>
              <Item Key="client_assertion_type">urn:ietf:params:oauth:client-assertion-type:jwt-bearer</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
              <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" PartnerClaimType="email"/>
              <!-- Optional claims to read from the access token. -->
              <!-- <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
                 <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
                 <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/> -->
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!-- 
    </ClaimsProviders> -->
    ``` 

1. De sectie InputClaims binnen het technische profiel **UserInfoIssuer** geeft de kenmerken op die u wilt retour neren. Het technische profiel UserInfoIssuer wordt aan het einde van de gebruikers reis aangeroepen. 
1. Het technische profiel **UserInfoAuthorization** valideert de hand tekening, naam van de verlener en de token doelgroep en extraheert de claim van het inkomende token. Wijzig de volgende meta gegevens zodat deze overeenkomen met uw omgeving:
    1. **verlener** : deze waarde moet gelijk zijn aan de `iss` claim binnen de claim van het toegangs token. Tokens die zijn uitgegeven door Azure AD B2C gebruiken een uitgever in de indeling `https://yourtenant.b2clogin.com/your-tenant-id/v2.0/` . Meer informatie over het [aanpassen van tokens](configure-tokens.md).
    1. **IdTokenAudience** -moet identiek zijn aan de `aud` claim binnen de claim van het toegangs token. In Azure AD B2C `aud` is de claim de id van uw Relying Party toepassing. Deze waarde is een verzameling en ondersteunt meerdere waarden met behulp van een komma als scheidings teken.

        In het volgende toegangs token is de `iss` claim waarde `https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/` . De `aud` claim waarde is `22222222-2222-2222-2222-222222222222` .

        ```json
        {
          "exp": 1605549468,
          "nbf": 1605545868,
          "ver": "1.0",
          "iss": "https://contoso.b2clogin.com/11111111-1111-1111-1111-111111111111/v2.0/",
          "sub": "44444444-4444-4444-4444-444444444444",
          "aud": "22222222-2222-2222-2222-222222222222",
          "acr": "b2c_1a_signup_signin",
          "nonce": "defaultNonce",
          "iat": 1605545868,
          "auth_time": 1605545868,
          "name": "John Smith",
          "given_name": "John",
          "family_name": "Smith",
          "tid": "11111111-1111-1111-1111-111111111111"
        }
        ```
    
1.  Het OutputClaims-element van het technische profiel **UserInfoAuthorization** geeft de kenmerken op die u wilt lezen uit het toegangs token. De **ClaimTypeReferenceId** is de verwijzing naar een claim type. De optionele **PartnerClaimType** is de naam van de claim die in het toegangs token is gedefinieerd.



### <a name="2-add-the-userjourney-element"></a>2. Voeg het element UserJourney toe 

Het [UserJourney](userjourneys.md) -element definieert het pad dat de gebruiker nodig heeft bij interactie met uw toepassing. Voeg het **UserJourneys** -element toe als het niet bestaat met de **UserJourney** geïdentificeerd als `UserInfoJourney` :

```xml
<!-- 
<UserJourneys> -->
  <UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
    <Authorization>
      <AuthorizationTechnicalProfiles>
        <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
      </AuthorizationTechnicalProfiles>
    </Authorization>
    <OrchestrationSteps >
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>objectId</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges UserIdentity="false">
          <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="UserInfoIssuer" />
    </OrchestrationSteps>
  </UserJourney>
<!-- 
</UserJourneys> -->
```

### <a name="3-include-the-endpoint-to-the-relying-party-policy"></a>3. Neem het eind punt op in het Relying Party beleid

Als u het user info-eind punt wilt toevoegen aan de Relying Party toepassing, voegt u een [eindpunt](relyingparty.md#endpoints) element toe aan het bestand *SocialAndLocalAccounts/SignUpOrSignIn.xml* . 

```xml
<!--
<RelyingParty> -->
  <Endpoints>
    <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
  </Endpoints>
<!-- 
</RelyingParty> -->
```

Het voltooide Relying Party-element ziet er als volgt uit:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="yourtenant.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin">
  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <Endpoints>
      <Endpoint Id="UserInfo" UserJourneyReferenceId="UserInfoJourney" />
    </Endpoints>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="4-upload-the-files"></a>4. de bestanden uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer een **Framework voor identiteits ervaring**.
1. Op de pagina **aangepast beleid** selecteert u **aangepast beleid uploaden**.
1. Selecteer **het aangepaste beleid overschrijven als dit al bestaat** en zoek en selecteer het *TrustframeworkExtensions.xml* bestand.
1. Klik op **Uploaden**.
1. Herhaal stap 5 tot en met 7 voor het Relying Party bestand, zoals *SignUpOrSignIn.xml*.

## <a name="calling-the-userinfo-endpoint"></a>Het user info-eind punt aanroepen

Het user info-eind punt maakt gebruik van de standaard OAuth2 Bearer-token-API, die wordt aangeroepen met behulp van het toegangs token dat is ontvangen bij het ophalen van een token voor uw toepassing. Het retourneert een JSON-antwoord met claims over de gebruiker. Het user info-eind punt wordt gehost op Azure AD B2C op:

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/openid/v2.0/userinfo
```

In het/.well-known configure endpoint (OpenID Connect Connect Discovery-document) wordt het veld weer gegeven `userinfo_endpoint` . U kunt het user info-eind punt programmatisch detecteren met het/.well-known configure endpoint op: 

```http
https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com/policy-name/v2.0/.well-known/openid-configuration 
```

### <a name="test-the-policy"></a>Het beleid testen

1. Onder **aangepast beleid** selecteert u het beleid waarmee u het user info-eind punt hebt geïntegreerd met. Bijvoorbeeld *B2C_1A_SignUpOrSignIn*.
1. Selecteer **nu uitvoeren**. 
1. Onder **toepassing selecteren** selecteert u de toepassing die u eerder hebt geregistreerd. Kies bij **Selecteer antwoord**-URL `https://jwt.ms` . Zie [een webtoepassing registreren in azure Active Directory B2C](tutorial-register-applications.md)voor meer informatie.
1. Meld u aan of Meld u aan met een e-mail adres of een sociaal account.
1. Kopieer de id_token in de gecodeerde indeling van de [https://jwt.ms](https://jwt.ms) website. U kunt dit gebruiken om een GET-aanvraag in te dienen bij het user info-eind punt en de gebruikers gegevens op te halen.
1. Een GET-aanvraag indienen bij het user info-eind punt en de gebruikers gegevens ophalen.

```http
GET /yourtenant.onmicrosoft.com/b2c_1a_signup_signin/openid/v2.0/userinfo
Host: b2cninja.b2clogin.com
Authorization: Bearer <your access token>
```

Een geslaagde reactie zou er als volgt uitzien:

```json
{
    "objectId": "44444444-4444-4444-4444-444444444444",
    "givenName": "John",
    "surname": "Smith",
    "displayName": "John Smith",
    "signInNames.emailAddress": "john.s@contoso.com"
}
```

## <a name="next-steps"></a>Volgende stappen

- U kunt een voor beeld van een user info-eindpunt beleid vinden op [github](https://github.com/azure-ad-b2c/samples/tree/master/policies/user-info-endpoint).

::: zone-end
