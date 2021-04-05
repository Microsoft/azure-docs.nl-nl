---
title: Certificaat referenties voor micro soft-identiteits platform
titleSuffix: Microsoft identity platform
description: In dit artikel worden de registratie en het gebruik van certificaat referenties voor toepassings verificatie beschreven.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: cfbcc8523ff1d5858317a3654b58ec7b2d23607a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99582023"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Referenties voor verificatie certificaat voor micro soft-identiteits platform

Met het micro soft Identity-platform kan een toepassing eigen referenties voor verificatie gebruiken overal waar een client geheim kan worden gebruikt, bijvoorbeeld in de OAuth 2,0-  [client referenties toekenning](v2-oauth2-client-creds-grant-flow.md) flow en de OBO-stroom ( [on-of](v2-oauth2-on-behalf-of-flow.md) ---).

Een van de referenties die een toepassing voor verificatie kan gebruiken is een [JSON Web token](./security-tokens.md#json-web-tokens-and-claims) (JWT)-verklaring die is ondertekend met een certificaat waarvan de toepassing eigenaar is.

## <a name="assertion-format"></a>Bevestigings indeling

Als u de bewering wilt berekenen, kunt u een van de vele JWT-bibliotheken gebruiken in de taal van uw keuze- [MSAL `.WithCertificate()` ondersteunt dit met ](msal-net-client-assertions.md). De informatie wordt uitgevoerd door het token in de [header](#header), [claims](#claims-payload)en [hand tekening](#signature).

### <a name="header"></a>Header

| Parameter |  Opmerkingen |
| --- | --- |
| `alg` | Moet **RS256** zijn |
| `typ` | Moet **JWT** zijn |
| `x5t` | De X. 509-certificaat-hash (ook bekend als de SHA-1- *vinger afdruk* van het certificaat) Hex-weer gave gecodeerd als een Base64url teken reeks waarde. Een voor beeld: een 509-certificaat-hash van `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (hex), `x5t` zou de claim zouden zijn `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url). |

### <a name="claims-payload"></a>Claims (Payload)

Claimtype | Waarde | Beschrijving
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | De claim ' AUD ' (doel groep) identificeert de ontvangers waarvoor de JWT is bedoeld (hier Azure AD) Zie [RFC 7519, sectie 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  In dit geval is die ontvanger de aanmeldings server (login.microsoftonline.com).
geldig | 1601519414 | De claim ' exp ' (verval tijd) identificeert de verval tijd op of waarna de JWT niet moet worden geaccepteerd voor verwerking. Zie [RFC 7519, sectie 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Op deze manier kan de bevestiging worden gebruikt tot dat tijdstip, dus kort 5-10 minuten na de `nbf` meeste.  Azure AD plaatst op dit moment geen beperkingen `exp` . 
ISS | ClientID | De claim ' ISS ' (verlener) identificeert de principal die de JWT heeft uitgegeven, in dit geval uw client toepassing.  Gebruik de GUID-toepassings-ID.
jti | (een GUID) | De claim ' JTI ' (JWT-ID) biedt een unieke id voor de JWT. De id-waarde moet worden toegewezen op een manier die ervoor zorgt dat er een Verwaarloos bare kans is dat dezelfde waarde per ongeluk wordt toegewezen aan een ander gegevens object. Als de toepassing meerdere verleners gebruikt, moeten conflicten worden voor komen tussen waarden die door verschillende verleners worden geproduceerd. De waarde ' JTI ' is een hoofdletter gevoelige teken reeks. [RFC 7519, sectie 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | De claim ' NBF ' (niet vóór) identificeert de tijd waarna de JWT niet moet worden geaccepteerd voor verwerking. [RFC 7519, sectie 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Het gebruik van de huidige tijd is van toepassing. 
sub | ClientID | De claim ' sub ' (subject) identificeert het onderwerp van de JWT, in dit geval ook voor uw toepassing. Gebruik dezelfde waarde als `iss` . 

### <a name="signature"></a>Handtekening

De hand tekening wordt berekend door het certificaat toe te passen zoals beschreven in de [RFC7519-specificatie van JSON Web token](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Voor beeld van een gedecodeerde JWT-bevestiging

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Voor beeld van een gecodeerde JWT-bevestiging

De volgende teken reeks is een voor beeld van een gecodeerde bevestiging. Als u er voorzichtig van gaat, ziet u drie secties, gescheiden door punten ( `.` ):

* De eerste sectie codeert de *header*
* In het tweede gedeelte worden de *claims* (Payload) gecodeerd
* De laatste sectie is de *hand tekening* die wordt berekend met de certificaten uit de inhoud van de eerste twee secties

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Uw certificaat registreren bij micro soft Identity platform

U kunt de certificaat referentie koppelen aan de client toepassing in het micro soft Identity-platform via de Azure Portal een van de volgende methoden gebruiken:

### <a name="uploading-the-certificate-file"></a>Het certificaat bestand uploaden

In de registratie van de Azure-app voor de client toepassing:
1. Selecteer **Certificaten en geheimen**.
2. Klik op **certificaat uploaden** en selecteer het certificaat bestand dat u wilt uploaden.
3. Klik op **Add**.
  Zodra het certificaat is geüpload, worden de vinger afdruk, de start datum en de verval waarden weer gegeven.

### <a name="updating-the-application-manifest"></a>Het toepassings manifest bijwerken

Na het verkrijgen van een certificaat berekent u deze waarden:

- `$base64Thumbprint` -Met base64 gecodeerde waarde van de certificaat-hash
- `$base64Value` -Base64-gecodeerde waarde van de onbewerkte gegevens van het certificaat

Geef een GUID op om de sleutel te identificeren in het manifest van de toepassing ( `$keyId` ).

In de registratie van de Azure-app voor de client toepassing:
1. Selecteer **manifest** om het toepassings manifest te openen.
2. Vervang de eigenschap voor kenmerk *referenties* door uw nieuwe certificaat gegevens met behulp van het volgende schema.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Sla de wijzigingen op in het manifest van de toepassing en upload het manifest naar het micro soft Identity-platform.

   De `keyCredentials` eigenschap is meerdere waarden, dus u kunt meerdere certificaten uploaden voor uitgebreid sleutel beheer.
   
## <a name="using-a-client-assertion"></a>Een client bevestiging gebruiken

Client verklaringen kunnen overal worden gebruikt waar een client geheim zou worden gebruikt.  In de [autorisatie code stroom](v2-oauth2-auth-code-flow.md)kunt u bijvoorbeeld door geven in een `client_secret` om te bewijzen dat de aanvraag afkomstig is uit uw app. U kunt dit vervangen door `client_assertion` en `client_assertion_type` para meters. 

| Parameter | Waarde | Beschrijving|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Dit is een vaste waarde die aangeeft dat u een certificaat referentie gebruikt. |
|`client_assertion`| JWT |Dit is de JWT die hierboven is gemaakt. |

## <a name="next-steps"></a>Volgende stappen

De [MSAL.net-bibliotheek verwerkt dit scenario](msal-net-client-assertions.md) in één regel code.

De [.net core daemon-console toepassing met behulp van het micro soft Identity platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) -code voorbeeld op github laat zien hoe een toepassing zijn eigen referenties voor verificatie gebruikt. Ook wordt uitgelegd hoe u [een zelfondertekend certificaat kunt maken](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) met behulp van de `New-SelfSignedCertificate` Power shell-cmdlet. U kunt ook de scripts voor het [maken van apps](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) in het voor beeld-opslag plaats gebruiken om certificaten te maken, de vinger afdruk te berekenen, enzovoort.
