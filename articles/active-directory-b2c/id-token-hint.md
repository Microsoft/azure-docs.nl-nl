---
title: Een technisch profiel voor ID-token hints definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een technisch profiel voor ID-token Hint in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d77e145cabcef2931d5fe6e76599da7931e576e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97669156"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technisch profiel voor ID-token hints definiëren in een Azure Active Directory B2C aangepast beleid

Met Azure AD B2C kunnen Relying Party toepassingen een inkomende JWT verzenden als onderdeel van de OAuth2-autorisatie aanvraag. De JWT-token kan worden uitgegeven door een Relying Party toepassing of een id-provider, en kan een hint voor de gebruiker of de autorisatie aanvraag door geven. Azure AD B2C valideert de hand tekening, naam van de verlener en de token doelgroep en extraheert de claim van het inkomende token.

## <a name="use-cases"></a>Gebruiksvoorbeelden

U kunt deze oplossing gebruiken om gegevens te verzenden naar Azure AD B2C ingekapseld in één JWT-token. De [aanmelding met een e-mail uitnodiging](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md), waarbij uw systeem beheerder een ondertekende uitnodiging kan verzenden aan gebruikers, is gebaseerd op id_token_hint. Alleen gebruikers met toegang tot het e-mail adres voor uitnodigingen kunnen het account in de Directory maken.

## <a name="token-signing-approach"></a>Methode voor token ondertekening

Met id_token_hint stelt de uitgever van het token (een Relying Party-app of een id-provider) het token samen en ondertekent deze vervolgens met behulp van een handtekening sleutel om te bewijzen dat het token afkomstig is van een vertrouwde bron. De handtekening sleutel kan symmetrisch of asymmetrisch zijn. Symmetrische crypto grafie of persoonlijke sleutel cryptografie gebruikt een gedeeld geheim om de hand tekening te ondertekenen en te valideren. Asymmetrische crypto grafie of open bare-sleutel cryptografie is een cryptografisch systeem dat gebruikmaakt van een persoonlijke sleutel en een open bare sleutel. De persoonlijke sleutel wordt alleen bekend bij de uitgever van het token en wordt gebruikt om het token te ondertekenen. De open bare sleutel wordt gedeeld met het Azure AD B2C-beleid om de hand tekening van het token te valideren.

## <a name="token-format"></a>Token indeling

De id_token_hint moet een geldig JWT-token zijn. De volgende tabel geeft een lijst van de claims die verplicht zijn. Aanvullende claims zijn optioneel.

| Name | Claim | Voorbeeldwaarde | Beschrijving |
| ---- | ----- | ------------- | ----------- |
| Doelgroep | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Identificeert de beoogde ontvanger van het token. De doel groep is een wille keurige teken reeks die is gedefinieerd door de uitgever van het token. Azure AD B2C valideert deze waarde en wijst het token af als het niet overeenkomt.  |
| Verlener | `iss` |`https://localhost` | Identificeert de beveiligings token service (token uitgever). De uitgever is een wille keurige URI die is gedefinieerd door de uitgever van het token. Azure AD B2C valideert deze waarde en wijst het token af als het niet overeenkomt.  |
| Verloop tijd | `exp` | `1600087315` | Het tijdstip waarop het token ongeldig wordt, uitgedrukt in de epoche-tijd. Azure AD B2C valideert deze waarde en wijst het token af als het token is verlopen.|
| Niet voor | `nbf` | `1599482515` | Het tijdstip waarop het token geldig wordt, uitgedrukt in de epoche-tijd. Deze tijd is doorgaans hetzelfde als het tijdstip waarop het token is uitgegeven. Azure AD B2C valideert deze waarde en wijst het token af als de levens duur van het token ongeldig is. |

 Het volgende token is een voor beeld van een geldig ID-token:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het **protocol** element moet worden ingesteld op `None` . Het protocol voor het **IdTokenHint_ExtractClaims** technische profiel is bijvoorbeeld `None` :

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

Het technische profiel wordt aangeroepen vanuit een Orchestration-stap met het type `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die moeten worden geëxtraheerd uit de JWT-token. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de JWT-token. U kunt ook claims toevoegen die niet worden geretourneerd door de JWT-token, op voor waarde dat u het kenmerk hebt ingesteld `DefaultValue` .

## <a name="metadata"></a>Metagegevens

De volgende meta gegevens zijn relevant voor het gebruik van symmetrische sleutels. 

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| uitgever | Ja | Identificeert de beveiligings token service (token uitgever). Deze waarde moet gelijk zijn aan de `iss` claim binnen de claim van de JWT-token. | 
| IdTokenAudience | Ja | Identificeert de beoogde ontvanger van het token. Moet gelijk zijn aan de `aud` claim binnen de JWT-token claim. | 

De volgende meta gegevens zijn relevant voor het gebruik van een asymmetrische sleutel. 

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| METAGEGEVENSARCHIEFMETHODE| Ja | Een URL die verwijst naar een configuratie document voor de certificaat Uitgever, dat ook wel een OpenID Connect goed bekend configuratie-eind punt wordt genoemd.   |
| uitgever | Nee | Identificeert de beveiligings token service (token uitgever). Deze waarde kan worden gebruikt om de waarde die is geconfigureerd in de meta gegevens te overschrijven en moet identiek zijn aan de `iss` claim binnen de JWT-token claim. |  
| IdTokenAudience | Nee | Identificeert de beoogde ontvanger van het token. Moet gelijk zijn aan de `aud` claim binnen de JWT-token claim. |  

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Wanneer u een symmetrische sleutel gebruikt, bevat het element **CryptographicKeys** het volgende kenmerk:

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| client_secret | Ja | De cryptografische sleutel die wordt gebruikt om de JWT-token handtekening te valideren.|


## <a name="how-to-guide"></a>Instructiegids

### <a name="issue-a-token-with-symmetric-keys"></a>Een token met symmetrische sleutels uitgeven

#### <a name="step-1-create-a-shared-key"></a>Stap 1. Een gedeelde sleutel maken 

Maak een sleutel die kan worden gebruikt voor het ondertekenen van het token. Gebruik bijvoorbeeld de volgende Power shell-code om een sleutel te genereren.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Met deze code wordt een geheime teken reeks gemaakt, zoals `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` .

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>Stap 2. De handtekening sleutel toevoegen aan Azure AD B2C

Dezelfde sleutel die wordt gebruikt door de token uitgever moet worden gemaakt in uw Azure AD B2C-beleids sleutels.  

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het pictogram **Map + Abonnement** in de werkbalk van de portal en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Zoek en selecteer **Azure AD B2C** in de Azure-portal.
1. Selecteer op de pagina overzicht onder **beleids regels** het **Framework identiteits ervaring**.
1. **Beleids sleutels** selecteren 
1. Selecteer **hand matig**.
1. Gebruik voor **naam** `IdTokenHintKey` .  
   Het voor voegsel `B2C_1A_` kan automatisch worden toegevoegd.
1. Voer in het vak **geheim** de aanmeldings sleutel in die u eerder hebt gegenereerd.
1. Gebruik **versleuteling** voor **sleutel gebruik**.
1. Selecteer **Maken**.
1. Bevestig dat u de sleutel hebt gemaakt `B2C_1A_IdTokenHintKey` .


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>Stap 3. Het technische profiel van de ID-token Hint toevoegen

Het volgende technische profiel valideert het token en extraheert de claims. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>Stap 4. Uw beleid voorbereiden

Voltooi de stap [uw beleid configureren](#configure-your-policy) .

#### <a name="step-5-prepare-the-code"></a>Stap 5. De code voorbereiden  

Het [github](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) -voor beeld is een ASP.NET-webtoepassing en-console-app waarmee een id-token wordt gegenereerd dat is ondertekend met behulp van een symmetrische sleutel. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Een token met asymmetrische sleutels uitgeven

Met een asymmetrische sleutel wordt het token ondertekend met behulp van RSA-certificaten. Deze toepassing fungeert als host voor een open ID Connect-eind punt voor meta gegevens en JSON-websleutels (JWKs) dat wordt gebruikt door Azure AD B2C om de hand tekening van het ID-token te valideren.

De uitgever van het token moet de volgende eind punten leveren:

* `/.well-known/openid-configuration` -Een goed bekend configuratie-eind punt met relevante informatie over het token, zoals de naam van de uitgever van het token en de koppeling naar het JWK-eind punt. 
* `/.well-known/keys` -het eind punt van de JSON-websleutel (JWK) met de open bare sleutel die wordt gebruikt voor het ondertekenen van de sleutel (met het deel van de persoonlijke sleutel van het certificaat).

Zie het voor beeld van de .NET MVC-controller [TokenMetadataController. cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .

#### <a name="step-1-prepare-a-self-signed-certificate"></a>Stap 1. Een zelfondertekend certificaat voorbereiden

Als u nog geen certificaat hebt, kunt u een zelfondertekend certificaat gebruiken voor deze hand leiding. In Windows kunt u de cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) van Power shell gebruiken voor het genereren van een certificaat.

Voer deze Power shell-opdracht uit om een zelfondertekend certificaat te genereren. Wijzig het `-Subject` argument naar wens voor uw toepassing en Azure AD B2C Tenant naam. U kunt ook de `-NotAfter` datum aanpassen om een andere verloop tijd voor het certificaat op te geven.

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>Stap 2. Het technische profiel van de ID-token Hint toevoegen 

Het volgende technische profiel valideert het token en extraheert de claims. Wijzig de meta gegevens-URI in het bekende configuratie-eind punt van uw token Uitgever.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>Stap 3. Uw beleid voorbereiden

Voltooi de stap [uw beleid configureren](#configure-your-policy) .

#### <a name="step-4-prepare-the-code"></a>Stap 4. De code voorbereiden 

Deze [github](https://github.com/azure-ad-b2c/id-token-builder) -voor beeld-ASP.NET-webtoepassing genereert id-tokens en host de eind punten van de meta gegevens die nodig zijn voor het gebruik van de para meter ' id_token_hint ' in azure AD B2C.


### <a name="configure-your-policy"></a>Uw beleid configureren

Voor zowel symmetrische als asymmetrische benaderingen `id_token_hint` wordt het technische profiel aangeroepen vanuit een indelings stap met type `GetClaims` en moet de invoer claims van het Relying Party beleid opgeven.

1. Voeg het technische profiel IdTokenHint_ExtractClaims toe aan uw extensie beleid.
1. Voeg de volgende Orchestration-stap toe aan uw gebruikers traject als het eerste item.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. Herhaal in uw Relying Party-beleid dezelfde invoer claims die u hebt geconfigureerd in het technische profiel van IdTokenHint_ExtractClaims. Bijvoorbeeld:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

Afhankelijk van uw bedrijfs vereisten moet u mogelijk token validaties toevoegen, bijvoorbeeld de indeling van het e-mail adres controleren. Om dit te doen, moet u de indelings stappen voor het aanroepen van een [technische profiel voor claim transformatie](claims-transformation-technical-profile.md)toevoegen. Voeg ook een [zelf-bevestigd technisch profiel](self-asserted-technical-profile.md) toe om een fout bericht weer te geven. 

### <a name="create-and-sign-a-token"></a>Een token maken en ondertekenen

De GitHub-voor beelden laten zien hoe u een dergelijk token maakt, een JWT die later als een `id_token_hint` query reeks parameter is verzonden. Hieronder volgt een voor beeld van een autorisatie aanvraag met id_token_hint-para meter
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [e-mail oplossing voor aanmelden met uitnodiging](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) voor de Azure AD B2C Community github opslag plaats.
