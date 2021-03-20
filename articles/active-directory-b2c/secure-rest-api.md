---
title: Een rest-service in uw Azure AD B2C beveiligen
titleSuffix: Azure AD B2C
description: Beveilig uw aangepaste REST API claim uitwisselingen in uw Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6907db7f6e53247a8f2fc0042e8c8e6b081dbd3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516370"
---
# <a name="secure-your-restful-services"></a>Uw REST-services beveiligen 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wanneer u een REST API integreert binnen een Azure AD B2C gebruikers traject, moet u uw REST API-eind punt beveiligen met verificatie. Dit zorgt ervoor dat alleen services die de juiste referenties hebben, zoals Azure AD B2C, aanroepen van uw REST API-eind punt kunnen aanbrengen.

Informatie over het integreren van een REST API binnen uw Azure AD B2C gebruikers traject in de gebruikers [invoer valideren](custom-policy-rest-api-claims-validation.md) en [rest API claims-uitwisselingen toevoegen aan aangepaste beleids](custom-policy-rest-api-claims-exchange.md) artikelen.

In dit artikel wordt uitgelegd hoe u uw REST API kunt beveiligen met HTTP Basic, client certificaat of OAuth2-verificatie. 

## <a name="prerequisites"></a>Vereisten

Volg de stappen in een van de volgende ' instructies ':

- [Integreer rest API claim uitwisselingen in uw Azure AD B2C gebruikers traject om gebruikers invoer te valideren](custom-policy-rest-api-claims-validation.md).
- [REST API claim uitwisselingen toevoegen aan aangepast beleid](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP-basis verificatie

Basis verificatie HTTP is gedefinieerd in [RFC 2617](https://tools.ietf.org/html/rfc2617). Basis verificatie werkt als volgt: Azure AD B2C verzendt een HTTP-aanvraag met de client referenties in de autorisatie-header. De referenties zijn ingedeeld als de base64-gecodeerde teken reeks ' naam: wacht woord '.  

### <a name="add-rest-api-username-and-password-policy-keys"></a>REST API gebruikers naam-en wachtwoord beleid sleutels toevoegen

Als u een REST API technisch profiel met HTTP-basis verificatie wilt configureren, maakt u de volgende cryptografische sleutels om de gebruikers naam en het wacht woord op te slaan:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C Directory.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Voor **Opties** selecteert u **hand matig**.
1. Typ **RestApiUsername** voor **naam**.
    De prefix *B2C_1A_* kan automatisch worden toegevoegd.
1. Voer in het vak **geheim** het rest API gebruikers naam in.
1. Voor **sleutel gebruik** selecteert u **versleuteling**.
1. Selecteer **Maken**.
1. Selecteer de **beleids sleutels** opnieuw.
1. Selecteer **Toevoegen**.
1. Voor **Opties** selecteert u **hand matig**.
1. Typ **RestApiPassword** voor **naam**.
    De prefix *B2C_1A_* kan automatisch worden toegevoegd.
1. Voer in het vak **geheim** het rest API wacht woord in.
1. Voor **sleutel gebruik** selecteert u **versleuteling**.
1. Selecteer **Maken**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Uw REST API technische profiel configureren voor het gebruik van HTTP-basis verificatie

Nadat u de benodigde sleutels hebt gemaakt, moet u de meta gegevens van uw REST API technische profiel configureren om te verwijzen naar de referenties.

1. Open in uw werkmap het extensie beleids bestand (TrustFrameworkExtensions.xml).
1. Zoek het technische profiel van REST API. Bijvoorbeeld `REST-ValidateProfile` of `REST-GetProfile` .
1. Zoek het `<Metadata>` element op.
1. Wijzig de *AuthenticationType* in `Basic` .
1. Wijzig de *AllowInsecureAuthInProduction* in `false` .
1. Voeg direct na het afsluitende `</Metadata>` element het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Hier volgt een voor beeld van een onderliggend technisch profiel dat is geconfigureerd met HTTP-basis verificatie:

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

## <a name="https-client-certificate-authentication"></a>Verificatie van HTTPS-client certificaten

Verificatie van client certificaten is een wederzijdse verificatie op basis van certificaten, waarbij de client, Azure AD B2C, het client certificaat levert aan de server om zijn identiteit te bewijzen. Dit gebeurt als onderdeel van de SSL-handshake. Alleen services die over de juiste certificaten beschikken, zoals Azure AD B2C, hebben toegang tot uw REST API-service. Het client certificaat is een X. 509-digitaal certificaat. In productie omgevingen moet het worden ondertekend door een certificerings instantie.

### <a name="prepare-a-self-signed-certificate-optional"></a>Een zelfondertekend certificaat voorbereiden (optioneel)

Als u nog geen certificaat hebt, kunt u voor niet-productie omgevingen een zelfondertekend certificaat gebruiken. In Windows kunt u de cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) van Power shell gebruiken voor het genereren van een certificaat.

1. Voer deze Power shell-opdracht uit om een zelfondertekend certificaat te genereren. Wijzig het `-Subject` argument naar wens voor uw toepassing en Azure AD B2C Tenant naam. U kunt ook de `-NotAfter` datum aanpassen om een andere verloop tijd voor het certificaat op te geven.
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
1. Open **gebruikers certificaten beheren**  >  **huidige gebruiker**  >  **persoonlijke**  >  **certificaten**  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Selecteer de **actie** certificaat >  >  **alle taken**  >  **exporteren**.
1. Selecteer **Ja**  >  **volgende**  >  **Ja, de persoonlijke sleutel exporteren**  >  **volgende**.
1. Accepteer de standaard instellingen voor de **export bestands indeling**.
1. Geef een wacht woord op voor het certificaat.

### <a name="add-a-client-certificate-policy-key"></a>Een beleids sleutel voor client certificaten toevoegen

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C Directory.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Selecteer **uploaden** in het vak **Opties** .
1. Typ **RestApiClientCertificate** in het vak **naam** .
    Het voor voegsel *B2C_1A_* automatisch toegevoegd.
1. Selecteer in het vak **bestand uploaden** het pfx-bestand van uw certificaat met een persoonlijke sleutel.
1. Typ in het vak **wacht** woord het wacht woord van het certificaat.
1. Selecteer **Maken**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Uw REST API technische profiel configureren voor het gebruik van verificatie van client certificaten

Nadat u de benodigde sleutel hebt gemaakt, moet u de meta gegevens van uw REST API technische profiel configureren om te verwijzen naar het client certificaat.

1. Open in uw werkmap het extensie beleids bestand (TrustFrameworkExtensions.xml).
1. Zoek het technische profiel van REST API. Bijvoorbeeld `REST-ValidateProfile` of `REST-GetProfile` .
1. Zoek het `<Metadata>` element op.
1. Wijzig de *AuthenticationType* in `ClientCertificate` .
1. Wijzig de *AllowInsecureAuthInProduction* in `false` .
1. Voeg direct na het afsluitende `</Metadata>` element het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Hier volgt een voor beeld van een onderliggend technisch profiel dat is geconfigureerd met een HTTP-client certificaat:

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

Verificatie van Bearer-tokens is gedefinieerd in het [OAuth 2.0-autorisatie raamwerk: Bearer-token gebruik (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). In Bearer-token authenticatie verzendt Azure AD B2C een HTTP-aanvraag met een token in de autorisatie-header.

```http
Authorization: Bearer <token>
```

Een Bearer-token is een ondoorzichtige teken reeks. Dit kan een JWT-toegangs token zijn of een wille keurige teken reeks die de REST API verwacht Azure AD B2C te verzenden in de autorisatie-header. Azure AD B2C ondersteunt de volgende typen:

- **Bearer-token**. Om het Bearer-token in het onderdrukken bare technische profiel te kunnen verzenden, moet uw beleid eerst het Bearer-token verkrijgen en het vervolgens gebruiken in het onderhouds technische profiel.  
- **Statisch Bearer-token**. Gebruik deze methode wanneer uw REST API een lange termijn toegangs token uitgeeft. Als u een statisch Bearer-token wilt gebruiken, maakt u een beleids sleutel en maakt u een verwijzing van het onderliggend technische profiel naar uw beleids sleutel. 


## <a name="using-oauth2-bearer"></a>OAuth2 Bearer gebruiken  

De volgende stappen laten zien hoe u client referenties gebruikt om een Bearer-token te verkrijgen en door te geven aan de autorisatie-header van de REST API-aanroepen.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Een claim definiëren om het Bearer-token op te slaan

Een claim biedt tijdelijke opslag van gegevens tijdens het uitvoeren van een Azure AD B2C beleid. Het [claim schema](claimsschema.md) is de plaats waar u uw claims declareert. Het toegangs token moet worden opgeslagen in een claim om later te worden gebruikt. 

1. Open het bestand extensies van uw beleid. Bijvoorbeeld <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Zoek het element [BuildingBlocks](buildingblocks.md) . Als het element niet bestaat, voegt u het toe.
1. Zoek het element [ClaimsSchema](claimsschema.md) . Als het element niet bestaat, voegt u het toe.
1. Voeg de volgende claims toe aan het **ClaimsSchema** -element.  

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

U kunt op een van de volgende manieren een toegangs token verkrijgen: door het te verkrijgen [bij een federatieve id-provider](idp-pass-through-user-flow.md), door een rest API aan te roepen dat een toegangs token retourneert, door gebruik te maken van een [ROPC-stroom](../active-directory/develop/v2-oauth-ropc.md)of door gebruik te maken van de [client referentie stroom](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).  

In het volgende voor beeld wordt een REST API technisch profiel gebruikt om een aanvraag naar het Azure AD-token eindpunt te maken met behulp van de client referenties die zijn door gegeven als HTTP-basis verificatie. Als u dit wilt configureren in azure AD, raadpleegt u [micro soft Identity platform en de OAuth 2,0 client credentials flow](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Mogelijk moet u dit wijzigen in de interface met uw ID-provider. 

Vervang uw-Tenant naam voor de ServiceUrl door de naam van uw Azure AD-Tenant. Zie de naslag informatie over de [rest technische profielen](restful-technical-profile.md) voor alle beschik bare opties.

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
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Het REST-technische profiel voor het gebruik van Bearer-token authenticatie wijzigen

Als u Bearer-token verificatie wilt ondersteunen in uw aangepaste beleid, wijzigt u het technische profiel van REST API met de volgende opties:

1. Open in uw werkmap het *TrustFrameworkExtensions.xml* extensie beleids bestand.
1. Zoek het `<TechnicalProfile>` knoop punt dat bevat `Id="REST-API-SignUp"` .
1. Zoek het `<Metadata>` element op.
1. Wijzig de *AuthenticationType* als *volgt*:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Wijzig of Voeg de *UseClaimAsBearerToken* als volgt toe aan *bearerToken*. De *bearerToken* is de naam van de claim waarvan het Bearer-token wordt opgehaald (de uitvoer claim van `SecureREST-AccessToken` ).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Zorg ervoor dat u de claim die hierboven is gebruikt, toevoegt als invoer claim:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Nadat u de bovenstaande fragmenten hebt toegevoegd, moet uw technische profiel eruitzien zoals in de volgende XML-code:

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

## <a name="using-a-static-oauth2-bearer"></a>Een statische OAuth2-Bearer gebruiken 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>De sleutel voor het OAuth2 Bearer-token beleid toevoegen

Als u een REST API technisch profiel wilt configureren met een OAuth2 Bearer-token, moet u een toegangs token verkrijgen van de REST API eigenaar. Maak vervolgens de volgende cryptografische sleutel om het Bearer-token op te slaan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C Directory.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Kies voor **Opties** `Manual` .
1. Voer een **naam** in voor de beleids sleutel. Bijvoorbeeld `RestApiBearerToken`. Het voor voegsel `B2C_1A_` wordt automatisch toegevoegd aan de naam van uw sleutel.
1. Voer in het **geheim** uw client geheim in dat u eerder hebt vastgelegd.
1. Selecteer voor **sleutel gebruik** `Encryption` .
1. Selecteer **Maken**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Uw REST API technische profiel configureren voor het gebruik van de sleutel voor het Bearer-token beleid

Nadat u de benodigde sleutel hebt gemaakt, moet u de meta gegevens van uw REST API technische profiel configureren om te verwijzen naar het Bearer-token.

1. Open in uw werkmap het extensie beleids bestand (TrustFrameworkExtensions.xml).
1. Zoek het technische profiel van REST API. Bijvoorbeeld `REST-ValidateProfile` of `REST-GetProfile` .
1. Zoek het `<Metadata>` element op.
1. Wijzig de *AuthenticationType* in `Bearer` .
1. Wijzig de *AllowInsecureAuthInProduction* in `false` .
1. Voeg direct na het afsluitende `</Metadata>` element het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Hier volgt een voor beeld van een onderliggend technisch profiel dat is geconfigureerd met Bearer-token authenticatie:

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

## <a name="api-key-authentication"></a>API-sleutel verificatie

API-sleutel is een unieke id die wordt gebruikt om een gebruiker te verifiëren voor toegang tot een REST API-eind punt. De sleutel wordt in een aangepaste HTTP-header verzonden. Zo gebruikt de [HTTP-trigger van Azure functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) de `x-functions-key` http-header om de aanvrager te identificeren.  

### <a name="add-api-key-policy-keys"></a>API-sleutel beleids sleutels toevoegen

Als u een REST API technisch profiel met API-sleutel verificatie wilt configureren, maakt u de volgende cryptografische sleutel om de API-sleutel op te slaan:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-Tenant bevat. Selecteer het filter **Directory + abonnement** in het bovenste menu en kies uw Azure AD B2C Directory.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer op de pagina overzicht **identiteits ervaring-Framework**.
1. Selecteer **beleids sleutels** en selecteer vervolgens **toevoegen**.
1. Voor **Opties** selecteert u **hand matig**.
1. Typ **RestApiKey** voor **naam**.
    De prefix *B2C_1A_* kan automatisch worden toegevoegd.
1. Voer in het vak **geheim** de rest API sleutel in.
1. Voor **sleutel gebruik** selecteert u **versleuteling**.
1. Selecteer **Maken**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Uw REST API technische profiel configureren om API-sleutel verificatie te gebruiken

Nadat u de benodigde sleutel hebt gemaakt, moet u de meta gegevens van uw REST API technische profiel configureren om te verwijzen naar de referenties.

1. Open in uw werkmap het extensie beleids bestand (TrustFrameworkExtensions.xml).
1. Zoek het technische profiel van REST API. Bijvoorbeeld `REST-ValidateProfile` of `REST-GetProfile` .
1. Zoek het `<Metadata>` element op.
1. Wijzig de *AuthenticationType* in `ApiKeyHeader` .
1. Wijzig de *AllowInsecureAuthInProduction* in `false` .
1. Voeg direct na het afsluitende `</Metadata>` element het volgende XML-fragment toe:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

De **id** van de cryptografische sleutel definieert de http-header. In dit voor beeld wordt de API-sleutel verzonden als **x-functions-sleutel**.

Hier volgt een voor beeld van een onderliggend technisch profiel dat is geconfigureerd voor het aanroepen van een Azure-functie met API-sleutel verificatie:

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

- Meer informatie over het element van het [rest technische profiel](restful-technical-profile.md) vindt u in de IEF-verwijzing.