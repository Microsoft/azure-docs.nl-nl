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
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 21da8f79772d9648836bedec89cb5d7014486dc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798356"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Wachtwoord wijzigingen configureren met aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

In Azure Active Directory B2C (Azure AD B2C) kunt u gebruikers die zijn aangemeld met een lokaal account, in staat stellen hun wacht woord te wijzigen zonder dat ze hun identiteit moeten bewijzen via e-mail verificatie. De stroom voor het wijzigen van het wacht woord bestaat uit de volgende stappen:

1. De gebruiker meldt zich aan bij het lokale account. Als de sessie nog steeds actief is, wordt de gebruiker door Azure AD B2C geautoriseerd en wordt de volgende stap overgeslagen.
1. De gebruiker controleert het **oude wacht woord**, waarna het **nieuwe wacht woord** wordt gemaakt en bevestigd.

![Wachtwoord wijzigings stroom](./media/add-password-change-policy/password-change-flow.png)  

> [!TIP]
> Met de stroom voor wachtwoord wijziging kunnen gebruikers hun wacht woord alleen wijzigen als de gebruiker het wacht woord kent en dit wil wijzigen. U wordt aangeraden om [selfservice voor wachtwoord herstel](add-password-reset-policy.md) ook in te scha kelen om aanvragen te ondersteunen waarbij de gebruiker het wacht woord vergeet.

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="prerequisites"></a>Vereisten

* Voer de stappen in aan de [slag met aangepast beleid in Active Directory B2C](custom-policy-get-started.md).
* Als u dit nog niet hebt gedaan, [registreert u een webtoepassing in azure Active Directory B2C](tutorial-register-applications.md).

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
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
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

3. Het [UserJourney](userjourneys.md) -element definieert het pad dat de gebruiker nodig heeft bij interactie met uw toepassing. Voeg het **UserJourneys** -element toe als het niet bestaat met de **UserJourney** geïdentificeerd als `PasswordChange` :

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
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
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
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

## <a name="upload-and-test-the-policy"></a>Het beleid uploaden en testen

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

- Zoek het voorbeeld beleid op [github](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
- Meer informatie over hoe u [wachtwoord complexiteit kunt configureren in azure AD B2C](password-complexity.md).
- Stel een [wachtwoord herstel stroom](add-password-reset-policy.md)in.

::: zone-end
