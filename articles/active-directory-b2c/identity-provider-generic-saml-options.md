---
title: Aanmelden met opties voor SAML-ID-provider instellen
titleSuffix: Azure Active Directory B2C
description: IdP-opties (Sign-in SAML ID-provider) configureren in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 32f9df410dabf1902e9a7d9aadbf47288bfa90f5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798235"
---
# <a name="configure-saml-identity-provider-options-with-azure-active-directory-b2c"></a>Opties configureren voor de SAML-ID-provider met Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) ondersteunt Federatie met SAML 2,0-id-providers. In dit artikel worden de configuratie opties beschreven die beschikbaar zijn bij het inschakelen van aanmelden met een SAML-ID-provider.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="claims-mapping"></a>Claim toewijzing

Het **OutputClaims** -element bevat een lijst met claims die zijn geretourneerd door de SAML-ID-provider. U moet de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de ID-provider. Controleer uw ID-provider voor de lijst met claims (verklaringen). U kunt ook de inhoud van het SAML-antwoord dat uw ID-provider retourneert, controleren. Zie [fouten opsporen in SAML-berichten](#debug-saml-protocol)voor meer informatie. Als u een claim wilt toevoegen, moet u eerst [een claim definiëren](claimsschema.md)en vervolgens de claim toevoegen aan de verzameling uitvoer claims.

U kunt ook claims toevoegen die niet worden geretourneerd door de ID-provider, zolang u het kenmerk hebt ingesteld `DefaultValue` . De standaard waarde kan statisch of dynamisch zijn, met behulp van [context claims](#enable-use-of-context-claims).

Het uitvoer claim element bevat de volgende kenmerken:

- **ClaimTypeReferenceId** is de verwijzing naar een claim type. 
- **PartnerClaimType** is de naam van de eigenschap die de SAML-bevestiging wordt weer gegeven. 
- **DefaultValue** is een vooraf gedefinieerde standaard waarde. Als de claim leeg is, wordt de standaard waarde gebruikt. U kunt ook een [claim resolver](claim-resolver-overview.md) gebruiken met een contextuele waarde, zoals de correlatie-id of het IP-adres van de gebruiker.

### <a name="subject-name"></a>Onderwerpnaam

Als u de SAML Assertion **NameID** in het **onderwerp** als een genormaliseerde claim wilt lezen, stelt u de claim **PartnerClaimType** in op de waarde van het `SPNameQualifier` kenmerk. Als het `SPNameQualifier` kenmerk niet wordt weer gegeven, stelt u de claim **PartnerClaimType** in op de waarde van het `NameQualifier` kenmerk.

SAML-bevestiging:

```xml
<saml:Subject>
  <saml:NameID SPNameQualifier="http://your-idp.com/unique-identifier" Format="urn:oasis:names:tc:SAML:2.0:nameid-format:transient">david@contoso.com</saml:NameID>
  <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
    <SubjectConfirmationData InResponseTo="_cd37c3f2-6875-4308-a9db-ce2cf187f4d1" NotOnOrAfter="2020-02-15T16:23:23.137Z" Recipient="https://<your-tenant>.b2clogin.com/<your-tenant>.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" />
    </SubjectConfirmation>
  </saml:SubjectConfirmation>
</saml:Subject>
```

Uitvoer claim:

```xml
<OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="http://your-idp.com/unique-identifier" />
```

Als zowel `SPNameQualifier` als `NameQualifier` kenmerken niet worden weer gegeven in de SAML-bevestiging, stelt u de claim **PartnerClaimType** in op `assertionSubjectName` . Zorg ervoor dat het **NameID** de eerste waarde in Assertion XML is. Wanneer u meer dan één bevestiging definieert, wordt in Azure AD B2C de onderwerpwaarde van de laatste bevestiging gekozen.


## <a name="configure-saml-protocol-bindings"></a>SAML-protocol bindingen configureren

De SAML-aanvragen worden verzonden naar de ID-provider zoals opgegeven in het meta gegevens element van de identiteits provider `SingleSignOnService` . De meeste autorisatie aanvragen van de id-providers worden direct in de URL-query reeks van een HTTP GET-aanvraag uitgevoerd (omdat de berichten betrekkelijk korte zijn). Raadpleeg de documentatie van uw ID-provider voor informatie over het configureren van de bindingen voor beide SAML-aanvragen.

Hier volgt een voor beeld van een Azure AD meta data-service voor eenmalige aanmelding met twee bindingen. De `HTTP-Redirect` krijgt voor rang op de `HTTP-POST` omdat deze het eerst wordt weer gegeven in de meta gegevens van de SAML-identiteits provider.

```xml
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
  <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/saml2"/>
</IDPSSODescriptor>
```

### <a name="assertion-consumer-service"></a>Assertion Consumer Service

De Assertion Consumer Service (of ACS) is de locatie waar de SAML-reacties van de identiteits provider kunnen worden verzonden en ontvangen door Azure AD B2C. SAML-reacties worden verzonden naar Azure AD B2C via een HTTP POST-binding. De ACS-locatie wijst naar het basis beleid van uw Relying Party. Als het vertrouwens beleid bijvoorbeeld is *B2C_1A_signup_signin*, is de ACS het basis beleid van de *B2C_1A_signup_signin*, zoals *B2C_1A_TrustFrameworkBase*.

Hier volgt een voor beeld van een meta gegevens Assertion Consumer Service-element van een Azure AD B2C beleid. 

```xml
<SPSSODescriptor AuthnRequestsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  ...
  <AssertionConsumerService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" Location="https://your-tenant.b2clogin.com/your-tenant/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer" index="0" isDefault="true"/>
</SPSSODescriptor>
```

## <a name="configure-the-saml-request-signature"></a>De SAML-aanvraag handtekening configureren

Azure AD B2C alle uitgaande verificatie aanvragen worden ondertekend met behulp van de cryptografische sleutel **SamlMessageSigning** . Als u de SAML-aanvraag handtekening wilt uitschakelen, stelt u de **WantsSignedRequests** in op `false` . Als de meta gegevens zijn ingesteld op `false` , worden de para meters **SigAlg** en **Signature** (query teken reeks of post parameter) wegge laten uit de aanvraag.

Deze meta gegevens bepalen ook het kenmerk **AuthnRequestsSigned** , dat deel uitmaakt van de meta gegevens van het Azure AD B2C technische profiel dat wordt gedeeld met de ID-provider. Azure AD B2C de aanvraag niet ondertekent als de waarde van **WantsSignedRequests** in de meta gegevens van het technische profiel is ingesteld op `false` en de meta gegevens **WantAuthnRequestsSigned** van de identiteits provider is ingesteld op `false` of niet is opgegeven.

In het volgende voor beeld wordt de hand tekening verwijderd uit de SAML-aanvraag.

```xml
<Metadata>
  ...
  <Item Key="WantsSignedRequests">false</Item>
</Metadata>
```

### <a name="signature-algorithm"></a>Handtekening algoritme

Azure AD B2C gebruikt `Sha1` voor het ondertekenen van de SAML-aanvraag. Gebruik de **XmlSignatureAlgorithm** -meta gegevens om de algoritme te configureren die moet worden gebruikt. Mogelijke waarden zijn `Sha256` , `Sha384` , `Sha512` , of `Sha1` (standaard). Met deze meta gegevens bepaalt u de waarde van de para meter  **SigAlg** (query teken reeks of post parameter) in de SAML-aanvraag. Zorg ervoor dat u het handtekening algoritme aan beide zijden met dezelfde waarde configureert. Gebruik alleen de algoritme die door uw certificaat wordt ondersteund.

### <a name="include-key-info"></a>Sleutel gegevens toevoegen

Wanneer de identiteits provider aangeeft dat Azure AD B2C binding is ingesteld op `HTTP-POST` , bevat Azure AD B2C de hand tekening en het algoritme in de hoofd tekst van de SAML-aanvraag. U kunt ook Azure AD zo configureren dat de open bare sleutel van het certificaat wordt toegevoegd wanneer de binding is ingesteld op `HTTP-POST` . Gebruik de **IncludeKeyInfo** -meta gegevens voor `true` of `false` . In het volgende voor beeld bevat Azure AD geen open bare sleutel van het certificaat.

```xml
<Metadata>
  ...
  <Item Key="IncludeKeyInfo">false</Item>
</Metadata>
```

## <a name="configure-saml-request-name-id"></a>SAML-aanvraag naam-ID configureren

Het element SAML-autorisatie aanvraag `<NameID>` geeft de SAML-naam-id-indeling aan. In deze sectie wordt de standaard configuratie beschreven en wordt uitgelegd hoe u het naam-ID-element kunt aanpassen.

## <a name="preferred-username"></a>Gewenste gebruikers naam

Tijdens een traject voor aanmeldings gebruikers kan een Relying Party toepassing een specifieke gebruiker richten. Met Azure AD B2C kunt u een voorkeurs gebruikersnaam verzenden naar de SAML-ID-provider. Het element **InputClaims** wordt gebruikt voor het verzenden van een **NameID** binnen het **onderwerp** van de SAML-autorisatie aanvraag.

Als u de onderwerpnaam-ID binnen de autorisatie aanvraag wilt toevoegen, voegt u het volgende- `<InputClaims>` element direct achter de toe `<CryptographicKeys>` . De **PartnerClaimType** moet worden ingesteld op `subject` .

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" />
</InputClaims>
```

In dit voor beeld verzendt Azure AD B2C de waarde van de claim **signInName** met **NameID** binnen het **onderwerp** van de SAML-autorisatie aanvraag.

```xml
<samlp:AuthnRequest ... >
  ...
  <saml:Subject>
    <saml:NameID>sam@contoso.com</saml:NameID>
  </saml:Subject>
</samlp:AuthnRequest>
```

U kunt [context claims](claim-resolver-overview.md)gebruiken, bijvoorbeeld `{OIDC:LoginHint}` om de claim waarde in te vullen.

```xml
<Metadata>
  ...
  <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
</Metadata>
  ...
<InputClaims>
  <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="subject" DefaultValue="{OIDC:LoginHint}" AlwaysUseDefaultValue="true" />
</InputClaims>
```

### <a name="name-id-policy-format"></a>Naam-ID-beleids indeling

De SAML-autorisatie aanvraag geeft standaard het `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` beleid op. Dit geeft aan dat een type identificatie dat wordt ondersteund door de ID-provider voor het aangevraagde onderwerp kan worden gebruikt.

Als u dit gedrag wilt wijzigen, raadpleegt u de documentatie van uw identiteits provider voor richt lijnen over welke naam-ID-beleids regels worden ondersteund. Voeg vervolgens de `NameIdPolicyFormat` meta gegevens in de bijbehorende beleids indeling toe. Bijvoorbeeld:

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
</Metadata>
```

De volgende SAML-autorisatie aanvraag bevat het naam-ID-beleid.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" />
</samlp:AuthnRequest>
```

### <a name="allow-creating-new-accounts"></a>Nieuwe accounts maken toestaan

Als u de [beleids indeling naam-id](#name-id-policy-format)opgeeft, kunt u ook de `AllowCreate` eigenschap van **NameIDPolicy** opgeven om aan te geven of de ID-provider een nieuw account mag maken tijdens de aanmeldings stroom. Raadpleeg de documentatie van uw identiteits provider voor hulp.

Azure AD B2C de `AllowCreate` eigenschap standaard weglaat. U kunt dit gedrag wijzigen met behulp van de `NameIdPolicyAllowCreate` meta gegevens. De waarde van deze meta gegevens is `true` of `false` .

In het volgende voor beeld ziet u hoe u `AllowCreate` eigenschap instelt `NameIDPolicy` op `true` .

```xml
<Metadata>
  ...
  <Item Key="NameIdPolicyFormat">urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress</Item>
  <Item Key="NameIdPolicyAllowCreate">true</Item>
</Metadata>
```


In het volgende voor beeld wordt een autorisatie aanvraag met **AllowCreate** van het **NameIDPolicy** -element in de autorisatie aanvraag gedemonstreerd.


```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:NameIDPolicy 
      Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress" 
      AllowCreate="true" />
</samlp:AuthnRequest>
```

### <a name="include-authentication-context-class-references"></a>Referenties voor de verificatie context klasse toevoegen

Een SAML-autorisatie aanvraag kan een **AuthnContext** -element bevatten, waarmee de context van een autorisatie aanvraag wordt opgegeven. Het element kan een verwijzing naar een verificatie context klasse bevatten die de SAML-ID-provider vertelt welke verificatie methode voor de gebruiker moet worden opgegeven.

Als u de referenties voor de verificatie context klasse wilt configureren, voegt u **IncludeAuthnContextClassReferences** -meta gegevens toe. Geef in de waarde een of meer URI-verwijzingen op waarmee verificatie context klassen worden aangeduid. Geef meerdere Uri's op als een door komma's gescheiden lijst. Raadpleeg de documentatie van uw identiteits provider voor hulp bij de **AuthnContextClassRef** -uri's die worden ondersteund.

In het volgende voor beeld kunnen gebruikers zich aanmelden met gebruikers naam en wacht woord en zich aanmelden met gebruikers naam en wacht woord via een beveiligde sessie (SSL/TLS).

```xml
<Metadata>
  ...
  <Item Key="IncludeAuthnContextClassReferences">urn:oasis:names:tc:SAML:2.0:ac:classes:Password,urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</Item>
</Metadata>
```

De volgende SAML-autorisatie aanvraag bevat de referenties van de verificatie context klasse.

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:RequestedAuthnContext>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml:AuthnContextClassRef>
    <saml:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</saml:AuthnContextClassRef>
  </samlp:RequestedAuthnContext>
</samlp:AuthnRequest>
```

## <a name="include-custom-data-in-the-authorization-request"></a>Aangepaste gegevens in de autorisatie aanvraag toevoegen

U kunt optioneel protocol bericht extensie-elementen toevoegen die zijn overeengekomen door zowel Azure AD BC als uw ID-provider. De uitbrei ding wordt weer gegeven in XML-indeling. U kunt uitbreidings elementen toevoegen door XML-gegevens toe te voegen aan het CDATA-element `<![CDATA[Your IDP metadata]]>` . Raadpleeg de documentatie van uw identiteits provider om na te gaan of het element Extensions wordt ondersteund.

In het volgende voor beeld ziet u het gebruik van extensie gegevens:

```xml
<Metadata>
  ...
  <Item Key="AuthenticationRequestExtensions"><![CDATA[
            <ext:MyCustom xmlns:ext="urn:ext:custom">
              <ext:AssuranceLevel>1</ext:AssuranceLevel>
              <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
            </ext:MyCustom>]]></Item>
</Metadata>
```

Wanneer u de SAML-protocol bericht extensie gebruikt, ziet het SAML-antwoord eruit als in het volgende voor beeld:

```xml
<samlp:AuthnRequest ... >
  ...
  <samlp:Extensions>
    <ext:MyCustom xmlns:ext="urn:ext:custom">
      <ext:AssuranceLevel>1</ext:AssuranceLevel>
      <ext:AssuranceDescription>Identity verified to level 1.</ext:AssuranceDescription>
    </ext:MyCustom>
  </samlp:Extensions>
</samlp:AuthnRequest>
```

## <a name="require-signed-saml-responses"></a>Ondertekende SAML-antwoorden vereisen

Voor Azure AD B2C moeten alle binnenkomende verklaringen worden ondertekend. U kunt deze vereiste verwijderen door de **WantsSignedAssertions** in te stellen op `false` . De ID-provider mag de beweringen in dit geval niet ondertekenen, maar zelfs als deze wel wel, Azure AD B2C de hand tekening niet valideren.

De **WantsSignedAssertions** meta gegevens regelen de SAML- **WantAssertionsSigned**, die is opgenomen in de meta gegevens van het Azure AD B2C technische profiel dat wordt gedeeld met de ID-provider.

```xml
<SPSSODescriptor AuthnRequestsSigned="true" WantAssertionsSigned="true" protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```

Als u de validatie van beweringen uitschakelt, kunt u ook de handtekening validatie van het antwoord bericht uitschakelen. Stel de **ResponsesSigned** -meta gegevens in op `false` . De ID-provider mag het SAML-respons bericht in dit geval niet ondertekenen, maar zelfs als dat wel het doet, wordt de hand tekening niet door Azure AD B2C gevalideerd.

In het volgende voor beeld worden zowel het bericht als de bevestigings handtekening verwijderd:

```xml
<Metadata>
  ...
  <Item Key="WantsSignedAssertions">false</Item>
  <Item Key="ResponsesSigned">false</Item>
</Metadata>
```

## <a name="require-encrypted-saml-responses"></a>Versleutelde SAML-antwoorden vereisen

Stel de **WantsEncryptedAssertions** -meta gegevens in om te vereisen dat alle binnenkomende verklaringen worden versleuteld. Wanneer versleuteling vereist is, gebruikt de ID-provider een open bare sleutel van een versleutelings certificaat in een Azure AD B2C technisch profiel. Azure AD B2C de reactie bevestiging ontsleutelt met behulp van het persoonlijke deel van het versleutelings certificaat.

Als u de berekenings versleuteling inschakelt, moet u mogelijk ook de validatie van de antwoord handtekening uitschakelen (Zie [ondertekende SAML-antwoorden vereisen](#require-signed-saml-responses)voor meer informatie.

Wanneer de **WantsEncryptedAssertions** -meta gegevens zijn ingesteld op `true` , bevat de meta gegevens van het technische profiel van Azure AD B2C de sectie **versleuteling** . De ID-provider leest de meta gegevens en versleutelt de SAML-reactie bevestiging met de open bare sleutel die is opgenomen in de meta gegevens van het technische profiel van Azure AD B2C.

In het volgende voor beeld ziet u de sectie Key descriptor van de SAML-meta gegevens die worden gebruikt voor versleuteling:

```xml
<SPSSODescriptor AuthnRequestsSigned="true"  protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
  <KeyDescriptor use="encryption">
    <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
      <X509Data>
        <X509Certificate>valid certificate</X509Certificate>
      </X509Data>
    </KeyInfo>
  </KeyDescriptor>
  ...
</SPSSODescriptor>
```

De bevestiging van een SAML-reactie versleutelen:

1. [Maak een beleids sleutel](identity-provider-generic-saml.md#create-a-policy-key) met een unieke id. Bijvoorbeeld `B2C_1A_SAMLEncryptionCert`.
2. In uw **CryptographicKeys** -verzameling voor technische SAML-profielen. Stel de **StorageReferenceId** in op de naam van de beleids sleutel die u in de eerste stap hebt gemaakt. De `SamlAssertionDecryption` id geeft het gebruik van de cryptografische sleutel aan voor het versleutelen en ontsleutelen van de bevestiging van het SAML-antwoord.

    ```xml
    <CryptographicKeys>
            ...
      <Key Id="SamlAssertionDecryption" StorageReferenceId="B2C_1A_SAMLEncryptionCert"/>
    </CryptographicKeys>
    ```

3. Stel de **WantsEncryptedAssertions** van de technische profielen in op `true` .
    
    ```xml
    <Metadata>
      ...
      <Item Key="WantsEncryptedAssertions">true</Item>
    </Metadata>
    ```

4. Werk uw ID-provider bij met de nieuwe meta gegevens voor het technische profiel van Azure AD B2C. U moet de sleutel **descriptor** zien met de eigenschap **use** ingesteld op `encryption` die de open bare sleutel van uw certificaat bevat.

## <a name="enable-use-of-context-claims"></a>Gebruik van context claims inschakelen

In de claim verzameling invoer en uitvoer kunt u claims toevoegen die niet worden geretourneerd door de ID-provider zolang u het kenmerk instelt `DefaultValue` . U kunt ook [context claims](claim-resolver-overview.md) gebruiken die in het technische profiel moeten worden opgenomen. Een context claim gebruiken:

1. Voeg een claim type toe aan het [ClaimsSchema](claimsschema.md) -element binnen [BuildingBlocks](buildingblocks.md).
2. Een uitvoer claim toevoegen aan de invoer-of uitvoer verzameling. In het volgende voor beeld wordt met de eerste claim de waarde van de ID-provider ingesteld. De tweede claim gebruikt de gebruikers-IP-adres [context claims](claim-resolver-overview.md).
    
    ```xml
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" AlwaysUseDefaultValue="true" />
      <OutputClaim ClaimTypeReferenceId="IpAddress" DefaultValue="{Context:IPAddress}" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    ```

## <a name="disable-single-logout"></a>Eenmalige afmelding uitschakelen

Bij een aanvraag voor een toepassings aanmelding Azure AD B2C probeert zich af te melden bij uw SAML-ID-provider. Zie [Azure AD B2C Session-afmelding](session-behavior.md#sign-out)voor meer informatie.  Als u het eenmalige afmeldings gedrag wilt uitschakelen, stelt u de **SingleLogoutEnabled** -meta gegevens in op `false` .

```xml
<Metadata>
  ...
  <Item Key="SingleLogoutEnabled">false</Item>
</Metadata>
```

## <a name="debug-saml-protocol"></a>Debug-protocol voor fout opsporing

U kunt een browser uitbreiding voor het SAML-protocol, zoals [SAML devtools-extensie](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) voor Chrome, [SAML-tracering](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) voor Firefox of [Edge-of IE-ontwikkel hulpprogramma's](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957), gebruiken voor het configureren en opsporen van fouten in Federatie met een SAML-ID-provider.

Met deze hulpprogram ma's kunt u de integratie van Azure AD B2C en uw SAML-ID-provider controleren. Bijvoorbeeld:

* Controleer of de SAML-aanvraag een hand tekening bevat en bepaal welk algoritme wordt gebruikt om de autorisatie aanvraag te ondertekenen.
* De claims (beweringen) onder de `AttributeStatement` sectie ophalen.
* Controleer of er een fout bericht wordt weer gegeven door de ID-provider.
* Controleer of de bevestigings sectie is versleuteld.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het vaststellen van problemen met uw aangepaste beleids regels met behulp van [Application Insights](troubleshoot-with-application-insights.md). 

::: zone-end
