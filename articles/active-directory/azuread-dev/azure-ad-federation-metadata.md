---
title: Azure AD Federation-meta gegevens | Microsoft Docs
description: In dit artikel wordt het document met federatieve meta gegevens beschreven dat Azure Active Directory publiceert voor services die Azure Active Directory-tokens accepteren.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 7f3bd8851fe723461c618499e539c987d79c0d68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650138"
---
# <a name="federation-metadata"></a>Federatieve metagegevens

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Azure Active Directory (Azure AD) publiceert een document met federatieve meta gegevens voor services die zijn geconfigureerd om de beveiligings tokens te accepteren die door Azure AD worden uitgegeven. De indeling van het federatieve meta gegevens document wordt beschreven in de [webservices-Federatie Language (WS-Federation) versie 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html), waarmee [de meta gegevens voor de Oasis Security Assertion Markup Language (SAML) v 2.0](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)worden uitgebreid.

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Tenant-en Tenant-onafhankelijke eind punten van meta gegevens
Azure AD publiceert Tenant-specifieke en Tenant-onafhankelijke eind punten.

Tenant-specifieke eind punten zijn ontworpen voor een bepaalde Tenant. De Tenant-specifieke federatieve meta gegevens bevatten informatie over de Tenant, met inbegrip van Tenant-specifieke uitgevers en eindpunt informatie. Toepassingen die de toegang tot één Tenant beperken, gebruiken Tenant-specifieke eind punten.

Tenant-onafhankelijke eind punten bieden informatie die gemeen schappelijk is voor alle Azure AD-tenants. Deze informatie is van toepassing op tenants die worden gehost op *login.microsoftonline.com* en worden gedeeld via tenants. Tenant-onafhankelijke eind punten worden aanbevolen voor toepassingen met meerdere tenants, omdat ze niet zijn gekoppeld aan een bepaalde Tenant.

## <a name="federation-metadata-endpoints"></a>Federatieve meta gegevens-eind punten
Azure AD publiceert federatieve meta gegevens op `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml` .

Voor **Tenant-specifieke eind punten** kan dit `TenantDomainName` een van de volgende typen zijn:

* Een geregistreerde domein naam van een Azure AD-Tenant, zoals: `contoso.onmicrosoft.com` .
* De onveranderbare Tenant-ID van het domein, zoals `72f988bf-86f1-41af-91ab-2d7cd011db45` .

De is voor **Tenant-onafhankelijke eind punten** `TenantDomainName` `common` . In dit document worden alleen de federatieve meta gegevenselementen vermeld die gemeen schappelijk zijn voor alle Azure AD-tenants die worden gehost op login.microsoftonline.com.

Een Tenant-specifiek eind punt kan bijvoorbeeld zijn `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml` . Het Tenant-onafhankelijke eind punt is [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml) . U kunt het document met federatieve meta gegevens weer geven door deze URL in een browser te typen.

## <a name="contents-of-federation-metadata"></a>Inhoud van federatieve meta gegevens
De volgende sectie bevat informatie die nodig is voor services die gebruikmaken van de tokens die zijn uitgegeven door Azure AD.

### <a name="entity-id"></a>Entiteits-id
Het `EntityDescriptor` element bevat een `EntityID` kenmerk. De waarde van het `EntityID` kenmerk vertegenwoordigt de uitgever, dat wil zeggen, de Security Token Service (STS) die het token heeft uitgegeven. Het is belang rijk dat u de uitgever valideert wanneer u een token ontvangt.

In de volgende meta gegevens ziet u een voor beeld van een Tenant-specifiek `EntityDescriptor` element met een- `EntityID` element.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
U kunt de Tenant-ID in het Tenant-onafhankelijke eind punt vervangen door de Tenant-ID om een Tenant-specifieke waarde te maken `EntityID` . De resulterende waarde is hetzelfde als de uitgever van het token. Met de strategie kan een multi tenant-toepassing de verlener voor een bepaalde Tenant valideren.

In de volgende meta gegevens ziet u een voor beeld van een Tenant-onafhankelijk `EntityID` element. Houd er rekening mee dat het `{tenant}` een letterlijke waarde is, geen tijdelijke aanduiding.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Token handtekening certificaten
Wanneer een service een token ontvangt dat is uitgegeven door een Azure AD-Tenant, moet de hand tekening van het token worden gevalideerd met een ondertekeningssleutel die is gepubliceerd in het document met federatieve meta gegevens. De federatieve meta gegevens bevatten het open bare deel van de certificaten die door de tenants worden gebruikt voor het ondertekenen van tokens. De onbewerkte certificaat bytes worden weer gegeven in het- `KeyDescriptor` element. Het token handtekening certificaat is alleen geldig voor ondertekening wanneer de waarde van het `use` kenmerk is `signing` .

Een document met federatieve meta gegevens dat door Azure AD wordt gepubliceerd, kan meerdere ondertekeningssleutel hebben, bijvoorbeeld wanneer Azure AD wordt voor bereid om het handtekening certificaat bij te werken. Wanneer een document met federatieve meta gegevens meer dan één certificaat bevat, moet een service die de tokens valideert alle certificaten in het document ondersteunen.

In de volgende meta gegevens ziet u een voorbeeld `KeyDescriptor` element met een handtekening sleutel.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

Het `KeyDescriptor` element wordt weer gegeven op twee plaatsen in het document met federatieve meta gegevens, in de sectie WS-Federation-specific en de sectie SAML-specific. De certificaten die in beide secties worden gepubliceerd, zijn hetzelfde.

In de sectie WS-Federation-specific worden de certificaten van een- `RoleDescriptor` element met het type in een WS-Federation lezer van de meta gegevens gelezen `SecurityTokenServiceType` .

In de volgende meta gegevens wordt een voorbeeld element weer gegeven `RoleDescriptor` .

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType" protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

In de sectie SAML-specific worden de certificaten van een-element gelezen met een WS-Federation-lezer `IDPSSODescriptor` .

In de volgende meta gegevens wordt een voorbeeld element weer gegeven `IDPSSODescriptor` .

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Er zijn geen verschillen in de indeling van Tenant-specifieke en Tenant-onafhankelijke certificaten.

### <a name="ws-federation-endpoint-url"></a>URL van WS-Federation eind punt
De federatieve meta gegevens bevatten de URL die door Azure AD wordt gebruikt voor eenmalige aanmelding en eenmalige afmelding in WS-Federation protocol. Dit eind punt wordt weer gegeven in het- `PassiveRequestorEndpoint` element.

In de volgende meta gegevens ziet u een voorbeeld `PassiveRequestorEndpoint` element voor een Tenant-specifiek eind punt.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Voor het Tenant-onafhankelijke eind punt wordt de WS-Federation URL weer gegeven in het WS-Federation-eind punt, zoals wordt weer gegeven in het volgende voor beeld.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>URL voor SAML-protocol eindpunt
De federatieve meta gegevens bevatten de URL die door Azure AD wordt gebruikt voor eenmalige aanmelding en eenmalige afmelding in het SAML 2,0-protocol. Deze eind punten worden weer gegeven in het- `IDPSSODescriptor` element.

De Url's voor aanmelden en afmelden worden weer gegeven in de `SingleSignOnService` `SingleLogoutService` elementen en.

In de volgende meta gegevens ziet u een `PassiveResistorEndpoint` voor beeld van een Tenant-specifiek eind punt.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Net als de eind punten voor de gemeen schappelijke SAML 2,0-protocol eindpunten worden gepubliceerd in de Tenant-onafhankelijke federatieve meta gegevens, zoals wordt weer gegeven in het volgende voor beeld.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
