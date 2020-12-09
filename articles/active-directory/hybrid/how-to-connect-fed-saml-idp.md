---
title: 'Azure AD Connect: een SAML 2,0-ID-provider gebruiken voor eenmalige aanmelding bij Azure'
description: In dit document wordt beschreven hoe u een SAML 2,0-compatibele IDP gebruikt voor eenmalige aanmelding.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20088ccc79abe978eb5b3f1c98e958f6c26bcb86
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861796"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Een SAML 2.0-id-provider (IdP) gebruiken voor eenmalige aanmelding

Dit document bevat informatie over het gebruik van een SAML 2,0-compatibele id-provider die volSP-Lite doet aan de voor keur voor de gewenste beveiligings token service (STS) of ID-provider. Dit scenario is handig als u al een gebruikers lijst en een wacht woord hebt opgeslagen op locatie die toegankelijk zijn via SAML 2,0. Deze bestaande gebruikers lijst kan worden gebruikt voor aanmelding bij Microsoft 365 en andere met Azure AD beveiligde bronnen. Het SAML 2,0-SP-Lite profiel is gebaseerd op de algemeen gebruikte Security Assertion Markup Language (SAML) federatieve identiteits norm om een aanmeldings-en kenmerk uitwisselings raamwerk te bieden.

>[!NOTE]
>Zie de [lijst met compatibiliteits problemen met Azure AD Federation](how-to-connect-fed-compatibility.md) voor een lijst met id van derden die zijn getest voor gebruik met Azure AD.

Micro soft biedt ondersteuning voor deze aanmeldings ervaring als de integratie van een micro soft-Cloud service, zoals Microsoft 365, met uw op de juiste manier geconfigureerde SAML 2,0-IdP. SAML 2,0-id-providers zijn producten van derden en daarom biedt micro soft geen ondersteuning voor de aanbevolen procedures voor implementatie, configuratie en probleem oplossing. Als de app is geconfigureerd, kan de integratie met de SAML 2,0-ID-provider worden getest op de juiste configuratie met behulp van het hulp programma micro soft Connectivity Analyzer. dit wordt hieronder nader beschreven. Vraag de organisatie die deze heeft geleverd voor meer informatie over uw SAML 2,0-SP-Lite op profiel gebaseerde ID-provider.

> [!IMPORTANT]
> Er is slechts een beperkt aantal clients beschikbaar in dit aanmeldings scenario met SAML 2,0-id-providers, waaronder:
> 
> - Webgebaseerde clients zoals Outlook Web Access en share point online
> - E-mail-uitgebreide clients die gebruikmaken van basis verificatie en een ondersteunde methode voor toegang tot Exchange, zoals IMAP, POP, Active Sync, MAPI, enzovoort. (het eind punt van het verbeterde client protocol moet worden geïmplementeerd), waaronder:
>     - Micro soft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (diverse iOS-versies)
>     - Verschillende Google Android-apparaten
>     - Windows Phone 7, Windows Phone 7,8 en Windows Phone 8,0
>     - Windows 8 e-mailclient en Windows 8,1-mailclient
>     - Windows 10-e-mailclient

Alle andere clients zijn niet beschikbaar in dit aanmeldings scenario met uw SAML 2,0-ID-provider. Bijvoorbeeld, de Lync 2010-bureaubladclient kan zich niet aanmelden bij de service met uw SAML 2,0-ID-provider die is geconfigureerd voor eenmalige aanmelding.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Vereisten voor Azure AD SAML 2,0-protocollen
Dit document bevat gedetailleerde vereisten voor het protocol en de bericht indeling die uw SAML 2,0-ID-provider moet implementeren om te communiceren met Azure AD om aanmelden op een of meer micro soft-Cloud Services (zoals Microsoft 365) in te scha kelen. De SAML 2,0 Relying Party (SP-STS) voor een micro soft-Cloud service die in dit scenario wordt gebruikt, is Azure AD.

Het is raadzaam om ervoor te zorgen dat uw SAML 2,0-ID-provider uitvoer berichten net zo vergelijkbaar zijn als de gegeven voorbeeld traceringen. Gebruik waar mogelijk specifieke kenmerk waarden van de opgegeven Azure AD-meta gegevens. Wanneer u tevreden bent met uw uitvoer berichten, kunt u met de micro soft Connectivity Analyzer testen zoals hieronder wordt beschreven.

De meta gegevens van Azure AD kunnen worden gedownload via deze URL: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml) .
Voor klanten in China die gebruikmaken van het China-specifiek exemplaar van Microsoft 365, moet het volgende Federatie-eind punt worden gebruikt: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml) .

## <a name="saml-protocol-requirements"></a>Vereisten voor het SAML-Protocol
In deze sectie wordt beschreven hoe de aanvraag-en antwoord bericht paren samen worden geplaatst, zodat u uw berichten op de juiste wijze kunt Format teren.

Azure AD kan worden geconfigureerd om te werken met id-providers die gebruikmaken van het SAML 2,0 SP Lite-profiel met enkele specifieke vereisten zoals hieronder wordt weer gegeven. Met behulp van het voor beeld van SAML-aanvraag-en-antwoord berichten en automatische en hand matige tests kunt u samen werken met Azure AD.

## <a name="signature-block-requirements"></a>Vereisten voor handtekening blok
In het SAML-antwoord bericht bevat het handtekening knooppunt informatie over de digitale hand tekening voor het bericht zelf. Het handtekening blok heeft de volgende vereisten:

1. Het bevestigings knooppunt zelf moet zijn ondertekend
2.  Het algoritme RSA-SHA1 moet worden gebruikt als de DigestMethod. Andere algoritmen voor digitale hand tekeningen worden niet geaccepteerd.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  U kunt ook het XML-document ondertekenen. 
4.  Het transformatie algoritme moet overeenkomen met de waarden in het volgende voor beeld:    `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Het SignatureMethod-algoritme moet overeenkomen met het volgende voor beeld:   `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Ondersteunde bindingen
Bindingen zijn de Trans Port-gerelateerde communicatie parameters die vereist zijn. De volgende vereisten zijn van toepassing op de bindingen

1. HTTPS is het vereiste Trans Port.
2.  Voor Azure AD is HTTP POST vereist voor het verzenden van tokens tijdens het aanmelden.
3.  Azure AD gebruikt HTTP POST voor de verificatie aanvraag bij de ID-provider en leidt het afmeldings bericht naar de ID-provider.

## <a name="required-attributes"></a>Vereiste kenmerken
In deze tabel worden de vereisten voor specifieke kenmerken in het SAML 2,0-bericht weer gegeven.
 
|Kenmerk|Beschrijving|
| ----- | ----- |
|Meid|De waarde van deze bevestiging moet gelijk zijn aan de ImmutableID van de Azure AD-gebruiker. Het kan Maxi maal 64 alfanumerieke tekens bevatten. Niet-HTML-veilige tekens moeten worden gecodeerd, bijvoorbeeld een "+"-teken als ". 2B".|
|IDPEmail|De User Principal Name (UPN) wordt weer gegeven in het SAML-antwoord als een element met de naam IDPEmail de UserPrincipalName (UPN) van de gebruiker in azure AD/Microsoft 365. De UPN heeft de indeling van het e-mail adres. UPN-waarde in Windows Microsoft 365 (Azure Active Directory).|
|Verlener|Moet een URI zijn van de ID-provider. Gebruik de certificaat verlener niet uit de voorbeeld berichten. Als u meerdere domeinen op het hoogste niveau in uw Azure AD-tenants hebt, moet de uitgever overeenkomen met de opgegeven URI-instelling die per domein is geconfigureerd.|

>[!IMPORTANT]
>Azure AD biedt momenteel ondersteuning voor de volgende NameID-indelings-URI voor SAML 2.0: urn: Oasis: names: TC: SAML: 2.0: NameID-indeling: persistent.

## <a name="sample-saml-request-and-response-messages"></a>Voor beeld van SAML-aanvraag en-antwoord berichten
Er wordt een aanvraag-en antwoord bericht paar weer gegeven voor de uitwisseling van aanmeldings berichten.
Hier volgt een voor beeld van een aanvraag bericht dat vanuit Azure AD wordt verzonden naar een SAML 2,0-ID-provider. De voor beeld-ID-provider van SAML 2,0 is Active Directory Federation Services (AD FS) geconfigureerd voor het gebruik van het SAML-P-protocol. Het testen van de interoperabiliteit is ook voltooid met andere SAML 2,0-id-providers.

```xml
    <samlp:AuthnRequest 
        xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
        xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" 
        ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" 
        IssueInstant="2014-01-30T16:18:35Z" 
        Version="2.0" 
        AssertionConsumerServiceIndex="0" >
            <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
            <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>
```

Hier volgt een voor beeld van een antwoord bericht dat wordt verzonden vanuit de voor beeld-ID-provider van SAML 2,0-compatibel naar Azure AD/Microsoft 365.

```xml
    <samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>
```

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Uw ID-provider voor SAML 2,0 compliant configureren
Deze sectie bevat richt lijnen voor het configureren van uw SAML 2,0-ID-provider voor het communiceren met Azure AD om eenmalige aanmelding toegang te bieden tot een of meer micro soft-Cloud Services (zoals Microsoft 365) met behulp van het SAML 2,0-protocol. De SAML 2,0-Relying Party voor een micro soft-Cloud service die in dit scenario wordt gebruikt, is Azure AD.

## <a name="add-azure-ad-metadata"></a>Azure AD-meta gegevens toevoegen
Uw SAML 2,0-ID-provider moet voldoen aan informatie over de Azure AD-Relying Party. Azure AD publiceert meta gegevens op https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml .

Het is raadzaam om altijd de nieuwste Azure AD-meta gegevens te importeren bij het configureren van uw SAML 2,0-ID-provider.

>[!NOTE]
>Azure AD leest geen meta gegevens van de ID-provider.

## <a name="add-azure-ad-as-a-relying-party"></a>Azure AD als Relying Party toevoegen
U moet de communicatie tussen uw SAML 2,0-ID-provider en Azure AD inschakelen. Deze configuratie is afhankelijk van uw specifieke ID-provider en u dient hiervoor documentatie te raadplegen. Normaal gesp roken stelt u de Relying Party-ID in op dezelfde als de entityID van de meta gegevens van Azure AD.

>[!NOTE]
>Controleer of de klok op uw SAML 2,0-ID-provider server is gesynchroniseerd met een nauw keurige tijds bron. Een onnauwkeurige klok tijd kan leiden tot het mislukken van federatieve aanmeldingen.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Windows Power Shell installeren voor aanmelding met SAML 2,0-ID-provider
Nadat u uw SAML 2,0-ID-provider voor gebruik met Azure AD-aanmelding hebt geconfigureerd, is de volgende stap het downloaden en installeren van de Azure Active Directory-module voor Windows Power shell. Na de installatie kunt u deze cmdlets gebruiken om uw Azure AD-domeinen als federatieve domeinen te configureren.

De Azure Active Directory-module voor Windows Power shell is een down load voor het beheren van uw organisatie gegevens in azure AD. Met deze module wordt een set cmdlets geïnstalleerd in Windows Power shell. u voert deze cmdlets uit om eenmalige aanmelding in te stellen voor Azure AD en vervolgens naar alle Cloud Services waarop u bent geabonneerd. Zie [/previous-versions/Azure/jj151815 (v = Azure. 100)](/previous-versions/azure/jj151815(v=azure.100)) voor instructies voor het downloaden en installeren van de cmdlets.

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Een vertrouwens relatie instellen tussen uw SAML-ID-provider en Azure AD
Voordat u een Federatie configureert in een Azure AD-domein, moet er een aangepast domein zijn geconfigureerd. U kunt het standaard domein dat door micro soft wordt verschaft niet dewaarderen. Het standaard domein van micro soft eindigt met ' onmicrosoft.com '.
U voert een reeks cmdlets uit in de Windows Power shell-opdracht regel interface om domeinen voor eenmalige aanmelding toe te voegen of te converteren.

Elk Azure Active Directory domein dat u wilt dewaarderen met uw SAML 2,0-ID-provider moet worden toegevoegd als een domein met eenmalige aanmelding of worden geconverteerd naar een domein met eenmalige aanmelding vanuit een standaard domein. Door een domein toe te voegen of te converteren, wordt een vertrouwens relatie tussen uw SAML 2,0-ID-provider en Azure AD ingesteld.

De volgende procedure helpt u bij het converteren van een bestaand standaard domein naar een federatief domein met behulp van SAML 2,0 SP-Lite. 

>[!NOTE]
>Uw domein kan een storing ondervinden die van invloed is op gebruikers tot twee uur nadat u deze stap hebt uitgevoerd.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Een domein configureren in uw Azure AD-Directory voor Federatie


1. Maak verbinding met uw Azure AD-Directory als een Tenant beheerder:

    ```powershell
    Connect-MsolService
    ```
    
2. Configureer uw gewenste Microsoft 365 domein om Federatie te gebruiken met SAML 2,0:

    ```powershell
    $dom = "contoso.com" 
    $BrandName - "Sample SAML 2.0 IDP" 
    $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" 
    $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" 
    $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" 
    $MyURI = "urn:uri:MySamlp2IDP" 
    $MySigningCert = "MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" 
    $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" 
    $Protocol = "SAMLP" 
    Set-MsolDomainAuthentication `
        -DomainName $dom `
        -FederationBrandName $BrandName `
        -Authentication Federated `
        -PassiveLogOnUri $LogOnUrl `
        -ActiveLogOnUri $ecpUrl `
        -SigningCertificate $MySigningCert `
        -IssuerUri $MyURI `
        -LogOffUri $LogOffUrl `
        -PreferredAuthenticationProtocol $Protocol
    ``` 

3.  U kunt de door base64 gecodeerde handtekening certificaat reeks verkrijgen uit uw IDP-meta gegevensbestand. Er is een voor beeld van deze locatie gegeven, maar dit kan enigszins verschillen op basis van uw implementatie.

    ```xml
    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
        <KeyDescriptor use="signing">
          <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
             <X509Data>
                 <X509Certificate> MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate>
              </X509Data>
            </KeyInfo>
        </KeyDescriptor>
    </IDPSSODescriptor>
    ``` 

Zie voor meer informatie over ' set-MsolDomainAuthentication ': [/previous-versions/Azure/dn194112 (v = Azure. 100)](/previous-versions/azure/dn194112(v=azure.100)).

>[!NOTE]
>U moet `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` alleen gebruiken als u een ECP-extensie voor uw ID-provider hebt ingesteld. Exchange Online-clients, met uitzonde ring van Outlook Web Application (OWA), zijn afhankelijk van een op POST gebaseerd actief eind punt. Als uw SAML 2,0 STS een actief eind punt implementeert dat vergelijkbaar is met de ECP-implementatie van shibboleth van een actief eind punt, is het mogelijk dat deze uitgebreide clients met de Exchange Online-service communiceren.

Zodra Federatie is geconfigureerd, kunt u terugschakelen naar ' niet-federatief ' (of ' beheerd '), maar deze wijziging duurt Maxi maal twee uur om te volt ooien en er moeten nieuwe, wille keurige wacht woorden worden toegewezen aan elke gebruiker in de Cloud. In sommige gevallen kan het nodig zijn om terug te gaan naar ' beheerd ' om een fout in uw instellingen te herstellen. Zie voor meer informatie over domein conversie: [/previous-versions/Azure/dn194122 (v = Azure. 100)](/previous-versions/azure/dn194122(v=azure.100)).

## <a name="provision-user-principals-to-azure-ad--microsoft-365"></a>Gebruikers principals inrichten voor Azure AD/Microsoft 365
Voordat u uw gebruikers kunt verifiëren voor Microsoft 365, moet u Azure AD inrichten met gebruikers principals die overeenkomen met de bewering in de SAML 2,0-claim. Als deze gebruikers-principals niet vooraf bekend zijn bij Azure AD, kunnen ze niet worden gebruikt voor federatieve aanmelding. Azure AD Connect of Windows Power shell kan worden gebruikt om gebruikers-principals in te richten.

Azure AD Connect kan worden gebruikt om principals in uw domeinen in uw Azure AD-Directory in te richten op de on-premises Active Directory. Zie [uw on-premises Directory's integreren met Azure Active Directory](whatis-hybrid-identity.md)voor meer gedetailleerde informatie.

Windows Power shell kan ook worden gebruikt om nieuwe gebruikers toe te voegen aan Azure AD en wijzigingen te synchroniseren vanuit de on-premises Directory. Als u de Windows Power shell-cmdlets wilt gebruiken, moet u de [Azure Active Directory-modules](/powershell/azure/active-directory/install-adv2)downloaden.

In deze procedure wordt uitgelegd hoe u één gebruiker toevoegt aan Azure AD.


1. Maak verbinding met uw Azure AD-Directory als een Tenant beheerder: Connect-MsolService.
2. Een nieuwe gebruikers-principal maken:

    ```powershell
    New-MsolUser `
      -UserPrincipalName elwoodf1@contoso.com `
      -ImmutableId ABCDEFG1234567890 `
      -DisplayName "Elwood Folk" `
      -FirstName Elwood `
      -LastName Folk `
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" `
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" `
      -UsageLocation "US" 
    ```

Voor meer informatie over de afhandeling van ' New-MsolUser ', [/previous-versions/Azure/dn194096 (v = Azure. 100)](/previous-versions/azure/dn194096(v=azure.100))

>[!NOTE]
>De waarde ' UserPrinciplName ' moet overeenkomen met de waarde die u wilt verzenden voor ' IDPEmail ' in uw SAML 2,0-claim en de waarde ' ImmutableID ' moet overeenkomen met de waarde die is verzonden in de bevestiging ' NameID '.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Eenmalige aanmelding controleren met uw SAML 2,0 IDP
Lees, voordat u eenmalige aanmelding verifieert en beheert (ook identiteits Federatie genoemd), de informatie en voer de stappen uit in de volgende artikelen om eenmalige aanmelding in te stellen met uw SAML 2,0 SP-Lite gebaseerde ID-provider:


1.  U hebt de vereisten voor het Azure AD SAML 2,0-protocol beoordeeld
2.  U hebt uw SAML 2,0-ID-provider geconfigureerd
3.  Windows Power Shell installeren voor eenmalige aanmelding met SAML 2,0-ID-provider
4.  Een vertrouwens relatie instellen tussen de SAML 2,0-ID-provider en Azure AD
5.  Een bekende test gebruiker-principal is ingericht voor Azure Active Directory (Microsoft 365) via Windows Power shell of Azure AD Connect.
6.  Configureer adreslijst synchronisatie met behulp van [Azure AD Connect](whatis-hybrid-identity.md).

Na het instellen van eenmalige aanmelding met uw SAML 2,0 SP-Lite-ID-provider, moet u controleren of deze goed werkt.

>[!NOTE]
>Als u een domein hebt geconverteerd, in plaats van het toe te voegen, kan het tot 24 uur duren om eenmalige aanmelding in te stellen.
Voordat u eenmalige aanmelding controleert, moet u het instellen van Active Directory synchronisatie volt ooien, uw directory's synchroniseren en uw gesynchroniseerde gebruikers activeren.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Gebruik het hulp programma om te controleren of eenmalige aanmelding juist is ingesteld
Als u wilt controleren of eenmalige aanmelding op de juiste wijze is ingesteld, kunt u de volgende procedure uitvoeren om te bevestigen dat u zich kunt aanmelden bij de Cloud service met de referenties van uw bedrijf.

Micro soft heeft een hulp programma meegeleverd dat u kunt gebruiken om uw ID-provider op basis van SAML 2,0 te testen. Voordat u het test programma uitvoert, moet u een Azure AD-Tenant hebben geconfigureerd om te communiceren met uw ID-provider.

>[!NOTE]
>Voor de Connectivity Analyzer is Internet Explorer 10 of hoger vereist.



1. Down load de Connectivity Analyzer vanuit, [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client) .
2.  Klik op nu installeren om te beginnen met het downloaden en installeren van het hulp programma.
3.  Selecteer Ik kan Federatie niet instellen met Office 365, Azure of andere services die gebruikmaken van Azure Active Directory.
4.  Wanneer het hulp programma is gedownload en uitgevoerd, ziet u het venster connectiviteits diagnostiek. Het hulp programma gaat u stapsgewijs door uw Federatie verbinding te testen.
5.  De connectiviteits analyse opent uw SAML 2,0 IDP om u aan te melden, voert u de referenties in voor de gebruikers-principal die u wilt testen: ![ scherm opname van het aanmeld venster voor uw SAML 2,0 IDP.](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  In het venster voor de Federatie test aanmelding voert u een account naam en wacht woord in voor de Azure AD-Tenant die is geconfigureerd voor federatief met uw SAML 2,0-ID-provider. Het hulp programma probeert zich aan te melden met die referenties en gedetailleerde resultaten van tests die tijdens de aanmeldings poging worden uitgevoerd, worden als uitvoer geleverd.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Dit venster toont een mislukt resultaat van testen. Als u op gedetailleerde resultaten bekijken klikt, wordt informatie weer gegeven over de resultaten voor elke test die is uitgevoerd. U kunt de resultaten ook opslaan op schijf om ze te delen.
 
>[!NOTE]
>De connectiviteits analyse test ook actieve Federatie met behulp van de WS *-en ECP/PAOS-protocollen. Als u deze niet gebruikt, kunt u de volgende fout negeren: de actieve aanmeldings stroom testen met het actieve Federatie-eind punt van uw identiteits provider.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Hand matig controleren of eenmalige aanmelding juist is ingesteld
Hand matige verificatie biedt extra stappen die u kunt nemen om ervoor te zorgen dat uw SAML 2,0-ID-provider in veel scenario's goed werkt.
Voer de volgende stappen uit om te controleren of eenmalige aanmelding juist is ingesteld:


1. Meld u op een computer die lid is van een domein aan bij uw Cloud service met dezelfde aanmeldings naam die u gebruikt voor uw bedrijfs referenties.
2.  Klik in het vak wacht woord. Als eenmalige aanmelding is ingesteld, wordt het vak wacht woord grijs weer gegeven en ziet u het volgende bericht: "u bent nu verplicht om u aan te melden bij &lt; uw bedrijf &gt; ."
3.  Klik op de koppeling aanmelden op &lt; uw bedrijf &gt; . Als u zich kunt aanmelden, is de eenmalige aanmelding ingesteld.

## <a name="next-steps"></a>Volgende stappen


- [Active Directory Federation Services beheer en aanpassingen met Azure AD Connect](how-to-connect-fed-management.md)
- [Azure AD-federation compatibiliteitslijst](how-to-connect-fed-compatibility.md)
- [Aangepaste installatie Azure AD Connect](how-to-connect-install-custom.md)
