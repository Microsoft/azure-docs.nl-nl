---
title: Registratie instellen en aanmelden met een Apple-ID
titleSuffix: Azure AD B2C
description: U kunt zich aanmelden bij klanten met Apple ID in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 24377cf02b30a550043ee63267229039d680cd1c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489131"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Registratie instellen en aanmelden met een Apple-ID met behulp van Azure Active Directory B2C (preview-versie)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Een Apple-ID-toepassing maken

Als u aanmelden voor gebruikers met een Apple-ID in Azure Active Directory B2C (Azure AD B2C) wilt inschakelen, moet u een toepassing maken in [https://developer.apple.com](https://developer.apple.com) . Zie [Aanmelden met Apple](https://developer.apple.com/sign-in-with-apple/get-started/)voor meer informatie. Als u nog geen Apple Developer-account hebt, kunt u zich aanmelden bij het [Apple-ontwikkelaars programma](https://developer.apple.com/programs/enroll/).

1. Meld u aan bij de [Apple-ontwikkelaars Portal](https://developer.apple.com/account) met uw account referenties.
1. Selecteer in het menu **certificaten, id's, & profielen** en selecteer vervolgens **(+)**.
1. Als u **een nieuwe id wilt registreren**, selecteert u **app-id's** en selecteert u **door gaan**.
1. Voor **Selecteer een type** selecteert u **app** en selecteert u vervolgens **door gaan**.
1. Voor **het registreren van een app-id**:
    1. Voer een **Beschrijving** in 
    1. Voer de **bundel-id** in, bijvoorbeeld `com.contoso.azure-ad-b2c` . 
    1. Voor **mogelijkheden** selecteert u **Aanmelden met Apple** vanuit de lijst met mogelijkheden. 
    1. Noteer uw app-ID-voor voegsel (team-ID) uit deze stap. U hebt deze later nodig.
    1. Selecteer **door gaan** en vervolgens **registreren**.
1. Selecteer in het menu **certificaten, id's, & profielen** en selecteer vervolgens **(+)**.
1. Als u **een nieuwe id wilt registreren**, selecteert u **Services-id's** en selecteert u **door gaan**.
1. Voor **het registreren van een service-id**:
    1. Voer een **Beschrijving** in. De beschrijving wordt weer gegeven aan de gebruiker op het scherm voor toestemming.
    1. Voer de **id** in, bijvoorbeeld `com.consoto.azure-ad-b2c-service` . De id is uw client-ID voor de OpenID Connect Connect-stroom.
    1. Selecteer **door gaan** en selecteer vervolgens **registreren**.
1. Selecteer vanuit **id's** de id die u hebt gemaakt.
1. Selecteer **Aanmelden met Apple** en selecteer vervolgens **configureren**.
    1. Selecteer de **id van de primaire app** waarvoor u aanmelden met Apple wilt configureren.
    1. Voer in **domeinen en subdomeinen** het in `your-tenant-name.b2clogin.com` . Vervang uw-Tenant naam door de naam van uw Tenant. Als u een [aangepast domein](custom-domain.md)gebruikt, voert u in `https://your-domain-name` .
    1. Voer in **retour-url's** in `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Als u een [aangepast domein](custom-domain.md)gebruikt, voert u in `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Vervang door `your-tenant-name` de naam van uw Tenant en `your-domain-name` met uw aangepaste domein.
    1. Selecteer **volgende** en selecteer vervolgens **gereed**.
    1. Wanneer het pop-upvenster is gesloten, selecteert u **door gaan** en selecteert u **Opslaan**.

## <a name="creating-an-apple-client-secret"></a>Een Apple-client geheim maken

1. Selecteer in het menu Apple-ontwikkelaars Portal de optie **sleutels** en selecteer vervolgens **(+)**.
1. Voor **het registreren van een nieuwe sleutel**:
    1. Typ een **sleutel naam**.
    1. Selecteer **Aanmelden met Apple** en selecteer vervolgens **configureren**.
    1. Voor de **id van de primaire app** selecteert u de app die u eerder hebt gemaakt en selecteert u **Opslaan**.
    1. Selecteer **configureren** en selecteer vervolgens **registreren** om het belangrijkste registratie proces te volt ooien.
1. Als u **uw sleutel wilt downloaden**, selecteert u **downloaden** om een. P8-bestand te downloaden dat uw sleutel bevat.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Apple als een id-provider configureren

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) als globale beheerder van uw Azure AD B2C Tenant.
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en kies de map die uw Azure AD B2C-tenant bevat.
1. Onder **Azure-Services** selecteert u **Azure AD B2C**. Of gebruik het zoekvak om **Azure AD B2C** te zoeken en te selecteren.
1. Selecteer **id-providers** en selecteer vervolgens **Apple (preview)**.
1. Voer een **naam** in. Bijvoorbeeld *Apple*.
1. Voer de **Apple Developer-id (team-ID)** in.
1. Voer de **Apple-Service-id (client-id)** in.
1. Voer de **id van de Apple-sleutel** in.
1. Selecteer en upload de **Apple-certificaat gegevens**.
1. Selecteer **Opslaan**.


> [!IMPORTANT] 
> - Als u zich aanmeldt met Apple, moet de beheerder elke 6 maanden hun client geheim vernieuwen. 
> - Tijdens de open bare preview van deze functie moet u het Apple client-geheim hand matig vernieuwen als het verloopt. Er wordt een waarschuwing weer gegeven op de Apple-ID-providers voor het configureren van de IDP-pagina, maar we raden u aan uw eigen herinnering in te stellen. 
> - Als u het geheim wilt vernieuwen, opent u Azure AD B2C in de Azure portal gaat u naar **id-providers**  >  **Apple** en selecteert u **geheim vernieuwen**.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>De Apple-ID-provider toevoegen aan een gebruikers stroom

Als u gebruikers wilt toestaan zich aan te melden met een Apple-ID, moet u de Apple-ID-provider toevoegen aan een gebruikers stroom. Aanmelden met Apple kan alleen worden geconfigureerd voor de **Aanbevolen** versie van gebruikers stromen. De Apple-ID-provider toevoegen aan een gebruikers stroom:

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Selecteer een gebruikers stroom waarvoor u de Apple-ID-provider wilt toevoegen. 
1. Onder **Social id-providers** selecteert u **Apple (preview)**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **gebruikers stroom uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden de optie **Apple** om u aan te melden met Apple ID.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Het client geheim ondertekenen

Gebruik het. P8-bestand dat u eerder hebt gedownload om het client geheim te ondertekenen in een JWT-token. Er zijn veel bibliotheken die de JWT voor u kunnen maken en ondertekenen. Gebruik de [functie Azure waarmee een token voor u wordt gemaakt](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) . 

1. Een [Azure-functie](../azure-functions/functions-create-function-app-portal.md)maken.
1. Selecteer onder **ontwikkelaar** **code + test**. 
1. Kopieer de inhoud van het bestand [Run. CSX](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) en plak het in de editor.
1. Selecteer **Opslaan**.
1. Maak een HTTP- `POST` aanvraag en geef de volgende informatie op:

    - **appleTeamId**: uw Apple Developer Team-ID
    - **appleServiceId**: de Apple-Service-id (ook de client-id)
    - **p8key**: de PEM-indelings sleutel. U kunt dit verkrijgen door het. P8-bestand te openen in een tekst editor en alles te kopiëren tussen `-----BEGIN PRIVATE KEY-----` en `-----END PRIVATE KEY-----` zonder regel einden.
 
De volgende JSON is een voor beeld van een aanroep van de Azure-functie:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

De functie Azure reageert met een correct opgemaakte en ondertekende client Secret JWT in een reactie, bijvoorbeeld:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet het client geheim opslaan dat u eerder in uw Azure AD B2C-Tenant hebt vastgelegd.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en kies de map die uw Azure AD B2C-tenant bevat.
2. Onder **Azure-Services** selecteert u **Azure AD B2C**. Of gebruik het zoekvak om **Azure AD B2C** te zoeken en te selecteren.
1. Selecteer op de pagina **overzicht** **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** de optie **hand matig**.
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld ' AppleSecret '. Het voor voegsel "B2C_1A_" wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in **geheim** de waarde in van een token dat wordt geretourneerd door de Azure-functie (een JWT-token).
1. Selecteer voor **sleutel gebruik** **hand tekening**.
1. Selecteer **Maken**.

> [!IMPORTANT] 
> - Als u zich aanmeldt met Apple, moet de beheerder elke 6 maanden hun client geheim vernieuwen.
> - U moet het Apple client-geheim hand matig vernieuwen als het is verlopen en de nieuwe waarde opslaan in de beleids sleutel.
> - We raden u aan uw eigen herinnering binnen zes maanden in te stellen om een nieuw client geheim te genereren. 

## <a name="configure-apple-as-an-identity-provider"></a>Apple als een id-provider configureren

Als u gebruikers wilt toestaan zich aan te melden met een Apple-ID, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een Apple-ID definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.

1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.firstName user.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Stel **client_id** in op de Service-id. Bijvoorbeeld `com.consoto.azure-ad-b2c-service`.
5. Sla het bestand op.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Selecteer uw Relying Party beleid, bijvoorbeeld `B2C_1A_signup_signin` .
1. Selecteer voor **toepassing** een webtoepassing die u [eerder hebt geregistreerd](troubleshoot-custom-policies.md#troubleshoot-the-runtime). De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **nu uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden de optie **Apple** om u aan te melden met Apple ID.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.

::: zone-end
