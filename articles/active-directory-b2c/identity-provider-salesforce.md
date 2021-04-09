---
title: Registratie instellen en aanmelden met een Sales Force-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden bij klanten met Sales Force-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f87ef03a3af110ec17b7d907d4c2eac1bc1ca58b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579923"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Sales Force-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Een Sales Force-toepassing maken

Als u aanmelden wilt inschakelen voor gebruikers met een Sales Force-account in Azure Active Directory B2C (Azure AD B2C), moet u een toepassing maken in uw Sales Force- [App-Manager](https://login.salesforce.com/). Zie voor meer informatie [basis instellingen voor verbonden apps configureren](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm)en [OAuth-instellingen voor API-integratie inschakelen](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm)

1. [Meld u aan bij Sales Force](https://login.salesforce.com/).
1. Selecteer in het menu de optie **Setup**.
1.  Vouw **apps** uit en selecteer vervolgens **app-beheer**.
1. Selecteer **nieuwe verbonden app**.
1. Onder de **basis informatie** voert u het volgende in:
    1. **Naam van verbonden app** : de naam van de verbonden app wordt weer gegeven in app manager en op de tegel voor het starten van de app. De naam moet uniek zijn binnen uw organisatie. 
    1. **API-naam** 
    1. **E-mail adres van contact persoon** : het e-mail adres voor Sales Force
1. Selecteer onder **API (OAuth-instellingen inschakelen)** de optie **OAuth-instellingen inschakelen**
    1. Voer in voor de **call back-URL** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Als u een [aangepast domein](custom-domain.md)gebruikt, voert u in `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Vervang door `your-tenant-name` de naam van uw Tenant en `your-domain-name` met uw aangepaste domein. U moet alle kleine letters gebruiken bij het invoeren van de naam van uw Tenant, zelfs als de Tenant is gedefinieerd met hoofd letters in Azure AD B2C.
    1. Selecteer in de **geselecteerde OAuth-scopes** **toegang tot uw basis gegevens (id, profiel, e-mail, adres, telefoon)** en **sta toegang toe tot uw unieke id (OpenID Connect)**.
    1. Selecteer **geheim vereisen voor webserver stroom**.
1. **ID-token configureren** selecteren 
    1. Stel het **token in op** 5 minuten geldig.
    1. Selecteer **standaard claims toevoegen**.
1. Klik op **Opslaan**.
1. Kopieer de waarden van de **consument sleutel** en het **consument geheim**. U hebt beide nodig om Sales Force te configureren als een id-provider in uw Tenant. **Client geheim** is een belang rijke beveiligings referentie.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Configureer Sales Force als een id-provider

1. Zorg ervoor dat u de map gebruikt die de Azure AD B2C-tenant bevat. Selecteer het filter **Map + Abonnement** in het bovenste menu en kies de map die uw Azure AD B2C-tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Id-providers** en selecteer vervolgens **Nieuwe OpenID Connect-provider**.
1. Voer een **naam** in. Voer bijvoorbeeld *Sales Force* in.
1. Voer voor **meta gegevens-URL** de URL in van het [Sales Force OpenID Connect Connect-configuratie document](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Voor een sandbox wordt login.salesforce.com vervangen door test.salesforce.com. Voor een community wordt login.salesforce.com vervangen door de Community-URL, zoals username.force.com/.well-known/openid-configuration. De URL moet HTTPS zijn.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. Voor **Client-id** voert u de toepassings-id in die u eerder hebt genoteerd.
1. Voor **Clientgeheim** voert u het clientgeheim in dat u eerder hebt genoteerd.
1. Voer de `openid id profile email` in voor **Bereik**.
1. Behoud de standaardwaarden voor **Antwoordtype** en **Antwoordmodus**.
1. Voer `contoso.com` in voor **Domeinhint** (optioneel). Raadpleeg [Direct aanmelden met behulp van Azure Active Directory B2C instellen](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Selecteer onder **Claimstoewijzing voor id-provider** de volgende claims:

    - **Gebruikers-id**: *Sub*
    - **Weergavenaam**: *name*
    - **Voornaam**: *given_name*
    - **Achternaam**: *family_name*
    - **E-mail**: *e-mail*

1. Selecteer **Opslaan**.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Een Sales Force-ID-provider toevoegen aan een gebruikers stroom 

Op dit moment is de Sales Force-ID-provider ingesteld, maar deze is nog niet beschikbaar op de aanmeldings pagina's. De Sales Force-ID-provider toevoegen aan een gebruikers stroom:

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom die u wilt toevoegen van de Sales Force-ID-provider.
1. Selecteer **Sales Force** onder de **providers voor sociale identificatie**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **gebruikers stroom uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden de optie **Sales Force** om u aan te melden met Sales Force-account.

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
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `SalesforceSecret`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in het **geheim** uw client geheim in dat u eerder hebt vastgelegd.
9. Selecteer voor **sleutel gebruik** `Signature` .
10. Klik op **Create**.

## <a name="configure-salesforce-as-an-identity-provider"></a>Configureer Sales Force als een id-provider

Als u gebruikers wilt toestaan zich aan te melden met een Sales Force-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een Sales Force-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.

1. Open de *TrustFrameworkExtensions.xml*.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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

4. De **meta gegevens** worden ingesteld op de URL van het [configuratie document Sales Force OpenID Connect Connect](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm). Voor een sandbox wordt login.salesforce.com vervangen door test.salesforce.com. Voor een community wordt login.salesforce.com vervangen door de Community-URL, zoals username.force.com/.well-known/openid-configuration. De URL moet HTTPS zijn.
5. Stel **client_id** van de toepassings-id in voor de registratie van de toepassing.
6. Sla het bestand op.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Selecteer uw Relying Party beleid, bijvoorbeeld `B2C_1A_signup_signin` .
1. Selecteer voor **toepassing** een webtoepassing die u [eerder hebt geregistreerd](troubleshoot-custom-policies.md#troubleshoot-the-runtime). De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **nu uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden de optie **Sales Force** om u aan te melden met Sales Force-account.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.


::: zone-end

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [door geven van een Sales Force-token aan uw toepassing](idp-pass-through-user-flow.md).
