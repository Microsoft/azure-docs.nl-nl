---
title: Aanmelden voor Azure AD met meerdere tenants instellen door aangepast beleid
titleSuffix: Azure AD B2C
description: Voeg een multi tenant-ID-provider voor Azure AD toe met behulp van aangepast beleid in Azure Active Directory B2C.
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
ms.openlocfilehash: 71e3bf429c7b8d3f4f8fe205c05b0701732fdef9
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653806"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Aanmelden voor multi tenant-Azure Active Directory instellen met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

In dit artikel wordt beschreven hoe u aanmelden kunt inschakelen voor gebruikers die gebruikmaken van het multi tenant-eind punt voor Azure Active Directory (Azure AD). Hierdoor kunnen gebruikers van meerdere Azure AD-tenants zich aanmelden met Azure AD B2C, zonder dat u een id-provider hoeft te configureren voor elke Tenant. Gast leden in een van deze tenants kunnen zich echter **niet** aanmelden. Hiervoor moet u [elke Tenant afzonderlijk configureren](identity-provider-azure-ad-single-tenant.md).


## <a name="register-an-application"></a>Een toepassing registreren

Als u het aanmelden voor gebruikers van een specifieke Azure AD-organisatie wilt inschakelen, moet u een toepassing registreren in de Azure AD-Tenant van de organisatie.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die de Azure AD-Tenant van uw organisatie bevat (bijvoorbeeld contoso.com). Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map die uw Tenant bevat.
1. Kies linksboven in de Azure Portal **Alle services**, zoek **App-registraties** en selecteer deze.
1. Selecteer **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Bijvoorbeeld `Azure AD B2C App`.
1. Selecteer **accounts in elke organisatie Directory (een Azure AD-Directory – multi tenant)** voor deze toepassing.
1. Voor de **omleidings-URI** accepteert u de waarde van **Web** en voert u de volgende URL in in kleine letters, waar `your-B2C-tenant-name` wordt vervangen door de naam van uw Azure AD B2C-Tenant.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Bijvoorbeeld `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Selecteer **Registreren**. Noteer de **Toepassings-id (client)** voor gebruik in een latere stap.
1. Selecteer **certificaten & geheimen** en selecteer vervolgens **Nieuw client geheim**.
1. Voer een **Beschrijving** in voor het geheim, selecteer een verloop en selecteer vervolgens **toevoegen**. Noteer de **waarde** van het geheim voor gebruik in een latere stap.

## <a name="configuring-optional-claims"></a>Optionele claims configureren

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
1. Wijzig de standaard waarden voor het **antwoord type**, de **antwoord modus** en de **domein Hint**.
1. Selecteer onder **Claimstoewijzing voor id-provider** de volgende claims:

    - **Gebruikers-id**: *oid*
    - **Weergavenaam**: *name*
    - **Voornaam**: *given_name*
    - **Achternaam**: *family_name*
    - **E-mail**: *preferred_username*

1. Selecteer **Opslaan**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet de toepassings sleutel opslaan die u hebt gemaakt in uw Azure AD B2C-Tenant.

1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het **filter Directory + abonnement** in het bovenste menu en kies vervolgens de map die uw Azure AD B2C Tenant bevat.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** `Manual` .
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `AADAppSecret`.  Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van de sleutel wanneer deze wordt gemaakt, zodat de verwijzing in de XML in de volgende sectie wordt *B2C_1A_AADAppSecret*.
1. Voer in het **geheim** uw client geheim in dat u eerder hebt vastgelegd.
1. Selecteer voor **sleutel gebruik** `Signature` .
1. Selecteer **Maken**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met behulp van Azure AD, moet u Azure AD definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunnen communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt Azure AD definiëren als een claim provider door Azure AD toe te voegen aan het **ClaimsProvider** -element in het extensie bestand van uw beleid.

1. Open het *TrustFrameworkExtensions.xml* -bestand.
1. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
1. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
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

1. Werk onder het element **ClaimsProvider** de waarde voor **domein** bij naar een unieke waarde die kan worden gebruikt om deze te onderscheiden van andere id-providers.
1. Werk onder het element **TechnicalProfile** de waarde voor **DisplayName**, bijvoorbeeld `Contoso Employee` . Deze waarde wordt weer gegeven op de aanmeldings knop op uw aanmeldings pagina.
1. Stel **client_id** in op de toepassings-id van de Azure AD-toepassing voor meerdere tenants die u eerder hebt geregistreerd.
1. Werk onder **CryptographicKeys** de waarde van **StorageReferenceId** bij naar de naam van de beleids sleutel die u eerder hebt gemaakt. Bijvoorbeeld `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Toegang beperken

> [!NOTE]
> `https://login.microsoftonline.com/`Als u de waarde voor **ValidTokenIssuerPrefixes** gebruikt, kunnen alle Azure AD-gebruikers zich aanmelden bij uw toepassing.

U moet de lijst met geldige token verleners bijwerken en de toegang beperken tot een specifieke lijst met gebruikers van Azure AD-tenants die zich kunnen aanmelden.

Als u de waarden wilt verkrijgen, bekijkt u de OpenID Connect Connect Discovery-meta gegevens voor elk van de Azure AD-tenants waarvan u wilt dat gebruikers zich aanmelden. De indeling van de meta gegevens-URL is vergelijkbaar met `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration` , waarbij `your-tenant` uw Azure AD-Tenant naam is. Bijvoorbeeld:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Voer de volgende stappen uit voor elke Azure AD-Tenant die moet worden gebruikt om u aan te melden:

1. Open uw browser en ga naar de OpenID Connect Connect meta data URL voor de Tenant. Zoek het object van de **Uitgever** en noteer de waarde ervan. Deze moet er ongeveer als volgt uitzien `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/` .
1. Kopieer en plak de waarde in de **ValidTokenIssuerPrefixes** -sleutel. Scheid meerdere verleners met een komma. In het voor beeld van het voor gaande XML-voorbeeld worden twee verleners weer gegeven `ClaimsProvider` .

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Nu hebt u uw beleid zodanig geconfigureerd dat Azure AD B2C weet hoe u kunt communiceren met uw Azure AD-directory's. Upload het extensie bestand van uw beleid alleen om te bevestigen dat er tot nu toe geen problemen zijn.

1. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
2. Schakel **het beleid overschrijven als dit bestaat** in en selecteer vervolgens het *TrustFrameworkExtensions.xml* bestand.
3. Selecteer **Uploaden**.

## <a name="register-the-claims-provider"></a>De claim provider registreren

Op dit moment is de ID-provider ingesteld, maar is deze niet beschikbaar in de schermen voor aanmelden/aanmelden. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zodat deze ook de Azure AD-ID-provider heeft.

1. Open het *TrustFrameworkBase.xml* -bestand in het Starter Pack.
2. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat bevat `Id="SignUpOrSignIn"` .
3. Open de *TrustFrameworkExtensions.xml* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
5. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInContoso`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op het scherm aanmelden/aanmelden. Als u een **ClaimsProviderSelection** -element toevoegt voor Azure AD, wordt er een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Zoek het **OrchestrationStep** -element dat is opgenomen `Order="1"` in de gebruikers traject die u hebt gemaakt in *TrustFrameworkExtensions.xml*.
1. Voeg onder **ClaimsProviderSelects** het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `AzureADExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met Azure AD om een token te ontvangen. Koppel de knop aan een actie door het technische profiel voor uw Azure AD-claim provider te koppelen.

1. Zoek de **OrchestrationStep** die `Order="2"` in de gebruikers reis zijn opgenomen.
2. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de **id** die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij naar de **id** van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Common-AAD`.

3. Sla het *TrustFrameworkExtensions.xml* bestand op en upload het opnieuw voor verificatie.

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Een Azure AD-ID-provider toevoegen aan een gebruikers stroom 

1. Selecteer in uw Azure AD B2C-Tenant **gebruikers stromen**.
1. Klik op de gebruikers stroom die u wilt van de Azure AD-ID-provider.
1. Selecteer **Contoso Azure AD** bij de **sociale id-providers**.
1. Selecteer **Opslaan**.
1. Als u het beleid wilt testen, selecteert u **gebruikers stroom uitvoeren**.
1. Selecteer voor **toepassing** de webtoepassing met de naam *testapp1* die u eerder hebt geregistreerd. De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Klik op **gebruikers stroom uitvoeren**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij dat de door u gemaakte gebruikers traject initieert:

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignContoso.xml*.
1. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignInContoso`.
1. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de gebruikers traject die u eerder hebt gemaakt. Bijvoorbeeld *SignUpSignInContoso*.
1. Sla de wijzigingen op en upload het bestand.
1. Selecteer in het geüploade **aangepaste beleid** het zojuist gemaakte beleid in de lijst.
1. Selecteer in de vervolg keuzelijst **toepassing selecteren** de Azure AD B2C toepassing die u eerder hebt gemaakt. Bijvoorbeeld *testapp1*.
1. Kopieer het **eind punt voor nu uitvoeren** en open het in een persoonlijk browser venster, bijvoorbeeld Incognito-modus in Google Chrome of een InPrivate-venster in micro soft Edge. Als u in een persoonlijk browser venster opent, kunt u de volledige gebruikers traject testen door geen gebruik te maken van de momenteel in de cache opgeslagen Azure AD-referenties.
1. Selecteer de knop aanmelden bij Azure AD, bijvoorbeeld contoso- *werk nemer*, en geef vervolgens de referenties voor een gebruiker op in een van uw Azure AD-tenants. U wordt gevraagd de toepassing te autoriseren en vervolgens gegevens voor uw profiel op te geven.

Als het aanmelden is gelukt, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat wordt geretourneerd door Azure AD B2C.

Voer de laatste twee stappen uit met behulp van de referenties voor een gebruiker die een andere Azure AD-Tenant heeft, om de aanmeld functionaliteit voor meerdere tenants te testen.

## <a name="next-steps"></a>Volgende stappen

Wanneer u werkt met aangepaste beleids regels, hebt u mogelijk aanvullende informatie nodig bij het oplossen van problemen met een beleid tijdens de ontwikkeling ervan.

Als u problemen wilt oplossen, kunt u het beleid tijdelijk in ' ontwikkelaars modus ' plaatsen en logboeken verzamelen met Azure-toepassing Insights. Ontdek hoe u in [Azure Active Directory B2C: Logboeken verzamelt](troubleshoot-with-application-insights.md).

::: zone-end