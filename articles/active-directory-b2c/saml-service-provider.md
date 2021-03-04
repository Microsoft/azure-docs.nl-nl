---
title: Azure Active Directory B2C als een SAML-IdP configureren voor uw toepassingen
title-suffix: Azure Active Directory B2C
description: Azure Active Directory B2C configureren om te voorzien in uw toepassingen (service providers) voor het SAML-protocol. Azure AD B2C fungeert als één ID-provider (IdP) voor uw SAML-toepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 1035f43642f3884e7cc0f6ab47e9c9afd1f29170
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107386"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Een SAML-toepassing registreren in Azure AD B2C

In dit artikel leert u hoe u uw Security Assertion Markup Language (SAML)-toepassingen (service providers) verbindt met Azure Active Directory B2C (Azure AD B2C) voor verificatie.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>Overzicht

Organisaties die gebruikmaken van Azure AD B2C als klant identiteits-en toegangs beheer oplossing, moeten mogelijk worden geïntegreerd met toepassingen die worden geverifieerd met het SAML-protocol. In het volgende diagram ziet u hoe Azure AD B2C fungeert als een *ID-provider* (IDP) om eenmalige aanmelding (SSO) te krijgen met op SAML gebaseerde toepassingen.

![Diagram met B2C als id-provider links en B2C als service provider aan de rechter kant.](media/saml-service-provider/saml-service-provider-integration.png)

1. De toepassing maakt een SAML authn-aanvraag die wordt verzonden naar het Azure AD B2C's SAML-aanmeld eindpunt.
2. De gebruiker kan een Azure AD B2C lokaal account of een andere federatieve id-provider (indien geconfigureerd) gebruiken om te verifiëren.
3. Als de gebruiker zich aanmeldt met een federatieve id-provider, wordt een token reactie verzonden naar Azure AD B2C.
4. Azure AD B2C genereert een SAML-bevestiging en verzendt deze naar de toepassing.

## <a name="prerequisites"></a>Vereisten

* Voer de stappen in aan de [slag met aangepast beleid in azure AD B2C](custom-policy-get-started.md). U hebt het aangepaste beleid voor *SocialAndLocalAccounts* nodig van het aangepaste beleid dat wordt beschreven in het artikel.
* Basis kennis van het SAML-protocol en de kennis van de SAML-implementatie van de toepassing.
* Een webtoepassing geconfigureerd als een SAML-toepassing. Voor deze zelf studie kunt u een door ons verstrekte [SAML-test toepassing][samltest] gebruiken.

## <a name="components"></a>Onderdelen

Er zijn drie belang rijke onderdelen vereist voor dit scenario:

* Een SAML- **toepassing** met de mogelijkheid om SAML-verificatie aanvragen te verzenden en SAML-antwoorden te ontvangen, te decoderen en te verifiëren van Azure AD B2C. De SAML-toepassing wordt ook wel de Relying Party toepassing of service provider genoemd.
* Het openbaar beschik bare SAML- **meta gegevens eindpunt** of XML-document van de SAML-toepassing.
* Een [Azure AD B2C-Tenant](tutorial-create-tenant.md)

Als u nog geen SAML-toepassing en een bijbehorend meta gegevens eindpunt hebt, kunt u deze voor beeld-SAML-toepassing gebruiken die we voor het testen beschikbaar hebben gesteld:

[SAML-test toepassing][samltest]

## <a name="set-up-certificates"></a>Certificaten instellen

Om een vertrouwens relatie tussen uw toepassing en Azure AD B2C te maken, moeten beide services elkaars hand tekeningen kunnen maken en valideren. U configureert x509-certificaten configureren in Azure AD B2C en uw toepassing.

**Toepassings certificaten**

| Gebruik | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| SAML-aanvraag ondertekening  | Nee | Een certificaat met een persoonlijke sleutel die is opgeslagen in uw web-app en die door uw toepassing wordt gebruikt voor het ondertekenen van SAML-aanvragen die naar Azure AD B2C worden verzonden. De web-app moet de open bare sleutel weer geven via het SAML-eind punt voor meta gegevens. Azure AD B2C valideert de hand tekening van de SAML-aanvraag met behulp van de open bare sleutel uit de meta gegevens van de toepassing.|
| Versleuteling van SAML-verklaring  | Nee | Een certificaat met een persoonlijke sleutel die is opgeslagen in uw web-app. De web-app moet de open bare sleutel weer geven via het SAML-eind punt voor meta gegevens. Azure AD B2C kunt bevestigingen naar uw toepassing versleutelen met behulp van de open bare sleutel. De toepassing maakt gebruik van de persoonlijke sleutel om de bewering te ontsleutelen.|

**Azure AD B2C certificaten**

| Gebruik | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| SAML-antwoord ondertekenen | Ja | Een certificaat met een persoonlijke sleutel die is opgeslagen in Azure AD B2C. Dit certificaat wordt door Azure AD B2C gebruikt voor het ondertekenen van het SAML-antwoord dat naar uw toepassing is verzonden. Uw toepassing leest de open bare sleutel van de Azure AD B2C meta gegevens om de hand tekening van het SAML-antwoord te valideren. |

In een productie omgeving kunt u het beste certificaten gebruiken die zijn uitgegeven door een open bare certificerings instantie. U kunt deze procedure echter ook volt ooien met zelfondertekende certificaten.

### <a name="prepare-a-self-signed-certificate-for-saml-response-signing"></a>Een zelfondertekend certificaat voorbereiden voor het ondertekenen van SAML-antwoorden

U moet een certificaat voor het ondertekenen van SAML-antwoorden maken zodat uw toepassing de bewering kan vertrouwen van Azure AD B2C.

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

## <a name="enable-your-policy-to-connect-with-a-saml-application"></a>Uw beleid inschakelen om verbinding te maken met een SAML-toepassing

Als u verbinding wilt maken met uw SAML-toepassing, moet Azure AD B2C SAML-antwoorden kunnen maken.

Open `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** het Starter Pack voor het aangepaste beleid.

Zoek de `<ClaimsProviders>` sectie en voeg het volgende XML-fragment toe om uw SAML-antwoord generator te implementeren.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="configure-the-issueruri-of-the-saml-response"></a>De IssuerUri van het SAML-antwoord configureren

U kunt de waarde van het `IssuerUri` meta gegevens item wijzigen in het SAML-token voor de uitgever van het technische profiel. Deze wijziging wordt weer gegeven in het `issuerUri` kenmerk dat wordt geretourneerd in het SAML-antwoord van Azure AD B2C. Uw toepassing moet worden geconfigureerd om dezelfde te accepteren `issuerUri` tijdens de SAML-respons validatie.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="IssuerUri">https://issuerUriMyAppExpects</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-metadata-optional"></a>De Azure AD B2C IdP SAML-meta gegevens ondertekenen (optioneel)

U kunt Azure AD B2C het document van de SAML IdP-meta gegevens te ondertekenen, indien dit door de toepassing wordt vereist. Als u dit wilt doen, genereert en uploadt u een SAML IdP-beleid sleutel voor het ondertekenen van meta gegevens, zoals wordt weer gegeven in [voor bereiding van een zelfondertekend certificaat voor SAML-reactie ondertekening](#prepare-a-self-signed-certificate-for-saml-response-signing). Configureer vervolgens het `MetadataSigning` meta gegevens item in het SAML-token Uitgever technisch profiel. De `StorageReferenceId` moet verwijzen naar de naam van de beleids sleutel.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlMetadataCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```

#### <a name="sign-the-azure-ad-b2c-idp-saml-response-element-optional"></a>Het Azure AD B2C SAML-antwoord element IdP ondertekenen (optioneel)

U kunt een certificaat opgeven dat moet worden gebruikt voor het ondertekenen van de SAML-berichten. Het bericht is het `<samlp:Response>` element binnen het SAML-antwoord dat naar de toepassing wordt verzonden.

Als u een certificaat wilt opgeven, genereert en uploadt u een beleids sleutel, zoals wordt weer gegeven in [voor bereiding van een zelfondertekend certificaat voor SAML-reactie ondertekening](#prepare-a-self-signed-certificate-for-saml-response-signing). Configureer vervolgens het `SamlMessageSigning` meta gegevens item in het SAML-token Uitgever technisch profiel. De `StorageReferenceId` moet verwijzen naar de naam van de beleids sleutel.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
        ...
      <CryptographicKeys>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlMessageCert"/>
        ...
      </CryptographicKeys>
    ...
    </TechnicalProfile>
```
## <a name="configure-your-policy-to-issue-a-saml-response"></a>Configureer uw beleid voor het uitgeven van een SAML-reactie

Nu uw beleid SAML-reacties kan maken, moet u het beleid configureren om een SAML-reactie uit te geven in plaats van de standaard JWT-respons voor uw toepassing.

### <a name="create-a-sign-up-or-sign-in-policy-configured-for-saml"></a>Een registratie-of aanmeldings beleid maken dat is geconfigureerd voor SAML

1. Maak een kopie van het *SignUpOrSignin.xml* -bestand in de werkmap van uw starter pakket en sla het op met een nieuwe naam. Bijvoorbeeld *SignUpOrSigninSAML.xml*. Dit bestand is uw Relying Party-beleids bestand en is geconfigureerd om standaard een JWT-respons uit te geven.

1. Open het *SignUpOrSigninSAML.xml* -bestand in de editor van uw voor keur.

1. Wijzig de `PolicyId` en `PublicPolicyUri` van het beleid in _B2C_1A_signup_signin_saml_ en `http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml` zoals hieronder wordt weer gegeven.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Aan het einde van de gebruikers reis bevat Azure AD B2C een `SendClaims` stap. Deze stap verwijst naar het technische profiel van de token Uitgever. Als u een SAML-reactie wilt uitgeven in plaats van de standaard-JWT-respons, wijzigt u de `SendClaims` stap zodat deze verwijst naar het nieuwe technische profiel voor de uitgever van het SAML-token `Saml2AssertionIssuer` .

Voeg het volgende XML-fragment net voor het- `<RelyingParty>` element toe. Met deze XML wordt het indelings stap nummer 7 in de _SignUpOrSignIn_ -gebruikers traject overschreven. Als u een andere map in het Starter Pack hebt gestart of als u de gebruikers reis hebt aangepast door het toevoegen of verwijderen van Orchestration-stappen, moet u ervoor zorgen dat het nummer in het- `order` element overeenkomt met het nummer dat is opgegeven in de gebruikers traject voor de stap van de uitgever van het token. In de andere Starter Pack-mappen is het bijbehorende stap nummer bijvoorbeeld 4 voor `LocalAccounts` , 6 voor `SocialAccounts` en 9 voor `SocialAndLocalAccountsWithMfa` .

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>
      <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys>
```

Het Relying Party-element bepaalt welk protocol door uw toepassing wordt gebruikt. De standaardwaarde is `OpenId`. Het `Protocol` element moet worden gewijzigd in `SAML` . Met de uitvoer claims wordt de claim toewijzing gemaakt voor de SAML-bevestiging.

Vervang het hele `<TechnicalProfile>` element in het `<RelyingParty>` -element door de volgende technische profiel-XML. Werk `tenant-name` bij met de naam van uw Azure AD B2C-Tenant.

```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
```

Het uiteindelijke Relying Party-beleids bestand moet er als volgt uitzien:

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

> [!NOTE]
> U kunt hetzelfde proces volgen voor het implementeren van andere typen gebruikers stromen (bijvoorbeeld aanmelden, wacht woord opnieuw instellen of proces voor het bewerken van profielen).

### <a name="upload-your-policy"></a>Uw beleid uploaden

Sla uw wijzigingen op en upload de nieuwe **TrustFrameworkExtensions.xml** en **SignUpOrSigninSAML.xml** -beleids bestanden naar de Azure Portal.

### <a name="test-the-azure-ad-b2c-idp-saml-metadata"></a>De Azure AD B2C IdP SAML-meta gegevens testen

Nadat de beleids bestanden zijn geüpload, gebruikt Azure AD B2C de configuratie gegevens om het SAML-meta gegevens document van de identiteits provider te genereren dat door de toepassing moet worden gebruikt. Het SAML-meta gegevens document bevat de locaties van services, zoals aanmeldings-en afmeldings methoden, certificaten, enzovoort.

De meta gegevens van het Azure AD B2C beleid zijn beschikbaar op de volgende URL:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/samlp/metadata`

Vervang door `<tenant-name>` de naam van uw Azure AD B2C-Tenant en `<policy-name>` met de naam (id) van het beleid, bijvoorbeeld:

`https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_signup_signin_saml/samlp/metadata`

## <a name="register-your-saml-application-in-azure-ad-b2c"></a>Uw SAML-toepassing registreren bij Azure AD B2C

Als u wilt dat Azure AD B2C uw toepassing vertrouwt, maakt u een Azure AD B2C toepassings registratie, die configuratie-informatie bevat, zoals het eind punt voor meta gegevens van de toepassing.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *SAMLApp1*.
1. Onder **ondersteunde account typen** selecteert u **alleen accounts in deze organisatie Directory**.
1. Onder **omleidings-URI** selecteert u **Web** en voert u in `https://localhost` . U wijzigt deze waarde later in het manifest van de toepassings registratie.
1. Selecteer **Registreren**.

### <a name="configure-your-application-in-azure-ad-b2c"></a>Uw toepassing configureren in Azure AD B2C

Voor SAML-apps moet u verschillende eigenschappen configureren in het manifest van de toepassings registratie.

1. Ga in het [Azure Portal](https://portal.azure.com)naar de registratie van de toepassing die u in de vorige sectie hebt gemaakt.
1. Onder **beheren** selecteert u **manifest** om de manifest editor te openen en wijzigt u de eigenschappen die in de volgende secties worden beschreven.

#### <a name="add-the-identifier"></a>De id toevoegen

Wanneer uw SAML-toepassing een aanvraag indient om Azure AD B2C, bevat de SAML Autha-aanvraag een `Issuer` kenmerk, dat doorgaans dezelfde waarde heeft als de meta gegevens van de toepassing `entityID` . Azure AD B2C gebruikt deze waarde om de registratie van de toepassing in de map op te zoeken en de configuratie te lezen. Deze zoek actie slaagt alleen als de `identifierUri` in de registratie van de toepassing is ingevuld met een waarde die overeenkomt met het `Issuer` kenmerk.

Zoek in het registratie manifest de `identifierURIs` para meter en voeg de juiste waarde toe. Deze waarde is hetzelfde als de waarde die is geconfigureerd in de SAML authn-aanvragen voor EntityId bij de toepassing, en de `entityID` waarde in de meta gegevens van de toepassing.

In het volgende voor beeld ziet u de `entityID` in de SAML-meta gegevens:

```xml
<EntityDescriptor ID="id123456789" entityID="https://samltestapp2.azurewebsites.net" validUntil="2099-12-31T23:59:59Z" xmlns="urn:oasis:names:tc:SAML:2.0:metadata">
```

De `identifierUris` eigenschap accepteert alleen url's in het domein `tenant-name.onmicrosoft.com` .

```json
"identifierUris":"https://samltestapp2.azurewebsites.net",
```

#### <a name="share-the-applications-metadata-with-azure-ad-b2c"></a>De meta gegevens van de toepassing delen met Azure AD B2C

Nadat de registratie van de toepassing is geladen door de `identifierUri` , Azure AD B2C gebruikt de meta gegevens van de toepassing om de SAML authn-aanvraag te valideren en te bepalen hoe moet worden gereageerd.

Het is raadzaam dat uw toepassing een openbaar toegankelijk eind punt voor meta gegevens beschikbaar stelt.

Als er eigenschappen zijn opgegeven in *zowel* de URL voor SAML-meta gegevens als het manifest van de toepassings registratie, worden deze *samengevoegd*. De eigenschappen die zijn opgegeven in de meta gegevens-URL worden eerst verwerkt en hebben voor rang.

Met de SAML-test toepassing als voor beeld gebruikt u de volgende waarde voor `samlMetadataUrl` in het manifest van de toepassing:

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="override-or-set-the-assertion-consumer-url-optional"></a>De URL van de bewerings verbruiker overschrijven of instellen (optioneel)

U kunt de antwoord-URL configureren waarnaar Azure AD B2C SAML-reacties verzendt. Antwoord-Url's kunnen worden geconfigureerd in het manifest van de toepassing. Deze configuratie is handig wanneer uw toepassing geen openbaar toegankelijk eind punt voor meta gegevens weergeeft.

De antwoord-URL voor een SAML-toepassing is het eind punt waarop de toepassing SAML-antwoorden verwacht te ontvangen. De toepassing biedt deze URL meestal in het meta gegevens document onder het `AssertionConsumerServiceUrl` -kenmerk, zoals hieronder wordt weer gegeven:

```xml
<SPSSODescriptor AuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    ...
    <AssertionConsumerService index="0" isDefault="true" Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://samltestapp2.azurewebsites.net/SP/AssertionConsumer" />        
</SPSSODescriptor>
```

Als u de meta gegevens in het kenmerk wilt overschrijven `AssertionConsumerServiceUrl` of de URL niet aanwezig is in het meta gegevens document, kunt u de URL configureren in het manifest onder de `replyUrlsWithType` eigenschap. De `BindingType` wordt ingesteld op `HTTP POST` .

Als u de SAML-test toepassing als voor beeld gebruikt, stelt u de `url` eigenschap `replyUrlsWithType` in op de waarde die wordt weer gegeven in het volgende JSON-code fragment.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="override-or-set-the-logout-url-optional"></a>De afmeldings-URL negeren of instellen (optioneel)

U kunt de afmeldings-URL configureren waarnaar Azure AD B2C de gebruiker wordt verzonden na een afmeldings aanvraag. Antwoord-Url's kunnen worden geconfigureerd in het manifest van de toepassing.

Als u de meta gegevens in het kenmerk wilt overschrijven `SingleLogoutService` of de URL niet aanwezig is in het meta gegevens document, kunt u deze configureren in het manifest onder de `Logout` eigenschap. De `BindingType` wordt ingesteld op `Http-Redirect` .

De toepassing biedt deze URL meestal in het meta gegevens document onder het `AssertionConsumerServiceUrl` -kenmerk, zoals hieronder wordt weer gegeven:

```xml
<IDPSSODescriptor WantAuthnRequestsSigned="false" WantAssertionsSigned="false" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://samltestapp2.azurewebsites.net/logout" ResponseLocation="https://samltestapp2.azurewebsites.net/logout" />

</IDPSSODescriptor>
```

U kunt de SAML-test toepassing als voor beeld gebruiken door in `logoutUrl` te stellen op `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

> [!NOTE]
> Als u ervoor kiest om de antwoord-URL en afmeldings-URL in het manifest van de toepassing te configureren zonder het meta gegevens eindpunt van de toepassing via de `samlMetadataUrl` eigenschap in te vullen, wordt de SAML-aanvraag hand tekening niet door Azure AD B2C gevalideerd en wordt de SAML-respons niet versleuteld.

## <a name="configure-azure-ad-b2c-as-a-saml-idp-in-your-saml-application"></a>Azure AD B2C configureren als een SAML-IdP in uw SAML-toepassing

De laatste stap is het inschakelen van Azure AD B2C als een SAML-IdP in uw SAML-toepassing. Elke toepassing verschilt en de stappen verschillen. Raadpleeg de documentatie van uw app voor meer informatie.

De meta gegevens kunnen worden geconfigureerd in uw toepassing als *statische meta gegevens* of *dynamische meta gegevens*. Kopieer in statische modus alle of een deel van de meta gegevens uit de meta gegevens van het Azure AD B2C beleid. Geef in de dynamische modus de URL naar de meta gegevens op en stel in dat uw toepassing de meta gegevens dynamisch kan lezen.

Enkele of alle volgende zijn doorgaans vereist:

* **Meta gegevens**: gebruik de indeling `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata` .
* **Verlener**: de SAML-aanvraag `issuer` waarde moet overeenkomen met een van de uri's die zijn geconfigureerd in het- `identifierUris` element van het toepassings registratie manifest. Als de SAML `issuer` -aanvraag naam niet aanwezig is in het `identifierUris` element, [voegt u deze toe aan het manifest voor toepassings registratie](#add-the-identifier). Bijvoorbeeld `https://contoso.onmicrosoft.com/app-name`. 
* **Aanmeldings-URL/SAML-eind punt/SAML-URL**: Controleer de waarde in het meta gegevensbestand Azure AD B2C SAML-beleid voor het `<SingleSignOnService>` XML-element.
* **Certificaat**: dit certificaat is *B2C_1A_SamlIdpCert*, maar zonder de persoonlijke sleutel. De open bare sleutel van het certificaat ophalen:

    1. Ga naar de meta gegevens-URL die hierboven is opgegeven.
    1. Kopieer de waarde in het `<X509Certificate>` element.
    1. Plak deze in een tekst bestand.
    1. Sla het tekst bestand op als een *CER* -bestand.

### <a name="test-with-the-saml-test-app"></a>Testen met de app voor SAML-tests

U kunt onze [SAML-test toepassing][samltest] gebruiken om uw configuratie te testen:

* Werk de naam van de Tenant bij.
* Werk de naam van het beleid bij, bijvoorbeeld *B2C_1A_signup_signin_saml*.
* Geef de URI voor de uitgever op. Gebruik een van de Uri's in het `identifierUris` -element in het manifest voor toepassings registratie `https://contoso.onmicrosoft.com/app-name` .

Selecteer **Aanmelden** en u moet een aanmeldings scherm van de gebruiker weer gegeven. Bij het aanmelden wordt een SAML-reactie weer gegeven aan de voorbeeld toepassing.

## <a name="supported-and-unsupported-saml-modalities"></a>Ondersteunde en niet-ondersteunde SAML-modaliteiten

De volgende SAML-toepassings scenario's worden ondersteund via uw eigen meta gegevens eindpunt:

* Meerdere afmeldings-Url's of POST-binding voor afmeldings-URL in het object Application/Service Principal.
* Geef een ondertekeningssleutel op om Relying Party (RP)-aanvragen in het object Application/Service Principal te controleren.
* Geef een token versleutelings sleutel op in het object Application/Service Principal.
* Met ID-provider geïnitieerde aanmelding, waarbij de ID-provider is Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

- Down load de web-app voor de SAML-test van [Azure AD B2C github Community opslag plaats](https://github.com/azure-ad-b2c/saml-sp-tester).
- Bekijk de [Opties voor het registreren van een SAML-toepassing in azure AD B2C](saml-service-provider-options.md)

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
