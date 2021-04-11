---
title: Registratie instellen en aanmelden met een LinkedIn-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met LinkedIn-accounts in uw toepassingen met Azure Active Directory B2C.
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
ms.openlocfilehash: 51a9635d42b07eb27b05312d292ca890c7963b4f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028227"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een LinkedIn-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>Een LinkedIn-toepassing maken

Als u aanmelden voor gebruikers met een LinkedIn-account in Azure Active Directory B2C (Azure AD B2C) wilt inschakelen, moet u een toepassing maken op de [website van LinkedIn-ontwikkel aars](https://www.developer.linkedin.com/). Zie [autorisatie code flow](/linkedin/shared/authentication/authorization-code-flow)voor meer informatie. Als u nog geen LinkedIn-account hebt, kunt u zich aanmelden bij [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Meld u aan bij de [LinkedIn-ontwikkelaars website](https://www.developer.linkedin.com/) met uw LinkedIn-account referenties.
1. Selecteer **mijn apps** en klik vervolgens op **app maken**.
1. Voer de **app-naam**, de LinkedIn- **pagina**, de URL van het **Privacybeleid** en het **app-logo** in.
1. Ga akkoord met de **gebruiks voorwaarden van** de LinkedIn API en klik op **app maken**.
1. Selecteer het tabblad **auth** . Kopieer onder **verificatie sleutels** de waarden voor **client-id** en **client geheim**. U hebt beide nodig om LinkedIn te configureren als een id-provider in uw Tenant. **Client geheim** is een belang rijke beveiligings referentie.
1. Selecteer het potlood bewerken naast **geautoriseerde omleidings-url's voor uw app** en selecteer vervolgens **omleidings-URL toevoegen**. Voer `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` in. Als u een [aangepast domein](custom-domain.md)gebruikt, voert u in `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Vervang door `your-tenant-name` de naam van uw Tenant en `your-domain-name` met uw aangepaste domein. U moet alle kleine letters gebruiken bij het invoeren van de naam van uw Tenant, zelfs als de Tenant is gedefinieerd met hoofd letters in Azure AD B2C. Selecteer **Update**.
1. Uw LinkedIn-app is standaard niet goedgekeurd voor scopes die betrekking hebben op aanmelden. Als u een beoordeling wilt aanvragen, selecteert u het tabblad **producten** en selecteert u **Aanmelden met LinkedIn**. Wanneer de controle is voltooid, worden de vereiste bereiken toegevoegd aan uw toepassing.
   > [!NOTE]
   > U kunt de bereiken weer geven die momenteel zijn toegestaan voor uw app op het tabblad **auth** in het gedeelte **OAuth 2,0 scopes** .

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>LinkedIn configureren als een id-provider

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers** en selecteer vervolgens **LinkedIn**.
1. Voer een **naam** in. Bijvoorbeeld *LinkedIn*.
1. Voer voor de **client-id** de client-id in van de LinkedIn-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim** het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>LinkedIn-ID-provider toevoegen aan een gebruikers stroom 

Op dit moment is de LinkedIn-ID-provider ingesteld, maar deze is nog niet beschikbaar op de aanmeldings pagina's. De LinkedIn-ID-provider toevoegen aan een gebruikers stroom:

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom waaraan u de LinkedIn-ID-provider wilt toevoegen.
1. Selecteer in het geval van **sociale id-providers** **LinkedIn**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **gebruikers stroom uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden de optie **LinkedIn** om u aan te melden met het LinkedIn-account.

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
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `LinkedInSecret`. De prefix *B2C_1A_* wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer in het **geheim** het client geheim in dat u eerder hebt vastgelegd.
9. Selecteer voor **sleutel gebruik** `Signature` .
10. Klik op **Create**.

## <a name="configure-linkedin-as-an-identity-provider"></a>LinkedIn configureren als een id-provider

Als u gebruikers wilt toestaan zich aan te melden met een LinkedIn-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

Definieer een LinkedIn-account als een claim provider door het toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid.

1. Open het bestand *SocialAndLocalAccounts/* * TrustFrameworkExtensions.xml** * in de editor. Dit bestand bevindt zich in het [aangepaste beleids Starter Pack][starter-pack] dat u hebt gedownload als onderdeel van een van de vereisten.
1. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
1. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
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

1. Vervang de waarde van **client_id** door de client-id van de LinkedIn-toepassing die u eerder hebt opgenomen.
1. Sla het bestand op.

### <a name="add-the-claims-transformations"></a>De claim transformaties toevoegen

Voor het technische profiel LinkedIn moeten de **ExtractGivenNameFromLinkedInResponse** -en **ExtractSurNameFromLinkedInResponse** -claim transformaties worden toegevoegd aan de lijst met ClaimsTransformations. Als er geen **ClaimsTransformations** -element in uw bestand is gedefinieerd, voegt u de bovenliggende XML-elementen toe zoals hieronder wordt weer gegeven. De claim transformaties hebben ook een nieuw claim type gedefinieerd met de naam **nullStringClaim**.

Voeg het element **BuildingBlocks** toe aan de bovenkant van het *TrustFrameworkExtensions.xml* -bestand. Zie *TrustFrameworkBase.xml* voor een voor beeld.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
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
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Selecteer uw Relying Party beleid, bijvoorbeeld `B2C_1A_signup_signin` .
1. Selecteer voor **toepassing** een webtoepassing die u [eerder hebt geregistreerd](tutorial-register-applications.md). De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **nu uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden de optie **LinkedIn** om u aan te melden met het LinkedIn-account.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.

## <a name="migration-from-v10-to-v20"></a>Migratie van v 1.0 naar v 2.0

LinkedIn heeft [de api's recent bijgewerkt van v 1.0 naar v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Als u de bestaande configuratie wilt migreren naar de nieuwe configuratie, gebruikt u de informatie in de volgende secties om de elementen in het technische profiel bij te werken.

### <a name="replace-items-in-the-metadata"></a>Items in de meta gegevens vervangen

Werk in het bestaande **META** gegevenselement van de **TechnicalProfile** de volgende **item** elementen bij van:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Aan:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Items toevoegen aan de meta gegevens

Voeg in de **meta gegevens** van de **TechnicalProfile** de volgende **item** elementen toe:

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>De OutputClaims bijwerken

Werk in de bestaande **OutputClaims** van de **TechnicalProfile** de volgende **output claim** -elementen bij:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Aan:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Nieuwe OutputClaimsTransformation-elementen toevoegen

Voeg in het **OutputClaimsTransformations** van de **TechnicalProfile** de volgende **OutputClaimsTransformation** -elementen toe:

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definieer de nieuwe claim transformaties en het claim type

In de laatste stap hebt u nieuwe claim transformaties toegevoegd die moeten worden gedefinieerd. Als u de claim transformaties wilt definiëren, voegt u deze toe aan de lijst met **ClaimsTransformations**. Als er geen **ClaimsTransformations** -element in uw bestand is gedefinieerd, voegt u de bovenliggende XML-elementen toe zoals hieronder wordt weer gegeven. De claim transformaties hebben ook een nieuw claim type gedefinieerd met de naam **nullStringClaim**.

Het element **BuildingBlocks** moet aan de bovenkant van het bestand worden toegevoegd. Zie de *TrustframeworkBase.xml* als voor beeld.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Een e-mail adres verkrijgen

Als onderdeel van de LinkedIn-migratie van v 1.0 naar v 2.0, is er een aanvullende aanroep naar een andere API vereist om het e-mail adres op te halen. Ga als volgt te werk als u het e-mail adres moet verkrijgen tijdens het aanmelden:

1. Voer de bovenstaande stappen uit om Azure AD B2C toe te staan om te communiceren met LinkedIn zodat gebruikers zich kunnen aanmelden. Als onderdeel van de Federatie ontvangt Azure AD B2C het toegangs token voor LinkedIn.
1. Sla het LinkedIn-toegangs token op in een claim. [Zie de instructies hier](idp-pass-through-user-flow.md).
1. Voeg de volgende claim provider toe die de aanvraag maakt voor de `/emailAddress` API van LinkedIn. Als u deze aanvraag wilt autoriseren, hebt u het LinkedIn-toegangs token nodig.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Voeg de volgende indeling toe Step Into uw gebruikers traject, zodat de API-claim provider wordt geactiveerd wanneer een gebruiker zich aanmeldt met behulp van LinkedIn. Zorg ervoor dat u het `Order` juiste nummer bijwerkt. Voeg deze stap onmiddellijk toe na de Orchestration-stap die het technische profiel voor LinkedIn activeert.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Het e-mail adres van LinkedIn ophalen tijdens het aanmelden is optioneel. Als u ervoor kiest om het e-mail bericht niet van LinkedIn te ontvangen, maar er wel een vereisen tijdens de aanmelding, moet de gebruiker het e-mail adres hand matig invoeren en valideren.

Voor een volledig voor beeld van een beleid dat gebruikmaakt van de LinkedIn-ID-provider, raadpleegt u het [aangepaste beleid Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migratie van v 1.0 naar v 2.0

LinkedIn heeft [de api's recent bijgewerkt van v 1.0 naar v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Als onderdeel van de migratie kan Azure AD B2C alleen de volledige naam van de LinkedIn-gebruiker verkrijgen tijdens de registratie. Als een e-mail adres een van de kenmerken is die worden verzameld tijdens het aanmelden, moet de gebruiker het e-mail adres hand matig invoeren en valideren.

::: zone-end