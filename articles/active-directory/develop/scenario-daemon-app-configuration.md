---
title: Daemon-apps configureren die web-Api's aanroepen-micro soft Identity-platform | Azure
description: Meer informatie over het configureren van de code voor uw daemon-toepassing die web-Api's aanroept (app-configuratie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/19/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 3c52d4d80fd3c77cff5e335967fc9d109212ce29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578427"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Daemon-app die web-Api's aanroept-code configuratie

Meer informatie over het configureren van de code voor uw daemon-toepassing die web-Api's aanroept.

## <a name="microsoft-libraries-supporting-daemon-apps"></a>Micro soft libraries ondersteunen daemon-apps

De volgende micro soft-bibliotheken ondersteunen daemon-apps:

[!INCLUDE [active-directory-develop-libraries-daemon](../../../includes/active-directory-develop-libraries-daemon.md)]

## <a name="configure-the-authority"></a>De instantie configureren

Voor daemon-toepassingen worden toepassings machtigingen gebruikt in plaats van gedelegeerde machtigingen. Het ondersteunde account type kan dus geen account zijn in een organisatorische map of persoonlijke Microsoft-account (bijvoorbeeld Skype, Xbox, Outlook.com). Er is geen Tenant beheerder om toestemming te verlenen voor een daemon-toepassing voor een persoonlijk micro soft-account. U moet *accounts in mijn organisatie* of *accounts in een organisatie* kiezen.

De in de toepassings configuratie opgegeven instantie moet worden getenantd (met een Tenant-ID of een domein naam die is gekoppeld aan uw organisatie).

Zelfs als u een multi tenant-hulp programma wilt opgeven, gebruikt u een Tenant-ID of domein naam en **niet** `common` of `organizations` met deze stroom, omdat de service niet betrouwbaar kan afleiden welke Tenant moet worden gebruikt.

## <a name="configure-and-instantiate-the-application"></a>De toepassing configureren en instantiëren

In MSAL-bibliotheken worden de client referenties (geheim of certificaat) door gegeven als para meter van de client toepassings constructie.

> [!IMPORTANT]
> Zelfs als uw toepassing een-console toepassing is die als een service wordt uitgevoerd, moet deze een een vertrouwelijke client toepassing zijn.

### <a name="configuration-file"></a>Configuratiebestand

Het configuratie bestand definieert het volgende:

- De Cloud instantie en Tenant-ID, die samen de *instantie* vormen.
- De client-ID die u hebt ontvangen van de registratie van de toepassing.
- Een client geheim of een certificaat.

# <a name="net"></a>[.NET](#tab/dotnet)

Hier volgt een voor beeld van het definiëren van de configuratie in een [*appsettings.jsin*](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/daemon-console/appsettings.json) het bestand. Dit voor beeld is afkomstig uit het voor beeld van een [.net Core-Console-daemon](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) op github.

```json
{
  "Instance": "https://login.microsoftonline.com/{0}",
  "Tenant": "[Enter here the tenantID or domain name for your Azure AD tenant]",
  "ClientId": "[Enter here the ClientId for your application]",
  "ClientSecret": "[Enter here a client secret for your application]",
  "CertificateName": "[Or instead of client secret: Enter here the name of a certificate (from the user cert store) as registered with your application]"
}
```

U geeft een `ClientSecret` of een op `CertificateName` . Deze instellingen zijn exclusief.

# <a name="java"></a>[Java](#tab/java)

```Java
 private final static String CLIENT_ID = "";
 private final static String AUTHORITY = "https://login.microsoftonline.com/<tenant>/";
 private final static String CLIENT_SECRET = "";
 private final static Set<String> SCOPE = Collections.singleton("https://graph.microsoft.com/.default");
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Configuratie parameters voor het [Node.js-daemon](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console/) -voor beeld bevinden zich in een *. env* -bestand:

```Text 
# Credentials
TENANT_ID=Enter_the_Tenant_Info_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

# <a name="python"></a>[Python](#tab/python)

Wanneer u een vertrouwelijke client met client geheimen bouwt, is hetparameters.jsin het configuratie bestand in het [python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) - [voor](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/1-Call-MsGraph-WithSecret/parameters.json) beeld als volgt:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "secret": "The secret generated by AAD during your confidential app registration",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

Wanneer u een vertrouwelijke client met certificaten bouwt, is het [parameters.jsvan](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/parameters.json) het configuratie bestand in het [python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) -voor beeld als volgt:

```Json
{
  "authority": "https://login.microsoftonline.com/<your_tenant_id>",
  "client_id": "your_client_id",
  "scope": [ "https://graph.microsoft.com/.default" ],
  "thumbprint": "790E... The thumbprint generated by AAD when you upload your public cert",
  "private_key_file": "server.pem",
  "endpoint": "https://graph.microsoft.com/v1.0/users"
}
```

---

### <a name="instantiate-the-msal-application"></a>De MSAL-toepassing instantiëren

Als u de MSAL-toepassing wilt instantiëren, moet u het MSAL-pakket toevoegen, raadplegen of importeren (afhankelijk van de taal).

De bouw is anders, afhankelijk van of u client geheimen of certificaten gebruikt (of, als een geavanceerd scenario, ondertekende beweringen).

#### <a name="reference-the-package"></a>Verwijzen naar het pakket

Raadpleeg het MSAL-pakket in de code van uw toepassing.

# <a name="net"></a>[.NET](#tab/dotnet)

Voeg het pakket [micro soft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) NuGet toe aan uw toepassing en voeg vervolgens een `using` instructie in uw code toe om hiernaar te verwijzen.

In MSAL.NET wordt de vertrouwelijke client toepassing vertegenwoordigd door de `IConfidentialClientApplication` interface.

```csharp
using Microsoft.Identity.Client;
IConfidentialClientApplication app;
```

# <a name="java"></a>[Java](#tab/java)

```java
import com.microsoft.aad.msal4j.ClientCredentialFactory;
import com.microsoft.aad.msal4j.ClientCredentialParameters;
import com.microsoft.aad.msal4j.ConfidentialClientApplication;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.IClientCredential;
import com.microsoft.aad.msal4j.MsalException;
import com.microsoft.aad.msal4j.SilentParameters;
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

U hoeft alleen de pakketten te installeren door `npm install` deze uit te voeren in de map waar *package.jsin* het bestand zich bevindt. Importeer vervolgens **het msal-knooppunt** pakket:

```JavaScript 
const msal = require('@azure/msal-node');
```

# <a name="python"></a>[Python](#tab/python)

```python
import msal
import json
import sys
import logging
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-secret"></a>De vertrouwelijke client toepassing instantiëren met een client geheim

Dit is de code voor het instantiëren van de vertrouwelijke client toepassing met een client geheim:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Het `Authority` is een samen voeging van het Cloud exemplaar en de Tenant-id, bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com` of `https://login.microsoftonline.com/eb1ed152-0000-0000-0000-32401f3f9abd` . In de *appsettings.jsvoor* het bestand dat wordt weer gegeven in de sectie [configuratie bestand](#configuration-file) , worden deze weer gegeven met de `Instance` `Tenant` waarden en respectievelijk.

In het code voorbeeld van het vorige fragment is opgehaald uit, `Authority` is een eigenschap van de klasse  [AuthenticationConfig](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/ffc4a9f5d9bdba5303e98a1af34232b434075ac7/1-Call-MSGraph/daemon-console/AuthenticationConfig.cs#L61-L70) en is als volgt gedefinieerd:

```csharp
/// <summary>
/// URL of the authority
/// </summary>
public string Authority
{
    get
    {
        return String.Format(CultureInfo.InvariantCulture, Instance, Tenant);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```JavaScript

const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const cca = new msal.ConfidentialClientApplication(msalConfig);
```

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

#### <a name="instantiate-the-confidential-client-application-with-a-client-certificate"></a>De vertrouwelijke client toepassing instantiëren met een client certificaat

Hier volgt de code voor het bouwen van een toepassing met een certificaat:

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```
# <a name="java"></a>[Java](#tab/java)

In MSAL Java zijn er twee bouwers voor het instantiëren van de vertrouwelijke client toepassing met certificaten:

```Java

InputStream pkcs12Certificate = ... ; /* Containing PCKS12-formatted certificate*/
string certificatePassword = ... ;    /* Contains the password to access the certificate */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(pkcs12Certificate, certificatePassword);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

of

```Java
PrivateKey key = getPrivateKey(); /* RSA private key to sign the assertion */
X509Certificate publicCertificate = getPublicCertificate(); /* x509 public certificate used as a thumbprint */

IClientCredential credential = ClientCredentialFactory.createFromCertificate(key, publicCertificate);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

De voorbeeld toepassing implementeert op dit moment geen initialisatie met certificaten.

# <a name="python"></a>[Python](#tab/python)

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

---

#### <a name="advanced-scenario-instantiate-the-confidential-client-application-with-client-assertions"></a>Geavanceerd scenario: de vertrouwelijke client toepassing instantiëren met client verklaringen

# <a name="net"></a>[.NET](#tab/dotnet)

In plaats van een client geheim of een certificaat kan de vertrouwelijke client toepassing ook de identiteit bewijzen door gebruik te maken van client verklaringen.

MSAL.NET heeft twee methoden om ondertekende bevestigingen te bieden aan de vertrouwelijke client-app:

- `.WithClientAssertion()`
- `.WithClientClaims()`

Wanneer u gebruikt `WithClientAssertion` , moet u een ondertekende JWT opgeven. Dit geavanceerde scenario wordt beschreven in [client verklaringen](msal-net-client-assertions.md).

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

Wanneer u gebruikt `WithClientClaims` , produceert MSAL.net een ondertekende bevestiging die de claims bevat die worden verwacht door Azure AD, plus aanvullende client claims die u wilt verzenden.
Deze code laat zien hoe u dit doet:

```csharp
string ipAddress = "192.168.1.2";
var claims = new Dictionary<string, string> { { "client_ip", ipAddress } };
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, claims)
                                          .Build();
```

Zie voor meer informatie [client verklaringen](msal-net-client-assertions.md).

# <a name="java"></a>[Java](#tab/java)

```Java
IClientCredential credential = ClientCredentialFactory.createFromClientAssertion(assertion);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

De voorbeeld toepassing implementeert op dit moment geen initialisatie met beweringen.

# <a name="python"></a>[Python](#tab/python)

In MSAL python kunt u client claims opgeven door gebruik te maken van de claims die worden ondertekend door de `ConfidentialClientApplication` persoonlijke sleutel van deze persoon.

```Python
# Pass the parameters.json file as an argument to this Python script. E.g.: python your_py_file.py parameters.json
config = json.load(open(sys.argv[1]))

# Create a preferably long-lived app instance that maintains a token cache.
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential={"thumbprint": config["thumbprint"], "private_key": open(config['private_key_file']).read()},
    client_claims = {"client_ip": "x.x.x.x"}
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

Zie de naslag documentatie voor MSAL python voor [ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)voor meer informatie.

---

## <a name="next-steps"></a>Volgende stappen

# <a name="net"></a>[.NET](#tab/dotnet)

Ga naar het volgende artikel in dit scenario en [Verkrijg een token voor de app](./scenario-daemon-acquire-token.md?tabs=dotnet).

# <a name="java"></a>[Java](#tab/java)

Ga naar het volgende artikel in dit scenario en [Verkrijg een token voor de app](./scenario-daemon-acquire-token.md?tabs=java).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ga naar het volgende artikel in dit scenario en [Verkrijg een token voor de app](./scenario-daemon-acquire-token.md?tabs=nodejs).

# <a name="python"></a>[Python](#tab/python)

Ga naar het volgende artikel in dit scenario en [Verkrijg een token voor de app](./scenario-daemon-acquire-token.md?tabs=python).

---
