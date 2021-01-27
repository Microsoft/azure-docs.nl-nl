---
title: Azure AD B2C als een SAML-IdP configureren voor uw toepassingen
title-suffix: Azure AD B2C
description: Azure AD B2C configureren om te voorzien in uw toepassingen (service providers) voor het SAML-protocol. Azure AD B2C fungeert als één ID-provider (IdP) voor uw SAML-toepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/17/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 96a72dbc0e45ebd50a49000ae66e3713cb28aa9a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916915"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Een SAML-toepassing registreren in Azure AD B2C

In dit artikel leert u hoe u Azure Active Directory B2C (Azure AD B2C) kunt configureren om te fungeren als een Security Assertion Markup Language (SAML)-ID-provider (IdP) voor uw toepassingen.

## <a name="scenario-overview"></a>Overzicht van scenario's

Organisaties die Azure AD B2C als klant identiteits-en toegangs beheer oplossing gebruiken, kunnen interactie vereisen met id-providers of toepassingen die zijn geconfigureerd voor verificatie met behulp van het SAML-protocol.

Azure AD B2C kan de SAML-interoperabiliteit op een van de volgende twee manieren worden gerealiseerd:

* Door te fungeren als een *ID-provider* (IDP) en te profiteren van eenmalige aanmelding (SSO) met op SAML gebaseerde service providers (uw toepassingen)
* Door als *service provider* (SP) te fungeren en te communiceren met id-providers op basis van SAML zoals Sales Force en ADFS

![Diagram met B2C als id-provider links en B2C als service provider aan de rechter kant](media/saml-identity-provider/saml-idp-diagram-01.jpg)

Samen vatting van de twee niet-exclusieve kern scenario's met SAML:

| Scenario | Azure AD B2C rol | Uitleg |
| -------- | ----------------- | ------- |
| Mijn toepassing verwacht een SAML-bevestiging om een verificatie te volt ooien. | **Azure AD B2C fungeert als de ID-provider (IdP)**<br />Azure AD B2C fungeert als een SAML-IdP voor de toepassingen. | Dit artikel. |
| Mijn gebruikers hebben eenmalige aanmelding nodig met een id-provider die SAML-compatibel is, zoals ADFS, Sales Force of shibboleth.  | **Azure AD B2C fungeert als service provider (SP)**<br />Azure AD B2C fungeert als een service provider bij het maken van verbinding met de SAML-ID-provider. Het is een Federatie proxy tussen uw toepassing en de SAML-ID-provider.  | <ul><li>[Aanmelden met behulp van aangepast beleid instellen met ADFS als een SAML-IdP](identity-provider-adfs.md)</li><li>[Aanmelden met een Sales Force SAML-provider instellen met behulp van aangepast beleid](identity-provider-salesforce-saml.md)</li></ul> |

## <a name="prerequisites"></a>Vereisten

* Voer de stappen in aan de [slag met aangepast beleid in azure AD B2C](custom-policy-get-started.md). U hebt het aangepaste beleid voor *SocialAndLocalAccounts* nodig van het aangepaste beleid dat wordt beschreven in het artikel.
* Basis informatie over het Security Assertion Markup Language (SAML)-protocol.
* Een webtoepassing die is geconfigureerd als een SAML-service provider (SP). Voor deze zelf studie kunt u een door ons verstrekte [SAML-test toepassing][samltest] gebruiken.

## <a name="components-of-the-solution"></a>Onderdelen van de oplossing

Er zijn drie belang rijke onderdelen vereist voor dit scenario:

* SAML- **service provider** met de mogelijkheid om SAML-aanvragen te verzenden en te ontvangen, te decoderen en te reageren op SAML-bevestigingen van Azure AD B2C. Service provider wordt ook wel de Relying Party toepassing genoemd.
* Openbaar beschikbaar SAML- **eind punt voor meta gegevens** voor uw service provider.
* [Azure AD B2C-tenant](tutorial-create-tenant.md)

Als u nog geen SAML-service provider en een bijbehorend meta gegevens eindpunt hebt, kunt u deze voor beeld-SAML-toepassing gebruiken die we voor het testen beschikbaar hebben gesteld:

[SAML-test toepassing][samltest]

## <a name="1-set-up-certificates"></a>1. certificaten instellen

Als u een vertrouwens relatie tussen uw service provider en Azure AD B2C wilt maken, moet u de x509-certificaten van de web-app opgeven.

* **Certificaten van service provider**
  * Certificaat met een persoonlijke sleutel die is opgeslagen in uw web-app. Dit certificaat wordt door uw service provider gebruikt voor het ondertekenen van de SAML-aanvraag die naar Azure AD B2C is verzonden. Azure AD B2C leest de open bare sleutel van de meta gegevens van de service provider om de hand tekening te valideren.
  * Beschrijving Certificaat met een persoonlijke sleutel die is opgeslagen in uw web-app. Azure AD B2C leest de open bare sleutel van de meta gegevens van de service provider om de SAML-bewering te versleutelen. De service provider gebruikt vervolgens de persoonlijke sleutel om de bewering te ontsleutelen.
* **Azure AD B2C certificaten**
  * Certificaat met een persoonlijke sleutel in Azure AD B2C. Dit certificaat wordt door Azure AD B2C gebruikt voor het ondertekenen van het SAML-antwoord dat is verzonden naar uw service provider. Uw service provider leest de open bare sleutel van de Azure AD B2C meta gegevens om de hand tekening van het SAML-antwoord te valideren.

U kunt een certificaat gebruiken dat is uitgegeven door een open bare certificerings instantie of, voor deze zelf studie, een zelfondertekend certificaat.

### <a name="11-create-a-self-signed-certificate"></a>1,1 een zelfondertekend certificaat maken

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

### <a name="12-upload-the-certificate"></a>1,2 het certificaat uploaden

Upload vervolgens de SAML-verklaring en het handtekening certificaat voor antwoorden naar Azure AD B2C.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en blader naar uw Azure AD B2C-Tenant.
1. Onder **beleids regels** selecteert u **identiteits ervaring-Framework** en vervolgens **beleids sleutels**.
1. Selecteer **toevoegen** en selecteer vervolgens **Opties**  >  **uploaden**.
1. Voer een **naam** in, bijvoorbeeld *SamlIdpCert*. De prefix *B2C_1A_* wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Upload uw certificaat met het besturings element voor het uploaden van bestanden.
1. Voer het wacht woord voor het certificaat in.
1. Selecteer **Maken**.
1. Controleer of de sleutel wordt weer gegeven zoals verwacht. Bijvoorbeeld *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. het beleid voorbereiden

### <a name="21-create-the-saml-token-issuer"></a>2,1 de uitgever van het SAML-token maken

Voeg nu de mogelijkheid voor uw Tenant toe om SAML-tokens te verlenen, met behulp van [SAML-token Uitgever](saml-issuer-technical-profile.md) en technische profielen van de [SAML-sessie provider](custom-policy-reference-sso.md#samlssosessionprovider) .

Open `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** het Starter Pack voor het aangepaste beleid.

Zoek de `<ClaimsProviders>` sectie en voeg het volgende XML-fragment toe.

U kunt de waarde van de `IssuerUri` meta gegevens wijzigen. Dit is de URI van de verlener die wordt geretourneerd in het SAML-antwoord van Azure AD B2C. Uw Relying Party toepassing moet worden geconfigureerd om een uitgever-URI te accepteren tijdens de validatie van de SAML-bevestiging.

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
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
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

## <a name="3-add-the-saml-relying-party-policy"></a>3. het SAML-Relying Party beleid toevoegen

Nu uw Tenant SAML-bevestigingen kan uitgeven, moet u het SAML-Relying Party beleid maken en de reis van de gebruiker wijzigen zodat deze een SAML-bevestiging uitgeeft in plaats van een JWT.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3,1 aanmelden of aanmeldings beleid maken

1. Maak een kopie van het *SignUpOrSignin.xml* -bestand in de werkmap van uw starter pakket en sla het op met een nieuwe naam. Bijvoorbeeld *SignUpOrSigninSAML.xml*. Dit is uw Relying Party-beleids bestand.

1. Open het *SignUpOrSigninSAML.xml* -bestand in de editor van uw voor keur.

1. Wijzig de `PolicyId` en `PublicPolicyUri` van het beleid in _B2C_1A_signup_signin_saml_ en `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` zoals hieronder wordt weer gegeven.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. Voeg het volgende XML-fragment net vóór het `<RelyingParty>` element toe. Met deze XML wordt de indelings stap nummer 7 van de _SignUpOrSignIn_ -gebruikers traject overschreven. Als u bent begonnen vanuit een andere map in het eerste pakket of als u uw gebruikers traject hebt aangepast door het toevoegen of verwijderen van de indelings stappen, moet u ervoor zorgen dat het aantal (in het `order` element) is uitgelijnd met het nummer dat is opgegeven in de gebruikers traject voor de stap van de uitgever van het token (bijvoorbeeld in de andere Starter Pack-mappen, de stap nummer 4 voor `LocalAccounts` , `SocialAccounts` `SocialAndLocalAccountsWithMfa`

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. Vervang het hele `<TechnicalProfile>` element in het `<RelyingParty>` -element door de volgende technische profiel-XML.

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

1. Werk `tenant-name` bij met de naam van uw Azure AD B2C-Tenant.

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
> Bij het implementeren van andere soorten gebruikers stromen (bijvoorbeeld aanmelden, wacht woord opnieuw instellen of profiel bewerking), is het proces in feite hetzelfde als beschreven in deze sectie. In stap 4 hierboven wijzigt u de laatste stap van de gebruikers reis van `JWTIssuer` naar `Saml2AssertionIssuer` . En in de sectie Relying Party van stap 6 hierboven wijzigt u het **protocol** van `OpenIdConnect` in `SAML2` .

### <a name="32-upload-and-test-your-policy-metadata"></a>3,2 de meta gegevens van uw beleid uploaden en testen

Sla de wijzigingen op en upload het nieuwe beleids bestand. Nadat u beide beleids regels (de extensie en de Relying Party bestanden) hebt geüpload, opent u een webbrowser en navigeert u naar de meta gegevens van het beleid.

Azure AD B2C IDP-beleid bevat informatie die wordt gebruikt in het SAML-protocol om de configuratie van een SAML-ID-provider beschikbaar te maken. Meta gegevens definiëren de locatie van de services, zoals aanmelden en afmelden, certificaten, aanmeldings methode en meer. De meta gegevens van het Azure AD B2C beleid zijn beschikbaar op de volgende URL. Vervang door `tenant-name` de naam van uw Azure AD B2C Tenant en `policy-name` met de naam (id) van het beleid bijvoorbeeld.../B2C_1A_signup_signin_saml/samlp/metadata:

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

Uw aangepaste beleid en Azure AD B2C Tenant zijn nu gereed. Maak vervolgens een toepassings registratie in Azure AD B2C.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. toepassing instellen in de Azure AD B2C Directory

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4,1 uw toepassing registreren in Azure AD B2C

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *SAMLApp1*.
1. Onder **ondersteunde account typen** selecteert u **alleen accounts in deze organisatie Directory**
1. Onder **omleidings-URI** selecteert u **Web** en voert u in `https://localhost` . U wijzigt deze waarde later in het manifest van de toepassings registratie.
1. Selecteer **Registreren**.

### <a name="42-update-the-app-manifest"></a>4,2 het app-manifest bijwerken

Voor SAML-apps zijn er verschillende eigenschappen die u moet configureren in het manifest van de toepassings registratie.

1. Ga in het [Azure Portal](https://portal.azure.com)naar de registratie van de toepassing die u in de vorige sectie hebt gemaakt.
1. Onder **beheren** selecteert u **manifest** om de manifest editor te openen. U kunt verschillende eigenschappen in de volgende secties wijzigen.

#### <a name="identifieruris"></a>identifierUris

De `identifierUris` is een teken reeks verzameling met door de gebruiker gedefinieerde URI (s) die een web-app in een eigen Azure AD B2C Tenant identificeren. De URI moet overeenkomen met de naam van de SAML-aanvraag `Issuer` . De door de gebruiker gedefinieerde URI is doorgaans dezelfde waarde als de meta gegevens van de service provider `entityID` .

#### <a name="samlmetadataurl"></a>samlMetadataUrl

Deze eigenschap vertegenwoordigt de openbaar beschik bare meta gegevens-URL van de service provider. De meta gegevens-URL kan verwijzen naar een meta gegevensbestand dat is geüpload naar elk anoniem toegankelijk eind punt, bijvoorbeeld Blob Storage.

De meta gegevens worden in het SAML-protocol gebruikt om de configuratie van een SAML-partij, zoals een service provider, beschikbaar te stellen. Meta gegevens definiëren de locatie van de services, zoals aanmelden en afmelden, certificaten, aanmeldings methode en meer. Azure AD B2C leest de meta gegevens van de service provider en reageert dienovereenkomstig. De meta gegevens zijn niet vereist. U kunt ook een aantal kenmerken opgeven, zoals de antwoord-URI en de afmeldings-URI rechtstreeks in het app-manifest.

Als er eigenschappen zijn opgegeven in *zowel* de URL voor SAML-meta gegevens en in het manifest van de toepassings registratie, worden deze **samengevoegd**. De eigenschappen die zijn opgegeven in de meta gegevens-URL worden eerst verwerkt en hebben voor rang.

Voor deze zelf studie, die gebruikmaakt van de SAML-test toepassing, gebruikt u de volgende waarde voor `samlMetadataUrl` :

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (optioneel)

Als u geen URI voor meta gegevens opgeeft, kunt u de antwoord-URL expliciet opgeven. Deze optionele eigenschap vertegenwoordigt de `AssertionConsumerServiceUrl` ( `SingleSignOnService` URL in de meta gegevens van de service provider) en `BindingType` wordt ervan uitgegaan dat deze is `HTTP POST` .

Als u ervoor kiest om de antwoord-URL en afmeldings-URL in het manifest van de toepassing te configureren zonder de meta gegevens van de service provider te gebruiken, wordt de SAML-aanvraag hand tekening niet door Azure AD B2C gevalideerd en versleutelt het SAML-antwoord niet.

Voor deze zelf studie, waarin u de SAML-test toepassing gebruikt, stelt u de `url` eigenschap in `replyUrlsWithType` op de waarde die wordt weer gegeven in het volgende JSON-code fragment.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl (optioneel)

Deze optionele eigenschap vertegenwoordigt de `Logout` URL ( `SingleLogoutService` URL in de Relying Party meta gegevens) en `BindingType` Er wordt van uitgegaan dat deze is `Http-Redirect` .

Voor deze zelf studie, waarin de SAML-test toepassing wordt gebruikt, moet `logoutUrl` u instellen op `https://samltestapp2.azurewebsites.net/logout` :

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. de toepassings code bijwerken

De laatste stap is het inschakelen van Azure AD B2C als een SAML-IdP in uw SAML-Relying Party toepassing. Elke toepassing wijkt af en de stappen die u kunt uitvoeren, zijn afhankelijk van elkaar. Raadpleeg de documentatie van uw app voor meer informatie.

De meta gegevens kunnen als "statische meta gegevens" of "dynamische meta gegevens" worden geconfigureerd in uw service provider. In statische modus kopieert u alle of een deel van de meta gegevens uit de meta gegevens van het Azure AD B2C beleid. In de dynamische modus stelt u de URL naar de meta gegevens in en laat u de meta gegevens dynamisch door de toepassing worden gelezen.

Enkele of alle volgende zijn doorgaans vereist:

* **Meta gegevens**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **Verlener**: de SAML-aanvraag `issuer` waarde moet overeenkomen met een van de uri's die zijn geconfigureerd in het- `identifierUris` element van het toepassings registratie manifest. Als de SAML `issuer` -aanvraag naam niet aanwezig is in het `identifierUris` element, [voegt u deze toe aan het manifest voor toepassings registratie](#identifieruris). Bijvoorbeeld `https://contoso.onmicrosoft.com/app-name`. 
* **Aanmeldings-URL/SAML-eind punt/SAML-URL**: Controleer de waarde in het meta gegevensbestand Azure AD B2C-SAML-beleid voor het `<SingleSignOnService>` XML-element
* **Certificaat**: dit is *B2C_1A_SamlIdpCert*, maar zonder de persoonlijke sleutel. De open bare sleutel van het certificaat ophalen:

    1. Ga naar de meta gegevens-URL die hierboven is opgegeven.
    1. Kopieer de waarde in het `<X509Certificate>` element.
    1. Plak deze in een tekst bestand.
    1. Sla het tekst bestand op als een *CER* -bestand.

### <a name="51-test-with-the-saml-test-app-optional"></a>5,1 test met de SAML-test-app (optioneel)

Als u deze zelf studie wilt volt ooien, gebruikt u de [SAML-test toepassing][samltest]:

* De Tenant naam bijwerken
* Update de naam van het beleid, bijvoorbeeld *B2C_1A_signup_signin_saml*
* Geef de URI voor de uitgever op. Gebruik een van de Uri's in het `identifierUris` -element in het manifest voor toepassings registratie `https://contoso.onmicrosoft.com/app-name` .

Selecteer **Aanmelden** en u moet een aanmeldings scherm van de gebruiker weer gegeven. Bij het aanmelden wordt een SAML-bevestiging weer gegeven aan de voorbeeld toepassing.

## <a name="enable-encrypted-assertions-optional"></a>Versleutelde verklaringen inschakelen (optioneel)

Voor het versleutelen van de SAML-bevestigingen die worden teruggestuurd naar de service provider, gebruikt Azure AD B2C het open bare-sleutel certificaat van de service providers. De open bare sleutel moet bestaan in de SAML-meta gegevens die in de bovenstaande ["samlMetadataUrl"](#samlmetadataurl) als sleutel descriptor met het gebruik van ' Encryption ' zijn beschreven.

De volgende XML-code is een voor beeld van de SAML-meta gegevens sleutel descriptor waarvoor een gebruik is ingesteld op versleuteling:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Als u wilt dat Azure AD B2C versleutelde beweringen verzendt, stelt u het **WantsEncryptedAssertion** -meta gegevens item `true` in op in het [technische profiel van Relying Party](relyingparty.md#technicalprofile). U kunt ook de algoritme configureren die wordt gebruikt om de SAML-bewering te versleutelen. Zie [Relying Party technische profiel meta gegevens](relyingparty.md#metadata)voor meer informatie. 

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="enable-identity-provider-initiated-flow-optional"></a>Door ID-provider geïnitieerde stroom inschakelen (optioneel)

In de door de provider geïnitieerde stroom wordt het aanmeldings proces geïnitieerd door de ID-provider (Azure AD B2C), die een ongevraagd SAML-antwoord naar de service provider (uw Relying Party toepassing) verzendt. Er worden momenteel geen scenario's ondersteund waarbij de initiator-ID-provider een externe ID-provider is, bijvoorbeeld [AD-FS](identity-provider-adfs.md)of [Sales Force](identity-provider-salesforce-saml.md).

Als u de stroom voor de ID-provider (Azure AD B2C) wilt inschakelen, stelt u het **IdpInitiatedProfileEnabled** -meta gegevens item `true` in op in het [technische profiel van Relying Party](relyingparty.md#technicalprofile).

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="IdpInitiatedProfileEnabled">true</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

Als u een gebruiker wilt aanmelden of zich wilt registreren via de door de provider geïnitieerde stroom, gebruikt u de volgende URL:

```
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/generic/login?EntityId=app-identifier-uri 
```

Vervang de volgende waarden:

* **Tenant naam** met de naam van uw Tenant
* **beleids naam** met uw SAML-Relying Party-beleids naam
* **App-ID-URI** met de `identifierUris` in het meta gegevensbestand, zoals `https://contoso.onmicrosoft.com/app-name`
## <a name="sample-policy"></a>Voorbeeldbeleid

We bieden een volledig voor beeld van een beleid dat u kunt gebruiken voor het testen met de app voor SAML-tests.

1. Het [voorbeeld beleid voor door SAML-SP geïnitieerde aanmelding](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated) downloaden
1. Werk `TenantId` bij met de naam van uw Tenant, bijvoorbeeld *contoso.b2clogin.com*
1. Behoud de beleids naam van *B2C_1A_signup_signin_saml*

## <a name="supported-and-unsupported-saml-modalities"></a>Ondersteunde en niet-ondersteunde SAML-modaliteiten

De volgende opties voor SAML-Relying Party (RP) worden ondersteund via uw eigen meta gegevens eindpunt:

* Meerdere afmeldings-Url's of POST-binding voor afmeldings-URL in het object Application/Service Principal.
* Geef de handtekening sleutel op voor het verifiëren van RP-aanvragen in het object Application/Service Principal.
* Geef een token versleutelings sleutel op in het object Application/Service Principal.
* De ID-provider heeft zich aangemeld, waarbij de ID-provider is Azure AD B2C.

## <a name="saml-token"></a>SAML-token

Een SAML-token is een beveiligings token dat is uitgegeven door Azure AD B2C nadat het aanmelden is geslaagd. Het bevat informatie over de gebruiker, de service provider waarvoor het token is bedoeld, de hand tekening en de geldigheids duur. De volgende tabel geeft een lijst van de claims en eigenschappen die u kunt verwachten in een SAML-token dat is uitgegeven door Azure AD B2C.

|Element  |Eigenschap  |Notities  |
|---------|---------|---------|
|`<Response>`| `ID` | Een automatisch gegenereerde unieke id van het antwoord. | 
|`<Response>`| `InResponseTo` | De ID van de SAML-aanvraag waarnaar dit bericht wordt gestuurd. | 
|`<Response>` | `IssueInstant` | Het tijdstip waarop het antwoord zich niet meer heeft voordoen. De tijd waarde wordt gecodeerd in UTC.  Als u de instellingen voor de levens duur van uw tokens wilt wijzigen, stelt u de `TokenNotBeforeSkewInSeconds` [meta gegevens](saml-issuer-technical-profile.md#metadata) van het SAML-token voor de uitgever van het technische profiel in. | 
|`<Response>` | `Destination`| Een URI-verwijzing die het adres aangeeft waarnaar deze reactie is verzonden. De waarde is gelijk aan de SAML-aanvraag `AssertionConsumerServiceURL` . | 
|`<Response>` `<Issuer>` | |Identificeert de uitgever van het token. Dit is een wille keurige URI die is gedefinieerd door de `IssuerUri` [meta gegevens](saml-issuer-technical-profile.md#metadata) van het SAML-token probleem     |
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     |         |De principal over welke het token informatie bedient, zoals de gebruikers object-ID. Deze waarde is onveranderbaar en kan niet opnieuw worden toegewezen of opnieuw worden gebruikt. Het kan worden gebruikt om autorisatie controles veilig uit te voeren, zoals wanneer het token wordt gebruikt voor toegang tot een bron. Standaard wordt de subject claim gevuld met de object-ID van de gebruiker in de Directory.|
|`<Response>` `<Assertion>` `<Subject>` `<NameID>`     | `Format` | Een URI-verwijzing voor de classificatie van gegevens op basis van teken reeks-id's. Deze eigenschap wordt standaard wegge laten. U kunt de Relying Party [SubjectNamingInfo](relyingparty.md#subjectnaminginfo) instellen om de indeling op te geven `NameID` , zoals `urn:oasis:names:tc:SAML:2.0:nameid-format:transient` . |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` |`NotBefore` |Het tijdstip waarop het token geldig wordt. De tijd waarde wordt gecodeerd in UTC. Uw toepassing moet deze claim gebruiken om de geldigheid van de levens duur van het token te controleren. Als u de instellingen voor de levens duur van uw tokens wilt wijzigen, stelt u de `TokenNotBeforeSkewInSeconds` [meta gegevens](saml-issuer-technical-profile.md#metadata) van het SAML-token probleem technisch profiel in. |
|`<Response>` `<Assertion>` `<Subject>` `<Conditions>` | `NotOnOrAfter` | Het tijdstip waarop het token ongeldig wordt. Uw toepassing moet deze claim gebruiken om de geldigheid van de levens duur van het token te controleren. De standaard waarde is 5 minuten na de `NotBefore` en kan worden bijgewerkt door de `TokenLifeTimeInSeconds` [meta gegevens](saml-issuer-technical-profile.md#metadata) van het SAML-token probleem met het technische profiel toe te voegen.|
|`<Response>` `<Assertion>` `<Conditions>` `<AudienceRestriction>` `<Audience>` | |Een URI-verwijzing waarmee een beoogde doel groep wordt geïdentificeerd. Hiermee wordt de beoogde ontvanger van het token geïdentificeerd. De waarde is gelijk aan de SAML-aanvraag `AssertionConsumerServiceURL` .|
|`<Response>``<Assertion>` `<AttributeStatement>` verzameling van`<Attribute>` | | Beweringen verzameling (claims), zoals geconfigureerd in de [Relying Party technische profiel](relyingparty.md#technicalprofile) uitvoer claims. U kunt de naam van de bewering configureren door de `PartnerClaimType` van de uitvoer claim in te stellen. |

## <a name="next-steps"></a>Volgende stappen

- [Op de Oasis-website](https://www.oasis-open.org/)vindt u meer informatie over het SAML-protocol.
- Down load de web-app voor de SAML-test van [Azure AD B2C github Community opslag plaats](https://github.com/azure-ad-b2c/saml-sp-tester).

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
