---
title: Stel registratie in en meld u aan met een QQ-account met behulp van Azure Active Directory B2C
description: Gebruik Azure Active Directory B2C om u aan te melden en u aan te melden bij klanten met QQ-accounts in uw toepassingen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2fa8c2ea990644fa82ae79114322fa087259378b
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448184"
---
# <a name="set-up-sign-up-and-sign-in-with-a-qq-account-using-azure-active-directory-b2c"></a>Stel registratie in en meld u aan met een QQ-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-qq-application"></a>Een QQ-toepassing maken

Als u aanmelden wilt inschakelen voor gebruikers met een QQ-account in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in [QQ ontwikkelaars Portal](http://open.qq.com). Als u nog geen QQ-account hebt, kunt u zich aanmelden bij [https://ssl.zc.qq.com](https://ssl.zc.qq.com/en/index.html?type=1&ptlang=1033) .

### <a name="register-for-the-qq-developer-program"></a>Registreren voor het QQ-ontwikkelaars programma

1. Meld u aan bij de [QQ-ontwikkelaars Portal](http://open.qq.com) met uw QQ-account referenties.
1. Nadat u zich hebt aangemeld, gaat u naar [https://open.qq.com/reg](https://open.qq.com/reg) om uzelf te registreren als ontwikkelaar.
1. Selecteer **个人** (afzonderlijke ontwikkelaar).
1. Voer de vereiste gegevens in en selecteer **下一步** (volgende stap).
1. Voltooi het e-mail verificatie proces. Nadat u zich hebt geregistreerd als ontwikkelaar, moet u een paar dagen wachten.

### <a name="register-a-qq-application"></a>Een QQ-toepassing registreren

1. Ga naar [https://connect.qq.com/index.html](https://connect.qq.com/index.html).
1. Selecteer **应用管理** (app Management).
1. Selecteer **创建应用** (app maken) en voer de vereiste gegevens in.
1. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name}.onmicrosoft.com/oauth2/authresp` in **授权回调域** (call back-URL) in. Als uw `tenant_name` is contoso bijvoorbeeld, stelt u de URL in op `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` .
1. Selecteer **创建应用** (app maken).
1. Selecteer op de pagina Bevestiging **应用管理** (app Management) om terug te keren naar de pagina voor het beheren van apps.
1. Selecteer **查看** (weer geven) naast de app die u hebt gemaakt.
1. Selecteer **修改** (bewerken).
1. Kopieer de **App-ID** en **app-sleutel**. U hebt beide waarden nodig om de ID-provider toe te voegen aan uw Tenant.

::: zone pivot="b2c-user-flow"

## <a name="configure-qq-as-an-identity-provider"></a>QQ configureren als een id-provider

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer **id-providers** en selecteer vervolgens **QQ (preview)**.
1. Voer een **naam** in. Bijvoorbeeld *QQ*.
1. Voer voor de **client-id** de app-id in van de QQ-toepassing die u eerder hebt gemaakt.
1. Voor het **client geheim** voert u de app-sleutel in die u hebt vastgelegd.
1. Selecteer **Opslaan**.

## <a name="add-qq-identity-provider-to-a-user-flow"></a>QQ-ID-provider toevoegen aan een gebruikers stroom 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom waaraan u de QQ-ID-provider wilt toevoegen.
1. Selecteer **QQ** onder de **sociale id-providers**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **gebruikers stroom uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden de optie **QQ** om u aan te melden met het QQ-account.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.


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
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `QQSecret`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in het **geheim** uw client geheim in dat u eerder hebt vastgelegd.
9. Selecteer voor **sleutel gebruik** `Signature` .
10. Klik op **Create**.

## <a name="configure-qq-as-an-identity-provider"></a>QQ configureren als een id-provider

Als u gebruikers wilt toestaan zich aan te melden met een QQ-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een QQ-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.

1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>qq.com</Domain>
      <DisplayName>QQ (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="QQ-OAuth2">
          <DisplayName>QQ</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">qq</Item>
            <Item Key="authorization_endpoint">https://graph.qq.com/oauth2.0/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://graph.qq.com/oauth2.0/token</Item>
            <Item Key="ClaimsEndpoint">https://graph.qq.com/oauth2.0/me</Item>
            <Item Key="scope">get_user_info</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="ClaimsResponseFormat">JsonP</Item>
            <Item Key="ResponseErrorCodeParamName">error</Item>
            <Item Key="external_user_identity_claim_id">openid</Item>
            <Item Key="client_id">Your QQ application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_QQSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="openid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="qq.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. Stel **client_id** van de toepassings-id in voor de registratie van de toepassing.
5. Sla het bestand op.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="QQExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="QQExchange" TechnicalProfileReferenceId="QQ-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Selecteer uw Relying Party beleid, bijvoorbeeld `B2C_1A_signup_signin` .
1. Selecteer voor **toepassing** een webtoepassing die u [eerder hebt geregistreerd](troubleshoot-custom-policies.md#troubleshoot-the-runtime). De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **nu uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden de optie **QQ** om u aan te melden met het QQ-account.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.

::: zone-end
