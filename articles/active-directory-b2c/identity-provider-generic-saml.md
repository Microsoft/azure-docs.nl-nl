---
title: Registratie instellen en aanmelden met SAML-ID-provider
titleSuffix: Azure Active Directory B2C
description: Stel registratie in en meld u aan met een SAML-ID-provider (IdP) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 71d51c4303dbc4c0c2668dbfcf388b0d6c6bcffe
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107408"
---
# <a name="set-up-sign-up-and-sign-in-with-saml-identity-provider-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met SAML-ID-provider met behulp van Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ondersteunt Federatie met SAML 2,0-id-providers. Dit artikel laat u zien hoe u aanmelden met een gebruikers account voor een SAML-ID-provider kunt inschakelen, zodat gebruikers zich kunnen aanmelden met hun bestaande sociale of zakelijke identiteiten, zoals [ADFS](identity-provider-adfs2016-custom.md) en [Sales Force](identity-provider-salesforce-saml.md).

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="scenario-overview"></a>Overzicht van scenario's

U kunt Azure AD B2C zodanig configureren dat gebruikers zich kunnen aanmelden bij uw toepassing met referenties van externe Social of ENTER prise SAML id providers (IdP). Wanneer Azure AD B2C federeert met een SAML-ID-provider, fungeert deze als een **service provider** die een SAML-aanvraag initieert naar de SAML- **ID-provider** en wacht op een SAML-reactie. In het volgende diagram:

1. De toepassing initieert een autorisatie aanvraag om Azure AD B2C. De toepassing kan een [OAuth 2,0](protocols-overview.md) -of [OpenID Connect Connect](openid-connect.md) -toepassing of een [SAML-service provider](saml-service-provider.md)zijn. 
1. Op de aanmeldings pagina van Azure AD B2C kiest u ervoor om u aan te melden met een SAML-ID-provider account (bijvoorbeeld *Contoso*). Azure AD B2C initieert een SAML-autorisatie aanvraag en neemt de gebruiker de SAML-ID-provider om de aanmelding te volt ooien.
1. De SAML-ID-provider retourneert een SAML-reactie.
1. Azure AD B2C valideert het SAML-token, extraheert claims, geeft een eigen token uit en neemt de gebruiker terug naar de toepassing.  

![Meld u aan met de stroom van de SAML-ID-provider](./media/identity-provider-generic-saml/sign-in-with-saml-identity-provider-flow.png)

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="components-of-the-solution"></a>Onderdelen van de oplossing

De volgende onderdelen zijn vereist voor dit scenario:

* Een SAML- **ID-provider** met de mogelijkheid om SAML-aanvragen van Azure AD B2C te ontvangen, te decoderen en erop te reageren.
* Een openbaar beschik bare SAML- **eind punt voor meta gegevens** voor uw ID-provider.
* Een [Azure AD B2C-Tenant](tutorial-create-tenant.md).
 

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

Als u een vertrouwens relatie tussen Azure AD B2C en uw SAML-ID-provider wilt instellen, moet u een geldig x509-certificaat met de persoonlijke sleutel opgeven. Azure AD B2C de SAML-aanvragen ondertekent met behulp van de persoonlijke sleutel van het certificaat. De ID-provider valideert de aanvraag met behulp van de open bare sleutel van het certificaat. De open bare sleutel is toegankelijk via technische profiel meta gegevens. U kunt het CER-bestand ook hand matig uploaden naar uw SAML-ID-provider.

Een zelfondertekend certificaat is acceptabel voor de meeste scenario's. Voor productie omgevingen kunt u het beste een x509-certificaat gebruiken dat is uitgegeven door een certificerings instantie. Zoals verderop in dit document wordt beschreven, kunt u voor een niet-productie omgeving de SAML-ondertekening aan beide zijden uitschakelen.

### <a name="obtain-a-certificate"></a>Een certificaat verkrijgen

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="upload-the-certificate"></a>Het certificaat uploaden

U moet uw certificaat opslaan in uw Azure AD B2C-Tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** `Upload` .
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `SAMLSigningCert`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Blader naar en selecteer het pfx-bestand van het certificaat met de persoonlijke sleutel.
1. Klik op **Create**.

## <a name="configure-the-saml-technical-profile"></a>Het technische SAML-profiel configureren

Definieer de SAML-ID-provider door deze toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid. De claim providers bevatten een SAML-technische profiel dat de eind punten bepaalt en de protocollen die nodig zijn om te communiceren met de SAML-ID-provider. Een claim provider toevoegen met een technische SAML-profiel:

1. Open de *TrustFrameworkExtensions.xml*.
1. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
1. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>Contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your SAML identity provider account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="http://schemas.microsoft.com/identity/claims/displayname" />
            <OutputClaim ClaimTypeReferenceId="email"  />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
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

Werk de volgende XML-elementen bij met de relevante waarde:

|XML-element  |Waarde  |
|---------|---------|
|ClaimsProvider\Domain  | De domein naam die wordt gebruikt voor [direct aanmelden](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider). Voer de domein naam in die u wilt gebruiken voor de directe aanmelding. Bijvoorbeeld *contoso.com*. |
|TechnicalProfile\DisplayName|Deze waarde wordt weer gegeven op de knop aanmelden op het aanmeldings scherm. Bijvoorbeeld *Contoso*. |
|Metadata\PartnerEntity|URL van de meta gegevens van de SAML-ID-provider. Of u kunt de meta gegevens van de identiteits provider kopiëren en deze toevoegen binnen het CDATA-element `<![CDATA[Your IDP metadata]]>` .|

## <a name="map-the-claims"></a>De claims toewijzen

Het **OutputClaims** -element bevat een lijst met claims die zijn geretourneerd door de SAML-ID-provider. Wijs de naam van de claim die in uw beleid is gedefinieerd toe aan de naam van de verklaring die is gedefinieerd in de ID-provider. Controleer uw ID-provider voor de lijst met claims (verklaringen). Zie [claim toewijzing](identity-provider-generic-saml-options.md#claims-mapping)voor meer informatie.

In het bovenstaande voor beeld bevat *Contoso-SAML2* de claims die worden geretourneerd door een SAML-ID-provider:

* De claim **issuerUserId** wordt toegewezen aan de **assertionSubjectName** -claim.
* De **first_name** claim wordt toegewezen aan de claim voor de **OpgegevenNaam** .
* De **last_name** claim wordt toegewezen aan de claim **Achternaam** .
* De **DisplayName** claim is toegewezen aan de `http://schemas.microsoft.com/identity/claims/displayname` claim.
* De **e-mail** claim zonder naam toewijzing.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de ID-provider:

* De claim **Identity provider** die de naam van de ID-provider bevat.
* De **authenticationSource** claim met de standaard waarde **socialIdpAuthentication**.
 
### <a name="add-the-saml-session-technical-profile"></a>Het technische profiel van de SAML-sessie toevoegen

Als u het `SM-Saml-idp` technische profiel van de SAML-sessie nog niet hebt, voegt u er een toe aan uw extensie beleid. Zoek de `<ClaimsProviders>` sectie en voeg het volgende XML-fragment toe. Als uw beleid al het `SM-Saml-idp` technische profiel bevat, gaat u verder met de volgende stap. Zie [sessie beheer voor eenmalige aanmelding](custom-policy-reference-sso.md)voor meer informatie.

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

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-create-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="configure-your-saml-identity-provider"></a>Uw SAML-ID-provider configureren

Nadat het beleid is geconfigureerd, moet u uw SAML-ID-provider configureren met de Azure AD B2C SAML-meta gegevens. De SAML-meta gegevens worden gebruikt in het SAML-protocol om de configuratie van uw beleid, de **service provider**, beschikbaar te stellen. Hiermee wordt de locatie van de services gedefinieerd, zoals aanmelden en afmelden, certificaten, aanmeldings methode en meer.

Elke SAML-ID-provider heeft verschillende stappen voor het instellen van een service provider. Sommige SAML-id-providers vragen om de Azure AD B2C meta gegevens, terwijl anderen het meta gegevensbestand hand matig moeten door lopen en de informatie kunnen opgeven. Raadpleeg de documentatie van uw identiteits provider voor hulp.

In het volgende voor beeld ziet u een URL-adres voor de SAML-meta gegevens van een Azure AD B2C technisch profiel:

```
https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

Vervang de volgende waarden:

- **uw-Tenant** met de naam van uw Tenant, zoals your-Tenant.onmicrosoft.com.
- **uw-beleid** met de naam van uw beleid. Bijvoorbeeld B2C_1A_signup_signin_adfs.
- **uw technische profiel** met de naam van uw technische profiel voor de SAML-identiteits provider. Bijvoorbeeld contoso-SAML2.

Open een browser en navigeer naar de URL. Zorg ervoor dat u de juiste URL typt en dat u toegang hebt tot het XML-bestand met meta gegevens.

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**
1. Selecteer uw Relying Party beleid, bijvoorbeeld `B2C_1A_signup_signin` .
1. Selecteer voor **toepassing** een webtoepassing die u [eerder hebt geregistreerd](troubleshoot-custom-policies.md#troubleshoot-the-runtime). De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **nu uitvoeren** .

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

- [Opties configureren voor de SAML-ID-provider met Azure Active Directory B2C](identity-provider-generic-saml-options.md)

::: zone-end
