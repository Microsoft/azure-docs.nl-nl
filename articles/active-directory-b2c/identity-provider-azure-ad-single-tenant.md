---
title: Aanmelding instellen voor een Azure AD-organisatie
titleSuffix: Azure AD B2C
description: Stel aanmelden in voor een specifieke Azure Active Directory organisatie in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8a3cca7740adb6fa44b162e8c8740d1be1c7aa6b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953883"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Aanmelden instellen voor een specifieke Azure Active Directory organisatie in Azure Active Directory B2C

In dit artikel leest u hoe u aanmelden kunt inschakelen voor gebruikers van een specifieke Azure AD-organisatie met behulp van een gebruikers stroom in Azure AD B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Een Azure AD-app registreren

Als u aanmelden wilt inschakelen voor gebruikers met een Azure AD-account van een specifieke Azure AD-organisatie, moet u in Azure Active Directory B2C (Azure AD B2C) een toepassing maken in [Azure Portal](https://portal.azure.com). Zie [Een toepassing registreren bij het Microsoft-identiteitsplatform](../active-directory/develop/quickstart-register-app.md) voor meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die de Azure AD-Tenant van uw organisatie bevat (bijvoorbeeld contoso.com). Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map die uw Azure AD-Tenant bevat.
1. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
1. Selecteer **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Bijvoorbeeld `Azure AD B2C App`.
1. Accepteer de standaard selectie van **accounts in deze organisatie Directory alleen** voor deze toepassing.
1. Voor de **omleidings-URI** accepteert u de waarde van **Web** en voert u de volgende URL in in kleine letters, waar `your-B2C-tenant-name` wordt vervangen door de naam van uw Azure AD B2C-Tenant.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Bijvoorbeeld `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecteer **Registreren**. Noteer de **Toepassings-id (client)** voor gebruik in een latere stap.
1. Selecteer **certificaten & geheimen** en selecteer vervolgens **Nieuw client geheim**.
1. Voer een **Beschrijving** in voor het geheim, selecteer een verloop en selecteer vervolgens **toevoegen**. Noteer de **waarde** van het geheim voor gebruik in een latere stap.

### <a name="configuring-optional-claims"></a>Optionele claims configureren

Als u de `family_name` en `given_name` claims van Azure ad wilt ophalen, kunt u optionele claims voor uw toepassing configureren in de Azure Portal gebruikers interface of het toepassings manifest. Zie [optionele claims voor uw Azure AD-app bieden](../active-directory/develop/active-directory-optional-claims.md)voor meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer in de sectie **beheren** de optie **app-registraties**.
1. Selecteer in de lijst de toepassing waarvoor u de optionele claims wilt configureren.
1. Selecteer in de sectie **beheren** de optie **token configuratie**.
1. Selecteer **optionele claim toevoegen**.
1. Selecteer **id** voor het **token type**.
1. Selecteer de optionele claims om toe te voegen `family_name` en `given_name` .
1. Klik op **Add**.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD configureren als een id-provider

1. Zorg ervoor dat u de map gebruikt die de Azure AD B2C-tenant bevat. Selecteer het filter **Map + Abonnement** in het bovenste menu en kies de map die uw Azure AD B2C-tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Id-providers** en selecteer vervolgens **Nieuwe OpenID Connect-provider**.
1. Voer een **naam** in. Voer bijvoorbeeld *Contoso Azure AD* in.
1. Voor **URL voor metagegevens** voert u de volgende URL in, waarbij `{tenant}` wordt vervangen door de domeinnaam van uw Azure AD-tenant:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Bijvoorbeeld `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Voor **Client-id** voert u de toepassings-id in die u eerder hebt genoteerd.
1. Voor **Clientgeheim** voert u het clientgeheim in dat u eerder hebt genoteerd.
1. Voer de `openid profile` in voor **Bereik**.
1. Behoud de standaardwaarden voor **Antwoordtype** en **Antwoordmodus**.
1. Voer `contoso.com` in voor **Domeinhint** (optioneel). Raadpleeg [Direct aanmelden met behulp van Azure Active Directory B2C instellen](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Selecteer onder **Claimstoewijzing voor id-provider** de volgende claims:

    - **Gebruikers-id**: *oid*
    - **Weergavenaam**: *name*
    - **Voornaam**: *given_name*
    - **Achternaam**: *family_name*
    - **E-mail**: *preferred_username*

1. Selecteer **Opslaan**.

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Een Azure AD-ID-provider toevoegen aan een gebruikers stroom 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom die u wilt toevoegen aan de Azure AD-ID-provider.
1. Selecteer **Contoso Azure AD** bij de **sociale id-providers**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet de toepassings sleutel opslaan die u hebt gemaakt in uw Azure AD B2C-Tenant.

1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** `Manual` .
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `ContosoAppSecret`.  Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van de sleutel wanneer deze wordt gemaakt, zodat de verwijzing in de XML in de volgende sectie wordt *B2C_1A_ContosoAppSecret*.
1. Voer in het **geheim** uw client geheim in dat u eerder hebt vastgelegd.
1. Selecteer voor **sleutel gebruik** `Signature` .
1. Selecteer **Maken**.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Azure AD configureren als een id-provider

Als u gebruikers wilt toestaan zich aan te melden met een Azure AD-account, moet u Azure AD definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt Azure AD definiëren als een claim provider door Azure AD toe te voegen aan het **ClaimsProvider** -element in het extensie bestand van uw beleid.

1. Open het *TrustFrameworkExtensions.xml* -bestand.
2. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
3. Voeg als volgt een nieuwe **ClaimsProvider** toe:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Werk onder het element **ClaimsProvider** de waarde voor **domein** bij naar een unieke waarde die kan worden gebruikt om deze te onderscheiden van andere id-providers. Bijvoorbeeld `Contoso`. U hoeft geen `.com` aan het einde van deze domein instelling te zetten.
5. Werk onder het element **ClaimsProvider** de waarde voor **DisplayName** bij naar een beschrijvende naam voor de claim provider. Deze waarde wordt momenteel niet gebruikt.

### <a name="update-the-technical-profile"></a>Het technische profiel bijwerken

Als u een token van het Azure AD-eind punt wilt ophalen, moet u de protocollen definiëren die Azure AD B2C moet gebruiken om te communiceren met Azure AD. Dit wordt gedaan binnen het **TechnicalProfile** -element van  **ClaimsProvider**.

1. Werk de ID van het **TechnicalProfile** -element bij. Deze ID wordt gebruikt om te verwijzen naar dit technische profiel van andere onderdelen van het beleid, bijvoorbeeld `AADContoso-OpenIdConnect` .
1. Werk de waarde voor **DisplayName** bij. Deze waarde wordt weer gegeven op de knop aanmelden op het aanmeldings scherm.
1. De waarde voor de **Beschrijving** bijwerken.
1. Azure AD maakt gebruik van het OpenID Connect Connect-protocol, dus zorg ervoor dat de waarde voor **protocol** is `OpenIdConnect` .
1. Stel de waarde van de **meta gegevens** in op `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration` , waarbij `tenant-name` uw Azure AD-Tenant naam is. Bijvoorbeeld: `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Stel **client_id** van de toepassings-id in voor de registratie van de toepassing.
1. Werk onder **CryptographicKeys** de waarde van **StorageReferenceId** bij naar de naam van de beleids sleutel die u eerder hebt gemaakt. Bijvoorbeeld `B2C_1A_ContosoAppSecret`.


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

## <a name="next-steps"></a>Volgende stappen

Wanneer u werkt met aangepaste beleids regels, hebt u mogelijk aanvullende informatie nodig bij het oplossen van problemen met een beleid tijdens de ontwikkeling ervan.

Als u problemen wilt oplossen, kunt u het beleid tijdelijk in ' ontwikkelaars modus ' plaatsen en logboeken verzamelen met Azure-toepassing Insights. Ontdek hoe u in [Azure Active Directory B2C: Logboeken verzamelt](troubleshoot-with-application-insights.md).

::: zone-end