---
title: Registratie instellen en aanmelden met een Amazon-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met Amazon-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.custom: project-no-code
ms.date: 01/15/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 79fcbb6d2bf10da566139b0d103a4f31930f3200
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98537991"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Amazon-account met Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-app-in-the-amazon-developer-console"></a>Een app maken in de Amazon-ontwikkelaars console

Als u aanmelden voor gebruikers met een Amazon-account in Azure Active Directory B2C (Azure AD B2C) wilt inschakelen, moet u een toepassing maken in de [Amazon-ontwikkelaars Services en-technologieën](https://developer.amazon.com). Zie [registreren voor aanmelding bij Amazon](https://developer.amazon.com/docs/login-with-amazon/register-web.html)voor meer informatie. Als u nog geen Amazon-account hebt, kunt u zich aanmelden bij [https://www.amazon.com/](https://www.amazon.com/) .

> [!NOTE]  
> Gebruik de volgende Url's in **stap 8** hieronder, waarbij u vervangt `your-tenant-name` door de naam van uw Tenant. Gebruik bij het invoeren van de naam van uw Tenant alle kleine letters, zelfs als de Tenant is gedefinieerd met hoofd letters in Azure AD B2C.
> - Voer voor **toegestane oorsprongen**`https://your-tenant-name.b2clogin.com` 
> - Voer voor **toegestane retour-url's**`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`

[!INCLUDE [identity-provider-amazon-idp-register.md](../../includes/identity-provider-amazon-idp-register.md)]

::: zone pivot="b2c-user-flow"

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Een Amazon-account configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers** en selecteer vervolgens **Amazon**.
1. Voer een **naam** in. Bijvoorbeeld *Amazon*.
1. Voer voor de **client-id** de client-id in van de Amazon-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim** het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet het client geheim opslaan dat u eerder in uw Azure AD B2C-Tenant hebt vastgelegd.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
5. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
6. Kies voor **Opties** `Manual` .
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `AmazonSecret`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in het **geheim** uw client geheim in dat u eerder hebt vastgelegd.
9. Selecteer voor **sleutel gebruik** `Signature` .
10. Klik op **Create**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met een Amazon-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een Amazon-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.


1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAUTH">
        <DisplayName>Amazon</DisplayName>
        <Protocol Name="OAuth2" />
        <Metadata>
          <Item Key="ProviderName">amazon</Item>
          <Item Key="authorization_endpoint">https://www.amazon.com/ap/oa</Item>
          <Item Key="AccessTokenEndpoint">https://api.amazon.com/auth/o2/token</Item>
          <Item Key="ClaimsEndpoint">https://api.amazon.com/user/profile</Item>
          <Item Key="scope">profile</Item>
          <Item Key="HttpBinding">POST</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="client_id">Your Amazon application client ID</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_AmazonSecret" />
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="amazon.com" />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        </OutputClaims>
          <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Stel **client_id** van de toepassings-id in voor de registratie van de toepassing.
5. Sla het bestand op.

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Nu hebt u uw beleid zodanig geconfigureerd dat Azure AD B2C weet hoe u kunt communiceren met uw Azure AD-adres lijst. Upload het extensie bestand van uw beleid alleen om te bevestigen dat er tot nu toe geen problemen zijn.

1. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
2. Schakel **het beleid overschrijven als dit bestaat** in en selecteer vervolgens het *TrustFrameworkExtensions.xml* bestand.
3. Klik op **Uploaden**.

## <a name="register-the-claims-provider"></a>De claim provider registreren

Op dit moment is de ID-provider ingesteld, maar is deze niet beschikbaar in de schermen voor aanmelden/aanmelden. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zo dat deze ook de Amazon-ID-provider heeft.

1. Open het *TrustFrameworkBase.xml* -bestand in het Starter Pack.
2. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat bevat `Id="SignUpOrSignIn"` .
3. Open de *TrustFrameworkExtensions.xml* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
5. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInAmazon`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op het scherm aanmelden/aanmelden. Als u een **ClaimsProviderSelection** -element toevoegt voor een Amazon-account, wordt een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Zoek het **OrchestrationStep** -element dat is opgenomen `Order="1"` in de gebruikers traject die u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelects** het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `AmazonExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met een Amazon-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die `Order="2"` in de gebruikers reis zijn opgenomen.
2. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij naar de id van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Amazon-OAuth`.

3. Sla het *TrustFrameworkExtensions.xml* bestand op en upload het opnieuw voor verificatie.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>Amazon-ID-provider toevoegen aan een gebruikers stroom 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom waaraan u de Amazon-ID-provider wilt toevoegen.
1. Selecteer in het geval van **sociale id-providers** **Amazon**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInAmazon.xml*.
1. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignInAmazon`.
1. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld:`http://contoso.com/B2C_1A_signup_signin_amazon`
1. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de nieuwe gebruikers traject die u hebt gemaakt (SignUpSignAmazon).
1. Sla de wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
1. Zorg ervoor dat Azure AD B2C toepassing die u hebt gemaakt, is geselecteerd in het veld **toepassing selecteren** en test deze door op **nu uitvoeren** te klikken.

::: zone-end
