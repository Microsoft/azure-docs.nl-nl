---
title: Registratie instellen en aanmelden met een ID.me-account
titleSuffix: Azure AD B2C
description: Gebruik Azure Active Directory B2C om u aan te melden en u aan te melden bij klanten met ID.me-accounts in uw toepassingen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c7d43a55878a07e424ce1b6f55782502c244239c
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98537941"
---
# <a name="set-up-sign-up-and-sign-in-with-a-idme-account-using-azure-active-directory-b2c"></a>Stel registratie in en meld u aan met een ID.me-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"


## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]


## <a name="create-an-idme-application"></a>Een ID.me-toepassing maken

Als u aanmelden wilt inschakelen voor gebruikers met een ID.me-account in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in [id.me Developer-Resources voor API & SDK](https://developers.id.me/). Zie voor meer informatie [OAuth-integratie handleiding](https://developers.id.me/documentation/oauth/overview/kyc). Als u nog geen ID.me-ontwikkelaars account hebt, kunt u zich aanmelden bij [https://developers.id.me/registration/new](https://developers.id.me/registration/new) .

1. Meld u aan bij de [id.me Developer-Resources voor API & SDK](https://developers.id.me/) met uw id.me-account referenties.
1. Selecteer **mijn toepassingen weer geven** en selecteer **door gaan**.
1. **Nieuw maken** selecteren
    1. Voer een **naam** en **weergave naam** in.
    1. Voer in de **omleidings-URI** in `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Vervang `your-tenant-name` door de naam van uw tenant. 
1. Klik op **Continue**.
1. Kopieer de waarden van de **client-id** en het **client geheim**. U hebt beide nodig om de ID-provider toe te voegen aan uw Tenant.

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet het client geheim opslaan dat u eerder in uw Azure AD B2C-Tenant hebt vastgelegd.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
5. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
6. Kies voor **Opties** `Manual` .
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `IdMeSecret`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in het **geheim** uw client geheim in dat u eerder hebt vastgelegd.
9. Selecteer voor **sleutel gebruik** `Signature` .
10. Klik op **Create**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met behulp van een ID.me-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een ID.me-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.

1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>id.me</Domain>
      <DisplayName>ID.me</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="IdMe-OAUTH2">
          <DisplayName>IdMe</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">api.id.me</Item>
            <Item Key="authorization_endpoint">https://api.id.me/oauth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.id.me/oauth/token</Item>
            <Item Key="ClaimsEndpoint">https://api.id.me/api/public/v2/attributes.json</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="scope">openid alumni</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your ID.me application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdMeSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="uuid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="fname" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lname" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="me.id.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateDisplayNameFromFirstNameAndLastName" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Stel **client_id** van de toepassings-id in voor de registratie van de toepassing.
5. Sla het bestand op.

### <a name="add-the-claims-transformations"></a>De claim transformaties toevoegen

Vervolgens hebt u een claim transformatie nodig om de displayName-claim te maken. Voeg de volgende claim transformatie toe aan het- `<ClaimsTransformations>` element binnen `<BuildingBlocks>` . 

```xml
 <ClaimsTransformations>
  <ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
    </OutputClaims>
   </ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Nu hebt u uw beleid zodanig geconfigureerd dat Azure AD B2C weet hoe u kunt communiceren met uw ID.me-account. Upload het extensie bestand van uw beleid alleen om te bevestigen dat er tot nu toe geen problemen zijn.

1. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
2. Schakel **het beleid overschrijven als dit bestaat** in en selecteer vervolgens het *TrustFrameworkExtensions.xml* bestand.
3. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>De claim provider registreren

Op dit moment is de ID-provider ingesteld, maar is deze niet beschikbaar in de schermen voor aanmelden/aanmelden. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zodat deze ook de ID.me-ID-provider heeft.

1. Open het *TrustFrameworkBase.xml* -bestand in het Starter Pack.
2. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat bevat `Id="SignUpOrSignIn"` .
3. Open de *TrustFrameworkExtensions.xml* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
5. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInIdMe`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op het scherm aanmelden/aanmelden. Als u een **ClaimsProviderSelection** -element toevoegt voor een id.me-account, wordt er een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Zoek het **OrchestrationStep** -element dat is opgenomen `Order="1"` in de gebruikers traject die u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelects** het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `IdMeExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="IdMeExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met een ID.me-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die `Order="2"` in de gebruikers reis zijn opgenomen.
2. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="IdMeExchange" TechnicalProfileReferenceId="IdMe-OAUTH2" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij naar de id van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `IdMe-OAUTH2`.

3. Sla het *TrustFrameworkExtensions.xml* bestand op en upload het opnieuw voor verificatie.

## <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInIdMe.xml*.
1. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignIdMe`.
1. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld:`http://contoso.com/B2C_1A_signup_signin_IdMe`
1. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de nieuwe gebruikers traject die u hebt gemaakt (SignUpSignIdMe).
1. Sla de wijzigingen op en upload het bestand.
1. Selecteer **B2C_1A_signup_signin** onder **aangepast beleid**.
1. Selecteer voor **Select-toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer **nu uitvoeren** en selecteer id.me om u aan te melden met id.me en het aangepaste beleid te testen.

::: zone-end