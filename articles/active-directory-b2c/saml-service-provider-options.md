---
title: Opties voor SAML-service providers configureren
title-suffix: Azure Active Directory B2C
description: Azure Active Directory B2C SAML-service provider opties configureren
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 09cfdd026105a34db976118f38b011e2c4578a24
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470775"
---
# <a name="options-for-registering-a-saml-application-in-azure-ad-b2c"></a>Opties voor het registreren van een SAML-toepassing in Azure AD B2C

In dit artikel worden de configuratie opties beschreven die beschikbaar zijn bij het verbinden van Azure Active Directory (Azure AD B2C) met uw SAML-toepassing.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="encrypted-saml-assertions"></a>Versleutelde SAML-bevestigingen

Als uw toepassing verwacht dat de SAML-bevestigingen in een versleutelde indeling zijn, moet u ervoor zorgen dat versleuteling is ingeschakeld in het Azure AD B2C beleid.

Azure AD B2C gebruikt het open bare-sleutel certificaat van de service provider om de SAML-bewering te versleutelen. De open bare sleutel moet bestaan in het eind punt voor meta gegevens van de SAML-toepassing met de sleutel descriptor use ingesteld op encryption, zoals wordt weer gegeven in het volgende voor beeld:

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Als u wilt dat Azure AD B2C versleutelde beweringen verzendt, stelt u het **WantsEncryptedAssertion** -meta gegevens item `true` in op in het [technische profiel van Relying Party](relyingparty.md#technicalprofile). U kunt ook de algoritme configureren die wordt gebruikt om de SAML-bewering te versleutelen.

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

### <a name="encryption-method"></a>Versleutelingsmethode

Als u de versleutelings methode wilt configureren die wordt gebruikt om de gegevens van de SAML-verklaring te versleutelen, stelt u de `DataEncryptionMethod` meta gegevens sleutel in de Relying Party Mogelijke waarden zijn `Aes256` (standaard), `Aes192` , `Sha512` of `Aes128` . De meta gegevens bepalen de waarde van het `<EncryptedData>` element in het SAML-antwoord.

Als u de versleutelings methode wilt configureren die wordt gebruikt voor het versleutelen van de kopie van de sleutel, die is gebruikt om de gegevens van de SAML-verklaring te versleutelen, stelt u de `KeyEncryptionMethod` meta gegevens sleutel in de Relying Party Mogelijke waarden zijn `Rsa15` (standaard): RSA-algoritme voor de open bare sleutel crypto grafie Standard (PKCS) versie 1,5 en `RsaOaep` -RSA OAEP-versleutelings algoritme (optimale asymmetrische-coderings opvulling).  De meta gegevens bepalen de waarde van het  `<EncryptedKey>` element in het SAML-antwoord.

In het volgende voor beeld wordt de `EncryptedAssertion` sectie van een SAML-verklaring weer gegeven. De versleutelde gegevens methode is `Aes128` en de versleutelde sleutel methode is `Rsa15` .

```xml
<saml:EncryptedAssertion>
  <xenc:EncryptedData xmlns:xenc="http://www.w3.org/2001/04/xmlenc#"
    xmlns:dsig="http://www.w3.org/2000/09/xmldsig#" Type="http://www.w3.org/2001/04/xmlenc#Element">
    <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#aes128-cbc" />
    <dsig:KeyInfo>
      <xenc:EncryptedKey>
        <xenc:EncryptionMethod Algorithm="http://www.w3.org/2001/04/xmlenc#rsa-1_5" />
        <xenc:CipherData>
          <xenc:CipherValue>...</xenc:CipherValue>
        </xenc:CipherData>
      </xenc:EncryptedKey>
    </dsig:KeyInfo>
    <xenc:CipherData>
      <xenc:CipherValue>...</xenc:CipherValue>
    </xenc:CipherData>
  </xenc:EncryptedData>
</saml:EncryptedAssertion>
```

U kunt de indeling van de versleutelde beweringen wijzigen. Als u de versleutelings indeling wilt configureren, stelt u de `UseDetachedKeys` meta gegevens sleutel in de Relying Party. Mogelijke waarden: `true` , of `false` (standaard). Wanneer de waarde is ingesteld op `true` , worden de versleutelde bevestigingen door de ontkoppelde sleutels als een onderliggend element van de `EncrytedAssertion` in plaats van de `EncryptedData` .

Configureer de versleutelings methode en-indeling, gebruik de meta gegevens sleutels binnen het [Relying Party technische profiel](relyingparty.md#technicalprofile):

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="DataEncryptionMethod">Aes128</Item>
      <Item Key="KeyEncryptionMethod">Rsa15</Item>
      <Item Key="UseDetachedKeys">false</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="identity-provider-initiated-flow"></a>Door ID-provider geïnitieerde stroom

Als uw toepassing verwacht een SAML-bevestiging te ontvangen zonder eerst een SAML authn-aanvraag naar de ID-provider te verzenden, moet u Azure AD B2C configureren voor de door de provider geïnitieerde stroom.

In de door de provider geïnitieerde stroom wordt het aanmeldings proces geïnitieerd door de ID-provider (Azure AD B2C), die een ongevraagd SAML-antwoord naar de service provider (uw Relying Party-toepassing) verzendt.

Er worden momenteel geen scenario's ondersteund waarbij de initiator-ID-provider een externe ID-provider is die federatief is voor Azure AD B2C, bijvoorbeeld [AD-FS](identity-provider-adfs.md)of [Sales Force](identity-provider-salesforce-saml.md). Het wordt alleen ondersteund voor de verificatie van Azure AD B2C lokale accounts.

Als u de door de provider geïnitieerde stroom wilt inschakelen, stelt u het **IdpInitiatedProfileEnabled** -meta gegevens item `true` in op het [technische profiel](relyingparty.md#technicalprofile)van de Relying Party.

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

Gebruik de volgende URL om u aan te melden of een gebruiker aan te melden via een door de provider geïnitieerde stroom:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/generic/login?EntityId=app-identifier-uri 
```

Vervang de volgende waarden:

* **Tenant naam** met de naam van uw Tenant
* **beleids naam** met uw SAML-Relying Party-beleids naam
* **App-ID-URI** met de `identifierUris` in het meta gegevensbestand, zoals `https://contoso.onmicrosoft.com/app-name`

### <a name="sample-policy"></a>Voorbeeldbeleid

We bieden een volledig voor beeld van een beleid dat u kunt gebruiken voor het testen met de app voor SAML-tests.

1. Down load het [voorbeeld beleid voor door SAML-SP geïnitieerde aanmelding](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated).
1. Werk `TenantId` bij met de naam van uw Tenant, bijvoorbeeld *contoso.b2clogin.com*.
1. Behoud de beleids naam *B2C_1A_signup_signin_saml*.

## <a name="saml-response-signature-algorithm"></a>Algoritme voor SAML-respons handtekeningen

U kunt de handtekening algoritme configureren die wordt gebruikt om de SAML-bewering te ondertekenen. Mogelijke waarden zijn `Sha256` , `Sha384` , `Sha512` , of `Sha1` . Zorg ervoor dat het technische profiel en de toepassing gebruikmaken van hetzelfde handtekening algoritme. Gebruik alleen de algoritme die door uw certificaat wordt ondersteund.

Configureer het handtekening algoritme met behulp van de `XmlSignatureAlgorithm` meta gegevens sleutel binnen het meta gegevenselement Relying Party.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="SAML2"/>
    <Metadata>
      <Item Key="XmlSignatureAlgorithm">Sha256</Item>
    </Metadata>
   ..
  </TechnicalProfile>
</RelyingParty>
```

## <a name="saml-response-lifetime"></a>Levens duur van SAML-antwoorden

U kunt configureren hoe lang het SAML-antwoord geldig blijft. Stel de levens duur in met behulp van het `TokenLifeTimeInSeconds` meta gegevens item in het SAML-token voor de uitgever van het technische profiel. Deze waarde is het aantal seconden dat kan verstrijken vanaf de `NotBefore` tijds tempel die wordt berekend op basis van de token uitgifte tijd. De standaard levensduur is 300 seconden (5 minuten).

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenLifeTimeInSeconds">400</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="saml-response-valid-from-skew"></a>SAML-reactie geldig vanaf scheefheid

U kunt het tijds verschil configureren dat wordt toegepast op het SAML-antwoord `NotBefore` tijds tempel. Deze configuratie zorgt ervoor dat als de tijden tussen twee platforms niet synchroon zijn, de SAML-bevestiging nog steeds geldig is wanneer deze tijd scheef wordt.

Stel het tijds verschil in met behulp van het `TokenNotBeforeSkewInSeconds` meta gegevens item in het SAML-token voor de uitgever van het technische profiel. De waarde voor schuin trekken wordt in seconden gegeven, met een standaard waarde van 0. De maximum waarde is 3600 (één uur).

Als bijvoorbeeld `TokenNotBeforeSkewInSeconds` is ingesteld op `120` seconden:

- Het token is uitgegeven om 13:05:10 UTC
- Het token is geldig van 13:03:10 UTC

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="TokenNotBeforeSkewInSeconds">120</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="remove-milliseconds-from-date-and-time"></a>Milliseconden van datum en tijd verwijderen

U kunt opgeven of de milliseconden worden verwijderd uit datum/tijd-waarden binnen het SAML-antwoord (dit zijn IssueInstant, NotBefore, NotOnOrAfter en AuthnInstant). Als u de milliseconden wilt verwijderen, stelt u de `RemoveMillisecondsFromDateTime
` meta gegevens sleutel in de Relying Party. Mogelijke waarden: `false` (standaard) of `true` .

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <Item Key="RemoveMillisecondsFromDateTime">true</Item>
      </Metadata>
      ...
    </TechnicalProfile>
```

## <a name="azure-ad-b2c-issuer-id"></a>ID van Azure AD B2C verlener

Als u meerdere SAML-toepassingen hebt die afhankelijk zijn van verschillende `entityID` waarden, kunt u de `issueruri` waarde in uw Relying Party-bestand overschrijven. Als u de uitgever-URI wilt overschrijven, kopieert u het technische profiel met de ID ' Saml2AssertionIssuer ' uit het basis bestand en overschrijft u de `issueruri` waarde.

> [!TIP]
> Kopieer de `<ClaimsProviders>` sectie uit het basis bestand en bewaar deze elementen binnen de claim provider: `<DisplayName>Token Issuer</DisplayName>` , `<TechnicalProfile Id="Saml2AssertionIssuer">` en `<DisplayName>Token Issuer</DisplayName>` .
 
Voorbeeld:

```xml
   <ClaimsProviders>   
    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Saml2AssertionIssuer">
          <DisplayName>Token Issuer</DisplayName>
          <Metadata>
            <Item Key="IssuerUri">customURI</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpInSAML" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2" />
      <Metadata>
     …
```

## <a name="session-management"></a>Sessiebeheer

U kunt de sessie tussen Azure AD B2C en de SAML-Relying Party toepassing beheren met het `UseTechnicalProfileForSessionManagement` element en de [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider).

## <a name="force-users-to-re-authenticate"></a>Gebruikers dwingen om zich opnieuw te verifiëren 

Om gebruikers te dwingen om zich opnieuw te verifiëren, kan de toepassing het `ForceAuthn` kenmerk in de SAML-verificatie aanvraag opnemen. Het `ForceAuthn` kenmerk is een Booleaanse waarde. Als deze eigenschap is ingesteld op True, wordt de sessie van de gebruiker bij Azure AD B2C ongeldig en wordt de gebruiker opnieuw geverifieerd. De volgende SAML-verificatie aanvraag laat zien hoe u het `ForceAuthn` kenmerk instelt op True. 


```xml
<samlp:AuthnRequest 
       Destination="https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1A_SAML2_signup_signin/samlp/sso/login"
       ForceAuthn="true" ...>
    ...
</samlp:AuthnRequest>
```

## <a name="debug-the-saml-protocol"></a>Fout opsporing voor het SAML-Protocol

Als u de integratie met uw service provider wilt configureren en fouten wilt opsporen, kunt u een browser extensie voor het SAML-protocol gebruiken, bijvoorbeeld [SAML devtools extension](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) for Chrome, [SAML-tracering](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) voor Firefox of [Edge of IE-ontwikkel hulpprogramma's](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Met deze hulpprogram ma's kunt u de integratie van uw toepassing en Azure AD B2C controleren. Bijvoorbeeld:

* Controleer of de SAML-aanvraag een hand tekening bevat en bepaal welk algoritme wordt gebruikt om de autorisatie aanvraag te ondertekenen.
* Controleer of Azure AD B2C een fout bericht retourneert.
* Controleer of het bevestigings gedeelte is versleuteld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het SAML-protocol vindt u [op de Oasis-website](https://www.oasis-open.org/).
- Haal de web-app voor de SAML-test op in de [github-opslag plaats](https://github.com/azure-ad-b2c/saml-sp-tester)van de Azure AD B2C.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp

::: zone-end
