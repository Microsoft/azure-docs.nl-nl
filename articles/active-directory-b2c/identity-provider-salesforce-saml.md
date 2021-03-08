---
title: Aanmelden met een Sales Force SAML-provider instellen met behulp van het SAML-Protocol
titleSuffix: Azure AD B2C
description: Stel aanmelden met een Sales Force SAML-provider in met behulp van het SAML-protocol in Azure Active Directory B2C.
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
ms.openlocfilehash: 63288bca124959463dc6ea16cb9d681c68ad00da
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448185"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-saml-protocol-in-azure-active-directory-b2c"></a>Aanmelden met een Sales Force SAML-provider instellen met behulp van het SAML-protocol in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"
[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel wordt beschreven hoe u aanmelden inschakelt voor gebruikers van een Sales Force-organisatie die gebruikmaakt van [aangepast beleid](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C). U schakelt aanmelden in door een SAML- [ID-provider](identity-provider-generic-saml.md) aan een aangepast beleid toe te voegen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]
- Als u dit nog niet hebt gedaan, meldt u zich aan voor een [gratis Developer Edition-account](https://developer.salesforce.com/signup). In dit artikel wordt de [Sales Force-ervaring](https://developer.salesforce.com/page/Lightning_Experience_FAQ)gebruikt.
- [Stel een mijn domein](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0) in voor uw Sales Force-organisatie.

## <a name="set-up-salesforce-as-an-identity-provider"></a>Sales Force instellen als een id-provider

1. [Meld u aan bij Sales Force](https://login.salesforce.com/).
2. Vouw in het menu links onder **instellingen** de optie **identiteit** uit en selecteer vervolgens **ID-provider**.
3. Selecteer **ID-provider inschakelen**.
4. Onder **Selecteer het certificaat** selecteert u het certificaat dat u wilt gebruiken om te communiceren met Azure AD B2C. U kunt het standaard certificaat gebruiken.
5. Klik op **Opslaan**.

### <a name="create-a-connected-app-in-salesforce"></a>Een verbonden app maken in Sales Force

1. Selecteer op de pagina **ID-provider** de optie **service providers die nu zijn gemaakt via verbonden apps. Klik hier.**
2. Voer onder **basis informatie** de vereiste waarden in voor de verbonden app.
3. Schakel onder **Web app-instellingen** het selectie vakje **SAML inschakelen** in.
4. Voer in het veld **Entiteits-ID** de volgende URL in. Zorg ervoor dat u de waarde vervangt door `your-tenant` de naam van uw Azure AD B2C-Tenant.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. Voer in het veld **ACS-URL** de volgende URL in. Zorg ervoor dat u de waarde vervangt door `your-tenant` de naam van uw Azure AD B2C-Tenant.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. Ga naar de onderkant van de lijst en klik vervolgens op **Opslaan**.

### <a name="get-the-metadata-url"></a>URL voor meta gegevens ophalen

1. Klik op de pagina overzicht van uw verbonden app op **beheren**.
2. Kopieer de waarde voor het **detectie-eind punt voor meta gegevens** en sla het op. U gebruikt deze verderop in dit artikel.

### <a name="set-up-salesforce-users-to-federate"></a>Sales Force-gebruikers instellen om te communiceren

1. Klik op de pagina **beheren** van uw verbonden app op **profielen beheren**.
2. Selecteer de profielen (of groepen gebruikers) die u wilt laten communiceren met Azure AD B2C. Als systeem beheerder selecteert u de **systeem beheerder** selectie vakje, zodat u kunt communiceren met uw Sales Force-account.

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet het certificaat dat u hebt gemaakt in uw Azure AD B2C-Tenant opslaan.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
5. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
6. Kies voor **Opties** `Upload` .
7. Voer een **naam** in voor het beleid. Bijvoorbeeld SAMLSigningCert. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Blader naar en selecteer het B2CSigningCert. pfx-certificaat dat u hebt gemaakt.
9. Voer het **wacht woord** voor het certificaat in.
3. Klik op **Create**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met een Sales Force-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een Sales Force-account definiëren als een claim provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid. Zie [een SAML-ID-provider definiëren](identity-provider-generic-saml.md)voor meer informatie.

1. Open de *TrustFrameworkExtensions.xml*.
1. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
1. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-SAML2">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-idp"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Werk de waarde van **PartnerEntity** bij met de URL voor Sales Force-meta gegevens die u eerder hebt gekopieerd.
1. Werk de waarde van beide exemplaren van **StorageReferenceId** bij naar de naam van de sleutel van uw handtekening certificaat. Bijvoorbeeld B2C_1A_SAMLSigningCert.
1. Zoek de `<ClaimsProviders>` sectie en voeg het volgende XML-fragment toe. Als uw beleid al het `SM-Saml-idp` technische profiel bevat, gaat u verder met de volgende stap. Zie [sessie beheer voor eenmalige aanmelding](custom-policy-reference-sso.md)voor meer informatie.

    ```xml
    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-Saml-idp">
          <DisplayName>Session Management Provider</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="IncludeSessionIndex">false</Item>
            <Item Key="RegisterServiceProviders">false</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```
1. Sla het bestand op.

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
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-SAML2" />
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