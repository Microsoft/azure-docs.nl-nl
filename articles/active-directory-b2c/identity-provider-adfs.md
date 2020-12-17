---
title: AD FS toevoegen als een SAML-ID-provider met behulp van aangepast beleid
titleSuffix: Azure AD B2C
description: Stel AD FS 2016 in met het SAML-protocol en aangepaste beleids regels in Azure Active Directory B2C
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
ms.openlocfilehash: 767f60cae2f74f7e2a928253d45011bb6ceb5d0e
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653840"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>AD FS toevoegen als een SAML-ID-provider met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel wordt beschreven hoe u aanmelden voor een AD FS gebruikers account inschakelt met [aangepaste beleids regels](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C). U schakelt aanmelden in door een SAML- [identiteits provider technisch profiel](saml-identity-provider-technical-profile.md) toe te voegen aan een aangepast beleid.

## <a name="prerequisites"></a>Vereisten

- Voer de stappen in aan de [slag met aangepast beleid in azure Active Directory B2C](custom-policy-get-started.md).
- Zorg ervoor dat u toegang hebt tot een pfx-bestand van het certificaat met een persoonlijke sleutel. U kunt een eigen ondertekend certificaat genereren en dit uploaden naar Azure AD B2C. Azure AD B2C gebruikt dit certificaat voor het ondertekenen van de SAML-aanvraag die is verzonden naar uw SAML-ID-provider. Zie [een handtekening certificaat genereren](identity-provider-salesforce-saml.md#generate-a-signing-certificate)voor meer informatie over het genereren van een certificaat.
- Om Azure het wacht woord voor het pfx-bestand te accepteren, moet het wacht woord worden versleuteld met de optie TripleDES-SHA1 in het export hulpprogramma voor Windows-certificaat archief in plaats van AES256-SHA256.

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet uw certificaat opslaan in uw Azure AD B2C-Tenant.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
5. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
6. Kies voor **Opties** `Upload` .
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `ADFSSamlCert`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Blader naar en selecteer het pfx-bestand van het certificaat met de persoonlijke sleutel.
9. Klik op **Maken**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met een AD FS-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunt communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een AD FS account definiëren als een claim provider door het toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid. Zie [het technische profiel van een SAML-identiteits provider definiëren](saml-identity-provider-technical-profile.md)voor meer informatie.

1. Open de *TrustFrameworkExtensions.xml*.
1. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
1. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso AD FS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso AD FS</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

1. Vervang door `your-AD-FS-domain` de naam van uw AD FS domein en vervang de waarde van de **Identity provider** -uitvoer claim door uw DNS (wille keurige waarde die uw domein aangeeft).

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

### <a name="upload-the-extension-file-for-verification"></a>Upload het extensie bestand voor verificatie

Nu hebt u uw beleid zodanig geconfigureerd dat Azure AD B2C weet hoe u kunt communiceren met AD FS-account. Upload het extensie bestand van uw beleid alleen om te bevestigen dat er tot nu toe geen problemen zijn.

1. Selecteer op de pagina **aangepaste beleids regels** in uw Azure AD B2C-Tenant de optie **beleid uploaden**.
2. Schakel **het beleid overschrijven als dit bestaat** in en selecteer vervolgens het *TrustFrameworkExtensions.xml* bestand.
3. Klik op **Uploaden**.

> [!NOTE]
> De B2C-extensie van Visual Studio code maakt gebruik van ' socialIdpUserId '. Er is ook een sociaal beleid vereist voor de AD FS.
>

## <a name="register-the-claims-provider"></a>De claim provider registreren

Op dit moment is de ID-provider ingesteld, maar is deze niet beschikbaar in de beschik bare registratie-of aanmeldings schermen. Om het beschikbaar te maken, maakt u een kopie van een bestaande sjabloon gebruiker en wijzigt u deze zo dat deze ook de AD FS-ID-provider heeft.

1. Open het *TrustFrameworkBase.xml* -bestand in het Starter Pack.
2. Zoek en kopieer de volledige inhoud van het **UserJourney** -element dat bevat `Id="SignUpOrSignIn"` .
3. Open de *TrustFrameworkExtensions.xml* en zoek het element **UserJourneys** . Als het element niet bestaat, voegt u er een toe.
4. Plak de volledige inhoud van het **UserJourney** -element dat u hebt gekopieerd als onderliggend element van het onderdeel **UserJourneys** .
5. Wijzig de naam van de gebruikers traject. Bijvoorbeeld `SignUpSignInADFS`.

### <a name="display-the-button"></a>De knop weer geven

Het element **ClaimsProviderSelection** is vergelijkbaar met een id-provider knop op een registratie-of aanmeldings scherm. Als u een **ClaimsProviderSelection** -element toevoegt voor een AD FS account, wordt een nieuwe knop weer gegeven wanneer een gebruiker op de pagina terechtkomt.

1. Zoek het **OrchestrationStep** -element dat is opgenomen `Order="1"` in de gebruikers traject die u hebt gemaakt.
2. Voeg onder **ClaimsProviderSelections** het volgende element toe. Stel de waarde van **TargetClaimsExchangeId** in op een geschikte waarde, bijvoorbeeld `ContosoExchange` :

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>De knop aan een actie koppelen

Nu er een knop aanwezig is, moet u deze koppelen aan een actie. De actie in dit geval is voor Azure AD B2C om te communiceren met een AD FS-account om een token te ontvangen.

1. Zoek de **OrchestrationStep** die `Order="2"` in de gebruikers reis zijn opgenomen.
2. Voeg het volgende **ClaimsExchange** -element toe om ervoor te zorgen dat u dezelfde waarde gebruikt voor de id die u hebt gebruikt voor **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    Werk de waarde van **TechnicalProfileReferenceId** bij naar de id van het technische profiel dat u eerder hebt gemaakt. Bijvoorbeeld `Contoso-SAML2`.

3. Sla het *TrustFrameworkExtensions.xml* bestand op en upload het opnieuw voor verificatie.


## <a name="configure-an-ad-fs-relying-party-trust"></a>Een vertrouwens relatie voor een AD FS Relying Party configureren

Als u AD FS als een id-provider in Azure AD B2C wilt gebruiken, moet u een AD FS Relying Party vertrouwens relatie met de Azure AD B2C SAML-meta gegevens maken. In het volgende voor beeld ziet u een URL-adres naar de SAML-meta gegevens van een Azure AD B2C technisch profiel:

```
https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/your-policy/samlp/metadata?idptp=your-technical-profile
```

Vervang de volgende waarden:

- **uw-Tenant** met de naam van uw Tenant, zoals your-Tenant.onmicrosoft.com.
- **uw-beleid** met de naam van uw beleid. Bijvoorbeeld B2C_1A_signup_signin_adfs.
- **uw technische profiel** met de naam van uw technische profiel voor de SAML-identiteits provider. Bijvoorbeeld contoso-SAML2.

Open een browser en navigeer naar de URL. Zorg ervoor dat u de juiste URL typt en dat u toegang hebt tot het XML-bestand met meta gegevens. Als u een nieuwe Relying Party-vertrouwens relatie wilt toevoegen met behulp van de module AD FS beheer en de instellingen hand matig wilt configureren, voert u de volgende procedure uit op een Federatie server. Lidmaatschap van **Administrators** of gelijkwaardig op de lokale computer is de minimale vereiste om deze procedure te volt ooien.

1. Selecteer in Serverbeheer **extra** en selecteer vervolgens **AD FS beheer**.
2. Selecteer **vertrouwens relatie van Relying Party toevoegen**.
3. Kies op de pagina **Welkom** de optie **claims herkennen** en klik vervolgens op **starten**.
4. Selecteer op de pagina **gegevens bron selecteren** de optie **gegevens importeren over de relying party online publiceren of op een lokaal netwerk**, geef uw Azure AD B2C meta gegevens-URL op en klik op **volgende**.
5. Voer op de pagina **weergave naam opgeven** een **weergave naam** in, onder **notities**, voer een beschrijving in voor deze Relying Party-vertrouwens relatie en klik vervolgens op **volgende**.
6. Selecteer op de pagina **Access Control beleid kiezen** een beleid en klik vervolgens op **volgende**.
7. Controleer de instellingen op de pagina **gereed om een vertrouwens relatie toe te voegen** en klik vervolgens op **volgende** om uw Relying Party vertrouwens gegevens op te slaan.
8. Klik op de pagina **volt ooien** op **sluiten**. met deze actie wordt automatisch het dialoog venster **claim regels bewerken** weer gegeven.
9. Selecteer **regel toevoegen**.
10. Selecteer in **claim regel sjabloon** **LDAP-kenmerken als claims verzenden**.
11. Geef een **claim regel naam** op. Selecteer voor het **kenmerk archief** **Active Directory selecteren**, voeg de volgende claims toe en klik vervolgens op **volt ooien** en **OK**.

    | LDAP-kenmerk | Uitgaand claim type |
    | -------------- | ------------------- |
    | Principal-naam van gebruiker | userPrincipalName |
    | Achternaam | family_name |
    | Given-Name | given_name |
    | E-mail adres | e-mail |
    | Display-Name | naam |

    Houd er rekening mee dat deze namen niet worden weer gegeven in de vervolg keuzelijst uitgaand claim type. U moet deze hand matig invoeren in. (De vervolg keuzelijst kan feitelijk worden bewerkt).

12.  Op basis van uw certificaat type moet u mogelijk het HASH-algoritme instellen. Selecteer in het venster Eigenschappen van Relying Party Trust (B2C demonstratie) het tabblad **Geavanceerd** en wijzig het **algoritme voor beveiligde hashes** in `SHA-256` en klik op **OK**.
13. Selecteer in Serverbeheer **extra** en selecteer vervolgens **AD FS beheer**.
14. Selecteer de Relying Party-vertrouwens relatie die u hebt gemaakt, selecteer **bijwerken uit federatieve meta gegevens** en klik vervolgens op **bijwerken**.

### <a name="update-and-test-the-relying-party-file"></a>Het Relying Party bestand bijwerken en testen

Werk het Relying Party (RP)-bestand bij waarmee de door u gemaakte gebruikers traject wordt gestart.

1. Maak een kopie van *SignUpOrSignIn.xml* in uw werkmap en wijzig de naam ervan. Wijzig de naam bijvoorbeeld in *SignUpSignInADFS.xml*.
2. Open het nieuwe bestand en werk de waarde van het kenmerk **PolicyId** voor **TrustFrameworkPolicy** met een unieke waarde bij. Bijvoorbeeld `SignUpSignInADFS`.
3. Werk de waarde van **PublicPolicyUri** bij met de URI voor het beleid. Bijvoorbeeld:`http://contoso.com/B2C_1A_signup_signin_adfs`
4. Werk de waarde van het kenmerk **ReferenceId** in **DefaultUserJourney** bij zodat dit overeenkomt met de id van de nieuwe gebruikers traject die u hebt gemaakt (SignUpSignInADFS).
5. Sla de wijzigingen op, upload het bestand en selecteer vervolgens het nieuwe beleid in de lijst.
6. Zorg ervoor dat Azure AD B2C toepassing die u hebt gemaakt, is geselecteerd in het veld **toepassing selecteren** en test deze door op **nu uitvoeren** te klikken.

## <a name="troubleshooting-ad-fs-service"></a>Problemen met AD FS-service oplossen  

AD FS is geconfigureerd voor het gebruik van het Windows-toepassings logboek. Als u problemen ondervindt bij het instellen van AD FS als een SAML-ID-provider met behulp van aangepast beleid in Azure AD B2C, kunt u het AD FS gebeurtenis logboek controleren:

1. Typ **Logboeken** op de Windows- **Zoek balk** en selecteer vervolgens de **Logboeken** bureau blad-app.
1. Als u het logboek van een andere computer wilt weer geven, klikt u met de rechter muisknop op **Logboeken (lokaal)**. Selecteer **verbinding maken met een andere computer** en vul de velden in om het dialoog venster **computer selecteren** in te vullen.
1. Open de **Logboeken toepassingen en services** in **Logboeken**.
1. Selecteer **AD FS** en selecteer vervolgens **beheerder**. 
1. Dubbel klik op de gebeurtenis om meer informatie over een gebeurtenis weer te geven.  

### <a name="saml-request-is-not-signed-with-expected-signature-algorithm-event"></a>SAML-aanvraag is niet ondertekend met de verwachte handtekening algoritme gebeurtenis

Deze fout geeft aan dat de SAML-aanvraag die door Azure AD B2C is verzonden, niet is ondertekend met het verwachte handtekening algoritme dat is geconfigureerd in AD FS. De SAML-aanvraag is bijvoorbeeld ondertekend met het handtekening algoritme `rsa-sha256` , maar het verwachte handtekening algoritme is `rsa-sha1` . U kunt dit probleem oplossen door ervoor te zorgen dat zowel Azure AD B2C als AD FS zijn geconfigureerd met hetzelfde handtekening algoritme.

#### <a name="option-1-set-the-signature-algorithm-in-azure-ad-b2c"></a>Optie 1: het handtekening algoritme instellen in Azure AD B2C  

U kunt configureren hoe de SAML-aanvraag in Azure AD B2C moet worden ondertekend. De [XmlSignatureAlgorithm](saml-identity-provider-technical-profile.md#metadata) -meta gegevens bepalen de waarde van de `SigAlg` para meter (query teken reeks of post-para meter) in de SAML-aanvraag. In het volgende voor beeld wordt Azure AD B2C geconfigureerd om het `rsa-sha256` handtekening algoritme te gebruiken.

```xml
<Metadata>
  <Item Key="WantsEncryptedAssertions">false</Item>
  <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
  <Item Key="XmlSignatureAlgorithm">Sha256</Item>
</Metadata>
```

#### <a name="option-2-set-the-signature-algorithm-in-ad-fs"></a>Optie 2: het handtekening algoritme instellen in AD FS 

U kunt ook de verwachte algoritme voor SAML-aanvraag handtekeningen configureren in AD FS.

1. Selecteer in Serverbeheer **extra** en selecteer vervolgens **AD FS beheer**.
1. Selecteer de **vertrouwens relatie van de Relying Party** die u eerder hebt gemaakt.
1. Selecteer **Eigenschappen** en selecteer vervolgens voor **schot**
1. Configureer het **algoritme voor beveiligde hashes** en selecteer **OK** om de wijzigingen op te slaan.

::: zone-end