---
title: Een gegevens stroom voor het wacht woord voor een resource-eigenaar instellen
titleSuffix: Azure AD B2C
description: Meer informatie over het instellen van de ROPC-stroom (resource owner password credentials) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a7c93bb0e0dcc51e35bc27fa0799d8410e66df6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581878"
---
# <a name="set-up-a-resource-owner-password-credentials-flow-in-azure-active-directory-b2c"></a>Een wacht woord voor de gegevens stroom van een resource-eigenaar instellen in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In Azure Active Directory B2C (Azure AD B2C) is de stroom voor het wacht woord voor de resource-eigenaar (ROPC) een OAuth-standaard verificatie stroom. In deze stroom wordt een toepassing, ook wel bekend als de Relying Party, de uitwisseling van geldige referenties voor tokens. De referenties bevatten een gebruikers-ID en wacht woord. De geretourneerde tokens zijn een ID-token, toegangs token en een vernieuwings token.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]


## <a name="ropc-flow-notes"></a>ROPC-stroom notities

In Azure Active Directory B2C (Azure AD B2C) worden de volgende opties ondersteund:

- **Systeem eigen client**: gebruikers interactie tijdens verificatie gebeurt wanneer code wordt uitgevoerd op een apparaat aan de gebruiker. Het apparaat kan een mobiele toepassing zijn die wordt uitgevoerd in een systeem eigen besturings systeem, zoals Android en iOS.
- **Open bare client stroom**: alleen gebruikers referenties, verzameld door een toepassing, worden in de API-aanroep verzonden. De referenties van de toepassing worden niet verzonden.
- **Nieuwe claims toevoegen**: de inhoud van het id-token kan worden gewijzigd om nieuwe claims toe te voegen.

De volgende stromen worden niet ondersteund:

- **Server-naar-server: voor** het systeem voor identiteits beveiliging is een betrouw bare IP-adres nodig dat door de aanroeper (de systeem eigen client) is verzameld als onderdeel van de interactie. In een API-aanroep aan de server zijde wordt alleen het IP-adres van de server gebruikt. Als een dynamische drempel van mislukte authenticaties wordt overschreden, kan het identiteits beschermings systeem een herhaald IP-adres identificeren als een aanvaller.
- **Vertrouwelijke client stroom**: de client-id van de toepassing wordt gevalideerd, maar het toepassings geheim wordt niet gevalideerd.

Wanneer u de ROPC-stroom gebruikt, moet u rekening houden met het volgende:

- ROPC werkt niet wanneer er sprake is van een onderbreking van de verificatie stroom die gebruikers interactie nodig heeft. Wanneer bijvoorbeeld een wacht woord is verlopen of moet worden gewijzigd, is [multi-factor Authentication](multi-factor-authentication.md) vereist of wanneer er meer informatie moet worden verzameld tijdens het aanmelden (bijvoorbeeld toestemming van de gebruiker).
- ROPC ondersteunt alleen lokale accounts. Gebruikers kunnen zich niet aanmelden met [federatieve id-providers](add-identity-provider.md) zoals micro soft, Google +, Twitter, AD-FS of Facebook.
- [Sessie beheer](session-behavior.md), met inbegrip van [aanmelden (KMSI)](session-behavior.md#enable-keep-me-signed-in-kmsi), is niet van toepassing.


## <a name="register-an-application"></a>Een toepassing registreren

[!INCLUDE [active-directory-b2c-appreg-ropc](../../includes/active-directory-b2c-appreg-ropc.md)]

::: zone pivot="b2c-user-flow"

##  <a name="create-a-resource-owner-user-flow"></a>Een gebruikers stroom van een resource-eigenaar maken

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij Azure Portal.
2. Als u wilt overschakelen naar uw Azure AD B2C Tenant, selecteert u de map B2C in de rechter bovenhoek van de portal.
3. Selecteer **gebruikers stromen** en selecteer **nieuwe gebruikers stroom**.
4. Selecteer **Aanmelden met wachtwoord referenties voor de resource-eigenaar (ROPC)**.
5. Controleer onder **versie** of **Preview** is geselecteerd en selecteer **maken**.
7. Geef een naam op voor de gebruikers stroom, bijvoorbeeld *ROPC_Auth*.
8. Klik onder **toepassings claims** op **weer geven**.
9. Selecteer de toepassings claims die u nodig hebt voor uw toepassing, zoals weergave naam, e-mail adres en ID-provider.
10. Selecteer **OK** en selecteer vervolgens **maken**.

::: zone-end

::: zone pivot="b2c-custom-policy"

##  <a name="create-a-resource-owner-policy"></a>Een beleid voor de eigenaar van een resource maken

1. Open het *TrustFrameworkExtensions.xml* -bestand.
2. Als deze nog niet bestaat, voegt u een **ClaimsSchema** -element en de onderliggende elementen toe als het eerste element onder het element **BuildingBlocks** :

    ```xml
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Voeg na **ClaimsSchema** een **ClaimsTransformations** -element en de onderliggende elementen toe aan het **BuildingBlocks** -element:

    ```xml
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>

      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Zoek het **ClaimsProvider** -element met een **DisplayName** van `Local Account SignIn` en voeg het volgende technische profiel toe:

    ```xml
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
        <Item Key="grant_type">password</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppId" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppId" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Vervang de **DefaultValue** van **Client_id** door de toepassings-id van de ProxyIdentityExperienceFramework-toepassing die u hebt gemaakt in de hand leiding voor vereisten. Vervang vervolgens **DefaultValue** van **Resource_id** door de toepassings-id van de IdentityExperienceFramework-toepassing die u ook hebt gemaakt in de hand leiding voor vereisten.

5. Voeg de volgende **ClaimsProvider** -elementen met hun technische profielen toe aan het **ClaimsProviders** -element:

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Voeg een **UserJourneys** -element en de onderliggende elementen toe aan het element **TrustFrameworkPolicy** :

    ```xml
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
8. Schakel **het beleid overschrijven als dit bestaat** in en selecteer vervolgens het *TrustFrameworkExtensions.xml* bestand.
9. Klik op **Uploaden**.

## <a name="create-a-relying-party-file"></a>Een Relying Party-bestand maken

Werk vervolgens het Relying Party bestand bij dat de door u gemaakte gebruikers traject initieert:

1. Maak een kopie van *SignUpOrSignin.xml* -bestand in uw werkmap en wijzig de naam ervan in *ROPC_Auth.xml*.
2. Open het nieuwe bestand en wijzig de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** in een unieke waarde. De beleids-ID is de naam van uw beleid. Bijvoorbeeld **B2C_1A_ROPC_Auth**.
3. Wijzig de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** in `ResourceOwnerPasswordCredentials` .
4. Wijzig het element **OutputClaims** zodat het de volgende claims bevat:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
6. Schakel **het beleid overschrijven als dit bestaat** in en selecteer vervolgens het *ROPC_Auth.xml* bestand.
7. Klik op **Uploaden**.


::: zone-end

## <a name="test-the-ropc-flow"></a>De ROPC-stroom testen

Gebruik uw favoriete API-ontwikkelings toepassing om een API-aanroep te genereren en Bekijk het antwoord op fout opsporing van uw beleid. Maak een aanroep zoals dit voor beeld met de volgende informatie als hoofd tekst van de POST-aanvraag:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Vervang `<tenant-name>` door de naam van uw Azure AD B2C-tenant.
- Vervang door `B2C_1A_ROPC_Auth` de volledige naam van het beleid voor wachtwoord referenties van uw resource-eigenaar.

| Sleutel | Waarde |
| --- | ----- |
| gebruikersnaam | `user-account` |
| wachtwoord | `password1` |
| grant_type | wachtwoord |
| scope | OpenID Connect `application-id` offline_access |
| client_id | `application-id` |
| response_type | token id_token |

- Vervang door `user-account` de naam van een gebruikers account in uw Tenant.
- Vervang door `password1` het wacht woord van het gebruikers account.
- Vervang door `application-id` de toepassings-id uit de *ROPC_Auth_app* registratie.
- *Offline_access* is optioneel als u een vernieuwings token wilt ontvangen.

De werkelijke POST-aanvraag ziet er ongeveer uit als in het volgende voor beeld:

```https
POST /<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: <tenant-name>.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Een geslaagde reactie met offline toegang lijkt op het volgende voor beeld:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Een vernieuwings token inwisselen

Een POST-aanroep maken zoals deze wordt weer gegeven. Gebruik de informatie in de volgende tabel als hoofd tekst van de aanvraag:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

- Vervang `<tenant-name>` door de naam van uw Azure AD B2C-tenant.
- Vervang door `B2C_1A_ROPC_Auth` de volledige naam van het beleid voor wachtwoord referenties van uw resource-eigenaar.

| Sleutel | Waarde |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Vervang door `application-id` de toepassings-id uit de *ROPC_Auth_app* registratie.
- Vervang door `refresh-token` de **refresh_token** die in het vorige antwoord is teruggestuurd.

Een geslaagde reactie ziet eruit als in het volgende voor beeld:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Gebruik een systeem eigen SDK of App-Auth

Azure AD B2C voldoet aan de OAuth 2,0-standaarden voor de referenties van het eigenaars wachtwoord voor open bare client bronnen en moet compatibel zijn met de meeste client-Sdk's. Zie voor de meest recente informatie [systeem eigen app SDK voor OAuth 2,0 en OpenID Connect Connect implementeren moderne Best practices](https://appauth.io/).

## <a name="next-steps"></a>Volgende stappen

Down load Working-voor beelden die zijn geconfigureerd voor gebruik met Azure AD B2C van GitHub, [voor Android](https://aka.ms/aadb2cappauthropc) en [IOS](https://aka.ms/aadb2ciosappauthropc).
