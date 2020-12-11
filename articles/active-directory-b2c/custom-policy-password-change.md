---
title: Wachtwoord wijziging configureren met aangepaste beleids regels
titleSuffix: Azure AD B2C
description: Meer informatie over hoe u gebruikers in staat stelt hun wacht woord te wijzigen met behulp van aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a471f510f60eeb6428c5c4e8ec7cccd1e0804d10
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108855"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Wachtwoord wijzigingen configureren met aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C) kunt u gebruikers die zijn aangemeld met een lokaal account, hun wacht woord wijzigen zonder dat ze hun authenticiteit moeten bewijzen via e-mail verificatie. Als de sessie verloopt op het moment dat de gebruiker het wacht woord wijzigt, wordt deze gevraagd om u opnieuw aan te melden. In dit artikel leest u hoe u wachtwoord wijzigingen in [aangepaste beleids regels](custom-policy-overview.md)kunt configureren. Het is ook mogelijk om [selfservice voor wachtwoord herstel](user-flow-self-service-password-reset.md) voor gebruikers stromen te configureren.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in aan de [slag met aangepast beleid in Active Directory B2C](custom-policy-get-started.md).

## <a name="add-the-elements"></a>De elementen toevoegen

1. Open uw *TrustframeworkExtensions.xml* -bestand en voeg het volgende **claim** type-element toe met een id van `oldPassword` aan het element [ClaimsSchema](claimsschema.md) :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Een [ClaimsProvider](claimsproviders.md) -element bevat het technische profiel waarmee de gebruiker wordt geverifieerd. Voeg de volgende claim providers toe aan het **ClaimsProviders** -element:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="grant_type">password</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Vervang door `IdentityExperienceFrameworkAppId` de toepassings-id van de IdentityExperienceFramework-toepassing die u hebt gemaakt in de hand leiding voor vereisten. Vervang door `ProxyIdentityExperienceFrameworkAppId` de toepassings-id van de ProxyIdentityExperienceFramework-toepassing die u eerder hebt gemaakt.

3. Het [UserJourney](userjourneys.md) -element definieert het pad dat de gebruiker nodig heeft bij interactie met uw toepassing. Voeg het **UserJourneys** -element toe als het niet bestaat met de **UserJourney** geïdentificeerd als `PasswordChange` :

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Sla het *TrustFrameworkExtensions.xml* -beleids bestand op.
5. Kopieer het *ProfileEdit.xml* bestand dat u met het eerste pakket hebt gedownload en geef het de naam *ProfileEditPasswordChange.xml*.
6. Open het nieuwe bestand en werk het kenmerk **PolicyId** bij met een unieke waarde. Deze waarde is de naam van uw beleid. Bijvoorbeeld *B2C_1A_profile_edit_password_change*.
7. Wijzig het kenmerk **ReferenceId** in `<DefaultUserJourney>` zodat dit overeenkomt met de id van de nieuwe gebruikers traject die u hebt gemaakt. Bijvoorbeeld *PasswordChange*.
8. Sla uw wijzigingen op.

U kunt [hier](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change)het voorbeeld beleid vinden.

## <a name="test-your-policy"></a>Uw beleid testen

Bij het testen van uw toepassingen in Azure AD B2C kan het nuttig zijn om het Azure AD B2C-token te retour neren om `https://jwt.ms` de claims daarin te kunnen bekijken.

### <a name="upload-the-files"></a>De bestanden uploaden

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer een **Framework voor identiteits ervaring**.
5. Klik op het tabblad Aangepaste beleids regels op **beleid uploaden**.
6. Selecteer **het beleid overschrijven als dit bestaat**, en zoek en selecteer het *TrustframeworkExtensions.xml* bestand.
7. Klik op **Uploaden**.
8. Herhaal stap 5 tot en met 7 voor het Relying Party bestand, zoals *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Het beleid uitvoeren

1. Open het beleid dat u hebt gewijzigd. Bijvoorbeeld *B2C_1A_profile_edit_password_change*.
2. Selecteer voor **toepassing** de toepassing die u eerder hebt geregistreerd. Om het token weer te geven, moet de **antwoord-URL** worden weer gegeven `https://jwt.ms` .
3. Klik op **Nu uitvoeren**. Meld u aan met het account dat u eerder hebt gemaakt. U hebt nu de mogelijkheid om het wacht woord te wijzigen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe u [wachtwoord complexiteit kunt configureren met aangepaste beleids regels in azure Active Directory B2C](password-complexity.md).
