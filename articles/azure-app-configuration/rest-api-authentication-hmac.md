---
title: Configuratie REST API-HMAC-verificatie Azure-app
description: HMAC gebruiken voor verificatie bij Azure-app configuratie met behulp van de REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4171155f5a9f72ef0c021bd0e37fe4ec2f206646
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95253351"
---
# <a name="hmac-authentication---rest-api-reference"></a>HMAC-verificatie-REST API referentie

U kunt HTTP-aanvragen verifiëren met behulp van het HMAC-SHA256-verificatie schema. (HMAC verwijst naar verificatie code op basis van hash-berichten.) Deze aanvragen moeten via TLS worden verzonden.

## <a name="prerequisites"></a>Vereisten

- **Referentie** - \<Access Key ID\>
- De **geheime** sleutel waarde voor een gedecodeerde toegangs code. ``base64_decode(<Access Key Value>)``

De waarden voor referentie (ook wel genoemd `id` ) en geheim (ook wel genoemd `value` ) moeten worden verkregen van het exemplaar van Azure-app configuratie. U kunt dit doen met behulp van de [Azure Portal](https://portal.azure.com) of de [Azure cli](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true).

Geef elke aanvraag met alle HTTP-headers die vereist zijn voor authenticatie. De mini maal vereiste waarde is:

|  Aanvraagheader | Beschrijving  |
| --------------- | ------------ |
| **Host** | De Internet-host en het poort nummer. Zie sectie  [3.2.2](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.2.2)voor meer informatie. |
| **Datum** | De datum en tijd waarop de aanvraag afkomstig is. De waarde mag niet meer dan 15 minuten uit de huidige Coordinated Universal Time (Greenwich Mean Time) zijn. De waarde is een HTTP-datum, zoals beschreven in sectie [3.3.1](https://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.3.1).
| **x-MS-date** | Hetzelfde als ```Date``` hierboven. U kunt dit in plaats daarvan gebruiken wanneer de agent niet rechtstreeks toegang heeft tot de ```Date``` aanvraag header of een proxy wijzigt. Als ```x-ms-date``` en ```Date``` beide zijn verschaft, ```x-ms-date``` heeft dit prioriteit. |
| **x-MS-content-sha256** | Base64 Encoded SHA256-hash van de aanvraag tekst. Het moet worden vermeld, zelfs als er geen hoofd tekst is. ```base64_encode(SHA256(body))```|
| **Autorisatie** | Verificatie gegevens die worden vereist door het HMAC-SHA256-schema. Indeling en Details worden verderop in dit artikel beschreven. |

**Voorbeeld:**

```http
Host: {myconfig}.azconfig.io
Date: Fri, 11 May 2018 18:48:36 GMT
x-ms-content-sha256: {SHA256 hash of the request body}
Authorization: HMAC-SHA256 Credential={Access Key ID}&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature={Signature}
```

## <a name="authorization-header"></a>Autorisatie-header

### <a name="syntax"></a>Syntax

``Authorization``: **HMAC-sha256**```Credential```=\<value\>&```SignedHeaders```=\<value\>&```Signature```=\<value\>

|  Argument | Description  |
| ------ | ------ |
| **HMAC-SHA256** | Autorisatie schema. _lang_ |
| **Referentie** | De ID van de toegangs sleutel die wordt gebruikt voor het berekenen van de hand tekening. _lang_ |
| **SignedHeaders** | HTTP-aanvraag headers worden toegevoegd aan de hand tekening. _lang_ |
| **Handtekening** | Base64 Encoded HMACSHA256 van teken reeks-naar-teken. _lang_|

### <a name="credential"></a>Referentie

De ID van de toegangs sleutel die wordt gebruikt om de hand tekening te berekenen.

### <a name="signed-headers"></a>Ondertekende headers

Namen van HTTP-aanvraag headers, gescheiden door punt komma's, die vereist zijn voor het ondertekenen van de aanvraag. Deze HTTP-headers moeten ook correct worden voorzien van de aanvraag. Gebruik geen spaties.

### <a name="required-http-request-headers"></a>Vereiste HTTP-aanvraag headers

```x-ms-date```[of ```Date``` ]; ```host``` ;```x-ms-content-sha256```

Eventuele andere HTTP-aanvraag headers kunnen ook worden toegevoegd aan het ondertekenen. U kunt ze gewoon toevoegen aan het ```SignedHeaders``` argument.

**Voorbeeld:**

x-MS-date; host; x-MS-content-sha256; ```Content-Type``` ;```Accept```

### <a name="signature"></a>Handtekening

Met base64 gecodeerde HMACSHA256-hash van de teken reeks-naar-teken. Er wordt gebruikgemaakt van de toegangs sleutel die wordt geïdentificeerd door `Credential` .
```base64_encode(HMACSHA256(String-To-Sign, Secret))```

### <a name="string-to-sign"></a>Teken reeks-naar-teken

Het is een canonieke representatie van de aanvraag:

_Teken reeks-naar-teken =_

**HTTP_METHOD** + ' \n ' + **path_and_query** + ' \n ' + **signed_headers_values**

|  Argument | Description  |
| ------ | ------ |
| **HTTP_METHOD** | De naam van de HTTP-methode die wordt gebruikt in combi natie met de aanvraag. Zie [Section 9](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)(Engelstalig) voor meer informatie. |
|**path_and_query** | Het samen voegen van de aanvraag absolute URI het pad en de query reeks. Zie voor meer informatie [sectie 3,3](https://tools.ietf.org/html/rfc3986#section-3.3).
| **signed_headers_values** | Door punt komma's gescheiden waarden van alle HTTP-aanvraag headers die worden vermeld in `SignedHeaders` . De indeling volgt `SignedHeaders` semantiek. |

**Voorbeeld:**

```js
string-To-Sign=
            "GET" + '\n' +                                                                                  // VERB
            "/kv?fields=*&api-version=1.0" + '\n' +                                                         // path_and_query
            "Fri, 11 May 2018 18:48:36 GMT;{myconfig}.azconfig.io;{value of ms-content-sha256 header}"      // signed_headers_values
```


### <a name="errors"></a>Fouten

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Reden:** De header van de autorisatie aanvraag met het HMAC-SHA256-schema is niet ingevuld.

**Oplossing:** Geef een geldige ```Authorization``` HTTP-aanvraag header op.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="The access token has expired", Bearer
```

**Reden:** ```Date``` of ```x-ms-date``` de aanvraag header is meer dan 15 minuten uit de huidige Coordinated Universal Time (Greenwich Mean Time).

**Oplossing:** Geef de juiste datum en tijd op.


```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid access token date", Bearer
```

**Reden:** Ontbrekende of ongeldige ```Date``` of ```x-ms-date``` aanvraag header.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="[Credential][SignedHeaders][Signature] is required", Bearer
```

**Reden:** Er ontbreekt een vereiste para meter in de ```Authorization``` aanvraag header.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Credential", Bearer
```

**Reden:** De gegeven [ ```Host``` ]/[toegangs sleutel-id] is niet gevonden.

**Oplossing:** Controleer de ```Credential``` para meter van de ```Authorization``` aanvraag header. Zorg ervoor dat het een geldige toegangs sleutel-ID is en zorg ervoor dat de ```Host``` header naar het geregistreerde account wijst.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature", Bearer
```

**Reden:** De ```Signature``` opgegeven waarde komt niet overeen met wat de server verwacht.

**Oplossing:** Controleer of het ```String-To-Sign``` juist is. Controleer of het ```Secret``` juist is en correct wordt gebruikt (met base64 gedecodeerd vóór het gebruik).

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Signed request header 'xxx' is not provided", Bearer
```

**Reden:** De aanvraag header ontbreekt die is vereist voor ```SignedHeaders``` de para meter in de  ```Authorization``` koptekst.

**Oplossing:** Geef de vereiste header op, met de juiste waarde.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="XXX is required as a signed header", Bearer
```

**Reden:** Ontbrekende para meter in ```SignedHeaders``` .

**Oplossing:** Controleer de minimale vereisten voor de ondertekende headers.

## <a name="code-snippets"></a>Codefragmenten

### <a name="javascript"></a>Javascript

*Vereisten*: [crypto-js](https://code.google.com/archive/p/crypto-js/)

```js
function signRequest(host, 
                     method,      // GET, PUT, POST, DELETE
                     url,         // path+query
                     body,        // request body (undefined of none)
                     credential,  // access key id
                     secret)      // access key value (base64 encoded)
{
        var verb = method.toUpperCase();
        var utcNow = new Date().toUTCString();
        var contentHash = CryptoJS.SHA256(body).toString(CryptoJS.enc.Base64);

        //
        // SignedHeaders
        var signedHeaders = "x-ms-date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = 
            verb + '\n' +                              // VERB
            url + '\n' +                               // path_and_query
            utcNow + ';' + host + ';' + contentHash;   // Semicolon separated SignedHeaders values

        //
        // Signature
        var signature = CryptoJS.HmacSHA256(stringToSign, CryptoJS.enc.Base64.parse(secret)).toString(CryptoJS.enc.Base64);

        //
        // Result request headers
        return [
            { name: "x-ms-date", value: utcNow },
            { name: "x-ms-content-sha256", value: contentHash },
            { name: "Authorization", value: "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signedHeaders + "&Signature=" + signature }
        ];
}
```

### <a name="c"></a>C#

```csharp
using (var client = new HttpClient())
{
    var request = new HttpRequestMessage()
    {
        RequestUri = new Uri("https://{config store name}.azconfig.io/kv?api-version=1.0"),
        Method = HttpMethod.Get
    };

    //
    // Sign the request
    request.Sign(<Credential>, <Secret>);

    await client.SendAsync(request);
}

static class HttpRequestMessageExtensions
{
    public static HttpRequestMessage Sign(this HttpRequestMessage request, string credential, byte[] secret)
    {
        string host = request.RequestUri.Authority;
        string verb = request.Method.ToString().ToUpper();
        DateTimeOffset utcNow = DateTimeOffset.UtcNow;
        string contentHash = Convert.ToBase64String(request.Content.ComputeSha256Hash());

        //
        // SignedHeaders
        string signedHeaders = "date;host;x-ms-content-sha256"; // Semicolon separated header names

        //
        // String-To-Sign
        var stringToSign = $"{verb}\n{request.RequestUri.PathAndQuery}\n{utcNow.ToString("r")};{host};{contentHash}";

        //
        // Signature
        string signature;

        using (var hmac = new HMACSHA256(secret))
        {
            signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.ASCII.GetBytes(stringToSign)));
        }

        //
        // Add headers
        request.Headers.Date = utcNow;
        request.Headers.Add("x-ms-content-sha256", contentHash);
        request.Headers.Authorization = new AuthenticationHeaderValue("HMAC-SHA256", $"Credential={credential}&SignedHeaders={signedHeaders}&Signature={signature}");

        return request;
    }
}

static class HttpContentExtensions
{
    public static byte[] ComputeSha256Hash(this HttpContent content)
    {
        using (var stream = new MemoryStream())
        {
            if (content != null)
            {
                content.CopyToAsync(stream).Wait();
                stream.Seek(0, SeekOrigin.Begin);
            }

            using (var alg = SHA256.Create())
            {
                return alg.ComputeHash(stream.ToArray());
            }
        }
    }
}
```

### <a name="java"></a>Java

```java
public CloseableHttpResponse signRequest(HttpUriRequest request, String credential, String secret)
        throws IOException, URISyntaxException {
    Map<String, String> authHeaders = generateHeader(request, credential, secret);
    authHeaders.forEach(request::setHeader);

    return httpClient.execute(request);
}

private static Map<String, String> generateHeader(HttpUriRequest request, String credential, String secret) 
        throws URISyntaxException, IOException {
    String requestTime = GMT_DATE_FORMAT.format(new Date());

    String contentHash = buildContentHash(request);
    // SignedHeaders
    String signedHeaders = "x-ms-date;host;x-ms-content-sha256";

    // Signature
    String methodName = request.getRequestLine().getMethod().toUpperCase();
    URIBuilder uri = new URIBuilder(request.getRequestLine().getUri());
    String scheme = uri.getScheme() + "://";
    String requestPath = uri.toString().substring(scheme.length()).substring(uri.getHost().length());
    String host = new URIBuilder(request.getRequestLine().getUri()).getHost();
    String toSign = String.format("%s\n%s\n%s;%s;%s", methodName, requestPath, requestTime, host, contentHash);

    byte[] decodedKey = Base64.getDecoder().decode(secret);
    String signature = Base64.getEncoder().encodeToString(new HmacUtils(HMAC_SHA_256, decodedKey).hmac(toSign));

    // Compose headers
    Map<String, String> headers = new HashMap<>();
    headers.put("x-ms-date", requestTime);
    headers.put("x-ms-content-sha256", contentHash);

    String authorization = String.format("HMAC-SHA256 Credential=%s, SignedHeaders=%s, Signature=%s",
            credential, signedHeaders, signature);
    headers.put("Authorization", authorization);

    return headers;
}

private static String buildContentHash(HttpUriRequest request) throws IOException {
    String content = "";
    if (request instanceof HttpEntityEnclosingRequest) {
        try {
            StringWriter writer = new StringWriter();
            IOUtils.copy(((HttpEntityEnclosingRequest) request).getEntity().getContent(), writer,
                    StandardCharsets.UTF_8);

            content = writer.toString();
        }
        finally {
            ((HttpEntityEnclosingRequest) request).getEntity().getContent().close();
        }
    }

    byte[] digest = new DigestUtils(SHA_256).digest(content);
    return Base64.getEncoder().encodeToString(digest);
}
```

### <a name="golang"></a>Golang

```golang
import (
    "bytes"
    "crypto/hmac"
    "crypto/sha256"
    "encoding/base64"
    "io/ioutil"
    "net/http"
    "strings"
    "time"
)

//SignRequest Setup the auth header for accessing Azure App Configuration service
func SignRequest(id string, secret string, req *http.Request) error {
    method := req.Method
    host := req.URL.Host
    pathAndQuery := req.URL.Path
    if req.URL.RawQuery != "" {
        pathAndQuery = pathAndQuery + "?" + req.URL.RawQuery
    }

    content, err := ioutil.ReadAll(req.Body)
    if err != nil {
        return err
    }
    req.Body = ioutil.NopCloser(bytes.NewBuffer(content))

    key, err := base64.StdEncoding.DecodeString(secret)
    if err != nil {
        return err
    }

    timestamp := time.Now().UTC().Format(http.TimeFormat)
    contentHash := getContentHashBase64(content)
    stringToSign := fmt.Sprintf("%s\n%s\n%s;%s;%s", strings.ToUpper(method), pathAndQuery, timestamp, host, contentHash)
    signature := getHmac(stringToSign, key)

    req.Header.Set("x-ms-content-sha256", contentHash)
    req.Header.Set("x-ms-date", timestamp)
    req.Header.Set("Authorization", "HMAC-SHA256 Credential="+id+", SignedHeaders=x-ms-date;host;x-ms-content-sha256, Signature="+signature)

    return nil
}

func getContentHashBase64(content []byte) string {
    hasher := sha256.New()
    hasher.Write(content)
    return base64.StdEncoding.EncodeToString(hasher.Sum(nil))
}

func getHmac(content string, key []byte) string {
    hmac := hmac.New(sha256.New, key)
    hmac.Write([]byte(content))
    return base64.StdEncoding.EncodeToString(hmac.Sum(nil))
}
```

### <a name="python"></a>Python

```python

import base64
import hashlib
import hmac
from datetime import datetime
import six

def sign_request(host,
                method,     # GET, PUT, POST, DELETE
                url,        # Path + Query
                body,       # Request body 
                credential, # Access Key ID
                secret):    # Access Key Value
    verb = method.upper()

    utc_now = str(datetime.utcnow().strftime("%b, %d %Y %H:%M:%S ")) + "GMT"

    if six.PY2:
        content_digest = hashlib.sha256(bytes(body)).digest()
    else:
        content_digest = hashlib.sha256(bytes(body, 'utf-8')).digest()

    content_hash = base64.b64encode(content_digest).decode('utf-8')

    # Signed Headers
    signed_headers = "x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names

    # String-To-Sign
    string_to_sign = verb + '\n' + \
                    url + '\n' + \
                    utc_now + ';' + host + ';' + content_hash  # Semicolon separated SignedHeaders values

    # Decode secret
    if six.PY2:
        decoded_secret = base64.b64decode(secret)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign), hashlib.sha256).digest()
    else:
        decoded_secret = base64.b64decode(secret, validate=True)
        digest = hmac.new(decoded_secret, bytes(
            string_to_sign, 'utf-8'), hashlib.sha256).digest()

    # Signature
    signature = base64.b64encode(digest).decode('utf-8')

    # Result request headers
    return {
        "x-ms-date": utc_now,
        "x-ms-content-sha256": content_hash,
        "Authorization": "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=" + signed_headers + "&Signature=" + signature
    }
```

### <a name="powershell"></a>PowerShell

```PowerShell
function Sign-Request(
    [string] $hostname,
    [string] $method,      # GET, PUT, POST, DELETE
    [string] $url,         # path+query
    [string] $body,        # request body
    [string] $credential,  # access key id
    [string] $secret       # access key value (base64 encoded)
)
{  
    $verb = $method.ToUpperInvariant()
    $utcNow = (Get-Date).ToUniversalTime().ToString("R", [Globalization.DateTimeFormatInfo]::InvariantInfo)
    $contentHash = Compute-SHA256Hash $body

    $signedHeaders = "x-ms-date;host;x-ms-content-sha256";  # Semicolon separated header names

    $stringToSign = $verb + "`n" +
                    $url + "`n" +
                    $utcNow + ";" + $hostname + ";" + $contentHash  # Semicolon separated signedHeaders values

    $signature = Compute-HMACSHA256Hash $secret $stringToSign

    # Return request headers
    return @{
        "x-ms-date" = $utcNow;
        "x-ms-content-sha256" = $contentHash;
        "Authorization" = "HMAC-SHA256 Credential=" + $credential + "&SignedHeaders=" + $signedHeaders + "&Signature=" + $signature
    }
}

function Compute-SHA256Hash(
    [string] $content
)
{
    $sha256 = [System.Security.Cryptography.SHA256]::Create()
    try {
        return [Convert]::ToBase64String($sha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $sha256.Dispose()
    }
}

function Compute-HMACSHA256Hash(
    [string] $secret,      # base64 encoded
    [string] $content
)
{
    $hmac = [System.Security.Cryptography.HMACSHA256]::new([Convert]::FromBase64String($secret))
    try {
        return [Convert]::ToBase64String($hmac.ComputeHash([Text.Encoding]::ASCII.GetBytes($content)))
    }
    finally {
        $hmac.Dispose()
    }
}

# Stop if any error occurs
$ErrorActionPreference = "Stop"

$uri = [System.Uri]::new("https://{myconfig}.azconfig.io/kv?api-version=1.0")
$method = "GET"
$body = $null
$credential = "<Credential>"
$secret = "<Secret>"

$headers = Sign-Request $uri.Authority $method $uri.PathAndQuery $body $credential $secret
Invoke-RestMethod -Uri $uri -Method $method -Headers $headers -Body $body
```

### <a name="bash"></a>Bash

*Vereisten*:

| Vereiste | Opdracht | Geteste versies |
| ------------ | ------- | --------------- |
| [Bash](https://www.gnu.org/software/bash/) | bash | 3.5.27, 4.4.23 |
| [coreutils](https://www.gnu.org/software/coreutils/) | tr | 8,28 |
| [curl](https://curl.haxx.se/) | curl | 7.55.1, 7.58.0 |
| [OpenSSL](https://www.openssl.org/) | openssl | 1.1.0 g, 1.1.1 a |
| [util-linux](https://github.com/karelzak/util-linux/) | hexdump | 2.14.1, 2.31.1 |

```bash
#!/bin/bash

sign_request () {
    local host="$1"
    local method="$2"      # GET, PUT, POST, DELETE
    local url="$3"         # path+query
    local body="$4"        # request body
    local credential="$5"  # access key id
    local secret="$6"      # access key value (base64 encoded)

    local verb=$(printf "$method" | tr '[:lower:]' '[:upper:]')
    local utc_now="$(date -u '+%a, %d %b %Y %H:%M:%S GMT')"
    local content_hash="$(printf "$body" | openssl sha256 -binary | base64)"

    local signed_headers="x-ms-date;host;x-ms-content-sha256"  # Semicolon separated header names
    local string_to_sign="$verb\n$url\n$utc_now;$host;$content_hash"  # Semicolon separated signed_headers values

    local decoded_secret="$(printf "$secret" | base64 -d | hexdump -v -e '/1 "%02x"')"
    local signature="$(printf "$string_to_sign" | openssl sha256 -mac HMAC -macopt hexkey:"$decoded_secret" -binary | base64)"

    # Output request headers
    printf '%s\n' \
           "x-ms-date: $utc_now" \
           "x-ms-content-sha256: $content_hash" \
           "Authorization: HMAC-SHA256 Credential=$credential&SignedHeaders=$signed_headers&Signature=$signature"
}

host="{config store name}.azconfig.io"
method="GET"
url="/kv?api-version=1.0"
body=""
credential="<Credential>"
secret="<Secret>"

headers=$(sign_request "$host" "$method" "$url" "$body" "$credential" "$secret")

while IFS= read -r line; do
    header_args+=("-H$line")
done <<< "$headers"
curl -X "$method" -d "$body" "${header_args[@]}" "https://$host$url"
```