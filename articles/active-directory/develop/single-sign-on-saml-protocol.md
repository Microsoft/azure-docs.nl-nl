---
title: Azure-SAML-protocol voor eenmalige aanmelding
titleSuffix: Microsoft identity platform
description: In dit artikel wordt het SAML-protocol single Sign-On (SSO) beschreven in Azure Active Directory
services: active-directory
documentationcenter: .net
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 40bf202e0f14f18d817e4e918f8372ba3c0a4ad8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950666"
---
# <a name="single-sign-on-saml-protocol"></a>SAML-protocol voor eenmalige aanmelding

In dit artikel worden de SAML 2,0-verificatie aanvragen en antwoorden beschreven die Azure Active Directory (Azure AD) ondersteunt voor single Sign-On (SSO).

In het onderstaande protocol diagram wordt de volg orde van eenmalige aanmelding beschreven. De Cloud service (de service provider) gebruikt een binding voor HTTP-omleiding om een- `AuthnRequest` element (verificatie aanvraag) door te geven aan Azure AD (de ID-provider). Azure AD maakt vervolgens gebruik van een HTTP Post-binding om een- `Response` element te posten naar de Cloud service.

![SSO-werk stroom (single Sign-On)](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

> [!NOTE]
> In dit artikel wordt beschreven hoe u SAML gebruikt voor eenmalige aanmelding. Zie [eenmalige aanmelding bij toepassingen in azure Active Directory](../manage-apps/what-is-single-sign-on.md)voor meer informatie over andere manieren voor het afhandelen van eenmalige aanmelding (bijvoorbeeld met behulp van OpenID Connect Connect of geïntegreerde Windows-verificatie).

## <a name="authnrequest"></a>AuthnRequest

Als u een gebruikers verificatie wilt aanvragen, verzendt Cloud Services een- `AuthnRequest` element naar Azure AD. Een voor beeld van een SAML 2,0 `AuthnRequest` kan er als volgt uitzien:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parameter | Type | Beschrijving |
| --- | --- | --- |
| Id | Vereist | Azure AD gebruikt dit kenmerk om het `InResponseTo` kenmerk van het geretourneerde antwoord in te vullen. De ID mag niet beginnen met een getal, dus een algemene strategie is om een teken reeks als ' id ' te laten voorafgaan door naar de teken reeks representatie van een GUID. `id6c1c178c166d486687be4aaf5e482730`Is bijvoorbeeld een geldige id. |
| Versie | Vereist | Deze para meter moet worden ingesteld op **2,0**. |
| IssueInstant | Vereist | Dit is een datum/tijd-teken reeks met een UTC-waarde en een [notatie voor retour afronding ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). In azure AD wordt een DateTime-waarde van dit type verwacht, maar de waarde wordt niet geëvalueerd of gebruikt. |
| AssertionConsumerServiceURL | Optioneel | Als deze para meter wordt opgegeven, moet deze overeenkomen met de `RedirectUri` Cloud service in azure AD. |
| ForceAuthn | Optioneel | Dit is een Booleaanse waarde. Als deze eigenschap waar is, betekent dit dat de gebruiker wordt afgedwongen om zich opnieuw te verifiëren, zelfs als ze een geldige sessie met Azure AD hebben. |
| IsPassive | Optioneel | Dit is een Booleaanse waarde die aangeeft of Azure AD de gebruiker zonder tussen komst van de gebruiker op de achtergrond moet verifiëren, met behulp van de sessie cookie als er een bestaat. Als dit het geval is, probeert Azure AD de gebruiker te verifiëren met behulp van de sessie cookie. |

Alle andere `AuthnRequest` kenmerken, zoals toestemming, doel, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex en ProviderName, worden **genegeerd**.

Azure AD negeert het `Conditions` element ook in `AuthnRequest` .

### <a name="issuer"></a>Verlener

Het `Issuer` element in een `AuthnRequest` moet exact overeenkomen met een van de **ServicePrincipalNames** in de Cloud service in azure AD. Dit is normaal gesp roken ingesteld op de **App-ID-URI** die is opgegeven tijdens de registratie van de toepassing.

Een SAML-fragment met het `Issuer` element ziet eruit als in het volgende voor beeld:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Dit element vraagt een bepaalde naam-ID-indeling aan in het antwoord en is optioneel in `AuthnRequest` elementen die naar Azure AD worden verzonden.

Een `NameIdPolicy` element ziet eruit als in het volgende voor beeld:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Als `NameIDPolicy` u een optioneel kenmerk opgeeft, kunt u het toevoegen `Format` . Het `Format` kenmerk kan slechts een van de volgende waarden hebben. een andere waarde resulteert in een fout.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: In Azure Active Directory wordt de NameID-claim als een Pairwise-id uitgegeven.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: In Azure Active Directory wordt de NameID-claim in de indeling van het e-mail adres beschreven.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Met deze waarde kan Azure Active Directory de indeling van de claim selecteren. Azure Active Directory wordt het NameID als een Pairwise-id uitgegeven.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory geeft de NameID-claim als een wille keurig gegenereerde waarde die uniek is voor de huidige SSO-bewerking. Dit betekent dat de waarde tijdelijk is en niet kan worden gebruikt om de verifiërende gebruiker te identificeren.

Als `SPNameQualifier` is opgegeven, neemt Azure AD hetzelfde `SPNameQualifier` op in het antwoord.

Het kenmerk wordt genegeerd door Azure AD `AllowCreate` .

### <a name="requestauthncontext"></a>RequestAuthnContext
Met het `RequestedAuthnContext` element worden de gewenste verificatie methoden opgegeven. Het is optioneel in `AuthnRequest` elementen die naar Azure AD worden verzonden. Azure AD ondersteunt `AuthnContextClassRef` waarden zoals `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` .

### <a name="scoping"></a>Bereik
Het `Scoping` element, dat een lijst met id-providers bevat, is optioneel in `AuthnRequest` elementen die naar Azure AD worden verzonden.

Als deze niet wordt ondersteund, neemt u het `ProxyCount` kenmerk `IDPListOption` of element niet op `RequesterID` .

### <a name="signature"></a>Handtekening
Een `Signature` element in `AuthnRequest` elementen is optioneel. Azure AD valideert ondertekende verificatie aanvragen niet als er een hand tekening aanwezig is. Er wordt een aanvrager verificatie gegeven voor door alleen de geregistreerde service-Url's van de consument te reageren.

### <a name="subject"></a>Onderwerp
Neem geen `Subject` element op. Azure AD biedt geen ondersteuning voor het opgeven van een onderwerp voor een aanvraag en er wordt een fout geretourneerd als er een wordt opgegeven.

## <a name="response"></a>Antwoord
Wanneer een aangevraagde aanmelding is voltooid, boekt Azure AD een reactie op de Cloud service. Een reactie op een geslaagde aanmeldings poging ziet eruit als in het volgende voor beeld:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Antwoord

Het `Response` element bevat het resultaat van de autorisatie aanvraag. Azure AD stelt de `ID` en `Version` `IssueInstant` waarden in het- `Response` element in. Ook worden de volgende kenmerken ingesteld:

* `Destination`: Wanneer de aanmelding is voltooid, wordt deze ingesteld op de `RedirectUri` service provider (Cloud service).
* `InResponseTo`: Dit is ingesteld op het `ID` kenmerk van het `AuthnRequest` element dat het antwoord heeft gestart.

### <a name="issuer"></a>Verlener

Azure AD stelt het `Issuer` element in op de `https://sts.windows.net/<TenantIDGUID>/` locatie \<TenantIDGUID> van de Tenant-id van de Azure AD-Tenant.

Een antwoord met het element Issuer kan er bijvoorbeeld als volgt uitzien:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

Het `Status` element geeft aan of het aanmelden is geslaagd of mislukt. Het bevat het `StatusCode` element, dat een code bevat of een set geneste codes die de status van de aanvraag vertegenwoordigt. Het bevat ook het `StatusMessage` element, dat aangepaste fout berichten bevat die tijdens het aanmeldings proces worden gegenereerd.

<!-- TODO: Add an authentication protocol error reference -->

Het volgende voor beeld is een SAML-reactie op een mislukte aanmeldings poging.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Assertion

Naast de `ID` `IssueInstant` en `Version` , stelt Azure AD de volgende elementen in het- `Assertion` element van het antwoord in.

#### <a name="issuer"></a>Verlener

Dit is ingesteld op `https://sts.windows.net/<TenantIDGUID>/` waar \<TenantIDGUID> de Tenant-id is van de Azure AD-Tenant.

```
<Issuer>https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Handtekening

Azure AD ondertekent de bevestiging als reactie op een geslaagde aanmelding. Het `Signature` element bevat een digitale hand tekening die de Cloud service kan gebruiken om de bron te verifiëren om de integriteit van de bevestiging te controleren.

Voor het genereren van deze digitale hand tekening maakt Azure AD gebruik van de handtekening sleutel in het `IDPSSODescriptor` element van het meta gegevens document.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Onderwerp

Hiermee geeft u de principal op die het onderwerp is van de instructies in de verklaring. Het bevat een `NameID` -element dat de geverifieerde gebruiker vertegenwoordigt. De `NameID` waarde is een doel-id die alleen wordt omgeleid naar de service provider die de doel groep is voor het token. Het is permanent. deze kan worden ingetrokken, maar wordt nooit opnieuw toegewezen. Het is ook ondoorzichtig, in dat betekent dat het geen informatie over de gebruiker onthult en niet kan worden gebruikt als een id voor kenmerk query's.

Het `Method` kenmerk van het `SubjectConfirmation` element is altijd ingesteld op `urn:oasis:names:tc:SAML:2.0:cm:bearer` .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Voorwaarden

Dit element bevat voor waarden die het acceptabele gebruik van SAML-bevestigingen definiëren.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

De `NotBefore` `NotOnOrAfter` Attributes en geven het interval op waarmee de bevestiging geldig is.

* De waarde van het `NotBefore` kenmerk is gelijk aan of enigszins (kleiner dan een seconde) later dan de waarde van `IssueInstant` het kenmerk van het `Assertion` element. Azure AD houdt geen rekening met het tijds verschil tussen zichzelf en de Cloud service (service provider) en voegt deze tijd niet toe aan een buffer.
* De waarde van het `NotOnOrAfter` kenmerk is 70 minuten later dan de waarde van het `NotBefore` kenmerk.

#### <a name="audience"></a>Doelgroep

Dit bevat een URI die een beoogde doel groep identificeert. Azure AD stelt de waarde van dit element in op de waarde van het `Issuer` element van de `AuthnRequest` die de aanmelding heeft gestart. Als u de `Audience` waarde wilt evalueren, gebruikt u de waarde van de `App ID URI` die is opgegeven tijdens de registratie van de toepassing.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Net als de `Issuer` waarde `Audience` moet de waarde exact overeenkomen met een van de service-principal-namen die de Cloud service in azure AD vertegenwoordigt. Als de waarde van het element echter `Issuer` geen URI-waarde is, is de `Audience` waarde in het antwoord de `Issuer` waarde die wordt voorafgegaan door `spn:` .

#### <a name="attributestatement"></a>AttributeStatement

Dit bevat claims over het onderwerp of de gebruiker. Het volgende fragment bevat een voorbeeld `AttributeStatement` element. Het weglatings teken geeft aan dat het element meerdere kenmerken en kenmerk waarden kan bevatten.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Claim naam** : de waarde van het `Name` kenmerk ( `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ) is de User Principal name van de geverifieerde gebruiker, zoals `testuser@managedtenant.com` .
* **Claim van ObjectIdentifier** : de waarde van het `ObjectIdentifier` kenmerk ( `http://schemas.microsoft.com/identity/claims/objectidentifier` ) is het `ObjectId` van het Directory-object dat de geverifieerde gebruiker in azure AD vertegenwoordigt. `ObjectId` is een onveranderbare, wereld wijd unieke en hergebruikte veilige id van de geverifieerde gebruiker.

#### <a name="authnstatement"></a>AuthnStatement

Dit element beweringt dat het onderwerp van de verklaring op een bepaald moment is geverifieerd met een bepaalde methode.

* `AuthnInstant`Met het kenmerk wordt het tijdstip aangegeven waarop de gebruiker is geverifieerd met Azure AD.
* Het `AuthnContext` element geeft de verificatie context op die wordt gebruikt om de gebruiker te verifiëren.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
