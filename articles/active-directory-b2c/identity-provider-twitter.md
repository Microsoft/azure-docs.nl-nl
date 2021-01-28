---
title: Registratie instellen en aanmelden met een Twitter-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met Twitter-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a998491729a1d3bd472ecc3de9722c142f8dc182
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953781"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Twitter-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Een app maken

Als u aanmelden voor gebruikers met een Twitter-account in Azure AD B2C wilt inschakelen, moet u een Twitter-toepassing maken. Als u nog geen Twitter-account hebt, kunt u zich aanmelden bij [https://twitter.com/signup](https://twitter.com/signup) . U moet ook [een ontwikkelaars account Toep assen](https://developer.twitter.com/en/apply/user.html). Zie [Toep assen voor toegang](https://developer.twitter.com/en/apply-for-access)voor meer informatie.

1. Meld u aan bij de [Twitter-ontwikkelaars Portal](https://developer.twitter.com/portal/projects-and-apps) met de referenties van uw Twitter-account.
1. Onder **zelfstandige apps** selecteert u **+ app maken**.
1. Voer de **naam** van een app in en selecteer **volt ooien**.
1. Kopieer de waarde van de **app-sleutel** en de **API-sleutel geheim**.  U kunt beide gebruiken om Twitter te configureren als een id-provider in uw Tenant. 
1. Selecteer onder **uw app instellen de** optie **app-instellingen**.
1. Onder **verificatie-instellingen** selecteert u **bewerken**
    1. Schakel **het selectie vakje 3-legged OAuth inschakelen in** .
    1. Selecteer het selectie vakje **e-mail adres aanvragen bij gebruikers** .
    1. Voer in voor de **call back-url's** `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp` . Vervang door `your-tenant` de naam van uw Tenant naam en `your-user-flow-Id` met de id van uw gebruikers stroom. Bijvoorbeeld `b2c_1A_signup_signin_twitter`. Gebruik alleen kleine letters wanneer u uw Tenant naam en gebruikers stroom-ID invoert, zelfs als ze zijn gedefinieerd met hoofd letters in Azure AD B2C.
    1. Voer in voor de URL van de **website** `https://your-tenant.b2clogin.com` . Vervang `your-tenant` door de naam van uw tenant. Bijvoorbeeld `https://contosob2c.b2clogin.com`.
    1. Voer bijvoorbeeld een URL in voor de **Service voorwaarden** `http://www.contoso.com/tos` . De beleids-URL is een pagina die u onderhoudt om voor waarden voor uw toepassing te bieden.
    1. Voer een URL in voor het **Privacybeleid**, bijvoorbeeld `http://www.contoso.com/privacy` . De beleids-URL is een pagina die u kunt onderhouden voor het verstrekken van privacy-informatie voor uw toepassing.
    1. Selecteer **Opslaan**.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>Twitter configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers** en selecteer vervolgens **Twitter**.
1. Voer een **naam** in. Bijvoorbeeld *Twitter*.
1. Voer voor de **client-id** de *API-sleutel* in van de Twitter-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim** het *API-sleutel geheim* in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Twitter-ID-provider toevoegen aan een gebruikers stroom 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Selecteer de gebruikers stroom waaraan u de Twitter-ID-provider wilt toevoegen.
1. Selecteer **Twitter** in het kader van de **sociale id-providers**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet de geheime sleutel opslaan die u eerder in uw Azure AD B2C-Tenant hebt vastgelegd.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
5. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
6. Kies voor **Opties** `Manual` .
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `TwitterSecret`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in het **geheim** uw client geheim in dat u eerder hebt vastgelegd.
9. Selecteer voor **sleutel gebruik** `Encryption` .
10. Klik op **Create**.

## <a name="configure-twitter-as-an-identity-provider"></a>Twitter configureren als een id-provider

Als u gebruikers wilt toestaan zich aan te melden met een Twitter-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een Twitter-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.

1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Vervang de waarde van **client_id** door het *geheim* van de API-sleutel dat u eerder hebt vastgelegd.
5. Sla het bestand op.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]


::: zone-end
