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
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b6c0d9d5430d84006b208c50e78b8d875c95b8ac
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028380"
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

1. Meld u aan bij de [Amazon-ontwikkelaars console](https://developer.amazon.com/dashboard) met uw Amazon-account referenties.
1. Als u dit nog niet hebt gedaan, selecteert u **registreren**, volgt u de registratie stappen voor de ontwikkelaar en gaat u akkoord met het beleid.
1. Selecteer in het dash board **Aanmelden bij Amazon**.
1. Selecteer **een nieuw beveiligings profiel maken**.
1. Voer een **naam voor het beveiligings profiel**, de beschrijving van het **beveiligings profiel** en de URL voor de privacyverklaring van de **toestemming** in, bijvoorbeeld `https://www.contoso.com/privacy` de URL van de privacyverklaring is een pagina die u beheert en die privacy-informatie verstrekt aan gebruikers. Klik vervolgens op **Opslaan**.
1. Selecteer in de sectie **Aanmelden met Amazon-configuraties** de **naam van het beveiligings profiel** dat u hebt gemaakt, selecteer het pictogram **beheren** en selecteer vervolgens **Webinstellingen**.
1. Kopieer de waarden van de **client-id** in het gedeelte **Web Settings** . Selecteer **geheim weer geven** om het client geheim te ontvangen en kopieer het vervolgens. U hebt beide waarden nodig om een Amazon-account te configureren als een id-provider in uw Tenant. **Client geheim** is een belang rijke beveiligings referentie.
1. Selecteer in de sectie **Webinstellingen** de optie **bewerken**. 
    1. Voer in **toegestane oorsprongen** in `https://your-tenant-name.b2clogin.com` . Vervang `your-tenant-name` door de naam van uw tenant. Als u een [aangepast domein](custom-domain.md)gebruikt, voert u in `https://your-domain-name` .
    1.  **Toegestane retour-url's** , invoeren `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` .  Als u een [aangepast domein](custom-domain.md)gebruikt, voert u in `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` .  Vervang door `your-tenant-name` de naam van uw Tenant en `your-domain-name` met uw aangepaste domein.
1. Selecteer **Opslaan**.

::: zone pivot="b2c-user-flow"

## <a name="configure-amazon-as-an-identity-provider"></a>Amazon configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers** en selecteer vervolgens **Amazon**.
1. Voer een **naam** in. Bijvoorbeeld *Amazon*.
1. Voer voor de **client-id** de client-id in van de Amazon-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim** het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.

## <a name="add-amazon-identity-provider-to-a-user-flow"></a>Amazon-ID-provider toevoegen aan een gebruikers stroom 

Op dit moment is de Amazon-ID-provider ingesteld, maar is deze nog niet beschikbaar op de aanmeldings pagina's. De Amazon ID-provider toevoegen aan een gebruikers stroom:

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom waaraan u de Amazon-ID-provider wilt toevoegen.
1. Selecteer in het geval van **sociale id-providers** **Amazon**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **gebruikers stroom uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden **Amazon** om u aan te melden met het Amazon-account.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.

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

## <a name="configure-amazon-as-an-identity-provider"></a>Amazon configureren als een id-provider

Als u gebruikers wilt toestaan zich aan te melden met een Amazon-account, moet u het account definiëren als een claim provider. waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een Amazon-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.


1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>amazon.com</Domain>
      <DisplayName>Amazon</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Amazon-OAuth2">
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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AmazonExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AmazonExchange" TechnicalProfileReferenceId="Amazon-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Selecteer uw Relying Party beleid, bijvoorbeeld `B2C_1A_signup_signin` .
1. Selecteer voor **toepassing** een webtoepassing die u [eerder hebt geregistreerd](tutorial-register-applications.md). De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **nu uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden **Amazon** om u aan te melden met het Amazon-account.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.

::: zone-end
