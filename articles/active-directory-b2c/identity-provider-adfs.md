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
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3082c249b04b5efc71187dd03515bc8c875b7c2f
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102448588"
---
# <a name="add-ad-fs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>AD FS toevoegen als een SAML-ID-provider met behulp van aangepast beleid in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In dit artikel wordt beschreven hoe u aanmelden voor een AD FS gebruikers account inschakelt met [aangepaste beleids regels](custom-policy-overview.md) in Azure Active Directory B2C (Azure AD B2C). U schakelt aanmelden in door een SAML- [ID-provider](identity-provider-generic-saml.md) aan een aangepast beleid toe te voegen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-a-self-signed-certificate"></a>Een zelfondertekend certificaat maken

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="create-a-policy-key"></a>Een beleids sleutel maken

U moet uw certificaat opslaan in uw Azure AD B2C-Tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies de map die uw Tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
5. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
6. Kies voor **Opties** `Upload` .
7. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `SAMLSigningCert`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Blader naar en selecteer het pfx-bestand van het certificaat met de persoonlijke sleutel.
9. Klik op **Create**.

## <a name="add-a-claims-provider"></a>Een claim provider toevoegen

Als u wilt dat gebruikers zich aanmelden met een AD FS-account, moet u het account definiëren als een claim provider waarmee Azure AD B2C met behulp van een eind punt kunt communiceren. Het eind punt biedt een set claims die wordt gebruikt door Azure AD B2C om te controleren of een specifieke gebruiker is geverifieerd.

U kunt een AD FS account definiëren als een claim provider door het toe te voegen aan het **ClaimsProviders** -element in het extensie bestand van uw beleid. Zie [een SAML-ID-provider definiëren](identity-provider-generic-saml.md)voor meer informatie.

1. Open de *TrustFrameworkExtensions.xml*.
1. Zoek het element **ClaimsProviders** . Als deze niet bestaat, voegt u deze toe onder het hoofd element.
1. Voeg als volgt een nieuwe **ClaimsProvider** toe:

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso</DisplayName>
          <Description>Login with your AD FS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-AD-FS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
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

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

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

## <a name="test-your-custom-policy"></a>Uw aangepaste beleid testen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework**
1. Selecteer uw Relying Party beleid, bijvoorbeeld `B2C_1A_signup_signin` .
1. Selecteer voor **toepassing** een webtoepassing die u [eerder hebt geregistreerd](tutorial-register-applications.md). De **antwoord-URL** moet `https://jwt.ms` weergeven.
1. Selecteer de knop **nu uitvoeren** .
1. Selecteer op de pagina aanmelden of aanmelden **contoso AD FS** om u aan te melden met de ID-provider van Contoso AD FS.

Als het aanmeldings proces is geslaagd, wordt uw browser omgeleid naar `https://jwt.ms` , waarin de inhoud wordt weer gegeven van het token dat is geretourneerd door Azure AD B2C.

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

U kunt configureren hoe de SAML-aanvraag in Azure AD B2C moet worden ondertekend. De [XmlSignatureAlgorithm](identity-provider-generic-saml.md) -meta gegevens bepalen de waarde van de `SigAlg` para meter (query teken reeks of post-para meter) in de SAML-aanvraag. In het volgende voor beeld wordt Azure AD B2C geconfigureerd om het `rsa-sha256` handtekening algoritme te gebruiken.

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
