---
title: Registratie instellen en aanmelden met een Weibo-account
titleSuffix: Azure AD B2C
description: Gebruik Azure Active Directory B2C om u aan te melden en u aan te melden bij klanten met Weibo-accounts in uw toepassingen.
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
ms.openlocfilehash: 26c4e154deec02b0642e6c131ced50acb02f9899
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98951518"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Stel registratie in en meld u aan met een Weibo-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-weibo-application"></a>Een Weibo-toepassing maken

Als u aanmelden wilt inschakelen voor gebruikers met een Weibo-account in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in [Weibo ontwikkelaars Portal](https://open.weibo.com/). Als u nog geen Weibo-account hebt, kunt u zich aanmelden bij [https://weibo.com](https://weibo.com/signup/signup.php?lang=en-us) .

1. Meld u aan bij de [Weibo-ontwikkelaars Portal](https://open.weibo.com/) met uw Weibo-account referenties.
1. Nadat u zich hebt aangemeld, selecteert u uw weergave naam in de rechter bovenhoek.
1. Selecteer in de vervolg keuzelijst **编辑开发者信息** (ontwikkelaars gegevens bewerken).
1. Voer de vereiste gegevens in en selecteer **提交** (verzenden).
1. Voltooi het e-mail verificatie proces.
1. Ga naar de [pagina identiteits verificatie](https://open.weibo.com/developers/identity/edit).
1. Voer de vereiste gegevens in en selecteer **提交** (verzenden).

### <a name="register-a-weibo-application"></a>Een Weibo-toepassing registreren

1. Ga naar de [nieuwe pagina](https://open.weibo.com/apps/new)voor het registreren van Weibo-apps.
1. Voer de benodigde toepassings gegevens in.
1. Selecteer **创建** (maken).
1. Kopieer de waarden van de **app-sleutel** en het app- **geheim**. U hebt beide nodig om de ID-provider toe te voegen aan uw Tenant.
1. Upload de vereiste Foto's en voer de benodigde gegevens in.
1. Selecteer **保存以上信息** (opslaan).
1. Selecteer **高级信息** (geavanceerde informatie).
1. Selecteer **编辑** (bewerken) naast het veld voor OAuth 2.0 **授权设置** (omleidings-URL).
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in voor OAuth 2.0- **授权设置** (omleidings-URL). Als uw Tenant naam bijvoorbeeld contoso is, stelt u de URL in op `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Selecteer **提交** (verzenden).

::: zone pivot="b2c-user-flow"

## <a name="configure-weibo-as-an-identity-provider"></a>Weibo configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers** en selecteer vervolgens **Weibo (preview)**.
1. Voer een **naam** in. Bijvoorbeeld *Weibo*.
1. Voor de **client-id** voert u de app-sleutel in van de Weibo-toepassing die u eerder hebt gemaakt.
1. Voer voor **Clientgeheim** het app-geheim in dat u hebt genoteerd.
1. Selecteer **Opslaan**.

## <a name="add-weibo-identity-provider-to-a-user-flow"></a>Weibo-ID-provider toevoegen aan een gebruikers stroom 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom waaraan u de Weibo-ID-provider wilt toevoegen.
1. Selecteer **Weibo** onder de **sociale id-providers**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet het client geheim opslaan dat u eerder in uw Azure AD B2C-Tenant hebt vastgelegd.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
5. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
6. Kies voor **Opties** `Manual` .
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `WeiboSecret`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in het **geheim** uw client geheim in dat u eerder hebt vastgelegd.
9. Selecteer voor **sleutel gebruik** `Signature` .
10. Klik op **Create**.

## <a name="configure-weibo-as-an-identity-provider"></a>Weibo configureren als een id-provider

Als u gebruikers wilt toestaan zich aan te melden met een Weibo-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een Weibo-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.

1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>weibo.com</Domain>
      <DisplayName>Weibo (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Weibo-OAuth2">
          <DisplayName>Weibo</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">weibo</Item>
            <Item Key="authorization_endpoint">https://api.weibo.com/oauth2/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://api.weibo.com/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weibo.com/2/account/get_uid.json</Item>
            <Item Key="scope">email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">uid</Item>
            <Item Key="client_id">Your Weibo application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeiboSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="numericUserId" PartnerClaimType="uid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="weibo.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="Weibo User" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="UserId" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateIssuerUserId" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
    ```

4. Stel **client_id** van de toepassings-id in voor de registratie van de toepassing.
5. Sla het bestand op.

### <a name="add-the-claims-transformations"></a>De claim transformaties toevoegen

Het technische profiel GitHub vereist dat de **CreateIssuerUserId** -claim transformaties worden toegevoegd aan de lijst met ClaimsTransformations. Als er geen **ClaimsTransformations** -element in uw bestand is gedefinieerd, voegt u de bovenliggende XML-elementen toe zoals hieronder wordt weer gegeven. De claim transformaties hebben ook een nieuw claim type gedefinieerd met de naam **numericUserId**.

1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de claim numericUserId toe aan het element **ClaimsSchema** .
1. Zoek het element [ClaimsTransformations](claimstransformations.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de CreateIssuerUserId-claim transformaties toe aan het **ClaimsTransformations** -element.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="numericUserId">
      <DisplayName>Numeric user Identifier</DisplayName>
      <DataType>long</DataType>
    </ClaimType>
  </ClaimsSchema>
  <ClaimsTransformations>
    <ClaimsTransformation Id="CreateIssuerUserId" TransformationMethod="ConvertNumberToStringClaim">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="WeiboExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="WeiboExchange" TechnicalProfileReferenceId="Weibo-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
