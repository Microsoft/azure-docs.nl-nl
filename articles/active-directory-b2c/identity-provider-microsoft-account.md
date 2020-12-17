---
title: Registratie instellen en aanmelden met een micro soft-account
titleSuffix: Azure AD B2C
description: Bied u de mogelijkheid om u aan te melden en u aan te melden bij klanten met micro soft-accounts in uw toepassingen met Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/07/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 123b36ba854bec8b363d59bbed5e70f18da1e578
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653704"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Registratie instellen en aanmelden met een Microsoft-account met behulp van Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-microsoft-account-application"></a>Een Microsoft-account-toepassing maken

Als u een Microsoft-account als een [ID-provider](openid-connect.md) in Azure Active Directory B2C (Azure AD B2C) wilt gebruiken, moet u een toepassing maken in de Azure AD-Tenant. De Azure AD-tenant is niet dezelfde als uw Azure AD B2C-tenant. Als u nog geen Microsoft-account hebt, kunt u er een op ontvangen [https://www.live.com/](https://www.live.com/) .

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD-tenant bevat door in het bovenste menu op het filter **Map en abonnement** te klikken en de map te kiezen waarin de Azure AD-tenant zich bevindt.
1. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
1. Selecteer **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Bijvoorbeeld *MSAapp1*.
1. Onder **ondersteunde account typen** selecteert u **accounts in elke organisatie Directory (een Azure AD-adres lijst en persoonlijke micro soft-accounts (bijvoorbeeld Skype, Xbox)**.

   Zie [Quick Start: een toepassing registreren bij het micro soft-identiteits platform](../active-directory/develop/quickstart-register-app.md)voor meer informatie over de verschillende selecties van het account type.
1. Onder **omleidings-URI (optioneel)** selecteert u **Web** en voert u `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/oauth2/authresp` in het tekstvak in. Vervang door `<tenant-name>` de naam van uw Azure AD B2C-Tenant.
1. Selecteer **Registreren**
1. Noteer de **id van de toepassing (client)** die wordt weer gegeven op de overzichts pagina van de toepassing. U hebt deze nodig wanneer u de ID-provider in de volgende sectie configureert.
1. **Certificaten & geheimen** selecteren
1. Klik op **Nieuw clientgeheim**
1. Voer een **Beschrijving** in voor het geheim, bijvoorbeeld *toepassings wachtwoord 1*, en klik vervolgens op **toevoegen**.
1. Noteer het toepassings wachtwoord dat wordt weer gegeven in de kolom **waarde** . U hebt deze nodig wanneer u de ID-provider in de volgende sectie configureert.

::: zone pivot="b2c-user-flow"

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Een Microsoft-account als een id-provider configureren

1. Meld u als globale beheerder van de Azure AD B2C-tenant aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **id-providers** en selecteer vervolgens **micro soft-account**.
1. Voer een **naam** in. Bijvoorbeeld *MSA*.
1. Voer voor de **client-id** de toepassings-id (client) in van de Azure AD-toepassing die u eerder hebt gemaakt.
1. Voer voor het **client geheim** het client geheim in dat u hebt vastgelegd.
1. Selecteer **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configuring-optional-claims"></a>Optionele claims configureren

Als u de `family_name` en `given_name` claims van Azure ad wilt ophalen, kunt u optionele claims voor uw toepassing configureren in de Azure Portal gebruikers interface of het toepassings manifest. Zie [optionele claims voor uw Azure AD-app bieden](../active-directory/develop/active-directory-optional-claims.md)voor meer informatie.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). Zoek naar **Azure Active Directory** en selecteer deze optie.
1. Selecteer in de sectie **beheren** de optie **app-registraties**.
1. Selecteer in de lijst de toepassing waarvoor u de optionele claims wilt configureren.
1. Selecteer in de sectie **beheren** de optie **token configuratie (preview)**.
1. Selecteer **optionele claim toevoegen**.
1. Selecteer het token type dat u wilt configureren.
1. Selecteer de optionele claims die u wilt toevoegen.
1. Klik op **Add**.

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

Nu u de toepassing hebt gemaakt in uw Azure AD-Tenant, moet u het client geheim van die toepassing opslaan in uw Azure AD B2C-Tenant.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** `Manual` .
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `MSASecret`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in het **geheim** het client geheim in dat u in de vorige sectie hebt vastgelegd.
1. Selecteer voor **sleutel gebruik** `Signature` .
1. Klik op **Maken**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat uw gebruikers zich kunnen aanmelden met een Microsoft-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunt communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt Azure AD definiëren als een claim provider door het element **ClaimsProvider** toe te voegen aan het extensie bestand van uw beleid.

1. Open het *TrustFrameworkExtensions.xml* -beleids bestand.
1. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
1. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. Vervang de waarde van **client_id** door de *client-id* van de Azure AD-toepassing die u eerder hebt vastgelegd.
1. Sla het bestand op.

U hebt nu uw beleid geconfigureerd zodat Azure AD B2C weet hoe u kunt communiceren met uw Microsoft-account toepassing in azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Voordat u doorgaat, moet u het gewijzigde beleid uploaden om te bevestigen dat het geen problemen tot nu toe heeft voorgedaan.

1. Navigeer naar uw Azure AD B2C-Tenant in de Azure Portal en selecteer **identiteits ervaring-Framework**.
1. Op de pagina **aangepast beleid** selecteert u **aangepast beleid uploaden**.
1. Schakel **het beleid overschrijven als dit bestaat** in en selecteer vervolgens het *TrustFrameworkExtensions.xml* bestand.
1. Klik op **Uploaden**.

Als er geen fouten worden weer gegeven in de portal, gaat u verder met de volgende sectie.

## <a name="register-the-claims-provider"></a>De claim provider registreren

Op dit moment hebt u de ID-provider ingesteld, maar deze is nog niet beschikbaar in een van de registratie-en aanmeld schermen. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zodat deze ook de Microsoft-account-ID-provider heeft.

1. Open het *TrustFrameworkBase.xml* -bestand in het Starter Pack.
1. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat bevat `Id="SignUpOrSignIn"` .
1. Open de *TrustFrameworkExtensions.xml* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
1. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
1. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInMSA`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op een registratie-of aanmeldings scherm. Als u een **ClaimsProviderSelection** -element toevoegt voor een Microsoft-account, wordt er een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Ga in het *TrustFrameworkExtensions.xml* bestand naar het **OrchestrationStep** -element dat is opgenomen `Order="1"` in de gebruikers traject die u hebt gemaakt.
1. Voeg onder **ClaimsProviderSelects** het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `MicrosoftAccountExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met een Microsoft-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die `Order="2"` in de gebruikers reis zijn opgenomen.
1. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij zodat deze overeenkomt met de `Id` waarde in het **TechnicalProfile** -element van de claim provider die u eerder hebt toegevoegd. Bijvoorbeeld `MSA-OIDC`.

1. Sla het *TrustFrameworkExtensions.xml* bestand op en upload het opnieuw voor verificatie.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-microsoft-identity-provider-to-a-user-flow"></a>Een micro soft-ID-provider toevoegen aan een gebruikers stroom 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom die u wilt van de micro soft-ID-provider.
1. Selecteer **micro soft-account** onder de **id-providers voor sociale netwerken**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInMSA.xml*.
1. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignInMSA`.
1. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld:`http://contoso.com/B2C_1A_signup_signin_msa`
1. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de gebruikers traject die u eerder hebt gemaakt (SignUpSignInMSA).
1. Sla de wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
1. Zorg ervoor dat Azure AD B2C toepassing die u hebt gemaakt in de vorige sectie (of door het volt ooien van de vereisten, bijvoorbeeld *webapp1* of *testapp1*) is geselecteerd in het veld **toepassing selecteren** en test deze door te klikken op **nu uitvoeren**.
1. Selecteer de knop **micro soft-account** en meld u aan.

::: zone-end