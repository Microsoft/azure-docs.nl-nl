---
title: Client verklaringen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over ondertekende client verklaringen ondersteuning voor vertrouwelijke client toepassingen in de micro soft Authentication Library voor .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/18/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8fb4ecb8fa8d6938e9afbc77064380b7b213029a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578733"
---
# <a name="confidential-client-assertions"></a>Verklaringen van vertrouwelijke client

Om hun identiteit te bewijzen, wisselen vertrouwelijke client toepassingen een geheim uit met Azure AD. Het geheim kan het volgende zijn:
- Een client geheim (toepassings wachtwoord).
- Een certificaat dat wordt gebruikt voor het bouwen van een ondertekende bevestiging met standaard claims.

Dit geheim kan ook rechtstreeks een ondertekende bevestiging zijn.

MSAL.NET heeft vier methoden om referenties of beweringen te verstrekken aan de vertrouwelijke client-app:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Hoewel het mogelijk is om de `WithClientAssertion()` API te gebruiken voor het verkrijgen van tokens voor de vertrouwelijke client, raden we u aan deze niet standaard te gebruiken, omdat het geavanceerder is en is ontworpen voor het afhandelen van zeer specifieke scenario's die niet gebruikelijk zijn. Met behulp `.WithCertificate()` van de API kan MSAL.net dit voor u afhandelen. Deze API biedt u de mogelijkheid om uw verificatie aanvraag zo nodig aan te passen, maar de standaard bevestiging die is gemaakt door `.WithCertificate()` , is voldoende voor de meeste verificatie scenario's. Deze API kan ook worden gebruikt als tijdelijke oplossing in sommige scenario's waarbij MSAL.NET de ondertekening bewerking niet intern kan uitvoeren.

### <a name="signed-assertions"></a>Ondertekende verklaringen

Een ondertekende client bevestiging gaat in de vorm van een ondertekende JWT met de nettolading die de vereiste verificatie claims bevat die zijn voorgeschreven door Azure AD, base64-gecodeerd. U gebruikt deze als volgt:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

U kunt ook het gedelegeerde formulier gebruiken, waarmee u de bewering precies in tijd kunt berekenen:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(() => { return GetSignedClientAssertion(); } )
                                          .Build();
```

De [claims die worden verwacht door Azure AD](active-directory-certificate-credentials.md) in de ondertekende bevestiging zijn:

Claimtype | Waarde | Beschrijving
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | De claim ' AUD ' (doel groep) identificeert de ontvangers waarvoor de JWT is bedoeld (hier Azure AD) Zie [RFC 7519, sectie 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  In dit geval is die ontvanger de aanmeldings server (login.microsoftonline.com).
geldig | 1601519414 | De claim ' exp ' (verval tijd) identificeert de verval tijd op of waarna de JWT niet moet worden geaccepteerd voor verwerking. Zie [RFC 7519, sectie 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Op deze manier kan de bevestiging worden gebruikt tot dat tijdstip, dus kort 5-10 minuten na de `nbf` meeste.  Azure AD plaatst op dit moment geen beperkingen `exp` . 
ISS | ClientID | De claim ' ISS ' (verlener) identificeert de principal die de JWT heeft uitgegeven, in dit geval uw client toepassing.  Gebruik de GUID-toepassings-ID.
jti | (een GUID) | De claim ' JTI ' (JWT-ID) biedt een unieke id voor de JWT. De id-waarde moet worden toegewezen op een manier die ervoor zorgt dat er een Verwaarloos bare kans is dat dezelfde waarde per ongeluk wordt toegewezen aan een ander gegevens object. Als de toepassing meerdere verleners gebruikt, moeten conflicten worden voor komen tussen waarden die door verschillende verleners worden geproduceerd. De waarde ' JTI ' is een hoofdletter gevoelige teken reeks. [RFC 7519, sectie 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
NBF | 1601519114 | De claim ' NBF ' (niet vóór) identificeert de tijd waarna de JWT niet moet worden geaccepteerd voor verwerking. [RFC 7519, sectie 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Het gebruik van de huidige tijd is van toepassing. 
sub | ClientID | De claim ' sub ' (subject) identificeert het onderwerp van de JWT, in dit geval ook voor uw toepassing. Gebruik dezelfde waarde als `iss` . 

Hier volgt een voor beeld van het aanwijzen van deze claims:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

U gaat als volgt te werk om een ondertekende client bevestiging te bevestigen:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(Certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims())));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Alternatieve methode

U hebt ook de mogelijkheid om [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) te gebruiken om de bewering voor u te maken. De code wordt weer gegeven in het volgende voor beeld:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Zodra u de ondertekende client bevestiging hebt, kunt u deze gebruiken met de MSAL-api's zoals hieronder wordt weer gegeven.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

`WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` Er wordt standaard een ondertekende bevestiging geproduceerd met de claims die worden verwacht door Azure AD plus aanvullende client claims die u wilt verzenden. Hier volgt een code fragment waarmee u dit kunt doen.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Als een van de claims in de door u opgegeven woorden lijst hetzelfde is als een van de verplichte claims, wordt de waarde van de extra claim in rekening gebracht. Hiermee worden de claims overschreven die worden berekend door MSAL.NET.

Als u uw eigen claims wilt opgeven, met inbegrip van de verplichte claims die door Azure AD worden verwacht, geeft u `false` de `mergeWithDefaultClaims` para meter door.
