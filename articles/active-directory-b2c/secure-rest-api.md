---
title: Een Restful-service in uw Azure AD B2C
titleSuffix: Azure AD B2C
description: Beveilig uw aangepaste REST API claims in uw Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 462d69a8bde0dec2689ac30620276b5bcd335410
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717689"
---
# <a name="secure-your-restful-services"></a>Uw RESTful-services beveiligen 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wanneer u een REST API in een Azure AD B2C gebruikerstraject integreert, moet u uw eindpunt REST API beschermen met verificatie. Dit zorgt ervoor dat alleen services met de juiste referenties, zoals Azure AD B2C, aanroepen naar uw REST API eindpunt kunnen doen.

Meer informatie over het integreren van een REST API in [](custom-policy-rest-api-claims-validation.md) uw Azure AD B2C gebruikerstraject in de artikelen Gebruikersinvoer valideren en REST API claims toevoegen aan artikelen over [aangepaste](custom-policy-rest-api-claims-exchange.md) beleidsregels.

In dit artikel wordt beschreven hoe u uw REST API met HTTP-basis-, clientcertificaat- of OAuth2-verificatie. 

## <a name="prerequisites"></a>Vereisten

Volg de stappen in een van de volgende handleidingen:

- Integreer REST API uitwisseling van claims in uw [Azure AD B2C gebruikerservaring om gebruikersinvoer te valideren.](custom-policy-rest-api-claims-validation.md)
- [Claims REST API toevoegen aan aangepaste beleidsregels](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP-basisverificatie

HTTP-basisverificatie wordt gedefinieerd in [RFC 2617.](https://tools.ietf.org/html/rfc2617) Basisverificatie werkt als volgt: Azure AD B2C http-aanvraag met de clientreferenties in de autorisatieheader. De referenties zijn opgemaakt als de met base64 gecodeerde tekenreeks 'naam:wachtwoord'.  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Beleidssleutels REST API gebruikersnaam en wachtwoord toevoegen

Als u een REST API profiel wilt configureren met HTTP-basisverificatie, maakt u de volgende cryptografische sleutels om de gebruikersnaam en het wachtwoord op te slaan:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C tenant. Selecteer het **filter Map en abonnement** in het bovenste menu en kies uw Azure AD B2C directory.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht de **Identity Experience Framework.**
1. Selecteer **Beleidssleutels** en selecteer vervolgens **Toevoegen.**
1. Selecteer **handmatig** bij **Opties.**
1. Bij **Naam** typt **u RestApiUsername.**
    Het *voorvoegsel B2C_1A_* automatisch worden toegevoegd.
1. Voer in **het** vak Geheim de REST API in.
1. Selecteer **versleuteling** bij **Sleutelgebruik.**
1. Selecteer **Maken**.
1. Selecteer **opnieuw Beleidssleutels.**
1. Selecteer **Toevoegen**.
1. Selecteer **handmatig** bij **Opties.**
1. Bij **Naam** typt u **RestApiPassword.**
    Het *voorvoegsel B2C_1A_* automatisch worden toegevoegd.
1. Voer in **het** vak Geheim het wachtwoord REST API in.
1. Selecteer **versleuteling** bij **Sleutelgebruik.**
1. Selecteer **Maken**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Uw technische REST API configureren voor het gebruik van HTTP-basisverificatie

Nadat u de benodigde sleutels hebt gemaakt, configureert REST API metagegevens van uw technische profiel om te verwijzen naar de referenties.

1. Open in uw werkmap het extensiebeleidsbestand (TrustFrameworkExtensions.xml).
1. Zoek naar het REST API profiel. Bijvoorbeeld `REST-ValidateProfile` of `REST-GetProfile` .
1. Zoek het `<Metadata>` -element.
1. Wijzig *het AuthenticationType* in `Basic` .
1. Wijzig *allowInsecureAuthInProduction* in `false` .
1. Voeg direct na het `</Metadata>` afsluitende element het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Hier volgt een voorbeeld van een technisch RESTful-profiel dat is geconfigureerd met HTTP-basisverificatie:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>HTTPS-clientcertificaatverificatie

Verificatie van clientcertificaten is een wederzijdse verificatie op basis van certificaten, waarbij de client, Azure AD B2C, het clientcertificaat aan de server verstrekt om de identiteit te bewijzen. Dit gebeurt als onderdeel van de SSL-handshake. Alleen services met de juiste certificaten, zoals Azure AD B2C, hebben toegang tot uw REST API service. Het clientcertificaat is een digitaal X.509-certificaat. In productieomgevingen moet deze worden ondertekend door een certificeringsinstantie.

### <a name="prepare-a-self-signed-certificate-optional"></a>Een zelfonder ondertekend certificaat voorbereiden (optioneel)

Als u nog geen certificaat hebt voor niet-productieomgevingen, kunt u een zelf-ondertekend certificaat gebruiken. In Windows kunt u de cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) van PowerShell gebruiken om een certificaat te genereren.

1. Voer deze PowerShell-opdracht uit om een zelf-ondertekend certificaat te genereren. Wijzig het `-Subject` argument waar nodig voor uw toepassing en wijzig Azure AD B2C tenantnaam. U kunt de datum ook `-NotAfter` aanpassen om een andere vervaldatum voor het certificaat op te geven.
    ```powershell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Open **Manage user certificates**  >  **Current User**  >  **Personal**  >  **Certificates**  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Selecteer het certificaat > **Actie**  >  **Alle taken**  >  **Exporteren.**
1. Selecteer **Ja**  >  **volgende**  >  **Ja, exporteert u de persoonlijke sleutel**  >  **Volgende.**
1. Accepteer de standaardwaarden voor **Bestandsindeling exporteren.**
1. Geef een wachtwoord op voor het certificaat.

### <a name="add-a-client-certificate-policy-key"></a>Een beleidssleutel voor clientcertificaten toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C tenant. Selecteer het **filter Map en abonnement** in het bovenste menu en kies uw Azure AD B2C map.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht **de optie Identity Experience Framework**.
1. Selecteer **Beleidssleutels** en selecteer vervolgens **Toevoegen.**
1. Selecteer uploaden **in** het vak **Opties.**
1. Typ  **restApiClientCertificate** in het vak Naam.
    Het *voorvoegsel B2C_1A_* automatisch toegevoegd.
1. Selecteer in **het vak Bestand** uploaden het PFX-bestand van uw certificaat met een persoonlijke sleutel.
1. Typ in **het** vak Wachtwoord het wachtwoord van het certificaat.
1. Selecteer **Maken**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Uw technische REST API configureren voor het gebruik van clientcertificaatverificatie

Nadat u de benodigde sleutel hebt gemaakt, configureert u de metagegevens REST API technische profiel om te verwijzen naar het clientcertificaat.

1. Open in uw werkmap het extensiebeleidsbestand (TrustFrameworkExtensions.xml).
1. Zoek naar het REST API profiel. Bijvoorbeeld `REST-ValidateProfile` of `REST-GetProfile` .
1. Zoek het `<Metadata>` -element.
1. Wijzig *het AuthenticationType* in `ClientCertificate` .
1. Wijzig *allowInsecureAuthInProduction* in `false` .
1. Voeg direct na het `</Metadata>` afsluitende element het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Hier volgt een voorbeeld van een technisch RESTful-profiel dat is geconfigureerd met een HTTP-clientcertificaat:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 Bearer-verificatie 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Bearer-tokenverificatie wordt gedefinieerd in [OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750).](https://www.rfc-editor.org/rfc/rfc6750.txt) Bij bearer-tokenverificatie verzendt Azure AD B2C http-aanvraag met een token in de autorisatieheader.

```http
Authorization: Bearer <token>
```

Een Bearer-token is een ondoorzichtige tekenreeks. Dit kan een JWT-toegangsteken zijn of een tekenreeks die de REST API verwacht Azure AD B2C in de autorisatieheader te verzenden. Azure AD B2C ondersteunt de volgende typen:

- **Bearer-token**. Als u het Bearer-token in het technische Restful-profiel wilt verzenden, moet uw beleid eerst het bearer-token verkrijgen en dit vervolgens gebruiken in het technische RESTful-profiel.  
- **Statisch bearer-token**. Gebruik deze methode wanneer uw REST API een token voor langetermijntoegang uit te geven. Als u een statisch Bearer-token wilt gebruiken, maakt u een beleidssleutel en verwijst u vanuit het technische RESTful-profiel naar uw beleidssleutel. 


## <a name="using-oauth2-bearer"></a>OAuth2 Bearer gebruiken  

In de volgende stappen wordt gedemonstreerd hoe u clientreferenties gebruikt om een Bearer-token te verkrijgen en dit door te geven aan de autorisatie-header van de REST API aanroepen.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Een claim definiëren voor het opslaan van het Bearer-token

Een claim biedt tijdelijke opslag van gegevens tijdens een Azure AD B2C uitvoering van het beleid. Het [claimschema](claimsschema.md) is de plaats waar u uw claims declareert. Het toegangstoken moet worden opgeslagen in een claim voor later gebruik. 

1. Open het extensiebestand van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Zoek het [element BuildingBlocks.](buildingblocks.md) Als het element niet bestaat, voegt u het toe.
1. Zoek het [element ClaimsSchema.](claimsschema.md) Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claims toe aan het **element ClaimsSchema.**  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Een toegangs token verkrijgen 

U kunt een toegangsteken op een van de volgende manieren verkrijgen: door het te verkrijgen bij een federatief [id-provider,](idp-pass-through-user-flow.md)door een REST API aan te roepen die een toegangsteken retourneert, met behulp van een [ROPC-stroom](../active-directory/develop/v2-oauth-ropc.md)of door de [clientreferentiestroom](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)te gebruiken. De clientreferentiestroom wordt vaak gebruikt voor server-naar-server-interacties die op de achtergrond moeten worden uitgevoerd, zonder directe interactie met een gebruiker.

#### <a name="acquiring-an-azure-ad-access-token"></a>Een Azure AD-toegangs token verkrijgen 

In het volgende voorbeeld wordt een REST API profiel gebruikt om een aanvraag te doen bij het Eindpunt van het Azure AD-token met behulp van de clientreferenties die worden doorgegeven als HTTP-basisverificatie. Zie Microsoft Identity Platform en [de OAuth 2.0-clientreferentiestroom voor meer informatie.](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) 

Als u een Azure AD-toegangs token wilt verkrijgen, maakt u een toepassing in uw Azure AD-tenant:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer het **filter Map en** abonnement in het bovenste menu en selecteer vervolgens de map die uw Azure AD-tenant bevat.
1. Selecteer **Azure Active Directory** in het linkermenu. Of selecteer **Alle services en** zoek en selecteer **Azure Active Directory**.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. U kunt *bijvoorbeeld Client_Credentials_Auth_app*.
1. Selecteer **onder Ondersteunde accounttypen** alleen **Accounts in deze organisatiemap.**
1. Selecteer **Registreren**.
2. Neem de **toepassings-id (client) op.** 


Voor een clientreferentiestroom moet u een toepassingsgeheim maken. Het clientgeheim wordt ook wel een toepassingswachtwoord genoemd. Het geheim wordt door uw toepassing gebruikt om een toegangs token te verkrijgen.

1. Selecteer op **Azure AD B2C - App-registraties** de toepassing die u hebt gemaakt, bijvoorbeeld *Client_Credentials_Auth_app*.
1. Selecteer in het linkermenu onder **Beheren** de optie **Certificaten en geheimen**.
1. Selecteer **Nieuw clientgeheim**.
1. Voer een beschrijving voor het clientgeheim in het vak **Beschrijving** in. Bijvoorbeeld *clientsecret1*.
1. Selecteer onder **Verloopt** een duur waarvoor het geheim geldig is en selecteer vervolgens **Toevoegen**.
1. Neem de waarde van het geheim **op voor** gebruik in de code van uw clienttoepassing. Deze geheime waarde wordt nooit meer weergegeven nadat u deze pagina hebt verlaten. U gebruikt deze waarde als het toepassingsgeheim in de code van uw toepassing.

#### <a name="create-azure-ad-b2c-policy-keys"></a>Beleidssleutels Azure AD B2C maken

U moet de client-id en het clientgeheim opslaan die u eerder hebt vastgelegd in uw Azure AD B2C tenant.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C tenant. Selecteer het **filter Map en abonnement** in het bovenste menu en kies de map die uw tenant bevat.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer op de pagina Overzicht de **Identity Experience Framework.**
5. Selecteer **Beleidssleutels** en selecteer vervolgens **Toevoegen.**
6. Kies **voor** `Manual` Opties.
7. Voer een **naam in** voor de beleidssleutel, `SecureRESTClientId` . Het `B2C_1A_` voorvoegsel wordt automatisch toegevoegd aan de naam van uw sleutel.
8. Voer **in Geheim** de client-id in die u eerder hebt genoteerd.
9. Selecteer **voor Sleutelgebruik.** `Signature`
10. Klik op **Create**.
11. Maak nog een beleidssleutel met de volgende instellingen:
    -   **Naam:** `SecureRESTClientSecret` .
    -   **Geheim:** voer het clientgeheim in dat u eerder hebt genoteerd

Vervang voor de ServiceUrl your-tenant-name door de naam van uw Azure AD-tenant. Zie de [reSTful technische profielreferentie](restful-technical-profile.md) voor alle beschikbare opties.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Het technische REST-profiel wijzigen voor het gebruik van Bearer-tokenverificatie

Als u bearer-tokenverificatie in uw aangepaste beleid wilt ondersteunen, wijzigt REST API technische profiel met het volgende:

1. Open in uw werkmap het *TrustFrameworkExtensions.xml* extensiebeleid.
1. Zoek naar het `<TechnicalProfile>` knooppunt dat `Id="REST-API-SignUp"` bevat.
1. Zoek het `<Metadata>` element .
1. Wijzig *authenticationType* als volgt *in Bearer:*
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Wijzig of voeg *het UseClaimAsBearerToken* als volgt toe aan *bearerToken.* *BearerToken* is de naam van de claim waaruit het bearer-token wordt opgehaald (de uitvoerclaim van `SecureREST-AccessToken` ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Zorg ervoor dat u de claim die hierboven wordt gebruikt als invoerclaim toevoegt:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Nadat u de bovenstaande codefragmenten hebt toevoegen, moet uw technische profiel lijken op de volgende XML-code:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Een statische OAuth2-bearer gebruiken 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>De OAuth2 Bearer-tokenbeleidssleutel toevoegen

Als u een REST API profiel wilt configureren met een OAuth2 Bearer-token, moet u een toegangsteken verkrijgen van de REST API eigenaar. Maak vervolgens de volgende cryptografische sleutel om het bearer-token op te slaan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C tenant. Selecteer het **filter Map en abonnement** in het bovenste menu en kies uw Azure AD B2C directory.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht de **Identity Experience Framework.**
1. Selecteer **Beleidssleutels** en selecteer vervolgens **Toevoegen.**
1. Kies **voor** `Manual` Opties.
1. Voer een **naam in** voor de beleidssleutel. Bijvoorbeeld `RestApiBearerToken`. Het `B2C_1A_` voorvoegsel wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer **in Geheim** het clientgeheim in dat u eerder hebt genoteerd.
1. Selecteer **voor Sleutelgebruik.** `Encryption`
1. Selecteer **Maken**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Uw technische REST API configureren voor het gebruik van de bearer-tokenbeleidssleutel

Nadat u de benodigde sleutel hebt gemaakt, configureert REST API metagegevens van uw technische profiel om te verwijzen naar het bearer-token.

1. Open in uw werkmap het extensiebeleidsbestand (TrustFrameworkExtensions.xml).
1. Zoek naar het REST API profiel. Bijvoorbeeld `REST-ValidateProfile` of `REST-GetProfile` .
1. Zoek het `<Metadata>` -element.
1. Wijzig *het AuthenticationType* in `Bearer` .
1. Wijzig *allowInsecureAuthInProduction* in `false` .
1. Voeg direct na het `</Metadata>` afsluitende element het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Hier volgt een voorbeeld van een technisch RESTful-profiel dat is geconfigureerd met bearer-tokenverificatie:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="api-key-authentication"></a>VERIFICATIE VAN API-sleutel

API-sleutel is een unieke id die wordt gebruikt om een gebruiker te verifiëren voor toegang tot REST API eindpunt. De sleutel wordt verzonden in een aangepaste HTTP-header. De [http-trigger voor Azure Functions maakt](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) bijvoorbeeld gebruik van de `x-functions-key` HTTP-header om de aanvragende gebruiker te identificeren.  

### <a name="add-api-key-policy-keys"></a>API-sleutelbeleidssleutels toevoegen

Als u een technisch profiel REST API api-sleutelverificatie wilt configureren, maakt u de volgende cryptografische sleutel om de API-sleutel op te slaan:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C tenant. Selecteer het **filter Map en abonnement** in het bovenste menu en kies uw Azure AD B2C map.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina Overzicht **de optie Identity Experience Framework**.
1. Selecteer **Beleidssleutels** en selecteer vervolgens **Toevoegen.**
1. Selecteer **handmatig** bij **Opties.**
1. Bij **Naam** typt u **RestApiKey.**
    Het *voorvoegsel B2C_1A_* automatisch toegevoegd.
1. Voer in **het** vak Geheim de REST API in.
1. Selecteer **versleuteling** bij **Sleutelgebruik.**
1. Selecteer **Maken**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Uw technische REST API configureren voor het gebruik van API-sleutelverificatie

Nadat u de benodigde sleutel hebt gemaakt, configureert REST API metagegevens van uw technische profiel om te verwijzen naar de referenties.

1. Open in uw werkmap het extensiebeleidsbestand (TrustFrameworkExtensions.xml).
1. Zoek naar het REST API profiel. Bijvoorbeeld `REST-ValidateProfile` of `REST-GetProfile` .
1. Zoek het `<Metadata>` element .
1. Wijzig *authenticationType* in `ApiKeyHeader` .
1. Wijzig *AllowInsecureAuthInProduction* in `false` .
1. Voeg direct na het `</Metadata>` afsluitende element het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

De **id** van de cryptografische sleutel definieert de HTTP-header. In dit voorbeeld wordt de API-sleutel verzonden als **x-functions-key.**

Hier volgt een voorbeeld van een technisch RESTful-profiel dat is geconfigureerd voor het aanroepen van een Azure-functie met API-sleutelverificatie:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [technische profielelement Restful](restful-technical-profile.md) vindt u in de IEF-naslaginformatie.
